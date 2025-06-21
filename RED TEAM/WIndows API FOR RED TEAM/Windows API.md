
what is windows api

The **Windows API (WinAPI)** is a collection of functions exposed by Microsoft to allow programs to interact with the Windows Operating System. These APIs give user-mode applications access to kernel-level features **without breaking security boundaries**.

The main goals of the Windows API are:

- Abstract direct hardware/system access
- Provide system services to applications
- Maintain compatibility between Windows versions
- Offer consistent programming interfaces for GUI, file systems, memory, threading, etc.

There are two major categories:

- **User-Mode APIs**: Functions in `kernel32.dll`, `user32.dll`, `advapi32.dll`, etc.
- **Native APIs (NT API)**: Internal system calls in `ntdll.dll`, exported as `Nt*` and `Zw*` functions
  
  
  ![[Pasted image 20250621011327.png]]


## API Layers: Win32, NT, and Syscalls

### Win32 API (High-Level)

- Most familiar to developers
- Calls like `CreateFileW`, `OpenProcess`, `VirtualAllocEx`
- Found in `kernel32.dll`, `user32.dll`

### NT API (Low-Level)

- Found in `ntdll.dll`
- Prefixed by `Nt` or `Zw` (e.g., `NtOpenProcess`)
- Direct wrappers over syscalls
- Used for stealthy operations, especially when **bypassing EDRs**

### Syscalls

- Low-level interface between `ntdll.dll` and the kernel
- `syscall` instruction in x64, `int 0x2e` in older x86
- Each syscall has a **System Service Number (SSN)**, e.g. `NtOpenProcess` = `0x26`

You can invoke a syscall manually with inline assembly or shellcode this is often used in **direct syscall** evasion techniques.

### Nt, Zw, and Internal Kernel Prefixes

In Windows internals, many low-level functions follow standardized **prefixes** that indicate which **kernel subsystem** the function belongs to. These prefixes are critical for anyone doing kernel development, reverse engineering, or security research, especially when analyzing drivers or crafting syscall stubs manually.

These functions are typically exposed in **Windows kernel modules**, and while some are directly accessible from kernel mode, others are indirectly invoked from user mode through `ntdll.dll`.

![[Pasted image 20250621012235.png]]

![[Pasted image 20250621012306.png]]

![[Pasted image 20250621012327.png]]

## What is the IAT?

The **Import Address Table (IAT)** is a runtime structure in PE files that stores the **addresses of functions imported from external DLLs**. When a program calls external APIs (e.g., `MessageBoxA`, `CreateFileW`, etc.), it doesn’t call the DLL function directly — it calls the address stored in the IAT. This mechanism enables **dynamic linking**: the actual function addresses are resolved by the **Windows Loader** when the program starts.

The loader walks through the list of imports (declared in the Import Table), finds the corresponding functions in each DLL (e.g., in `kernel32.dll`, `user32.dll`), and fills the IAT entries with the **actual function pointers**. If someone hooks the IAT, they can **redirect those calls to other functions**, making IAT a common target in **malware, debuggers, and API hooking frameworks**.

![[Pasted image 20250621014425.png]]

