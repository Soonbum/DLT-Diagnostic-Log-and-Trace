# Diagnostic Log and Trace Viewer

## 개요

Diagnostic Log and Trace Viewer는 DLT 데몬에 제어 메시지를 주고 받을 수 있는 애플리케이션입니다. 예를 들어, 애플리케이션과 컨텍스트의 개별 로그 레벨을 설정하거나 DLT 데몬에 등록된 애플리케이션과 컨텍스트의 목록을 가져올 수 있습니다. DLT 뷰어는 Windows 및 Linux용 Qt를 기반으로 하며 공식적으로 지원되지는 않지만 Mac에서 실행할 수 있습니다.

## 문서

+ DLT 릴리즈 노트: ReleaseNotes.txt
+ DLT 설치: INSTALL.md

### 커맨드 라인 사용법;

+ Windows: `dlt-viewer.exe [OPTIONS]`
+ Linux: `dlt-viewer [OPTIONS]`
+ MacOS: `open -a $DLT_BUILD_DIR/bin/DLT\ Viewer.app --args [OPTIONS]`

옵션:
+ `-h` Print usage
+ `-p projectfile` Loading project file on startup (must end with .dlp)
+ `-l logfile`     Loading logfile on startup (must end with .dlt)
+ `-f filterfile`  Loading filterfile on startup (must end with .dlf)
+ `-s or --silent`  Enable silent mode without warning message boxes
+ `-v or --version` Only show version and buildtime information
+ `-c logfile textfile`  Convert logfile file to textfile (logfile must end with .dlt)
+ `-u` Conversion will be done in UTF8 instead of ASCII";
+ `-csv` Conversion will be done in CSV format";
+ `-d` Conversion will NOT be done, save in dlt file format again instead"
+ `-dd` Conversion will NOT be done, save as decoded messages in dlt format"
+ `-e "name of plugin|command in plugin|param1|..|param<n>"` Execute a plugin command with <n> parameters

## API 문서

See INSTALL.txt regarding doxygen API documentation generation.

## 소프트웨어/하드웨어

Developed with QT 5 (http://qt-project.org/)
Tested with Windows 10 + Qt 5.12.12 + Microsoft Visual Studio Build Tools 2017
Tested with Ubuntu Linux 18.04 + Qt 5.9.5
Tested with macOS Sierra + Qt 5.7.0
Dynamically linked open source software

Qt 5.x.x (LGPLv3 - Qt)
GCC (GPL)
(Optional) Mingw (GPL + BSD Variante + Public Domain)
Open Icon Library (http://openiconlibrary.sourceforge.net,Multiple Licenses)
License

Full information on the license for this software is available in the "LICENSE.txt" file.

## DLT 뷰어를 위한 소스 코드

https://github.com/COVESA/dlt-viewer 

## 홈페이지

Github 상의 [Diagnostic Log and Trace Homepage](https://github.com/COVESA/dlt-viewer)

## 이슈 및 질문

[Diagnostic Log and Trace Homepage Issues](https://github.com/COVESA/dlt-viewer/issues)

## 추가 플러그인

https://github.com/svlad-90/DLT-Message-Analyzer

## 연락처

Alexander Wenzel Alexander.AW.Wenzel@bmw.de and Gernot Wirschal Gernot.Wirschal@bmw.de
