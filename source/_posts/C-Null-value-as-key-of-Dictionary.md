---
title: "C# Null value as Dictionary Key"
date: 2018-09-14 23:50:27
tags: C#
categories: C#
---

This is a post about how to use null as dictionary key.

<!-- more -->

```csharp
static class NullableKey {
    public static NullableKey<T> Of<T> (T value) => new NullableKey<T> (value);

    public static NullableKey<T> Of<T> (object value) => new NullableKey<T> (value);

}
struct NullableKey<T> : IEquatable<NullableKey<T>> {

    private bool hasValue;
    private T value;

    public NullableKey (T value) {

        if (value == null) {
            // value type would never step into here.
            this.value = default (T);
            this.hasValue = false;
        } else {
            this.value = value;
            this.hasValue = true;
        }

    }

    public NullableKey (object value) {

        if (value is T init) {
            this = new NullableKey<T> (init);
        } else {
            this.value = default (T);
            this.hasValue = false;
        }
    }

    public bool HasValue () => this.hasValue;

    public T GetValue () => this.value;

    public bool Equals (NullableKey<T> other) {
        if (this.hasValue == other.hasValue) {
            if (!this.hasValue) return true;
            else return EqualityComparer<T>.Default.Equals (this.value, other.value);

        } else {
            return false;
        }
    }

    public override bool Equals (object obj) {
        if (obj is T init) {
            return this.Equals (init);
        } else {
            return false;
        }
    }

    public override int GetHashCode () {
        if (!this.hasValue) {

            if (typeof (T) == typeof (bool)) return -1;
            else return 0;

        } else return this.value.GetHashCode ();
    }
}
class Program {
    static void Main (string[] args) {

        bool? variable = null;

        var map = new Dictionary<NullableKey<bool>, string> ();
        map.Add (NullableKey.Of<bool> (variable), "this is null"); // need to declare T for null or nullable value
        map.Add (NullableKey.Of (false), "this is false");
        map.Add (NullableKey.Of (true), "this is true");
        Console.WriteLine (map[NullableKey.Of<bool> (variable)]);
        Console.WriteLine (map[NullableKey.Of (false)]);
        Console.WriteLine (map[NullableKey.Of (true)]);

        var map2 = new Dictionary<NullableKey<string>, string> ();
        map2.Add (NullableKey.Of<string> (null), "this is null string");
        map2.Add (NullableKey.Of (string.Empty), "this is empty string");
        Console.WriteLine (map2[NullableKey.Of<string> (null)]);
        Console.WriteLine (map2[NullableKey.Of (string.Empty)]);

    }
}
```
