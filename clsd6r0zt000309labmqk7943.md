---
title: "Passing an array of structs from C++ to C#"
datePublished: Thu Nov 12 2020 14:17:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd6r0zt000309labmqk7943
slug: passing-an-array-of-structs-from-c-to-c
canonical: https://roderickkennedy.com/dbgdiary/passing-an-array-of-structs-from-c-to-c

---

Passing an array of structs from C++ to C#
==========================================

12 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

To pass an array of structs from C++ to C#, you can pass a pointer to a C-style array. In C++ you may have a struct, e.g.  

`#pragma pack(push)   #pragma pack(1)   struct InputEvent   {   uint32_t eventId;   float floatValue;   uint32_t intValue;   };   #pragma pack(pop)   `  
The delegate in C++ is:  
`typedef void(__stdcall* ProcessNewInputFn) (int numEvents, const InputEvent**);` Telling C++ what C# function to call: 

   
`extern "C" __declspec(dllexport) void SetInputProcessingDelegate(ProcessNewInputFn newInputProcessing)   {   processNewInput = newInputProcessing;   }   `

Using this from C++

`std::vector<InputEvent> inputEvents;`  
`const avs::InputEvent *v=inputEvents.data(); processNewInput(inputEvents.size(), &v);` 

In C# the struct is defined as:

`[StructLayout(LayoutKind.Sequential, Pack = 1)]   public struct InputEvent   {   public UInt32 eventId;   public float floatValue;   public UInt32 intValue;   };`   
Note the packing! It must match what we had in C++. Now C# must declare the the delegate type it will implement:

`[UnmanagedFunctionPointer(CallingConvention.StdCall)] delegate void OnNewInput(int numEvents, in IntPtr newEvents);` 

  
And declare in C# the C++ function that sets the delegate:

`[DllImport("dllname")] static extern void SetInputProcessingDelegate(OnNewInput onNewInput );`  

This is called with  

`ok = SetInputProcessingDelegate(ProcessingClass.SetInput);` 

  
Where we have a class like this:  
`class ProcessingClass   {   public static void StaticProcessInput(int numEvents, in IntPtr inputEventsPtr )   {       int EventSize = System.Runtime.InteropServices.Marshal.SizeOf(typeof(avs.InputEvent));       avs.InputEvent[] inputEvents = new avs.InputEvent[inputState.numEvents];       IntPtr ptr=  inputEventsPtr;       for (int i = 0; i < inputState.numEvents; i++)       {          inputEvents[i]=Marshal.PtrToStructure<avs.InputEvent>(ptr);          ptr += EventSize;   }           }   }`  
Here, we take the C++ style pointer-to-array, and iterate through the array elements, copying each in turn into the C# style array.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Passing an array of structs from C++ to C#
==========================================

12 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

To pass an array of structs from C++ to C#, you can pass a pointer to a C-style array. In C++ you may have a struct, e.g.  

`#pragma pack(push)   #pragma pack(1)   struct InputEvent   {   uint32_t eventId;   float floatValue;   uint32_t intValue;   };   #pragma pack(pop)   `  
The delegate in C++ is:  
`typedef void(__stdcall* ProcessNewInputFn) (int numEvents, const InputEvent**);` Telling C++ what C# function to call: 

   
`extern "C" __declspec(dllexport) void SetInputProcessingDelegate(ProcessNewInputFn newInputProcessing)   {   processNewInput = newInputProcessing;   }   `

Using this from C++

`std::vector<InputEvent> inputEvents;`  
`const avs::InputEvent *v=inputEvents.data(); processNewInput(inputEvents.size(), &v);` 

In C# the struct is defined as:

`[StructLayout(LayoutKind.Sequential, Pack = 1)]   public struct InputEvent   {   public UInt32 eventId;   public float floatValue;   public UInt32 intValue;   };`   
Note the packing! It must match what we had in C++. Now C# must declare the the delegate type it will implement:

`[UnmanagedFunctionPointer(CallingConvention.StdCall)] delegate void OnNewInput(int numEvents, in IntPtr newEvents);` 

  
And declare in C# the C++ function that sets the delegate:

`[DllImport("dllname")] static extern void SetInputProcessingDelegate(OnNewInput onNewInput );`  

This is called with  

`ok = SetInputProcessingDelegate(ProcessingClass.SetInput);` 

  
Where we have a class like this:  
`class ProcessingClass   {   public static void StaticProcessInput(int numEvents, in IntPtr inputEventsPtr )   {       int EventSize = System.Runtime.InteropServices.Marshal.SizeOf(typeof(avs.InputEvent));       avs.InputEvent[] inputEvents = new avs.InputEvent[inputState.numEvents];       IntPtr ptr=  inputEventsPtr;       for (int i = 0; i < inputState.numEvents; i++)       {          inputEvents[i]=Marshal.PtrToStructure<avs.InputEvent>(ptr);          ptr += EventSize;   }           }   }`  
Here, we take the C++ style pointer-to-array, and iterate through the array elements, copying each in turn into the C# style array.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)