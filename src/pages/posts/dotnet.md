---
layout: '../../layouts/MarkdownPost.astro'
title: '总结C#所学'
pubDate: 2023-03-07
description: '总结C#所学如Attributes、Reflection'
author: 'Aaron'
cover:
    url: '/preview/effective_cpp.jpg'
    square: ''
    alt: 'cover'
tags: ["C#"]
theme: 'light'
featured: false
---

# Attribute

```c#
// create Attribute
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method)]
public class SampleAttribute : Attribute
{
  public string Name { get; set; }
  public int Version { get; set; }
}

// use Attribute
[Sample(Name = "John", Version = 1)]
public class Test
{
  public int IntValue { get; set; }
  public void Method() { }
}
```

# Reflection
```c#
// Program.cs
namespace ConsoleApp1
{
  class Program
  {
    static void Main(string[] args)
    {
      var assembly = Assembly.GetExecutingAssembly();
      Console.WriteLine(assembly.FullName);

      // Types are just classes
      var types = assembly.GetTypes();
      foreach (var type in types)
      {
        Console.WriteLine("Type: " + type.name);

        var props = type.GetProperties();
        foreach (var prop in props)
        {
          Console.WriteLine("\tProperty: " + prop.name);
        }
      }
    }
  }

  public class Sample
  {
    // This is Property
    public string Name { get; set; }
    // This is Field
    public int Age;

    public void MyMethod() { }
  }
}
```

```bash
# output:
ConsoleApp1, Version=1.0.0.0 Culture=neutral, PublicKeyToken=null
Type: Program
Type: Sample
        Property: Name
```