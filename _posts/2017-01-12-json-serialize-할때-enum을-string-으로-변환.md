---
layout: post
title: 170112 json serialize 할때 enum을 string 으로 변환
comments: true
tags:
- .net
- json.net
- study
- StringEnumConverter
- 꿀팁
---

<!-- TOC -->


<!-- /TOC -->
<br>
<br>
<br>

- 이게 필요해서 만들까 했는데 역시 필요한건 미리 누군가가 만들어 놓았음.
- StringEnumConverter 가 이미 만들어져 있음.
- 바로 코드부터 보면...

```c#
[TestClass]
public class SomeTests
{
    public enum Genders
    {
        Male,
        Female,
    }

    public class Person
    {
        public int Age { get; set; }
        public string Name { get; set; }

        public Genders Gender { get; set; }

        [JsonConverter(typeof(StringEnumConverter))]
        public Genders Gender2 { get; set; }
    }
    
    [TestMethod]
    public async void StringEnumConverter_test()
    {
        var p = new Person
        {
            Name = "황현동",
            Age = 37,
            Gender = Genders.Male,
            Gender2 = Genders.Male,
        };

        var pStr = JsonConvert.SerializeObject(p, Formatting.Indented);
        Trace.TraceInformation($"pStr : {pStr}");
    }
```

```tex
vstest.executionengine.x86.exe Information: 0 : pStr : {
  "Age": 37,
  "Name": "황현동",
  "Gender": 0,
  "Gender2": "Male"
}
```