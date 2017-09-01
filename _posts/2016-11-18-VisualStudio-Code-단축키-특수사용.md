---
layout: post
title: 161118 VisualStudio Code 단축키, 특수사용
comments: true
tags:
- .net
- win32
- wpf
- native
- zmq
- ipc
- study
- blog
---


<!-- TOC -->

- [vscode 다운로드](#vscode-다운로드)
- [단축키](#단축키)
- [필수 익스텐션](#필수-익스텐션)
    - [hnw.vscode-auto-open-markdown-preview](#hnwvscode-auto-open-markdown-preview)
    - [HookyQR.beautify](#hookyqrbeautify)
    - [ms-vscode.csharp](#ms-vscodecsharp)
    - [msjsdiag.debugger-for-chrome](#msjsdiagdebugger-for-chrome)
    - [abusaidm.html-snippets](#abusaidmhtml-snippets)
    - [DavidAnson.vscode-markdownlint](#davidansonvscode-markdownlint)
    - [ms-vscode.PowerShell](#ms-vscodepowershell)
- [디버깅 관련 ...](#디버깅-관련-)
- [Command Line Extension Management](#command-line-extension-management)
- [node.js hello world](#nodejs-hello-world)
    - [app.js 구현](#appjs-구현)
    - [.vscode/launch.json 설정](#vscodelaunchjson-설정)
    - [Running Hello World](#running-hello-world)
    - [Debugging Hello World](#debugging-hello-world)
- [api-debugging](#api-debugging)

<!-- /TOC -->



## vscode 다운로드
http://code.visualstudio.com/B?utm_expid=101350005-31.YsqwCVJESWmc4UCMDLsNRw.1


| 키 | 설명 |
|---|---|
| CTRL + ALT + DOWN | 멀티선택 |
| CTRL + B | 사이드바 보기 |
| CTRL + J | 하단패널 보기 |
| CTRL + P | 파일팔레트 보기 |
| CTRL + SHIFT + C | 외부에서 쉘열기 |
| CTRL + SHIFT + E | 파일탐색기 보기 |
| CTRL + SHIFT + F5 | Restart |
| CTRL + SHIFT + G | GIT 보기 |
| CTRL + SHIFT + M | 문제 보기 |
| CTRL + SHIFT + O | 심볼팔레트 보기 |
| CTRL + SHIFT + P | 명령팔레트 보기 |
| CTRL + SHIFT + P, beautify | 파일 인덴트 정리 |
| CTRL + SHIFT + U | 출력 보기 |
| CTRL + SHIFT + V | 미리 보기 |
| CTRL + SHIFT + X | EXTENSION 보기 |
| CTRL + SHIFT + Y | 디버그콘솔 보기 |
| CTRL + SPACE | 자동완성 |
| CTRL + W | 창닫기 |
| F10 | Step Over |
| F11 | 전체화면 |
| F11 | Step Into, Step Out Shift |
| F12 | 정의로 이동 |
| F2 | 이름변경 |
| F5 | Continue / Pause |
| F5 | Stop Shift |
| F8 | markdown 상세오류 보기 |
| SHIFT + ALT + DRAG | 박스선택 |



## 단축키
| 키 | 설명 |
|---|---|
| CTRL + \ | 화면분할 |
| CTRL + ` | 내부에서 쉘열기 |
| CTRL + <\-> | 축소 |
| CTRL + <+> | 확대 |
| CTRL + ALT + DOWN | 멀티선택 |
| CTRL + B | 사이드바 보기 |
| CTRL + J | 하단패널 보기 |
| CTRL + P | 파일팔레트 보기 |
| CTRL + SHIFT + C | 외부에서 쉘열기 |
| CTRL + SHIFT + E | 파일탐색기 보기 |
| CTRL + SHIFT + F5 | Restart |
| CTRL + SHIFT + G | GIT 보기 |
| CTRL + SHIFT + M | 문제 보기 |
| CTRL + SHIFT + O | 심볼팔레트 보기 |
| CTRL + SHIFT + P | 명령팔레트 보기 |
| CTRL + SHIFT + P, beautify | 파일 인덴트 정리 |
| CTRL + SHIFT + U | 출력 보기 |
| CTRL + SHIFT + V | 미리 보기 |
| CTRL + SHIFT + X | EXTENSION 보기 |
| CTRL + SHIFT + Y | 디버그콘솔 보기 |
| CTRL + SPACE | 자동완성 |
| CTRL + W | 창닫기 |
| F10 | Step Over |
| F11 | 전체화면 |
| F11 | Step Into, Step Out Shift |
| F12 | 정의로 이동 |
| F2 | 이름변경 |
| F5 | Continue / Pause |
| F5 | Stop Shift |
| F8 | markdown 상세오류 보기 |
| SHIFT + ALT + DRAG | 박스선택 |


## 필수 익스텐션


### hnw.vscode-auto-open-markdown-preview
```powershell
code --install-extension hnw.vscode-auto-open-markdown-preview
```
- 마크다운 프리뷰를 항상 문서 오른쪽에 표시

### HookyQR.beautify
```powershell
code --install-extension HookyQR.beautify
```
- javascript, JSON, CSS, Sass, and HTML 의 코드를 beautify 해줌

### ms-vscode.csharp
```powershell
code --install-extension ms-vscode.csharp
```
- Lightweight development tools for .NET Core.

### msjsdiag.debugger-for-chrome
```powershell
code --install-extension msjsdiag.debugger-for-chrome
```
- Setting breakpoints, including in source files when source maps are enabled
- Press the play button or F5 to start

### abusaidm.html-snippets
```powershell
code --install-extension abusaidm.html-snippets
```
- rich language support for the HTML Markup
	- Full HTML5 Tags
	- Colorization
	- Snippets
	- [partially implemented] Quick Info
	- description mentions if tag deprecated

### DavidAnson.vscode-markdownlint
```powershell
code --install-extension DavidAnson.vscode-markdownlint
```
- Markdown linting and style checking
	- Ctrl+Shift+M : open the Errors and Warnings dialog
	- F8 : see the warning

### ms-vscode.PowerShell
```powershell
code --install-extension ms-vscode.PowerShell
```
- Syntax highlighting
- Code snippets
- IntelliSense for cmdlets and more
- Rule-based analysis provided by PowerShell Script Analyzer
- Go to Definition of cmdlets and variables
- Find References of cmdlets and variables
- Document and workspace symbol discovery
- Run selected selection of PowerShell code using F8
- Launch online help for the symbol under the cursor using Ctrl+F1
- Local script debugging and basic interactive console support!



## 디버깅 관련 ...
http://code.visualstudio.com/docs/editor/debugging

- Debug actions
	- Continue / Pause F5
	- Step Over F10
	- Step Into F11
	- Step Out Shift+F11
	- Restart Ctrl+Shift+F5
	- Stop Shift+F5

- 앱 디버깅
	- /.vscode/launch.json 파일생성
```
{
	"version": "0.2.0",
	"configurations": [
		{
			"type": "node",
			"request": "launch",
			"name": "Launch Program",
			"program": "${workspaceRoot}/app.js",
			"cwd": "${workspaceRoot}"
		},
		{
			"type": "node",
			"request": "attach",
			"name": "Attach to Process",
			"port": 5858
		}
	]
}
```

- Launch.json attributes
    - type - type of configuration which maps to the registered debug extension (node, php, go)
    - request- debug request, either to launch or attach
    - name- friendly name which appears in the Debug launch configuration dropdown
    - program - executable or file to run when launching the debugger
    - cwd - current working directory for finding dependencies and other files
    - port - port when attaching to a running process
    - stopOnEntry - break immediately when the program launches
    - internalConsoleOptions - control visibility of the Debug Console panel during a debugging session

- Variable substitution
    - ${workspaceRoot} the path of the folder opened in VS Code
    - ${file} the current opened file
    - ${relativeFile} the current opened file relative to workspaceRoot
    - ${fileBasename} the current opened file's basename
    - ${fileDirname} the current opened file's dirname
    - ${fileExtname} the current opened file's extension
    - ${cwd} the task runner's current working directory on startup







## Command Line Extension Management
http://code.visualstudio.com/docs/editor/extension-gallery
```powershell
code --list-extensions
code --install-extension ms-vscode.cpptools
code --uninstall-extension ms-vscode.csharp
```


## node.js hello world
http://code.visualstudio.com/docs/runtimes/nodejs

###  app.js 구현
```javascript
var msg = 'Hello World';
console.log(msg);
```

### .vscode/launch.json 설정
- 프로젝트가 실행될 환경을 설정
- runtimeExecutable 을 node.exe의 실제경로로 설정해 주는것이 중요
```json
{
    // Use IntelliSense to learn about possible Node.js debug attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "프로그램 시작",
            "program": "${workspaceRoot}/app.js",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "C:\\Program Files\\nodejs\\node.exe"
        },
        {
            "type": "node",
            "request": "attach",
            "name": "프로세스에 연결",
            "port": 5858,
            "runtimeExecutable": "C:\\Program Files\\nodejs\\node.exe"
        }
    ]
}
```

### Running Hello World
```powershell
PS> node app.js
```

### Debugging Hello World
1. F9 : 정지점 찍기
2. F5 : 실행



## api-debugging
http://code.visualstudio.com/docs/extensionAPI/api-debugging
- 각 개발언어의 디버거를 플러그인 형태로 개발해서 붙이는 원리를 소개한 글
- 이 저용량의 가벼운 텍스트편집기가 javascript와 c#의 디버깅기능을 전부지원하고, 자동완성, evaluation까지 거의 완벽하게 지원해서 원리가 궁금했었는데ㅎ
- nodejs 가 v8엔진을 사용해서 운영하는 것과 같은원리로 디버거를 만들었네요
- 혹시나 나중에 nodejs 를 깊이 튜닝해야 한다면 혹시 되움될수도 있겠습니다.
