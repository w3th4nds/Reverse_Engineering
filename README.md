# Reverse Engineering Tips - Write ups - Challenges

## Tips (from [@va_start](https://twitter.com/va_start) - [Full guide](https://blog.vastart.dev/2020/04/guys-30-reverse-engineering-tips-tricks.html?m=1))


### Tip 1
Long branch-less functions w/many `xors` & `rols` are usually `hash` functions. (e.g MD5 hash)

### Tip 2
Building on **tip 1**, after finding a hash function, google its constant to identify the exact hash algorithm.  
[Images](https://twitter.com/va_start/status/1245656402862854144/photo/1)  

### Tip 3
Find the function controlling authentication in any exe by diff'ing the execution trace of a valid vs failed login. Traces will split right after the auth check.  
[Images](https://twitter.com/va_start/status/1246018981321900032/photo/1)

### Tip 4
Use a function's neighboring code to understand its functionality:
Devs group related funcs together && compilers like to keep the order of funcs from src to exe ⇒ related funcs are closely grouped in the exe.  
[Images](https://twitter.com/va_start/status/1246376443657019393/photo/1)

### Tip 5
Hate updating breakpoint addrs each time a module loads w/a new base? Patch the `exe/dll` header to disable the DYNAMIC_BASE flag for a static base addr.  
[link](https://docs.microsoft.com/en-us/windows/win32/debug/pe-format#dll-characteristics)  

### Tip 6
When reversing C++: Use `virtuailor` to *automatically* create class vtables & add xrefs to virtual funcs. It uses runtime inspection to evaluate func addrs to do its magic.    
[Virtuailor](https://github.com/0xgalz/Virtuailor)  

### Tip 7
Reversing is more fun w/symbols. If symbols are stripped, try looking for symbols in older versions, versions for other OSs, beta builds, and the mobile app versions. If still no symbols, check which has the most debug prints.  
[Images](https://twitter.com/va_start/status/1247387110761512961/photo/1)

### Tip 8
Want a faster way to open IDA on your exe? 
* Open Explorer 
* Enter “sendto” in the path bar 
* Drag an IDA Pro shortcut to this folder 
* You can now right-click `send to IDA` on any file. 

### Tip 9
Search [magnumdb](http://www.magnumdb.com) to name any unknown Windows constant/guid/error code you come across while reversing.

### Tip 10
Pimp your `gdb` experience with ‘layout asm’ & ‘layout regs’, or take it a step further by installing [pwndbg](https://github.com/pwndbg/pwndbg).

### Tip 11
Want to reverse code handling a GUI window?
Find the window’s Resource ID with [ResourceHacker](http://www.angusj.com/resourcehacker/), then search **IDA** for where that ID is used (alt+I to search).

### Tip 12
Get the best from both static & dynamic analysis by sync’ing your debugger (WinDbg/GDB/x64dbg/ and more) with your disassembler (IDA/Ghidra) through [Ret-Sync](https://github.com/bootleg/ret-sync). 

### Tip 13
Optimized magic numbers can uncover functionality. Examples: 0x7efefeff is used in strlen, and 0x5f3759df is used to find the inverse square root (1/sqrt(x))
more info: https://en.wikipedia.org/wiki/Fast_inverse_square_root

### Tip 14
Multiplying by a constant followed by +’ing then shifting(>>) can be a sign of optimized ÷ 
Example: ×’ing by 0x92492493 is the first step in efficiently dividing by 7. More info: https://reverseengineering.stackexchange.com/questions/1397/how-can-i-reverse-optimized-integer-division-modulo-by-constant-operations

### Tip 15
In IDA's disassembler, you can use the numpad +/- keys to change the number of args passed to a function w/variable arguments such as printf.  

### Tip 16
Controlling input & running a function we're reversing is super useful. You can do this by "converting" the func’s exe → dll, and then invoke the func like a dll func. Explanatory blog post: https://blog.vastart.dev/2020/04/calling-arbitrary-functions-in-exes.html?m=1

### Tip 17
Extracted embedded firmware but don’t know its base addr to give **IDA**?
Use string ptrs: use a script such as [rbasefind](https://github.com/sgayou/rbasefind) to find which base addr aligns the most ptrs to valid strings.


### Tip 18
Building on tip 17, another way to find a fw’s base addr is using absolute calls:  
Find which base addr results in the most calls "landing" on beginnings of functions (code init’ing the frame pointer & allocating stack vars).  

### Tip 19
Found a bunch of strings, but no xref to their use? You probably found a str array: the strs are accessed w/an offset from the 1st str (array base), which will have an xref.  
[Images](https://twitter.com/va_start/status/1251775461912449024/photo/1)

### Tip 20
**GDB TIPS EDITION**
The ability to break on reading/writing memory is well known, BUT
did you know you can break on a write to a register?!
[watch](https://i.imgur.com/NCrGFeD.png) 

Anytime ***GDB*** prints $<number>, it is actually creating a new variable you can use:
[call](https://i.imgur.com/qrNCtng.png)  

3 ways to write to memory in GDB (commands in back-ticks):
* use `set`
* `call` memcpy/strcpy
* write data from a file to memory with `restore`
[set_call_restore](https://i.imgur.com/1UGBEDr.png)

### Tip 21
Trick to help find libc functions: Exploit the xref count
memcpy/strlen will be called often, while strtok for example probably less.  

### Tip 22
***STRING HUNTING EDITION***
strs help identify code functionality; how can we find a str seen in the GUI that isn’t in IDA’s strings window? a thread!  

1) The strings window only has auto-identified strings, which misses some. 
Use `ALT-B` to search the whole binary, even places not identified as data regions.
2) Sometimes the string isn’t embedded in the binary, but is imported from an external resource file. find the str by grepping the program's installation folder.  
```sh
$ grep -ri "file transer"
```
Google the str to see if it’s system generated: error strings for example, can be right from lib functions such as perror()/FormatMessageA(), in which case the str won’t be in the exe.  

Last resort: Time Travel Debugging. 
* Start debugging the program with [TTD](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/time-travel-debugging-overview) 
* When the string appears, scan the program’s memory for its addr. 
* Go back in time to find when/where the string was put there.  

### Tip 23
The easiest way to find the `main()` function is by working in reverse: find which function’s return value (saved in `$eax`) is passed to `exit()` - that’s `main()`.  
![main](https://i.imgur.com/zTmWGP7.png)  

### Tip 24
Looking to find code handling certain logic?
Search previous versions’ release notes to find when that logic was last updated. Next, bindiff the version before & of that release, the diff will have your target func. https://www.zynamics.com/bindiff.html

### Tip 25
Get the best from both ***IDA’s*** `decompiler` & `disassembler` by overlaying the C code on the ASM in `graph view` (by clicking the `/` key). 
![graph](https://i.imgur.com/RfNtTv3.png)

### Tip 26
Ensure malware you’re researching won’t accidentally run by appending `.dontrunme` to the extension, which will prevent windows from executing the file when it’s double clicked. On linux just `chmod -x`.

### Tip 27
Sometimes you can reverse a function solely by looking at its call graph. The function calls to/from a target reveal a lot about its logic.  
The easiest way to view a call graph in IDA is to use the “Proximity Viewer” from the `View` menu.  

### Tip 28
**IDA** `auto-analysis` missed a function arg because it was passed in an "unexpected" register?
Use the "usercall" call convention with "@<register_name>" to declare args & their location.
![args](https://i.imgur.com/jwz3fMi.png)  

### Tip 29
Analyze the block layout before diving into ASM code. `Layout view` is available on many disassemblers.  

### Tip 30
> ***"Βetter than anything technical I can share: RE isn’t a 1337 h4ck3r only reserved field! Like anything else: It’s open to everyone! And like any skill, "just" enjoy it, work hard, and you’ll get it."***

