---
date: 2021-01-15T22:09
tags: 
  - programming/working with strings
  - programming/optimize memory usage
  - prog lang/advanced c# techniques
---

# Concatenating ReadOnlySpan&lt;T&gt;

There's no add operator between `Span<T>`'s nor `ReadOnlySpan<T>`. But there are
some new methods added to the standard library that now accepts these new types
and give great value to them, especially when it comes to the `<char>` variants.

You can keep on using `ReadOnlySpan<char>` without the performance cost or
memory allocations of creating intermediate strings just to build bigger strings.

## StringBuilder.Append

[`StringBuilder.Append`](https://docs.microsoft.com/en-us/dotnet/api/system.text.stringbuilder)
is the de-facto standard for concatenating +2 strings in an efficient way.
If you're working with `ReadOnlySpan<char>`, then you'd be delighted to know that
the `StringBuilder.Append` method does have native support for them.

```cs
public sealed partial class StringBuilder
{
    public System.Text.StringBuilder? Append (ReadOnlySpan<char> value);
}
```

```cs
var builder = new StringBuilder();
builder.Append(new [] { 'a', 'b', 'c', 'd', 'e' }.AsSpan());
builder.Append("fghij".AsSpan());

Console.WriteLine(builder.ToString());
// abcdefghij
```

## String.Concat

[`String.Concat`](https://docs.microsoft.com/en-us/dotnet/api/system.string.concat)
is a marvelous example. An otherwise unused method (as you can simply add `+`
strings together), but since .NET Core 3.0 you can pass it `ReadOnlySpan<T>`!

```cs
public sealed partial class String
{
    // Concat 2 ReadOnlySpan<char>'s
    public static string? Concat (ReadOnlySpan<char> str0, ReadOnlySpan<char> str1);
    // Concat 3 ReadOnlySpan<char>'s
    public static string? Concat (ReadOnlySpan<char> str0, ReadOnlySpan<char> str1, ReadOnlySpan<char> str2);
    // Concat 4 ReadOnlySpan<char>'s
    public static string? Concat (ReadOnlySpan<char> str0, ReadOnlySpan<char> str1, ReadOnlySpan<char> str2, ReadOnlySpan<char> str3);
}
```

```cs
var chars = new [] { 'a', 'b', 'c', 'd', 'e' }.AsSpan();
var text = "fghij".AsSpan();

Console.WriteLine(string.Concat(chars,text));
// abcdefghij
```

