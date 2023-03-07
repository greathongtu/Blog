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
tags: ["dotnet"]
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

      // Output: ConsoleApp1, Version=1.0.0.0 Culture=neutral, PublicKeyToken=null
      Console.WriteLine(assembly.FullName);

      // Types are just classes
      var types = assembly.GetTypes();
      foreach (var type in types)
      {
        // Type: Program
        // Type: Sample
        Console.WriteLine("Type: " + type.name);

        var props = type.GetProperties();
        foreach (var prop in props)
        {
          // Property: Name
          Console.WriteLine("\tProperty: " + prop.name);
        }

        // GetFields
        // GetMethods
      }
      var sample = new Sample { Name = "John", Age = 25 };
      var sampleType = typeof(Sample);
      var nameProperty = sampleType.GetProperty("Name");
      // output: John
      Console.WriteLine("Property: " + nameProperty.GetValue(sample));

      var meth = sampleType.GetMethod("MyMethod");

      meth.Invoke(sample, null);

      var types2 = assembly.GetTypes().Where(t => t.GetCustomAttributes<MyClassAttribute>().Count() > 0);
      foreach (var type in types2)
      {
        // output: Sample
        Console.WriteLine(type.name);

        var methods = type.GetMethods().Where(t => t.GetCustomAttributes<MyMethodAttribute>().Count() > 0);
        foreach (var method in methods)
        {
          // output: MyMethod
          Console.WriteLine(method.name);
        }
      }
    }
  }

  [MyClass]
  public class Sample
  {
    // This is Property
    public string Name { get; set; }
    // This is Field
    public int Age;

    [MyMethod]
    public void MyMethod() { }
  }
}
```