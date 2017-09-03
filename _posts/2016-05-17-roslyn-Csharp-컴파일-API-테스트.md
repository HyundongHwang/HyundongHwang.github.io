---
layout: post
title: 160517 roslyn C# 컴파일 API 테스트
comments: true
tags:
- roslyn
- c#
- study
- .net
---

<!-- TOC -->

- [roslyn 추가](#roslyn-추가)
- [예제 다운로드](#예제-다운로드)
- [소스코드를 로드후, 네임스페이스, 클래스, 속성, 메소드 분석](#소스코드를-로드후-네임스페이스-클래스-속성-메소드-분석)
- [컴파일 수행후, pdb exe 생성](#컴파일-수행후-pdb-exe-생성)
- [컴파일 수행후, 메소드 내부에 구현된 구현내용을 분석](#컴파일-수행후-메소드-내부에-구현된-구현내용을-분석)

<!-- /TOC -->

<br/>
<br/>
<br/>

## roslyn 추가
- nuget package manager로 추가
 
```powershell
PM> Install-Package Microsoft.CodeAnalysis -Pre
```

<br/>
<br/>
<br/>

## 예제 다운로드
https://github.com/HyundongHwang/MyRoslynTest

<br/>
<br/>
<br/>

## 소스코드를 로드후, 네임스페이스, 클래스, 속성, 메소드 분석

```csharp
/// <summary>
/// 소스코드를 로드
/// CSharpSyntaxTree 로 구조화
/// 네임스페이스, 클래스, 속성, 메소드를 차례로 순환하며 분석
/// </summary>
private static void _PrintNsClassPropMethod()
{
    // CSharpSyntaxTree로 소스코드 파싱
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



    // 루트노드 조사
    var root = (CompilationUnitSyntax)tree.GetRoot();
    Console.WriteLine($@"root : {root}");

    // namespace 노드 리스팅
    foreach (var ns in root.Members.OfType<NamespaceDeclarationSyntax>())
    {
        Console.WriteLine($@"ns.Name : {ns.Name}");
        var nsName = ns.Name.ToString();

        foreach (var nsMem in ns.Members)
        {
            // class 노드
            if (nsMem is ClassDeclarationSyntax)
            {
                var cls = (ClassDeclarationSyntax)nsMem;
                Console.WriteLine($@"cls : {cls}");

                // class 노드의 멤버들 리스팅
                foreach (var clsChild in cls.ChildNodes())
                {
                    Console.WriteLine($@"clsChild : {clsChild}");

                    //속성
                    if (clsChild is PropertyDeclarationSyntax) 
                    {
                        var prop = (PropertyDeclarationSyntax)clsChild;
                        Console.WriteLine($@"prop.Identifier.ValueText : {prop.Identifier.ValueText}");
                    }
                    // 메서드
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

## 컴파일 수행후, pdb exe 생성

```csharp
/// <summary>
/// 소스코드 로드, CSharpSyntaxTree로 구조화
/// object의 어셈블리 (즉 mscorlib.dll)을 로드함.
/// 로드한 어셈블리와 소스코드트리를 이용하여 컴파일객체를 생성
/// 파싱 에러 체크
/// 심벌 바인딩 에러 체크
/// Emit(exe, pdb 생성)
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



    // Compilation 생성
    var mscorlibRef = MetadataReference.CreateFromFile(Assembly.GetAssembly(typeof(object)).Location); // mscorlib
    var compilation = CSharpCompilation.Create("test").AddReferences(mscorlibRef).AddSyntaxTrees(tree);



    // 파싱 에러 체크
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



    // 심벌 바인딩 에러 체크
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
        // EXE 생성
        Console.WriteLine("Emit ...");
        var emitResult = compilation.Emit(@"test.exe", @"test.pdb");

        // Emit 에러 체크
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

## 컴파일 수행후, 메소드 내부에 구현된 구현내용을 분석

```csharp
/// <summary>
/// 소스코드로드, CSharpSyntaxTree로 구조화
/// Compilation 생성
/// GlobalNamespace 부터 시작하여 클래스, 속성, 메소드 까지 탐색...
/// 특정메소드(Run) 내부에 구현된 InvocationExpression을 조회하여 출력...
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