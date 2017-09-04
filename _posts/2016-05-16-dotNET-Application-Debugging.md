---
layout: post
title: 160516 .NET Application Debugging
comments: true
tags:
- .net
- c#
- debugging
- study
- windbg
- english
---

<!-- TOC -->

- [Summary](#summary)
- [Sample Sources](#sample-sources)
- [Global exception handling](#global-exception-handling)
    - [Simple application for test](#simple-application-for-test)
    - [At occuring an excption, print the exception and callstack information.(like a usual .NET application)](#at-occuring-an-excption-print-the-exception-and-callstack-informationlike-a-usual-net-application)
- [Create dump file manually](#create-dump-file-manually)
- [Download windbg](#download-windbg)
- [By windbg, load dump file, and analyze](#by-windbg-load-dump-file-and-analyze)
    - [Load dump file](#load-dump-file)
    - [Analyze dump file](#analyze-dump-file)
- [Load at VS debugger](#load-at-vs-debugger)
- [Note that when using VS debugger](#note-that-when-using-vs-debugger)
- [To create dump files from within .NET applications](#to-create-dump-files-from-within-net-applications)

<!-- /TOC -->

<br>
<br>
<br>

## Summary
I'll introduce the debugging solution of .NET Application that is variety and pratical.

- Global exception handling
    - Most frequently used.
    - Use as the after-death debugging.
    - Usually upload the exception log include the callstack information, and analyze it at the global exception routine.
    - By this callstack information, about 80% ~ 90% errors can be anaylized.
    - But this cannot analyze the exact variables's value.
- At runtime, attach the VS debugger.
    - Connect DevPC( or remote pc with debugging env ) and debug based on the break point solution.
    - Very strong solution because it is based on the break point solution.
    - But it is not possible as after-death debugging, demanded to effort setting as dev environment.
- After creating dump file, and analyze with windbg.
    - At the runtime or at the crash time, debug with windbg by the dump file.
    - Callstack inforamtion, memory information also can check.
    - Can use all the strong functions provided by windbg.
    - But should know windbg's commands very well.
    - And also windbg don't support the convinient GUI.
        - In debugging, visualization is very important, so weakness at this is very big disadvantage.
- After creating dumpfiles, analyze with VS debugger.
    - Has same advantages as the analyzing dump file solution.
    - As using VS debugger, don't need difficult commands, and supported convinient GUI!

<br/>
<br/>
<br/>

## Sample Sources
Download here
https://github.com/HyundongHwang/DotNetDebugging

<br/>
<br/>
<br/>

## Global exception handling
### Simple application for test
- At AppDomain.CurrentDomain.UnhandledException , it handles global exceptions.
- Added initializing codes for local variable, static variable.
- Q, D, E keys are acts as quit, dump, exception.

```csharp
    class Program
    {
        private static void Main(string[] args)
        {
            AppDomain.CurrentDomain.UnhandledException += _AppDomain_UnhandledException;

            var p = new MyPerson();
            p.id = 101;
            p.name = "william";

            MySingleton.Current.id = 12345;
            MySingleton.Current.name = "william";
            MySingleton.Current.friends = new List<string> { "brandon", "kevin" };

            Console.WriteLine($@"
commands
----------------
q : exit program
d : create dump
e : create exception

enter command : 
            ");

            while (true)
            {
                var key = Console.ReadKey();

                if (key.Key == ConsoleKey.D)
                {
                    Console.WriteLine("create dump...");
                    MiniDumpWriter.Write();
                }
                else if (key.Key == ConsoleKey.Q)
                {
                    Console.WriteLine("exit program...");
                    break;
                }
                else if (key.Key == ConsoleKey.E)
                {
                    Console.WriteLine("create exception...");
                    throw new Exception("my exception is occured!!!");
                    break;
                }
            }
        }

        private static void _AppDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e)
        {
            Console.WriteLine($@"e : {e}");
            Console.WriteLine($@"e.ExceptionObject : {e.ExceptionObject}");
        }
    }
    
    public class MyPerson
    {
        public int id { get; set; }
        public string name { get; set; }
    }

    public class MySingleton
    {
        public int id { get; set; }
        public string name { get; set; }
        public List<string> friends { get; set; }


        #region 싱글톤
        static MySingleton _Current;
        static public MySingleton Current
        {
            get
            {
                if (_Current == null)
                {
                    _Current = new MySingleton();
                }
                return _Current;
            }
        }
        MySingleton()
        {
        }
        #endregion
    }
```

### At occuring an excption, print the exception and callstack information.(like a usual .NET application)
- The callstack information are printed very well!!!
- If it is enough to analyze error situation, you don't need to read this article more.

```powershell
commands
----------------
q : exit program
d : create dump
e : create exception

enter command :
e
create exception...

e : System.UnhandledExceptionEventArgs
e.ExceptionObject : System.Exception: my exception is occured!!!
   위치: MySimpleConApp.Program.Main(String[] args) 파일 C:\temp\MySimpleConApp\
Program.cs:줄 53
```

<br/>
<br/>
<br/>

## Create dump file manually
- As the easiest way to create dump file, open the task manager, and find the exact process, and right click, and create dump file.
- This dump file that is created by task manager is full-dump file. This contains callstack, variables(=memory) informations.
- Instead of task manager, you can use ProcessExplorer. This can create mini-dump, full-dump files separatelly. At this time you can analyze callstack information only by mini-dump file.
- Of course, by this reason, full-dump file has very big volume.

![Alt text](./1463382078028.png)

- Despite of very small application, full dump file occupy 97Mb.

```powershell
PS C:\Users\Hyundong\AppData\Local\Temp> ls *.dmp

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----     2016-05-16   오후 3:45       97156600 MySimpleConApp.DMP
```

<br/>
<br/>
<br/>

## Download windbg
- Download here : https://msdn.microsoft.com/en-us/windows/hardware/hh852365.aspx
- Install with WDK10
- C:\Program Files (x86)\Windows Kits\10\Debuggers\x64\windbg.exe

<br/>
<br/>
<br/>

## By windbg, load dump file, and analyze
### Load dump file

```powershell
PS C:\Users\Hyundong\AppData\Local\Temp> windbg -z .\MySimpleConApp.DMP
```

### Analyze dump file
- Load windbg 
- Load MySimpleConApp.DMP
- Specify the properties of 'User Mini Dump File with Full Memory'

```powershell
Microsoft (R) Windows Debugger Version 10.0.10586.567 AMD64
Copyright (c) Microsoft Corporation. All rights reserved.
Loading Dump File [C:\Users\Hyundong\AppData\Local\Temp\MySimpleConApp.DMP]
User Mini Dump File with Full Memory: Only application data is available

Symbol search path is: srv*
Executable search path is: 
Windows 10 Version 10586 MP (4 procs) Free x64
Product: WinNt, suite: SingleUserTS
Built by: 10.0.10586.0 (th2_release.151029-1700)
Machine Name:
Debug session time: Mon May 16 15:45:27.000 2016 (UTC + 9:00)
System Uptime: 0 days 5:39:46.399
Process Uptime: 0 days 0:04:06.000
........................................
Loading unloaded module list
.
ntdll!NtDeviceIoControlFile+0x14:
00007ffd`734351c4 c3              ret
0:000>
```

- Load sos.dll clr.dll.
- At windbg, .NET application commands are included this dlls.
- `.loadby sos clr`

```powershell
0:000> .loadby sos clr
```

- Print callstack informations.
- All debuggings are begin with printing callstack information!
- `!clrstack -a`

```powershell
0:000> !clrstack -a
OS Thread Id: 0x1dc4 (0)
        Child SP               IP Call Site
0000003a9acfed28 00007ffd734351c4 [InlinedCallFrame: 0000003a9acfed28] Microsoft.Win32.Win32Native.ReadConsoleInput(IntPtr, InputRecord ByRef, Int32, Int32 ByRef)
0000003a9acfed28 00007ffd416893a1 [InlinedCallFrame: 0000003a9acfed28] Microsoft.Win32.Win32Native.ReadConsoleInput(IntPtr, InputRecord ByRef, Int32, Int32 ByRef)
0000003a9acfecf0 00007ffd416893a1 DomainNeutralILStubClass.IL_STUB_PInvoke(IntPtr, InputRecord ByRef, Int32, Int32 ByRef)
    PARAMETERS:
        <no data>
        <no data>
        <no data>
        <no data>

0000003a9acfee00 00007ffd4177e8d9 System.Console.ReadKey(Boolean)
    PARAMETERS:
        intercept (0x0000003a9acfef08) = 0x0000000000000000
    LOCALS:
        <no data>
        <no data>
        <no data>
        <no data>
        <no data>
        0x0000003a9acfee40 = 0x000001963d847c98
        <no data>
        <no data>
        <no data>

0000003a9acfef00 00007ffce4f7063c *** WARNING: Unable to verify checksum for MySimpleConApp.exe
MySimpleConApp.Program.Main(System.String[]) [C:\project\160516_DotNetDebugging\MySimpleConApp\Program.cs @ 35]
    PARAMETERS:
        args (0x0000003a9acfefd0) = 0x000001963d8443f8
    LOCALS:
        0x0000003a9acfef68 = 0x000001963d844640
        0x0000003a9acfefa0 = 0x0000000000000000
        0x0000003a9acfef9c = 0x0000000000000000
        0x0000003a9acfef98 = 0x0000000000000000
        0x0000003a9acfef94 = 0x0000000000000000
        0x0000003a9acfef90 = 0x0000000000000001
0000003a9acff200 00007ffd445d4073 [GCFrame: 0000003a9acff200] 
```

- At above result, if you click 0x000001963d844640 (Program.Main's local variable), you can print object's dump.
- This variable is 'var p = new MyPerson();'
- As 'p.id = 101;, you can check this value.
- `!DumpObj /d 000001963d844640`

```powershell
0:000> !DumpObj /d 000001963d844640
Name:        MySimpleConApp.MyPerson
MethodTable: 00007ffce4e65b10
EEClass:     00007ffce4fb1068
Size:        32(0x20) bytes
File:        C:\project\160516_DotNetDebugging\MySimpleConApp\bin\Debug\MySimpleConApp.exe
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd4115af60  4000001       10         System.Int32  1 instance              101 <id>k__BackingField
00007ffd41158538  4000002        8        System.String  0 instance 000001963d844410 <name>k__BackingField
```

- Trying to check p's name, print dump 000001963d844410.
- It can be checked text value 'william'.
- `!DumpObj /d 000001963d844410`

```powershell
0:000> !DumpObj /d 000001963d844410
Name:        System.String
MethodTable: 00007ffd41158538
EEClass:     00007ffd40aa4ab8
Size:        40(0x28) bytes
File:        C:\WINDOWS\Microsoft.Net\assembly\GAC_64\mscorlib\v4.0_4.0.0.0__b77a5c561934e089\mscorlib.dll
String:      william
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd4115af60  4000243        8         System.Int32  1 instance                7 m_stringLength
00007ffd411596e8  4000244        c          System.Char  1 instance               77 m_firstChar
00007ffd41158538  4000248       80        System.String  0   shared           static Empty
                                 >> Domain:Value  000001963b9500e0:NotInit  <<
```

- In addtion to this, if you want to know the value of all the memroy allocated to the heap, recklessly...
- Sorted by address and types are printed clearly as with statistical.
- But the result is longer because of enormous internal system variables, even though the short program. :(
- `!dumpheap`

```powershell
0:000> !dumpheap
         Address               MT     Size
000001963d841000 000001963b9560a0       24 Free
000001963d841018 000001963b9560a0       24 Free
000001963d841030 000001963b9560a0       24 Free
000001963d841048 00007ffd41158768      160     
000001963d8410e8 00007ffd41158950      160     
000001963d841188 00007ffd411589c8      160     
000001963d841228 00007ffd41158a40      160     
...

Statistics:
              MT    Count    TotalSize Class Name
00007ffd4117a140        1           24 System.Reflection.Missing
00007ffd41179ff8        1           24 System.__Filters
00007ffd41179890        1           24 System.IntPtr
00007ffd4115fea0        1           24 System.Security.HostSecurityManager
...
Total 417 objects
```

- Check the value of the MySingleton as a static variable.
- Once you see the code for the intializing again...

```csharp
MySingleton.Current.id = 12345;
MySingleton.Current.name = "william";
MySingleton.Current.friends = new List<string> { "brandon", "kevin" };
```

- Dump the heap variable to specify the type Singleton.
- `!dumpheap -type MySingleton`

```powershell
0:000> !dumpheap -type MySingleton
         Address               MT     Size
000001963d844660 00007ffce4e65c78       40     

Statistics:
              MT    Count    TotalSize Class Name
00007ffce4e65c78        1           40 MySimpleConApp.MySingleton
Total 1 objects
0:000> !DumpObj /d 000001963d844660
Name:        MySimpleConApp.MySingleton
MethodTable: 00007ffce4e65c78
EEClass:     00007ffce4fb10e0
Size:        40(0x28) bytes
File:        C:\project\160516_DotNetDebugging\MySimpleConApp\bin\Debug\MySimpleConApp.exe
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd4115af60  4000003       18         System.Int32  1 instance            12345 <id>k__BackingField
00007ffd41158538  4000004        8        System.String  0 instance 000001963d844410 <name>k__BackingField
00007ffd40abd6c8  4000005       10 ...tring, mscorlib]]  0 instance 000001963d844688 <friends>k__BackingField
00007ffce4e65c78  4000006        8 ...onApp.MySingleton  0   static 000001963d844660 _Current
```

- Once you determine the name attribute.
- `!DumpObj /d 000001963d844410`

```powershell
0:000> !DumpObj /d 000001963d844410
Name:        System.String
MethodTable: 00007ffd41158538
EEClass:     00007ffd40aa4ab8
Size:        40(0x28) bytes
File:        C:\WINDOWS\Microsoft.Net\assembly\GAC_64\mscorlib\v4.0_4.0.0.0__b77a5c561934e089\mscorlib.dll
String:      william
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd4115af60  4000243        8         System.Int32  1 instance                7 m_stringLength
00007ffd411596e8  4000244        c          System.Char  1 instance               77 m_firstChar
00007ffd41158538  4000248       80        System.String  0   shared           static Empty
                                 >> Domain:Value  000001963b9500e0:NotInit  <<
```

- Friends will take a few steps because List<string> type.
- Once dump the friends.
- `!DumpObj /d 000001963d844688`

```powershell
0:000> !DumpObj /d 000001963d844688
Name:        System.Collections.Generic.List`1[[System.String, mscorlib]]
MethodTable: 00007ffd40abd6c8
EEClass:     00007ffd40b6b310
Size:        40(0x28) bytes
File:        C:\WINDOWS\Microsoft.Net\assembly\GAC_64\mscorlib\v4.0_4.0.0.0__b77a5c561934e089\mscorlib.dll
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd41147288  4001820        8     System.__Canon[]  0 instance 000001963d8446c8 _items
00007ffd4115af60  4001821       18         System.Int32  1 instance                2 _size
00007ffd4115af60  4001822       1c         System.Int32  1 instance                2 _version
00007ffd41158b18  4001823       10        System.Object  0 instance 0000000000000000 _syncRoot
00007ffd41147288  4001824        0     System.__Canon[]  0   shared           static _emptyArray
                                 >> Domain:Value dynamic statics NYI 000001963b9500e0:NotInit  <<
```                                 

- Again dump _items.
- At dumping _items, use dumparray, not dumpobject.
- `!DumpArray /d 000001963d8446c8`

```powershell
0:000> !DumpArray /d 000001963d8446c8
Name:        System.String[]
MethodTable: 00007ffd41159880
EEClass:     00007ffd40b624a8
Size:        56(0x38) bytes
Array:       Rank 1, Number of elements 4, Type CLASS
Element Methodtable: 00007ffd41158538
[0] 000001963d844438
[1] 000001963d844460
[2] null
[3] null
```

- It is confirmed that 2 arrays are assigned only.
- If you check 0th value, you can find value of brandon.
- If you check 1th value, you can find value of kevin.
- `!DumpObj /d 000001963d844438`
- `!DumpObj /d 000001963d844460`

```powershell
0:000> !DumpObj /d 000001963d844438
Name:        System.String
MethodTable: 00007ffd41158538
EEClass:     00007ffd40aa4ab8
Size:        40(0x28) bytes
File:        C:\WINDOWS\Microsoft.Net\assembly\GAC_64\mscorlib\v4.0_4.0.0.0__b77a5c561934e089\mscorlib.dll
String:      brandon
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd4115af60  4000243        8         System.Int32  1 instance                7 m_stringLength
00007ffd411596e8  4000244        c          System.Char  1 instance               62 m_firstChar
00007ffd41158538  4000248       80        System.String  0   shared           static Empty
                                 >> Domain:Value  000001963b9500e0:NotInit  <<
0:000> !DumpObj /d 000001963d844460
Name:        System.String
MethodTable: 00007ffd41158538
EEClass:     00007ffd40aa4ab8
Size:        36(0x24) bytes
File:        C:\WINDOWS\Microsoft.Net\assembly\GAC_64\mscorlib\v4.0_4.0.0.0__b77a5c561934e089\mscorlib.dll
String:      kevin
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
00007ffd4115af60  4000243        8         System.Int32  1 instance                5 m_stringLength
00007ffd411596e8  4000244        c          System.Char  1 instance               6b m_firstChar
00007ffd41158538  4000248       80        System.String  0   shared           static Empty
                                 >> Domain:Value  000001963b9500e0:NotInit  <<
```

<br/>
<br/>
<br/>

## Load at VS debugger
- Compared to the windbg it is very simple!
- It should open the dump file in VS debugger
    - Just dmp file open in Visual Studio
    
![Alt text](./1463399398325.png)

- Load the dump file path, a dump file creation time, process information, may be collected based on a variety of information such as OS information.
- `Debug Managed Only` Click the Start Debugging

![Alt text](./1463399510831.png)

- Go looking for the main thread in the thread list, and click the last routine function of expanding this Program.Main

![Alt text](./1463399553973.png)

- Program.Main source code of the function is displayed on the screen
- A yellow arrow appears on the stopping point of the source code.
- It is possible to access the local variables p, Singleton.Current static variable.

![Alt text](./1463399716505.png)

<br/>
<br/>
<br/>

## Note that when using VS debugger
 - Pdb file for the application that created the dmp file must exist in the same directory in the analysis.
 - It shall have the same version of the source code exists. VS does not find it, to find the source code dialog box is derived.
 - Pdb file version is also exactly the same as the dmp file. However, the Fair is the modified source code other than analysis.
 - Good to use it practically every connected prior to full-scale tests such as CBT, OBT, put together by the pdb version, if necessary.
     
<br/>
<br/>
<br/>
 
## To create dump files from within .NET applications
- The application crash situation, a point in time of the execution, requires a way to perform an immediate dump.
     - Crash-after debugging
     - Debugging at the user's runtime
     - Customer Experience Improvement Program(?)
- API functions are provided as well as create a dump file
    - Unfortunately, so you must use Win32 API pinvoke.
    
```c#
namespace MySimpleConApp
{
    public static class MiniDumpWriter
    {
        [Flags]
        public enum MINIDUMP_TYPE
        {
            MiniDumpNormal = 0x00000000,
            MiniDumpWithDataSegs = 0x00000001,
            MiniDumpWithFullMemory = 0x00000002,
            MiniDumpWithHandleData = 0x00000004,
            MiniDumpFilterMemory = 0x00000008,
            MiniDumpScanMemory = 0x00000010,
            MiniDumpWithUnloadedModules = 0x00000020,
            MiniDumpWithIndirectlyReferencedMemory = 0x00000040,
            MiniDumpFilterModulePaths = 0x00000080,
            MiniDumpWithProcessThreadData = 0x00000100,
            MiniDumpWithPrivateReadWriteMemory = 0x00000200,
            MiniDumpWithoutOptionalData = 0x00000400,
            MiniDumpWithFullMemoryInfo = 0x00000800,
            MiniDumpWithThreadInfo = 0x00001000,
            MiniDumpWithCodeSegs = 0x00002000
        }

        [DllImport("dbghelp.dll", EntryPoint = "MiniDumpWriteDump", CallingConvention = CallingConvention.StdCall, CharSet = CharSet.Unicode, ExactSpelling = true, SetLastError = true)]
        static extern bool MiniDumpWriteDump(IntPtr hProcess, uint processId, SafeHandle hFile, uint dumpType, IntPtr expParam, IntPtr userStreamParam, IntPtr callbackParam);

        public static bool Write()
        {
            var currentProcess = Process.GetCurrentProcess();
            var currentProcessHandle = currentProcess.Handle;
            var currentProcessId = (uint)currentProcess.Id;

            {
                var fileName = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, $@"{DateTime.Now.ToString("yyMMdd-HHmmss")}.mini.dmp");
                var options = MINIDUMP_TYPE.MiniDumpNormal;

                using (var fs = new FileStream(fileName, FileMode.Create, FileAccess.ReadWrite, FileShare.Write))
                {
                    MiniDumpWriteDump(currentProcessHandle, currentProcessId, fs.SafeFileHandle, (uint)options, IntPtr.Zero, IntPtr.Zero, IntPtr.Zero);
                }
            }

            {
                var fileName = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, $@"{DateTime.Now.ToString("yyMMdd-HHmmss")}.full.dmp");

                var options = 
                    MINIDUMP_TYPE.MiniDumpNormal |
                    MINIDUMP_TYPE.MiniDumpWithFullMemory |
                    MINIDUMP_TYPE.MiniDumpWithHandleData |
                    MINIDUMP_TYPE.MiniDumpWithProcessThreadData |
                    MINIDUMP_TYPE.MiniDumpWithThreadInfo; ;

                using (var fs = new FileStream(fileName, FileMode.Create, FileAccess.ReadWrite, FileShare.Write))
                {
                    MiniDumpWriteDump(currentProcessHandle, currentProcessId, fs.SafeFileHandle, (uint)options, IntPtr.Zero, IntPtr.Zero, IntPtr.Zero);
                }
            }

            return true;
        }
    }
}
```

- Use MiniDumpWriteDump function of dbghelp.dll. It also made a mini dumps and full dumps.
- The mini dump will contain only callstack information. full dump includes both callstack and memory information.
- Code are as follows:
    - Clicking d, it performs.

```c#
if (key.Key == ConsoleKey.D)
{
    Console.WriteLine("create dump...");
    MiniDumpWriter.Write();
}
```

<br/>
<br/>
<br/>