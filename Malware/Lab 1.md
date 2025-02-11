
File Hash & VT Analysis

find the cha356 value of the malware

~~~
sha256sum.exe filename
92730427321a1c4ccfc0d0580834daef98121efa9bb8963da332bfd6cf1fda8a *Malware.Unknown.exe.malz
~~~

md5 hash value
sha256
~~~
md5sum.exe filename
1d8562c0adcaee734d63f7baaca02f7c *Malware.Unknown.exe.malz
~~~

VT analysis:

The FLARE Obfuscated String Solver (FLOSS, formerly FireEye Labs Obfuscated String Solver) uses advanced static analysis techniques to automatically extract and deobfuscate all strings from malware binaries. You can use it just like `strings.exe` to enhance the basic static analysis of unknown binaries.

~~~
floss filename
~~~

floss output

~~~
jjjj
cmd.exe /C ping 1.1.1.1 -n 1 -w 3000 > Nul & Del /f /q "%s"
http://ssl-6582datamanager.helpdeskbros.local/favicon.ico
C:\Users\Public\Documents\CR433101.dat.exe
Mozilla/5.0
http://huskyhacks.dev
ping 1.1.1.1 -n 1 -w 3000 > Nul & C:\Users\Public\Documents\CR433101.dat.exe
open
~~~

IAT  & PEveiw

![[PEviewlab1.png]]





CPU instruction

MOV , JMP ,SUB, PUSH, POP

 Memory Register
 
 ~~~
eax  -  accumulator 

edx -   data 

ebx  -  base

esp -  extender stack pointer

ebp - extender base pointer

eip   -  exterder  instructor pointer

~~~


stack

0Xfffffffff

0x00000


