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
+ `-h` 사용법 출력
+ `-p projectfile` 구동시 프로젝트 파일 불러오기 (.dlp로 끝나야 함)
+ `-l logfile`     구동시 로그 파일 불러오기 (.dlt로 끝나야 함)
+ `-f filterfile`  구동시 필터 파일 불러오기 (.dlf로 끝나야 함)
+ `-s 또는 --silent`  경고 메시지 박스가 없는 사일런스 모드 활성화
+ `-v 또는 --version` 버전 및 빌드 시간 정보만 보여줌
+ `-c logfile textfile`  로그 파일을 텍스트 파일로 변환함 (로그 파일은 .dlt로 끝나야 함)
+ `-u` ASCII 대신 UTF8로 변환하게 됨";
+ `-csv` CSV 포맷으로 변환하게 됨";
+ `-d` 변환하지 않을 것입니다. 대신 dlt 파일 포맷으로 다시 저장함"
+ `-dd` 변환하지 않을 것입니다. dlt 포맷 형태로 디코딩된 메시지를 저장함"
+ `-e "name of plugin|command in plugin|param1|..|param<n>"` n개 파라미터와 함께 플러그인 커맨드를 실행함

## API 문서

doxygen API 문서 생성에 관해서는 INSTALL.txt를 보십시오.

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
