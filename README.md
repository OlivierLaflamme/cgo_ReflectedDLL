Go combined with reflection dll 

Reflective dll technology has been around for a long time, please Google it for the principle and so on. I'm too lazy to write here. 

The use of cgo (golang and C language are mutually adjusted) combines the core code of reflection dll of go and c. This template can easily compile go code into reflection dll file

I'v directly upload the key code to acheive this. 

dllmain.c
```c
#include "dllmain.h"
#include

DWORD WINAPI MyThreadFunction() {
OnProcessAttach();
    return 0;
}

BOOL WINAPI DllMain(
    HINSTANCE hinstDLL,  // handle to DLL module
    DWORD fdwReason,     // reason for calling function
    LPVOID lpReserved)   // reserved
{
    switch (_fdwReason) {
    case DLL_PROCESS_ATTACH:
        {
        OnProcessAttach();
        MyThreadFunction();
        }
        break;
    case DLL_PROCESS_DETACH:
        // Perform any necessary cleanup.
        break;
    case DLL_THREAD_DETACH:
        // Do thread-specific cleanup.
        break;
    case DLL_THREAD_ATTACH:
        // Do thread-specific initialization.
        break;
    }
    return TRUE; // Successful.
}
```

main.go
```go
package main

import "C"

import (
    "syscall"
    "unsafe"
)


func IntPtr(n int) uintptr {
    return uintptr(n)
}
func StrPtr(s string) uintptr {
    return uintptr(unsafe.Pointer(syscall.StringToUTF16Ptr(s)))
}
func MessageBox(title, text string) {
    user32 := syscall.NewLazyDLL("user32.dll")
    MessageBoxW := user32.NewProc("MessageBoxW")
    MessageBoxW.Call(IntPtr(0), StrPtr(text), StrPtr(title), IntPtr(0))
}

//export OnProcessAttach
func OnProcessAttach() {
    MessageBox("OnProcessAttach","OnProcessAttach")
}
//export test
func test() {
    MessageBox("test","test")
}

func main()  {

}
```

Support compiling to x86 and x64

x64.bat compiling to x64 dll    
x86.bat compiling to x86 dll       

![image](https://user-images.githubusercontent.com/25066959/134020702-8907823a-aad8-4ca8-835a-40ba14e6105d.png)

------

References:             
https://github.com/NaniteFactory/dllmain            

https://github.com/stephenfewer/ReflectiveDLLInjection             

https://github.com/rapid7/ReflectiveDLLInjection             
