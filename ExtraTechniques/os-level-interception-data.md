# OS Level Interception Data

## Tools

We will work with Windows API functions in this chapter. C++ language is the best choice for this task. We will use the [Visual Studio 2015 Community IDE](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx#) to compile our examples. More details about this IDE is available in the [In-game Bots](../InGameBots/tools.md) section.

There are several open source solutions to simplify process of hook WinAPI calls. We will use the [Deviare](http://www.nektra.com/products/deviare-api-hook-windows/) open source hooking engine.

There are steps to install Deviare software:

1. Download the last version of the [release binaries](https://github.com/nektra/Deviare2/releases/download/v2.8.0/Deviare.2.8.0.zip).

2. Download the latest version of the [source code](https://github.com/nektra/Deviare2/archive/v2.8.0.zip).

3. Unpack both archives in two different directories.

You can find a list of all available releases in the [github project](https://github.com/nektra/Deviare2/releases). Please make sure that the version of binaries matches to the version of sources.

## DLL Import

Before we start to consider Windows API hooking, it will be useful to know how application interacts with DLL. When we start an application, Windows loader reads executable file into process memory. Typical Windows executable file has [**PE**](https://msdn.microsoft.com/en-us/library/ms809762.aspx) format. This format is a standard for data structures, which are stored in a header of file. These structures contain necessary information to launch executable code by Windows loader. List of required DLLs is a part of this information.

Next step of the loader is to find files of all required DLLs. These files are read into the process memory too. Now we face an issue. Locations of the DLL modules in a process memory are not constant. These locations can change in a new version of DLL. Therefore, a compiler cannot hardcode addresses of DLL functions in the executable module. This issue is solved by [**Import Table**](http://sandsprite.com/CodeStuff/Understanding_imports.html). There is some kind of confusion with Import Table and **Thunk Table**.

Each element of Import Table matches to one imported DLL module. This element contains name of the module, `OriginalFirstThunk` pointer and `FirstThunk` pointer. You can find more details [here](http://ntcore.com/files/inject2it.htm). The `OriginalFirstThunk` points to the first element of array with ordinal numbers and names of imported functions. The `FirstThunk` points to the first element of array (also known as Import Address Table or IAT), which is overwritten by Windows loader with actual addresses of library functions. And this is a source of confusion because both these arrays do not contain the stuff that is named [**thunk**](https://en.wikipedia.org/wiki/Thunk#Overlays_and_dynamic_linking).

Import Table is a part of PE header and it contains constant meta information about imported DLLs. This table holds in the read-only process memory segment with PE header. Thunk table (also known as jump table) is a part of executable code and it contains `JMP` instructions to transfer control to library functions. This table holds in the read and executable `.text` segment with all other application code. Import Address Table holds in the read and write `.idata` segment. The `.idata` segment also contains an array, which is pointed by OriginalFirstThunk. As you see all three tables hold different segments.

This scheme illustrates a call of DLL function from the MinGW compiler generated code:

![DLL call MinGW](dll-call-mingw.png)

This scheme illustrates the same call from the Visual C++ compiler generated code:

![DLL call Visual C++](dll-call-visual-cpp.png)

You can see that Visual C++ compiler uses the IAT address directly without extra jump via Thunk Table.

## API Hooking Techniques

Game application interacts with OS via calls of WinAPI functions. There are several approaches to hook these calls. This [article](http://www.internals.com/articles/apispy/apispy.htm) describes these approaches in details.

Tools like [API Monitor](../ClickerBots/tools.md) are based on one of hooking approaches. We can implement a bot application that behaves in a similar way. But unlike these tools the bot should simulate player actions instead of logging WinAPI calls.

Now we will briefly consider most common API hooking techniques. 

TODO: Briefly describe advantages and disadvantages of below approaches.

TODO: Make the schemas.

### Proxy DLL

### IAT Patching

### API Patching

## Test Application

## Deviare Hooking Engine

## Bot Application

## Summary