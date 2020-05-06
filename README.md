# Reverse Engineering Tips - Write ups - Challenges

## Tips (from [@va_start](https://twitter.com/va_start))
[Full guide](https://blog.vastart.dev/2020/04/guys-30-reverse-engineering-tips-tricks.html?m=1) 

### Tip 1
Long branch-less functions w/many `xors` & `rols` are usually `hash` functions. (e.g MD5 hash)

### Tip 2
Building on **tip 1**, after finding a hash function, google its constant to identify the exact hash algorithm.  
[photos](https://twitter.com/va_start/status/1245656402862854144/photo/1)  

### Tip 3
Find the function controlling authentication in any exe by diff'ing the execution trace of a valid vs failed login. Traces will split right after the auth check.  
[photos](https://twitter.com/va_start/status/1246018981321900032/photo/1)

### Tip 4
Use a function's neighboring code to understand its functionality:
Devs group related funcs together && compilers like to keep the order of funcs from src to exe ⇒ related funcs are closely grouped in the exe.  
[photos](https://twitter.com/va_start/status/1246376443657019393/photo/1)

### Tip 5
Hate updating breakpoint addrs each time a module loads w/a new base? Patch the `exe/dll` header to disable the DYNAMIC_BASE flag for a static base addr.  
[link](https://docs.microsoft.com/en-us/windows/win32/debug/pe-format#dll-characteristics)  

### Tip 6
When reversing C++: Use `virtuailor` to *automatically* create class vtables & add xrefs to virtual funcs. It uses runtime inspection to evaluate func addrs to do its magic.    
[Virtuailor](https://github.com/0xgalz/Virtuailor)  

### Tip 7
Reversing is more fun w/symbols. If symbols are stripped, try looking for symbols in older versions, versions for other OSs, beta builds, and the mobile app versions. If still no symbols, check which has the most debug prints.  
[photo](https://twitter.com/va_start/status/1247387110761512961/photo/1)

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

### Tip 18

### Tip 19

### Tip 20

### Tip 21

### Tip 22

### Tip 23

### Tip 24

### Tip 25

### Tip 26

### Tip 27

### Tip 28

### Tip 29

### Tip 30

