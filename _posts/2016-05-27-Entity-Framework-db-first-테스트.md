---
layout: post
title: 160527 Entity Framework db first 테스트
comments: true
tags:
- .net
- c#
- ef
- entityframework
- db
- database
- mssql
- sqlserver
---


<!-- TOC -->

- [개요](#개요)
- [관련링크](#관련링크)
- [샘플 프로젝트 다운로드](#샘플-프로젝트-다운로드)
- [클라이언트 프로젝트 생성 (zoro base C# console application)](#클라이언트-프로젝트-생성-zoro-base-c-console-application)
- [DB, 테이블 생성 (CREATE DATABASE, CREATE TABLE)](#db-테이블-생성-create-database-create-table)
- [EF 참조추가](#ef-참조추가)
- [EDMX 파일 생성 (Entity Data Model)](#edmx-파일-생성-entity-data-model)
- [클라이언트 코딩 (SELECT, INSERT, DELETE, UPDATE)](#클라이언트-코딩-select-insert-delete-update)

<!-- /TOC -->



<br/>
<br/>
<br/>

## 개요

- entity framework의 가장 실용적인 샘플은 무엇일까?
    - EF DB first
- EF DB first 상세내용
    - DB, 테이블 생성은 sql을 직접 사용해서 만들고,
    - ADO.NET entities (=edmx파일) 자동생성 기능으로 Context 클래스 자동생성하여,
    - SELECT, UPDATE, INSERT, DELETE 해결!
- 아래는 EF DB first 의 zero base에서의 샘플 
    - 로컬 SQL Server에서 DB, 테이블 생성
    - 콘솔어플리케이션 클라이언트로 구현방식 기술

<br/>
<br/>
<br/>

## 관련링크

https://www.asp.net/mvc/overview/getting-started/database-first-development/setting-up-database
https://www.asp.net/mvc/overview/getting-started/database-first-development/creating-the-web-application
https://www.asp.net/mvc/overview/getting-started/database-first-development/changing-the-database

<br/>
<br/>
<br/>

## 샘플 프로젝트 다운로드 

> https://github.com/HyundongHwang/MyEfDbFirstTest

<br/>
<br/>
<br/>

## 클라이언트 프로젝트 생성 (zoro base C# console application)
> zoro base C# console application 생성

<br/>
<br/>
<br/>

## DB, 테이블 생성 (CREATE DATABASE, CREATE TABLE)
- sql 파일 생성후 프로젝트에 추가
    - 이 파일에서 CREATE DATABASE, CREATE TABLE를 할것임.
- sql 코딩을 하기전에 SQL Server에 연결
    - SQL Server를 외부서버나 Azure서버등 외부 SQL Server에 연결할 수 있지만 여기서는 로컬 SQL Server 인스턴스를 선택함.
    - ![Alt text](./1465363803745.png)
- 이렇게 선택된 SQL Server위에 DB, 테이블을 생성함.
    - sql 파일에 직접 sql 코딩을 함.
    - 실제로 실행함...
    
<create-tables.sql>
```sql
SELECT @@VERSION
SELECT @@SERVERNAME
EXEC sp_databases

USE tempdb

IF (EXISTS(SELECT name 
FROM master.dbo.sysdatabases
WHERE name = 'mydb'))
    BEGIN
    PRINT 'mydb is existed!'
    DROP DATABASE mydb
    END
ELSE
    PRINT 'mydb is not existed!'

CREATE DATABASE mydb
USE mydb
SELECT DB_NAME()

CREATE TABLE [dbo].[Course] (
    [CourseID]  INT           IDENTITY (1, 1) NOT NULL,
    [Title]     NVARCHAR (50) NULL,
    [Credits]   INT           NULL,
    [Credits2]  INT           NULL,
    PRIMARY KEY CLUSTERED ([CourseID] ASC)
)

CREATE TABLE [dbo].[Student] (
    [StudentID]      INT           IDENTITY (1, 1) NOT NULL,
    [LastName]       NVARCHAR (50) NULL,
    [FirstName]      NVARCHAR (50) NULL,
    [EnrollmentDate] DATETIME      NULL,
    PRIMARY KEY CLUSTERED ([StudentID] ASC)
)

SELECT * FROM information_schema.tables
```

<br/>
<br/>
<br/>

## EF 참조추가
```powershell
PM> install-package EntityFramework
```

<br/>
<br/>
<br/>

## EDMX 파일 생성 (Entity Data Model)
- 새아이템 추가 > Entity Data Model 
    - ![Alt text](./1465365961870.png)
- EF designer from DB 선택
    - ![Alt text](./1465366009366.png)
- 서버이름, DB이름을 지정
    - 서버이름은 정확하게 `localhost\myinst` 처럼 타이핑해서 입력
    - db이름은 자동조회 되므로 드롭다운해서 `mydb` 를 선택
    - connection string은 app.config에 입력하지 않을건데 나중에 하드코딩해 줄거임.
    - ![Alt text](./1465366180421.png)
    - ![Alt text](./1465366186772.png)
- connection string 을 작성
    - SQL 서버 탐색기에서 복사해도 되지만
    - 그냥 간단하게 아래 자동생성된 컨텍스트 코드에서 Data Source, Initial Catalog 부분만 변경해도 된다.
```c#
public partial class Entities : DbContext
{
    public Entities()
        : base(
        @"
        Data Source=HHDX1CARBON\MYINST;
        Initial Catalog=mydb;
        Integrated Security=True;
        Connect Timeout=15;
        Encrypt=False;
        TrustServerCertificate=True;
        ApplicationIntent=ReadWrite;
        MultiSubnetFailover=False
        ")
    {
    }
```

<br/>
<br/>
<br/>

## 클라이언트 코딩 (SELECT, INSERT, DELETE, UPDATE)

> - 자동 생성된 Context 클래스인 Entities를 이용하여 Linq를 사용하여 SELECT, INSERT, DELETE, UPDATE 를 수행할 수 있다.

```c#
class Program
{
    static void Main(string[] args)
    {
        using (var context = new Entities())
        {
            context.Database.Log += (log) =>
            {
                Console.WriteLine($"log : {log}");
            };

            context.Student.Add(new Student
            {
                LastName = "황",
                FirstName = "현동",
                EnrollmentDate = DateTime.Now
            });

            context.SaveChanges();

            var list = context.Student.ToList();
        }
    }
}
```

<br/>
<br/>
<br/>