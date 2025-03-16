
• Enumerate following for the us.techcorp.local domain: 
− ACL for the Domain Admins group
− All modify rights/permissions for the studentuserx


To enumerate ACL

```
Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs - Verbose
```

Now, to check for modify rights/permissions for the studentuserx, we can use Find-InterestingDomainACL from PowerView.

```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "studentuserx"}
```

We don't get any output. This means studentuserx has no modify permissions on any object in the domain.

try for the StudentUsers group

```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "StudentUsers"}
```

