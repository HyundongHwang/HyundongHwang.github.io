---
layout: post
title: 170723 .net c# wpf 스터디
comments: true
tags:
- study
- c#
- .net
- wpf
---



<!-- TOC -->

- [Using WPF dll's in UWP app](#using-wpf-dlls-in-uwp-app)
- [닷넷프레임워크 히스토리](#닷넷프레임워크-히스토리)
- [Understanding .NET 2015](#understanding-net-2015)
- [닷넷 컴파일](#닷넷-컴파일)
- [WPF 어플리케이션 개발시 필요한 것들](#wpf-어플리케이션-개발시-필요한-것들)
- [C#, WPF 특수 문법/기능](#c-wpf-특수-문법기능)
- [닷넷월드의 용어정리](#닷넷월드의-용어정리)
- [C# 코딩 컨벤션](#c-코딩-컨벤션)
- [참고링크](#참고링크)
- [추천도서](#추천도서)

<!-- /TOC -->


## Using WPF dll's in UWP app
- http://stackoverflow.com/questions/34477450/using-wpf-dlls-in-uwp-app

```
Q : WPF dll을 UWP 에서 사용할 수 있는가?
A : 없다. 
WPF와 UWP는 근본이 다른 프레임워크로 기본적으로는 코드를 공유할 수 없다.
그렇지만 부분적으로는 PCL(Portable Class Library)로 닷넷월드 내에서 공유가능한 라이브러리 형태로 만들수 있는 코드가 있다.
```

- 닷넷월드 아키텍쳐
![](http://i.stack.imgur.com/9rpoC.png)

- PCL(Portable Class Library) 라이브러리 플랫폼 의존성 선택화면
![](http://i.stack.imgur.com/8vEFH.png)



## 닷넷프레임워크 히스토리 
- https://ko.wikipedia.org/wiki/%EB%8B%B7%EB%84%B7_%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC
- 1.0
    - 2002.1
    - 시작
- 2.0
    - 2005.11
    - 제너릭 도입
- 3.0
    - 2006.11
    - WPF 도입 
    - 비스타에 프리로드
- 3.5
    - 2007.11
    - LINQ 도입
    - Entity Framework 도입
- 4.0
    - 2010.4
    - PLINQ, dynamic 도입 
    - async/await 라이브러리로 하위호환 지원.
    - Windows XP는 4.0까지만 지원함.
- 4.5
    - 2012.8 (Windows 8.0 출시)
    - Windows 8.0에 프리로드 
    - async/await 포함.
    - roslyn 컴파일러 라이브러리로 하위호환 지원.
- 5.0
    - 개발중
    - .NET Core : 모든 운영체제 지원
    - 컴파일러, 닷넷프레임워크 자체의 오픈소스화.
    - roslyn 컴파일러 (Compiler as Service)





## Understanding .NET 2015
닷넷월드 미래상???
https://blogs.msdn.microsoft.com/bethmassi/2015/02/25/understanding-net-2015/
![](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/81/88/metablogapi/1682.image_thumb_17D67A88.png)



## 닷넷 컴파일
http://www.c-sharpcorner.com/uploadfile/puranindia/net-framework-and-architecture/
![](http://csharpcorner.mindcrackerinc.netdna-cdn.com/UploadFile/puranindia/net-framework-and-architecture/Images/managed_code.gif)



## WPF 어플리케이션 개발시 필요한 것들
- 필수
    - 개발툴
        - Visual Studio 2015
        - Blend 2015
            - xaml코드 GUI 편집툴
            - xaml코드를 텍스트로 편집하는 것보다 생산성 크게 향상.
        - nuget
            - 외부 라이브러리 임포트, 버전관리
            - 닷넷월드의 모든 라이브러리들은 nuget으로 관리됨. 
            - 닷넷 5.0 부터는 컴파일러와 닷넷프레임워크 자체도 nuget으로 배포예정
    - 라이브러리
        - 닷넷프레임워크
            - XP지원을 위해 4.0으로 사용함.
        - WPF
            - Windows Presentation Framework
            - 닷넷으로 윈도우 어플리케이션을 만들기 위한 UI 프레임워크
            - xaml UI + C# code
            - UI 개발생산성 크게 증대!
            - 게임UI와 같이 모든 화면에 DirectX를 사용하여 렌더링 속도 매우 빠름.
        - Entity Framework
            - MS 공식 ORM 라이브러리
            - http://www.hoons.net/lecture/view/507
            - DB connection string 만 있으면 자동으로 DB 매핑코드가 생성/업데이트 됨.
            - 이 DB 매핑코드는 LINQ 구문으로 사용됨.
            - nuget 으로 설치됨.
        - sqlite
            - 가장 많이 사용되는 클라이언트 파일 DB
            - MS도 최근 자사의 MS SQL CE를 밀어내고 더 많이 밀어줌.
            - sqlite + Entity Framework 권장.
            - nuget 으로 설치됨.
        - json.net
            - 가장 많이 사용되는 json xml 라이브러리
            - nuget 으로 설치됨.
    - 기술
        - async/await
            - 비동기 프로그래밍 프로그래밍 기술
            - 멀티스레드 프로그래밍시 발생하는 callback hell을 없애 코드복잡도를 단일스레드 수준으로 낮춤
        - LINQ
            - DB, Memory Collection, json, xml 등 CRUD가 가능한 모든 데이타를 SQL과 유사한 구문으로 사용할 수 있게 한 기술.
            - LINQ 구문은 언어에 완전히 통합되어 자동완성, 정지점 디버깅 등 일반 닷넷 프로그래밍의 이점을 똑같이 유지함.
        - xaml UI
            - 안드로이드, html5 등과 같은 xml 기반의 선언형 UI 기술.
            - 단순한 선언형 UI 외에도 스타일, 템플릿, 더미테스트, 애니메이션, 모듈재사용 등 고급기능도 단일하게 지원함.
        - Data Binding
            - Data Model과 UI표현 사이를 자동 반영할 수 있도록 지원한 기술.
            - event 키워드로 구현된 닷넷의 옵저버패턴의 이벤트 소스를 binding 키워드로 구현된 xaml UI가 수신하는 형태.
        - P/Invoke
            - 닷넷 어플리케이션이 win32 navite 어플리케이션과 통신하기 위한 일반적인 방식
            - 닷넷 어플리케이션이 win32 dll의 c-style 함수를 call 할수 있음.
            - 닷넷 어플리케이션이 win32 dll에 자신의 callback function을 전달하여 callback을 받을수 있음.
            - DotNetApp.exe <--> Win32.dll <--> Win32App.exe
- 옵션
    - 개발툴 
        - fiddler
            - http, https, 웹소켓 세션분석
        - powershell
            - 기존 윈도우 도스쉘을 대체한 쉘
            - bash를 따라하여 명령어가 리눅스 개발자에 친숙.
            - 내부에서 닷넷프레임워크를 사용하여 닷넷 프레임워크의 모든기능 사용가능.
            - 닷넷월드의 최신기능이 먼저 적용됨.
            - https://hhd2002.blogspot.kr/2015/11/151119.html
        - VS Code Snippet
            - 코드 자동완성
            - 닷넷코드중 이벤트소스, dependancy-property 는 boiler-plate 코드가 꽤 길다. 
            - 이외에도 여기저기 사용하면 유용함.
            - https://gitlab.com/hhd2002/hhdvscodesnippets
        - Dotfuscator
            - 닷넷 난독화 툴(유료)
            - (DotNetApp.exe + DotNet.dll) --> Dotfuscator.exe -> OfuscatedDotNetApp.exe + OfuscatedDotNet.dll
            - 상용 배포시에 필요함.
        - SandCastle, GhostDoc
            - http://sandcastle.codeplex.com/
            - http://submain.com/products/ghostdoc.aspx
            - 닷넷 코드문서 생성툴
            - 안써봐서 뭐가 좋은지 모르지만 필요하면 2개중 하나 써야 할듯.
        - Splunk MINT
            - 어플리케이션 크래시리포트 수집, 분석 서비스(대용량 되면 유료전환)
            - 크래시리포트 수집툴 중 웹 어드민 UI가 가장 좋았음.
            - 서비스 자체는 안드로이드, 아이폰 앱의 크래시에 맞춰져 있는데, REST API로도 서비스되는 터라 WPF 어플리케이션에서 써도 무방함.
    - 라이브러리
        - Telerik WPF Control
            - UI 라이브러리(유료)
            - flat style control
            - drag and drop card control
            - rich chart control
            - rich data grid control
        - VisualStudio UnitTest
            - VS와 통합된 닷넷 유닛테스트 라이브러리
            - ASP.NET 서버개발이라면 TDD하면 개발생산성 크게 좋아짐.
            - 클라개발은 TDD 정도는 아니라도 중간중간 로직 테스트 하기에 쓸만함.
            - full test 이후에 테스트 결과가 html형태로 예쁘게 나옴.



## C#, WPF 특수 문법/기능
- get, set 키워드
    - https://msdn.microsoft.com/ko-kr/library/w86s7x04.aspx
    - 속성은 fields와 methods의 특징을 모두 갖고 있습니다

    ```
    public class Employee
    {
        private string name;
        public string Name
        {
            get { return name; }
            set { name = value; }
        }
    }

    class Customer
    {
        // Auto-Impl Properties for trivial get and set
        public double TotalPurchases { get; set; }
        public string Name { get; set; }
        public int CustomerID { get; set; }

        // Constructor
        public Customer(double purchases, string name, int ID)
        {
            TotalPurchases = purchases;
            Name = name;
            CustomerID = ID;
        }
        // Methods
        public string GetContactInfo() {return "ContactInfo";}
        public string GetTransactionHistory() {return "History";}

        // .. Additional methods, events, etc.
    }
    ```

- delegate 키워드
    - https://msdn.microsoft.com/ko-kr/library/ms173171.aspx
    - 대리자는 특정 매개 변수 목록 및 반환 형식이 있는 메서드에 대한 참조를 나타내는 형식입니다. 대리자를 인스턴스화하면, 호환되는 시그니처와 반환 형식을 가진 모든 메서드를 대리자 인스턴스에 연결할 수 있습니다. 대리자 인스턴스를 통해 메서드를 호출할 수 있습니다.
    - 쉽게 말하면 함수의 타입!

    ```
    public delegate void Del(string message);

    public static void DelegateMethod(string message)
    {
        System.Console.WriteLine(message);
    }

    // Instantiate the delegate.
    Del handler = DelegateMethod;

    // Call the delegate.
    handler("Hello World");
    ```

- event 키워드
    - http://www.csharpstudy.com/CSharp/CSharp-event.aspx
    - 이벤트는 클래스내에 특정한 일(event)이 있어났음을 외부의 이벤트 가입자(subscriber)들에게 알려주는 기능을 한다. C#에서 이벤트는 event라는 키워드를 사용하여 표시하며, 클래스 내에서 일종의 필드처럼 정의된다.

    ```
    // 클래스 내의 이벤트 정의
    class MyButton
    {
       public string Text;
       // 이벤트 정의
       public event EventHandler Click;

       public void MouseButtonDown()
       {
          if (this.Click != null)
          {
             // 이벤트핸들러들을 호출
             Click(this, EventArgs.Empty);
          }
       }
    }

    // 이벤트 사용
    public void Run()
    {
       MyButton btn = new MyButton();
       // Click 이벤트에 대한 이벤트핸들러로
       // btn_Click 이라는 메서드를 지정함
       btn.Click += new EventHandler(btn_Click);
       btn.Text = "Run";
       //....
    }

    void btn_Click(object sender, EventArgs e)
    {
       MessageBox.Show("Button 클릭");
    }
    ```

- dependancy property
    - https://msdn.microsoft.com/ko-kr/library/ms752914(v=vs.110).aspx
    - http://www.dominikschmidt.net/2010/12/net-c-binding-custom-dependencyproperty-to-viewmodel-property/
    - event 소스가 데이타바인딩을 통해서 통지될때 이를 수신해 주는 코딩
        - 실무적으로는 커스톰컨트롤을 개발할때 데이타바인딩을 받아줄 수신부를 만들어 낼때 사용
        - 예를들면 MyTextBox컨트롤의 SpecialText속성 정도???

    - ViewModel (implements INotifyPropertyChanged):

    ```
    private string _someProperty;
     
    public string SomeProperty
    {
        get { return _someProperty; }
        set
        {
            _someProperty = value;
            if (PropertyChanged != null)
                PropertyChanged(this, new PropertyChangedEventArgs("SomeProperty"));
        }
    }
    ```

    - UserControl (class “MyUserControl”):
    
    ```
    public static readonly DependencyProperty DemoProperty = 
        DependencyProperty.Register
        (
            "Demo", 
            typeof(string), 
            typeof(MyUserControl), 
            new FrameworkPropertyMetadata() 
            { 
                PropertyChangedCallback = OnDemoChanged, 
            }
        );

    public string Demo
    {
        get { return this.GetValue(DemoProperty ); }
        set { this.SetValue(DemoProperty , value); }
    }

    private static void OnDemoChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
    {
        if (e.OldValue != e.NewValue)
        {
            // code to be executed on value update
        }
    }
    ```

    - data binding

    ```
    <local:MyUserControl Demo="{Binding MyViewModel.SomeProperty}"/>
    ```

- attribute
    - https://msdn.microsoft.com/ko-kr/library/aa288454(v=vs.71).aspx
    - 특성은 선언 정보를 C# 코드(형식, 메서드, 속성 등)와 연결하는 강력한 방법을 제공합니다
    - java의 annotation과 같음
    
    ```
    public class HelpAttribute : System.Attribute 
    {
       public readonly string Url;

       public string Topic               // Topic is a named parameter
       {
          get 
          { 
             return topic; 
          }
          set 
          { 

            topic = value; 
          }
       }

       public HelpAttribute(string url)  // url is a positional parameter
       {
          this.Url = url;
       }

       private string topic;
    }



    [HelpAttribute("http://localhost/MyClassInfo")]
    class MyClass 
    {
    }
    ```

- reflection
    - http://www.csharpstudy.com/Practical/Prac-reflection.aspx
    - .NET Reflection은 .NET 객체의 클래스 타입, 메서드, 프로퍼티 등의 메타 정보를 런타임 중에 알아내는 기능을 제공한다. 또한, 이러한 메타 정보를 얻은 후, 직접 메서드를 호출하거나 프로퍼티를 변경하는 등의 작업도 가능하다. 물론 객체에서 메서드를 직접 호출하는 경우가 더 빠르겠지만, 어떤 경우는 런타임중에 이런 메타 정보가 동적으로 알아낼 필요가 있다. 
    - java의 reflection과 동일
    
    ```
    class Program
    {
        static void Main(string[] args)
        {
            MyClass1 m1 = new MyClass1();
            SetDefaultName(m1);
            Console.WriteLine(m1.Name);
        }
        
        static void SetDefaultName(object myObject)
        {
            // Name이라는 속성이 있는지?
            PropertyInfo pi = myObject.GetType().GetProperty("Name");

            // 있으면 속성값 설정
            if (pi != null)
            {
                pi.SetValue(myObject, "Lee", null);
            }
        }

    }

    class MyClass1
    {
        public string Name { get; set; }
    }
    ```

- extension method
    - https://msdn.microsoft.com/ko-kr/library/bb383977.aspx
    - 확장 메서드를 사용하면 새 파생 형식을 만들거나 다시 컴파일하거나 원래 형식을 수정하지 않고도 기존 형식에 메서드를 "추가"할 수 있습니다.
    
    ```
    public static class MyExtensions
    {
        public static int WordCount(this String str)
        {
            return str.Split(new char[] { ' ', '.', '?' }, 
                             StringSplitOptions.RemoveEmptyEntries).Length;
        }
    }



    string s = "Hello Extension Methods";
    int i = s.WordCount();
    ```

- var 키워드
    - https://msdn.microsoft.com/ko-kr/library/bb383973.aspx
    - 메서드 범위에서 선언된 변수가 암시적 형식 var를 가질 수 있습니다. 암시적으로 형식화된 지역 변수는 형식 자체를 선언했던 것처럼 강력하게 형식화되었지만 컴파일러에서 형식을 결정합니다.
    
    ```
    var i = 10; // implicitly typed
    int i = 10; //explicitly typed
    ```

    - 실용예제

    ```
    // Example #1: var is optional because
    // the select clause specifies a string
    string[] words = { "apple", "strawberry", "grape", "peach", "banana" };
    var wordQuery = from word in words
                    where word[0] == 'g'
                    select word;

    // Because each element in the sequence is a string, 
    // not an anonymous type, var is optional here also.
    foreach (string s in wordQuery)
    {
        Console.WriteLine(s);
    }
    ```

- dynamic 키워드
    - https://msdn.microsoft.com/ko-kr/library/dd264736.aspx
    - 이 형식의 개체는 정적 형식 검사에서 제외됩니다. 대부분의 경우 이 형식의 개체는 object 형식의 개체처럼 작동합니다.
    - 이 형식의 개체에서 값을 가져오는 소스가 COM API, IronPython 같은 동적 언어, HTML DOM(문서 개체 모델)

    ```
    Type typeExcel = Type.GetTypeFromProgID("Excel.Application");

    dynamic excel = Activator.CreateInstance(typeExcel);
    excel.Visible = true;

    dynamic workbooks = excel.Workbooks;
    workbooks.Add();
    workbooks.Add();
    ```

- verbatim string
    - http://www.c-sharpcorner.com/uploadfile/hirendra_singh/verbatim-strings-in-C-Sharp-use-of-symbol-in-string-literals/
    - @ 심볼을 사용해서 \ hell을 피함.

    ```
    Str1 = "this is the \n test string";


 
    Output of the str1 is :
 
    this is the
     test string
    ```

    - @ 사용 예제 

    ```
    str2 = @"this is the \n test string";



    output of the str2:

    this is the \n test string
    ```

- string interpolation
    - https://msdn.microsoft.com/ko-kr/library/dn961160.aspx
    - 포함된 식을 식 결과의 ToString 표현으로 대체하여 문자열을 만듭니다.
    
    ```
    Console.WriteLine($"Name = {name}, hours = {hours:hh}")
    ```

- using 키워드
    - https://msdn.microsoft.com/ko-kr/library/yh598w02.aspx
    - IDisposable 개체를 올바르게 사용할 수 있게 해 주는 편리한 구문
    - 기존의 일반적인 리소스 패턴
    
    ```
    {
      Font font1 = new Font("Arial", 10.0f);
      try
      {
        byte charset = font1.GdiCharSet;
      }
      finally
      {
        if (font1 != null)
          ((IDisposable)font1).Dispose();
      }
    }
    ```

    - using 활용한 리소스 패턴

    ```
    using (Font font1 = new Font("Arial", 10.0f)) 
    {
        byte charset = font1.GdiCharSet;
    }
    ```

- labda expression
    - https://msdn.microsoft.com/ko-kr/library/bb397687.aspx
    
    ```
    delegate int del(int i);
    
    static void Main(string[] args)
    {
        del myDelegate = x => x * x;
        int j = myDelegate(5); //j = 25
    }
    ```

    - UI에서 lambda expression 사용
    
    ```
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            button1.Click += async (sender, e) =>
            {
                // ExampleMethodAsync returns a Task.
                await ExampleMethodAsync();
                textBox1.Text += "\r\nControl returned to Click event handler.\r\n";
            };
        }

        async Task ExampleMethodAsync()
        {
            // The following line simulates a task-returning asynchronous process.
            await Task.Delay(1000);
        }
    }
    ```

- anonymous types
    - https://msdn.microsoft.com/ko-kr/library/bb397696.aspx
    - 익명 형식을 사용하면 먼저 명시적으로 형식을 정의할 필요 없이 읽기 전용 속성 집합을 단일 개체로 편리하게 캡슐화할 수 있습니다. 형식 이름은 컴파일러에 의해 생성되며 소스 코드 수준에서 사용할 수 없습니다. 각 속성의 형식은 컴파일러에서 유추합니다.
    
    ```
    var v = new { Amount = 108, Message = "Hello" };

    // Rest the mouse pointer over v.Amount and v.Message in the following
    // statement to verify that their inferred types are int and string.
    Console.WriteLine(v.Amount + v.Message);
    ```
    
    - 실용적인 예
    
    ```
    var productQuery = 
        from prod in products
        select new { prod.Color, prod.Price };

    foreach (var v in productQuery)
    {
        Console.WriteLine("Color={0}, Price={1}", v.Color, v.Price);
    }
    ```

- LINQ
    - https://msdn.microsoft.com/ko-kr/library/bb397676.aspx
    - https://msdn.microsoft.com/ko-kr/library/bb397941.aspx
    - LINQ(Language-Integrated Query)는 C# 언어(또한 Visual Basic 및 잠재적으로 다른 모든 .NET 언어)에 대한 쿼리 기능의 직접 통합을 기반으로 하는 기술 집합의 이름입니다. LINQ를 사용하면 쿼리가 클래스, 메서드, 이벤트와 같은 고급 언어 구문이 됩니다.
    - 기본적인 select
    
    ```
    // Specify the data source.
    int[] scores = new int[] { 97, 92, 81, 60 };

    // Define the query expression.
    IEnumerable<int> scoreQuery =
        from score in scores
        where score > 80
        select score;

    // Execute the query.
    foreach (int i in scoreQuery)
    {
        Console.Write(i + " ");
    }
    ```

    - 내부 조인 수행
    
    ```
    public static void InnerJoinExample()
    {
        Person magnus = new Person { FirstName = "Magnus", LastName = "Hedlund" };
        Person terry = new Person { FirstName = "Terry", LastName = "Adams" };
        Person charlotte = new Person { FirstName = "Charlotte", LastName = "Weiss" };
        Person arlene = new Person { FirstName = "Arlene", LastName = "Huff" };
        Person rui = new Person { FirstName = "Rui", LastName = "Raposo" };

        Pet barley = new Pet { Name = "Barley", Owner = terry };
        Pet boots = new Pet { Name = "Boots", Owner = terry };
        Pet whiskers = new Pet { Name = "Whiskers", Owner = charlotte };
        Pet bluemoon = new Pet { Name = "Blue Moon", Owner = rui };
        Pet daisy = new Pet { Name = "Daisy", Owner = magnus };

        // Create two lists.
        List<Person> people = new List<Person> { magnus, terry, charlotte, arlene, rui };
        List<Pet> pets = new List<Pet> { barley, boots, whiskers, bluemoon, daisy };

        // Create a collection of person-pet pairs. Each element in the collection
        // is an anonymous type containing both the person's name and their pet's name.
        var query = from person in people
                    join pet in pets on person equals pet.Owner
                    select new { OwnerName = person.FirstName, PetName = pet.Name };

        foreach (var ownerAndPet in query)
        {
            Console.WriteLine("\"{0}\" is owned by {1}", ownerAndPet.PetName, ownerAndPet.OwnerName);
        }
    }
    ```

- async/await
    - http://www.csharpstudy.com/CSharp/CSharp-async-await.aspx
    - C# 5.0부터 새로운 C# 키워드로 async와 await가 추가되었다. 이 키워드들은 기존의 비동기 프로그래밍 (asynchronous programming)을 보다 손쉽게 지원하기 위해 C# 5.0에 추가된 중요한 기능이다.
    
    ```
    private async void button1_Click(object sender, EventArgs e)
    {
        await RunAsync();  //UI Thread에서 실행
    }

    private async void RunAsync()
    {    
        int sum = await LongCalc2(10);
        this.label1.Text = "Sum = " + sum;
        this.button1.Enabled = true;
    }

    private async Task<int> LongCalc2(int times)
    {
        //UI Thread에서 실행
        Debug.WriteLine(Thread.CurrentThread.ManagedThreadId);
        int result = 0;
        for (int i = 0; i < times; i++)
        {
            result += i;                
            await Task.Delay(1000);
        }
        return result;
    }
    ```

- 커스톰컨트롤, 유저컨트롤
    - 커스톰컨트롤
        - 기존의 WPF컨트롤을 상속받아 기능을 확장함.
        - ex) MyButton, MyPasswordTextBox, TelerikDataGrid ...
        - 기존 컨트롤의 기능을 훼손하지 않으면서 기능을 변경하기가 어려울때가 많아 난이도 높음.
        - 실무에선 새 커스톰컨트롤 만들기 보단 그냥 Telerik 처럼 사다 씀.
    - 유저컨트롤
        - 기존의 WPF컨트롤들을 조합해서 재사용 가능한 모듈을 만듬.
        - ex) NntProfileToolbar, NntTextContentPage
        - UserControl을 상속받아서 차일드로 기존 WPF컨트롤을 원하는 모양으로 붙이는 개발이라 난이도 낮음.
        - 실무에서 자주 쓰이고, 유저컨트롤 구조를 잘 잡는게 WPF UI 설계의 관건.



## 닷넷월드의 용어정리
- UWP
    - Universal Windows Platform
    - W10, W10 Mobile, W10 IoT Core 에서 사용되는 단일한 개발환경
    - 마켓배포, 보안정책
        - 개발된 앱은 패키징되어 윈도우 마켓플레이스에 배포되고 사용자에게 전달됨.(앱스토어, 플레이스토어 모델)
        - 앱은 아이폰앱과 같이 강력한 sandbox 적용을 받아 앱외부의 파일접근이 불가.
        - 허용된 안전한 win32 API를 사용할 수 있고 이때도 sandbox 정책의 적용을 받음.
    - 개발 언어
        - C#, VB.NET, javascript, C++/CX 를 사용가능함.
        - C#이 권장사항이지만 다른언어도 되긴됨.
    - 개발 실무
        - 실무개발상 C# code + xaml UI 인 점은 일반 닷넷 어플리케이션 개발과 같음.
        - 하지만 닷넷프레임워크와 UWP의 BCL은 엄연히 다르고 이 때문에 바이너리 상의 모듈 참조는 안됨.
        - PCL (Portable Class Library)를 이용하여 닷넷프레임워크와 UWP 호환 라이브러리를 만들수는 있다고 하지만 사실 BCL이 너무 달라서 실무에는 비추천.
- W10, W10 Mobile, W10 IoT Core
    - PC, 폰, IoT 디바이스가 UWP로 통일됨.
    - ![](https://regmedia.co.uk/2015/05/20/windows10iot.png)
    - W10
        - PC용 윈도우, 우리가 매일 사용하는 그 Windows 10
    - W10 Mobile
        - 망해버린 윈도우폰의 최신버전.
        - 폰OS로 활약한다기 보다, IoT Core의 rich edition으로 활용될 가능성이 큼.
        - Windows Mobile 6(Win32 C++) -> Windows Phone 7(Silverlight C#) -> Windows Phone 7.5(Silverlight C#) -> Windows Phone 8(WinRT C#) -> Windows 10 Mobile(UWP C#)
    - W10 IoT Core
        - IoT 디바이스를 위한 윈도우
        - 라즈베리파이, 드레곤보드, 아두이노 등 제너럴 IoT 기기에서 잘 동작함.
        - IoT OS이므로 기본 GUI쉘이 없어서 네트워크 설정, 가상키보드, 시계... 등 기본기능을 필요시 직접 만들어서 제공해야 함.
        - MS에서 신경써서 매주 OS업데이트, 풍부한 샘플코드를 업데이트 해서 개발상 어려움은 적음.
- CodeFirst, DB First
    - Entity Framework 사용시 ORM code 를 생성하는 방법 구분
    - CodeFirst
        - 닷넷클래스를 먼저 생성한뒤 DAO에 대한 첫 접근시 CREATE TABLE이 수행됨.
        - 주로 빈 DB로 시작하는 닷넷클라이언트 개발시 사용됨.
        - ASP.NET 개발시에도 개발자가 DBA를 겸하면 사용함.
        - 운영중 CREATE TABLE, ADD COLUMN 을 닷넷클래스 코드에 attribute로 붙임

- ASP.NET
    - 닷넷프레임워크 위에서 동작하는 WAS
    - 요청 -> aspx 파일 찾기 -> 컴파일 -> 캐시 -> 응답
    - 최신버전은 .NET Core 5 + ASP.NET 5 로 리눅스에서 설치되며 동작함.
        - MS에서 만들지만 Windows, IIS 종속성은 없음.
- ASP
    - IIS 위에서 동작하는 WAS
    - 예전버전의 WAS (지금은 거의 사용되지 않음)
    - 요청 -> asp 파일 찾기 -> 인터프리터 해석 -> 응답
- ASP.NET 웹컨트롤, post back 기법
    - ASP.NET 에서 사용되는 웹 UI 기술
    - 웹 화면의 전체상태를 시리얼라이즈 해서 화면상의 액션마다 서버로 계속 재송신 하는 방식
    - 웹 화면이 복잡하면 서버가 부하가 과중해짐.
    - 하지만 웹 UI를 event driven 방식으로 개발할 수 있어서 초기 개발 생산성이 높음.
    - 트래픽이 적고 화면이 복잡한 서비스에 적합.
- signalr
    - javascript 웹 어플리케이션이 서버와 양방향 통신을 지원하는 라이브러리
    - 웹소켓, 롱폴링, ServerSendEvent 을 모두 지원하며 클라이언트가 허용하는 통신방식을 자동으로 설정하여 통신함.
    - 서버측 라이브러리는 ASP.NET 에서만 사용가능.
    - 클라측 라이브러리는 javascript 웹어플리케이션, 닷넷어플리케이션, UWP, 안드로이드, 아이폰 등 거의 모든 클라이언트 지원함.
- ADO.NET
    - https://msdn.microsoft.com/ko-kr/library/h43ks021(v=vs.110).aspx
    - ADO.NET은 OLE DB 및 ODBC를 통해 노출되는 데이터 소스, SQL Server 및 XML과 같은 데이터 소스에 대한 일관성 있는 액세스를 제공합니다. 
    - LINQ to SQL, Entity Framework 의 베이스기술
    - ADO.NET DataSet 개체를 통해 DB CRUD 함.
- WinForm
    - https://msdn.microsoft.com/ko-kr/library/dd30h2yb(v=vs.110).aspx
    - http://blog.naver.com/jjoommnn/130033346945
    - http://blog.naver.com/jjoommnn/130033352225
    - WPF 이전의 윈도우 어플리케이션 UI 프레임워크
    - win32 UI컨트롤을 c#으로만 생성/관리 할 수 있게 돕는 정도...
    - gdi, gdi+를 사용함.
- WCF
    - Windows Comunication Framework
    - 닷넷프레임워크용 UI프레임워크에 WPF가 있다면 네트워크 프레임워크는 WCF이다.
    - XML SOAP, REST json API 프로토콜 레이어 지원
    - HTTP, HTTPS, TCP, SSL, 등 네트워크 레이어 지원
    - WCF서버를 띄워 놓으면 xsd 프로토콜 분석해서 자동으로 클라용 proxy 코드를 생성해줌!
    - WCF서버를 사용해야 하기 때문에 국내에선 사용빈도 낮음.
- Azure
- Silverlight
- Mono Framework
- Unity Framework



## C# 코딩 컨벤션
- https://msdn.microsoft.com/ko-kr/library/ff926074.aspx


## 참고링크
- https://msdn.microsoft.com/ko-kr/library/aa288436(v=vs.71).aspx
	- msdn c# 자습서
	- msdn인 만큼 c#을 공부하기 위한 가장 기본적이면서도 충분한 설명과 예제가 있음.
	- 한글화 잘되어 있음.

- https://msdn.microsoft.com/ko-kr/library/ms742119(v=vs.110).aspx
	- msdn wpf
	- 한글화는 잘 되어 있는데 실용적인 학습서가 될지는 잘 모르겠음...

- https://docs.microsoft.com/ko-kr/dotnet/csharp/programming-guide/concepts/linq/getting-started-with-linq
	- msdn linq
	- linq는 .net 에서 아주 중요함.
	- .net에선 배열, 리스트, 맵 등 모든 메모리 컬렉션, DB커넥션 등 모든 데이타는 linq로 관리될수 있고 그게 효율적임.
	- 역시 한글화 잘 되어 있고 내용도 상당히 알참.
	- wpf 기본만 필요하다면 linq to sql 등 DB관련된 내용은 건너뛰어도 무방함.

- http://www.csharpstudy.com/CSharp/CSharp-intro.aspx
	- c# 언어 기본
	- 초보적이면서 짧은 내용으로 술술 넘어가는 내용
	- 은근히 놓치기 쉬운 내용을 잘 수록해 놓아서 꽤 자주 방문하는 사이트.

- http://www.csharpstudy.com/Guide/Coding-guide.aspx
	- c# 코딩 가이드
	- 역시 내용이 알참.

- http://www.csharpstudy.com/Data/Data.aspx
	- c# 데이타
	- json, xml 처리
	- 파일 입출력 처리
	- linq에 대한 짧고 이해하기 쉬운 내용들

- http://www.csharpstudy.com/Tip/Tip-linq-groupby.aspx
	- c# 팁
	- 쉽지만 기본기에 대한 내용들...

- http://www.csharpstudy.com/DS/array.aspx
	- c# 자료구조
	- 리스트, 배열, 맵, 세트, 큐 등 기본이지만 모든 자료구조를 다룸.

- http://www.csharpstudy.com/Threads/task.aspx
	- c# 비동기 처리
	- C# 5.0 await 아주 중요
	- 매우중요한 async/await 비동기처리 뿐 아니라 일반적인 비동기처리에 대해서도 좋은 내용이 많음.
	- 이렇게 쉽게 잘 정리해 놓은 자료는 거의 없음.

- https://wpftutorial.net/Home.html
	- wpf 에 대해 꼼꼼하게 잘 설명한 사이트
	- 짧은 예제 위주로 읽기 쉬움.
	- 양이 좀 많은게 흠.
	- 어느정도 읽다보면 뛰어넘을 만한 내용들이 판단될것.

- http://sysnet.pe.kr/
	- MVP 정성태님의 블로그
	- 좋은 내용의 글이 많고, 관리도 잘 되어 있음.
	- 블로그 답게 두서는 없긴 하지만, 시간날때 차례대로 읽다 보면 어느덧 고수될듯

- http://blog.naver.com/techshare/
	- MVP 정성태님의 옛날 블로그


## 추천도서

- http://www.yes24.com/24/goods/19427525
	- <img src="http://image.yes24.com/momo/TopCate1232/MidCate003/123120694.jpg" style="width:100px;">
	- 시작하세요! C# 6.0 프로그래밍 - 정성태
	- 입문서로 적당한 내용과 목차
	- 특히 최신버전인 c# 6.0 !

- http://www.yes24.com/24/goods/3030590
	- <img src="http://image.yes24.com/momo/TopCate463/MidCate007/46263972.jpg" style="width:100px;">
	- 찰스 페졸드의 WPF
	- 저자 이름이 브랜드인 그런책
	- wpf에 대한 내용도 역시 깊이 있고 알차고 방대하다.
	- 그만큼 입문서로는 부담스럽다.

- http://www.yes24.com/24/goods/4346535
	- <img src="http://image.yes24.com/momo/TopCate95/MidCate06/9458887.jpg" style="width:100px;">
	- 실버 라이트 4 & WP 7 - 조성택, 하승민 공저
	- wpf 한국어 입문서가 적당치 않다면 wpf와 코드가 98% 동일한 silverlight 로 입문해도 문제 없음.
	- 내용이 가벼워서 잘 읽힌다. 그렇지만 은근히 알차다. 입문서로 제격!
	- UI framework 내용은 wpf와 거의 같아서 책에서 발췌한 코드를 wpf 프로젝트에 그대로 사용해도 됨.
	- 책의 내용중 silverlight와 windows phone 에 관한 내용은 그냥 건너뛰어도 됨.(silverlight나 windows phone 은 망해버린 기술이라 배울필요는 없음.)
