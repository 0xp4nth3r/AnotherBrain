
- **User Mode Initiation (GUI/CLI/Script)**
    
- **Windows API / Subsystem Interface**
    
- **NT Loader / NTDLL.dll**
    
- **Kernel Transition (System Calls)**
    
- **Process Creation in Kernel**
    
- **Thread Creation**
    
- **Loader Loads Executable**
    
- **Entry Point Execution**

![[Pasted image 20250418221206.png]]

1. **Applications** represent the programs that users interact with, like “firefox.exe”

2. Applications make calls to Dynamic Link Libraries**(DLLs**) to leverage Windows functionalities without needing direct access to lower-level code. Some of these DLLs might handle tasks such as user interface operations or file management

3. **Kernel32.dll** is one of the core DLLs in the Windows operating system. It provides essential functions for memory management, operations or process and thread creation

4. **Ntdll.dll** is the lowest level of user-mode DLLs. It exposes the NT API (Native API), which allows applications to make low-level calls that interface with the Windows kernel. This library acts as a middle layer between user-mode components (like applications) and the kernel, converting user requests into system calls**(syscalls**, more on that later) that the kernel can understand
   
5. The **kernel** is the core part of the Windows operating system, responsible for managing system resources, including memory, processes, and hardware interactions. It operates in “kernel-mode,” which has unrestricted access to the hardware, unlike “user-mode”, where applications and DLLs run with limited permissions. The kernel handles system calls made from Ntdll.dll and executes them with direct access to system resources

### Example of operation flow for creating a file in C++.

1. **CreateFile function is invoked** **in the code** – Applications make API calls to interact with the system
   
2. **CreateFile forwards to NtCreateFile** – Calls are passed through various DLLs (such as Kernel32) or directly to Ntdll.dll
   
3. **NtCreateFile syscall is triggered** – Ntdll.dll converts these API calls into low-level system calls**(syscalls**)
   
4. **Creates the file and returns a handle** – The kernel executes these system calls, managing hardware and system resources as needed

