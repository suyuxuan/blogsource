---
title: "C# Explicit Struct Layout"
date: 2018-09-12 22:07:26
tags:
  - C#
categories:
  - C#
---

It's been a long time since this blog site was built up. So let me take some notes about c# structure layout here.

In c#, it provides ability to control the physical layout of data fields of structure in memory. The following code block shows how to change field memory location in a structure by using `LayoutKind.Explicit`:

<!-- more -->

```csharp
[StructLayout (LayoutKind.Explicit)]
public struct CompositedBits {

    public CompositedBits (ulong ab) {
        this.A = 0;
        this.B = 0;
        this.AB = ab;
    }

    public CompositedBits (uint a, uint b) {
        this.AB = 0;
        this.A = a;
        this.B = b;
    }

    [FieldOffset (0)]
    public readonly uint A;

    [FieldOffset (4)]
    public readonly uint B;

    [FieldOffset (0)] //point AB location to 0 similiar with A;
    public readonly ulong AB;

}
```

In `CompositedBits`, three numeric fields have been created: A - `UInt32`, B - `UInt32` and AB - `UInt64`. Meanwhile, an attribute `[StructLayout (LayoutKind.Explicit)]` is added at the top of structure, also, `[FieldOffset]` for each fields. Memory offset is been set as 0 for AB which is similiar with A. Here I target to use AB to cover both A and B space.

Now, let me fullfill an instance of `CompositedBits` and print the A, B and AB values to see if it's the case we want.

```csharp
static void Main (string[] args) {

    CompositedBits bits = new CompositedBits (0x0, 0x88888888);
    Console.WriteLine ("0x{0}", bits.AB.ToString ("X"));
    // 0x8888888800000000

    CompositedBits bits2 = new CompositedBits (0x1111111133333333);
    Console.WriteLine ("0x{0}", bits2.A.ToString ("X"));//0x33333333
    Console.WriteLine ("0x{0}", bits2.B.ToString ("X"));//0x11111111

}
```

From the outputs, we can see `AB` is sharing 64-bits space with `A` and `B` as expected. With this feature, we can also write some magic code shown as below:

```csharp
[StructLayout (LayoutKind.Explicit)]
public struct RefPointers {

    public RefPointers (BClazz b) {
        this.APointer = null;
        this.BPointer = b;
    }

    [FieldOffset (0)]
    public readonly AClazz APointer;

    [FieldOffset (0)]
    public readonly BClazz BPointer;

}

public class AClazz {

    public override string ToString () {
        return "little a";
    }
    public string Bark () {
        return "This is A - " + this.ToString ();
    }
}

public class BClazz {

    public override string ToString () {
        return "little b";
    }
    public string Bark () {
        return "This is B - " + this.ToString ();
    }

}
```

Instead of value type, two fields declared in the structure are reference type. Again, mark their `FieldOffset` as 0. After that, print the results of `pointers.APointer.Bark()` and `pointers.BPointer.Bark()`.

```csharp
static void Main (string[] args) {

    BClazz b = new BClazz ();
    RefPointers pointers = new RefPointers (b);

    Console.WriteLine (pointers.APointer.Bark ());
    Console.WriteLine (pointers.BPointer.Bark ());

    /*
        output:
            This is A - little b
            This is B - little b
    */

}
```

Obviously, it works and it calls `BClazz.ToString()` in `pointers.APointer.Bark()`. That makes sense because `APointer` is sharing the memory with `BPointer` where the underlying object is of type `BClazz`.

Note: we cannot call virtual method here. If `AClazz.Bark()` is virtual and we use instance of ClazzB to call it, it just throws Exception.
