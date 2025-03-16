
Task
• Enumerate all domains in the techcorp.local forest.
• Map the trusts of the us.techcorp.local domain. 
• Map external trusts in techcorp.local forest. 
• Identify external trusts of us domain. Can you enumerate trusts for a trusting forest?

To enumerate all forest using ADModule

```
(Get-ADForst).Domains
```

To map the trusts of the us.techcorp.local domain:

```
Get-ADTrust -Filter *
```

If we want to map all the trusts of the techcorp.local forest:

```
Get-ADTrust -Filter 'intraForest -ne $True' -Server (GetADForest).Name
```

Now, to list only the external trusts, using the ActiveDirectory module:

```
(Get-ADForest).Domains | %{Get-ADTrust -Filter '(intraForest -ne $True) -and (ForestTransitive -ne $True)' -Server $_}
```

To list only the external trusts using Powerview:

```
Get-ForestDomain -Verbose | Get-DomainTrust | ?{$_.TrustAttributes -eq 'FILTER_SIDS'}
```

Note that we have a bi-directional trust with eu.local. In a bi-directional trust or incoming one-way trust from eu.local to us.techcorp.local, we can extract information from the eu.local forest. Let's go for the last task and enumerate trusts for eu.local forest using the Active Directory module

```
Get-ADTrust -Filter * -Server eu.local
```

using powerview:

```
Get-ForestTrust -Forest eu.local
```

