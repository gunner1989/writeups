### Malwareunicorn re101 workshop 

Jump into code in static disassembly then rename and comment on interesting assembly routines

### String Anti Analysis

String stacking: hide strings on the stack (`push` instructions)

![image](https://user-images.githubusercontent.com/88914262/130342140-a1a87c7b-e200-446d-8f44-96ff6c3378ca.png)

- Values are little endian, LSB. Hex values appear backwards

Converting values of all sequential pushes from offset `00401151` to `00401188`

![image](https://user-images.githubusercontent.com/88914262/130342155-25f92026-9f93-4d10-9e9d-b309acc2ff10.png)

### Looking for interesting strings

**Interesting string:** `.rdata:00402064	0000000B	C	stage2.exe`

- Which code is referencing this string

**Reference label:** `aStage2Exe`

xrefs menu

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f1264e4c-f718-4b36-beb6-bdbd5564b4a2/Untitled.png)

Jumping to selected cross reference. Address `sub_401000` will take you to where the string is referenced at `004010B0`.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6a1aa2a2-1589-4c2a-a0b3-113253db47b2/Untitled.png)

### Trace back to the Start function

- Following the xrefs of the functions backwards until start function

First location label

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9d94d7f1-cd20-487a-897f-46280e4a6c3f/Untitled.png)

- xref function name to follow the branch or jump instruction (branch or jump instruction is jbe or jmp)

`loc_40104C` branches to this location

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6222ded0-62ac-4ec7-aaf2-ec67ca595bfa/Untitled.png)

          *Note: keep track of where these branch statements occur as apart of the flow.

xref next location label

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f3c48e4-45c4-497d-97e7-46450fdcd2d9/Untitled.png)

xref subroutine/function

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/91efb4f0-f928-4e2b-b9f4-ec269d5e7393/Untitled.png)

Start function (sub_401000)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/52500a30-1df2-457a-9e12-e4a75f81bde7/Untitled.png)

### Analyze function sub_401000

- Need to capture when stage2.exe file is created and written

*Note: Remember that functions have their arguments pushed onto the stack before the function is called.

stage2.exe is concatenated together with the string referenced by the register esi

- esi is used as the first argument for `CreateFileA`

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c366ede0-ffcf-4d08-b1d5-6f2902367389/Untitled.png)

Push instructions = arguments

Function called: `CreateFileA`

esi register containing the new string is used as an argument for a call to `CreateProcessA`

### Interesting routines using control flow instruction (branch, jumps, calls)

Mapping

- Start
- Call `401000`
- Jmp `@401023`
- Jbe `@401048`
- Createfile `@4010CB`
- Create Process `@401136`

### Other interesting API function patterns:

- Opening a file from the resource section
- FindResource
- SizeofResource
- LoadResource
- LockResource
- Creating a file
- GetEnvironmentVariable
- CreateFile
- WriteFile
- CloseHandle
- Sleep
- Starting a new process
- CreateProcess
