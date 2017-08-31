---
layout: post
title: 160517 roslyn C# compile, API tests
comments: true
tags:
- roslyn
- c#
- study
- .net
---

<!-- TOC -->

- [add roslyn](#add-roslyn)
- [download sample](#download-sample)
- [After you load the source code, analysis namespaces, classes, properties, methods.](#after-you-load-the-source-code-analysis-namespaces-classes-properties-methods)
- [After compiling, create pdb exe files](#after-compiling-create-pdb-exe-files)
- [After compilation performed, analyzing the implementation within the method](#after-compilation-performed-analyzing-the-implementation-within-the-method)

<!-- /TOC -->

<br/>
<br/>
<br/>

## add roslyn 
- add by nuget package manager
 
```powershell
PM> Install-Package Microsoft.CodeAnalysis -Pre
```

<br/>
<br/>
<br/>

## download sample
https://github.com/HyundongHwang/MyRoslynTest

<br/>
<br/>
<br/>

## After you load the source code, analysis namespaces, classes, properties, methods.

```csharp
/// <summary>
/// load source code
/// structure CSharpSyntaxTree 
/// Analysis namespaces, classes, properties, and methods in order
/// </summary>
private static void _PrintNsClassPropMethod()
{
    // Parsing source code by CSharpSyntaxTree
    var tree = CSharpSyntaxTree.ParseText(
@"
    namespace NS1
    { 
        class Class1
        { 
            public int ID { get; set; }
            public string Name { get; set; }
            public void Run(int id) { }        
        } 
    }");

    Console.WriteLine($@"tree : {tree}");



    // inspect root node
    var root = (CompilationUnitSyntax)tree.GetRoot();
    Console.WriteLine($@"root : {root}");

    // list namespace nodes
    foreach (var ns in root.Members.OfType<NamespaceDeclarationSyntax>())
    {
        Console.WriteLine($@"ns.Name : {ns.Name}");
        var nsName = ns.Name.ToString();

        foreach (var nsMem in ns.Members)
        {
            // class node
            if (nsMem is ClassDeclarationSyntax)
            {
                var cls = (ClassDeclarationSyntax)nsMem;
                Console.WriteLine($@"cls : {cls}");

                // list class node's members
                foreach (var clsChild in cls.ChildNodes())
                {
                    Console.WriteLine($@"clsChild : {clsChild}");

                    // properties
                    if (clsChild is PropertyDeclarationSyntax) 
                    {
                        var prop = (PropertyDeclarationSyntax)clsChild;
                        Console.WriteLine($@"prop.Identifier.ValueText : {prop.Identifier.ValueText}");
                    }
                    // methods
                    else if (clsChild is MethodDeclarationSyntax) 
                    {
                        var mth = (MethodDeclarationSyntax)clsChild;
                        Console.WriteLine($@"mth.Identifier.ValueText : {mth.Identifier.ValueText}");
                    }
                }
            }
        }
    }
}
```

<br/>
<br/>
<br/>

## After compiling, create pdb exe files

```csharp
/// <summary>
/// load source code, structure CSharpSyntaxTree
/// object's assembly (in other words, mscorlib.dll) load.
/// Generating a compiled object by using a loaded assembly and the source code tree
/// check parsing errors
/// check symbol's binding errors
/// emit exe, pdb 
/// </summary>
private static void _Compile()
{
    var tree = CSharpSyntaxTree.ParseText(@"
    using System;

    namespace HelloWorld 
    {
        class Program 
        {
            static void Main(string[] args) 
            {
                Console.WriteLine(""Hello, World!"");
            }
        }
    }");



    // create Compilation 
    var mscorlibRef = MetadataReference.CreateFromFile(Assembly.GetAssembly(typeof(object)).Location); // mscorlib
    var compilation = CSharpCompilation.Create("test").AddReferences(mscorlibRef).AddSyntaxTrees(tree);



    // check parsing error
    var parseDiags = compilation.GetParseDiagnostics();

    if (parseDiags.Any())
    {
        Console.WriteLine("Parse Errors!!!");
        parseDiags.ToList().ForEach(p => Console.WriteLine(p.ToString()));
    }
    else
    {
        Console.WriteLine("Parse OK");
    }



    // check symbol's binding error
    var declDiags = compilation.GetDeclarationDiagnostics();

    if (declDiags.Any())
    {
        Console.WriteLine("Symbol Binding Errors!!!");
        declDiags.ToList().ForEach(p => Console.WriteLine(p.ToString()));
    }
    else
    {
        Console.WriteLine("Symbol Binding OK");
    }


    try
    {
        // create EXE 
        Console.WriteLine("Emit ...");
        var emitResult = compilation.Emit(@"test.exe", @"test.pdb");

        // check Emit error
        if (emitResult.Diagnostics.Any())
        {
            Console.WriteLine("Emit Errors!!!");
            emitResult.Diagnostics.ToList().ForEach(p => Console.WriteLine(p.ToString()));
        }
        else
        {
            Console.WriteLine("Emit OK");
        }

    }
    catch (Exception ex)
    {
        Console.WriteLine($@"ex : {ex}");
    }
}
```

<br/>
<br/>
<br/>

## After compilation performed, analyzing the implementation within the method

```csharp
/// <summary>
/// load source code, structure CSharpSyntaxTree
/// create Compilation
/// start from global Namespace, navigating classes, properties, methods, ...
/// print implementation of specific method (Run) by querying InvocationExpression ...
/// </summary>
private static void _CompileAndSenmaticAnalysis()
{
    var tree = CSharpSyntaxTree.ParseText(@"
using System;
namespace NS1 { 
class Class1 { 
private int _flag;
public int ID { get; set; }
public string Name { get; set; }
public void Run(int id) {
   int a = 1;
   a = a + id * 10;
   Console.WriteLine(a);
}
} 

class Class2 {} 
}");



    // Compilation 생성
    var mscorlibRef = MetadataReference.CreateFromFile(Assembly.GetAssembly(typeof(object)).Location); // mscorlib
    var compilation = CSharpCompilation.Create("test").AddReferences(mscorlibRef).AddSyntaxTrees(tree);



    // GlobalNamespace 심벌
    var globalNs = compilation.GlobalNamespace;

    foreach (var gNsMem in globalNs.GetMembers())
    {
        if (gNsMem.Name == "NS1")
        {
            // namespace 심벌
            var ns1 = (INamespaceSymbol)gNsMem;

            // class 심벌
            foreach (var ns1Mem in ns1.GetMembers())
            {
                Console.WriteLine(ns1Mem.ToDisplayString());
            }
        }
    }



    var root = tree.GetRoot();
    // Syntax tree에 대한 Semantic Model 객체 
    var semModel = compilation.GetSemanticModel(tree);
    // 메서드 트리노드 찾기
    var methodFirst = root.DescendantNodes().OfType<MethodDeclarationSyntax>().First();
    // 메서드 심벌 리턴
    var methodSym = semModel.GetDeclaredSymbol(methodFirst);
    Console.WriteLine($@"methodSym.ToDisplayString() : {methodSym.ToDisplayString()}");



    var classNode = root.DescendantNodes().OfType<ClassDeclarationSyntax>().First();
    var clsSym = semModel.GetDeclaredSymbol(classNode);

    foreach (var clsSymMem in clsSym.GetMembers())
    {
        if (clsSymMem is IMethodSymbol)
        {
            // 메서드 심벌 출력                
            Console.WriteLine($@"IMethodSymbol clsSymMem : {clsSymMem}");
        }
        else if (clsSymMem is IFieldSymbol)
        {
            // 필드 심벌 출력                
            Console.WriteLine($@"IFieldSymbol clsSymMem : {clsSymMem}");
        }
        else
        {
            // skip
        }
    }



    // Run() 안의 Console.WriteLine() 호출
    var firstInvokeExpSyntax = methodFirst.DescendantNodes().OfType<InvocationExpressionSyntax>().First();
    Console.WriteLine($@"firstInvokeExpSyntax : {firstInvokeExpSyntax}");

    var symInfo = semModel.GetSymbolInfo(firstInvokeExpSyntax);
    var symInfoSym = symInfo.Symbol;

    if (symInfoSym.ContainingAssembly.Name == "mscorlib" && symInfoSym.ContainingNamespace.Name == "System" &&
        symInfoSym.ContainingType.Name == "Console" && symInfoSym.Name == "WriteLine")
    {
        Console.WriteLine("System.Console 클래스의 WriteLine 메서드임");
    }



    // 표현식 : a = a + id * 10
    var firstBinExpSyntax = methodFirst.DescendantNodes().OfType<BinaryExpressionSyntax>().First();
    var firstBinExpTypeInfo = semModel.GetTypeInfo(firstBinExpSyntax);
    Console.WriteLine(firstBinExpTypeInfo.Type);  // int
}
```