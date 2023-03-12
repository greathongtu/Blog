---
layout: '../../layouts/MarkdownPost.astro'
title: '总结C#所学'
pubDate: 2023-03-07
description: '总结C#所学如Attributes、Reflection'
author: 'Aaron'
cover:
    url: '/preview/dotnet.png'
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

## GetCustomAttributes 
```c#
// Multiuse attribute.  
[System.AttributeUsage(System.AttributeTargets.Class |  
                       System.AttributeTargets.Struct,  
                       AllowMultiple = true)  // Multiuse attribute.  
]  
public class Author : System.Attribute  
{  
    string name;  
    public double version;  
  
    public Author(string name)  
    {  
        this.name = name;  
  
        // Default value.  
        version = 1.0;  
    }  
  
    public string GetName()  
    {  
        return name;  
    }  
}  
  
// Class with the Author attribute.  
[Author("P. Ackerman")]  
public class FirstClass  
{  
    // ...  
}  
  
// Class without the Author attribute.  
public class SecondClass  
{  
    // ...  
}  
  
// Class with multiple Author attributes.  
[Author("P. Ackerman"), Author("R. Koch", version = 2.0)]  
public class ThirdClass  
{  
    // ...  
}  
  
class TestAuthorAttribute  
{  
    static void Test()  
    {  
        PrintAuthorInfo(typeof(FirstClass));  
        PrintAuthorInfo(typeof(SecondClass));  
        PrintAuthorInfo(typeof(ThirdClass));  
    }  
  
    private static void PrintAuthorInfo(System.Type t)  
    {  
        System.Console.WriteLine("Author information for {0}", t);  
  
        // Using reflection.  
        System.Attribute[] attrs = System.Attribute.GetCustomAttributes(t);  // Reflection.  
  
        // Displaying output.  
        foreach (System.Attribute attr in attrs)  
        {  
            if (attr is Author)  
            {  
                Author a = (Author)attr;  
                System.Console.WriteLine("   {0}, version {1:f}", a.GetName(), a.version);  
            }  
        }  
    }  
}  
/* Output:  
    Author information for FirstClass  
       P. Ackerman, version 1.00  
    Author information for SecondClass  
    Author information for ThirdClass  
       R. Koch, version 2.00  
       P. Ackerman, version 1.00  
*/
```

# Assembly

```c#
Assembly assembly = Assembly.GetExecutingAssembly();
Assembly assembly = Typename.GetType().Assembly;
Assembly assembly = Assembly.Loadfile("File path");
```
一个 solution 包含多个 Csproj， 一个 Csproj 对应一个 assembly (即.dll 或者 .exe)。
一个 assembly 中包含多个模块，一个模块包含多个 Type。
Type 即是 Class/Interface。

一个程序运行起来以后，有一个应用程序域（AppDomain），在这个应用程序域（AppDomain）中放了我们用到的所有程序集（Assembly）。我们所写的所有代码都会编译到【程序集】文件（.exe .dll）中，并在运行时以【Assembly对象】方式加载到内存中运行，

```c#
// 获得当前【应用程序域】中的所有程序集
Assembly[] ass = AppDomain.CurrentDomain.GetAssemblies();

// 获得Type对象的方式：
Type t1 = typeof(Person);
Type t2 = person.GetType();  
Type t3 = this.GetType();

// 获得程序集中定义的所有的public类
Type[] allPublicTypes = ass1.GetExportedTypes();

// 获得程序集中定义的所有的类
Type[] allTypes = ass1.GetTypes();
```