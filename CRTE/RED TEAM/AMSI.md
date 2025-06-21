


![[Pasted image 20250515235529.png]]

`amsi.dll` is loaded into any new processes to hook any input in the PowerShell command line or to analyze content for `[System.Reflection.Assembly]::Load()` calls. `amsi.dll` includes multiple functions that are used for analysis of data, for instance: **AmsiInitialize**, **AmsiOpenSession,** **AmsiScanbuffer**, etc.

