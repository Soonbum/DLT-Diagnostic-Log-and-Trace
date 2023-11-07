# DLT 빌드 옵션

DLT는 매우 구성하기 쉽습니다. 특정 기술이나 구현 중 일부를 선택하고 특정 기능을 켜고 끌 수 있습니다. 이렇게 하면 필요에 맞게 조정하고 빌드 프로세스를 최대한 단순하게 유지할 수 있습니다.

이 옵션을 변경하기 위해, cmake를 사용하여 이 값들을 변경하고 CmakeList.txt 또는 cmake에 대한 커맨드라인을 통해 적절히 바꿀 수 있습니다.

다음을 이용하여 값을 변경하십시오: cmake -D\<Variable\>=\<Value\>

예시는 다음과 같습니다.

```bash
cmake .. -DWITH_SYSTEMD=ON -DWITH_SYSTEMD_JOURNAL=ON -DCMAKE_INSTALL_PREFIX=/usr
```

## 일반 옵션

옵션 | 값 | 코멘트
:--- | :--- | :---
BUILD\_SHARED\_LIBS | ON | Set to OFF to build static libraries
DLT\_IPC                          |"FIFO"          | "UNIX\_SOCKET" 또는 "FIFO"로 설정
WITH\_DLT\_USE\_IPv6              | ON             | IPv6 지원을 위해 ON으로 설정
WITH\_DLT\_EXAMPLES               | ON             | src/examples 바이너리를 빌드하기 위해 ON으로 설정
DLT\_USER                         | covesa         | root로 실행되지 않는 프로세스에 대한 사용자 설정
WITH\_CHECK\_CONFIG\_FILE         | OFF            | CheckIncludeFiles와 CheckFunctionExists의 구성 파일을 생성하기 위해 ON으로 설정
CMAKE\_INSTALL\_PREFIX            | /usr/local
CMAKE\_BUILD\_TYPE                | RelWithDebInfo
WITH\_UDP\_CONNECTION             | OFF            | dlt UDP 멀티캐스트 지원을 활성화하기 위해 ON으로 설정
WITH\_DLT\_DAEMON\_VSOCK\_IPC     | OFF            | 데몬에서 VSOCK 지원을 위해 ON으로 설정
WITH\_DLT\_LIB\_VSOCK\_IPC        | OFF            | libdlt에서 VSOCK 지원을 위해 ON으로 설정 (DLT\_IPC는 libdlt에서 오버라이드됨)
DLT\_VSOCK\_PORT                  | 13490          | VSOCK 통신에 사용할 포트
WITH\_LEGACY\_INCLUDE\_PATH       | ON             | CMake 구성 파일을 위한 경로들을 포함시키기 위해 <prefix>뿐만 아니라 <prefix>/dlt를 추가하고자 ON으로 설정
WITH\_DLT\_LOG\_LEVEL\_APP\_CONFIG | OFF           | 애플리케이션 id들을 기반으로 기본 로그 레벨을 활성화하기 위해 ON으로 설정

## 커맨드 라인 도구 옵션

 옵션 | 값 | 코멘트
 :--- | :--- | :---
WITH\_DLT\_ADAPTOR                | OFF            | src/adaptor 바이너리를 빌드하기 위해 ON으로 설정
WITH\_DLT\_CONSOLE                | ON             | src/console 바이너리를 빌드하기 위해 ON으로 설정
WITH\_DLT\_SYSTEM                 | OFF            | src/system 바이너리를 빌드하기 위해 ON으로 설정
WITH\_DLT\_LOGSTORAGE\_CTRL\_UDEV | OFF            | 프로토타입! 빌드하기 위해 ON으로 설정
WITH\_DLT\_KPI                    | OFF            | src/kpi 바이너리를 빌드하기 위해 ON으로 설정
WITH\_EXTENDED\_FILTERING         | OFF            | 확장 필터링 없이 빌드하기 위해 OFF로 설정. json 필터 파일 사용은 json-c 및 QNX가 있는 Linux 기반 시스템에서만 지원됨

## 리눅스 OS 통합 옵션

 옵션 | 값 | 코멘트
 :--- | :--- | :---
WITH\_SYSTEMD                     | OFF            | systemd에서 CMakeLists.txst를 실행하기 위해 ON으로 설정
WITH\_SYSTEMD\_WATCHDOG           | OFF            | dlt-daemon에서 systemd watchdog를 사용하기 위해 ON으로 설정
WITH\_SYSTEMD\_WATCHDOG\_ENFORCE\_MSG\_RX | OFF    | 마지막으로 통지한 이후 dlt-daemon에서 수신된 새 메시지가 있는 경우에만 watchdog에게 통지하도록 ON으로 설정
WITH\_SYSTEMD\_WATCHDOG\_ENFORCE\_MSG\_RX\_DLT\_SYSTEM | OFF    | 마지막으로 통지한 이후 dlt-system에서 수신된 새 메시지가 있는 경우에만 watchdog에게 통지하도록 ON으로 설정
WITH\_SYSTEMD\_JOURNAL            | OFF            | dlt-system에서 systemd journal을 사용하기 위해 ON으로 설정
WITH\_DLT\_DBUS                   | OFF            | src/dbus 바이너리를 빌드하기 위해 ON으로 설정

## QNX OS 통합 옵션

옵션 | 값 | 코멘트
:--- | :--- | :---
WITH\_DLT\_QNX\_SYSTEM            | OFF            | QNX 시스템 바이너리 dlt-qnx-system를 빌드하기 위해 ON으로 설정
DLT\_QNX\_SLOG\_ADAPTER\_WAIT\_BUFFER\_TIMEOUT\_MS | 100 | slog의 메시지가 삭제되기 전에 dlt의 버퍼 공간을 기다리는 최대 시간(밀리초)

## 문서 옵션

옵션 | 값 | 코멘트
 :--- | :--- | :---
WITH\_DOC                         | OFF            | API 문서를 빌드하기 위해 ON으로 설정
WITH\_MAN                         | OFF            | man pages를 빌드하기 위해 ON으로 설정

## 테스트 옵션

옵션 | 값 | 코멘트
:--- | :--- | :---
WITH\_TESTSCRIPTS                 | OFF            | 테스트 스크립트에서 CMakeLists.txt를 실행하기 위해 ON으로 설정
WITH\_DLT\_TESTS                  | ON             | src/test 바이너리를 빌드하기 위해 ON으로 설정
WITH\_DLTTEST                     | OFF            | 손상된 메시지가 있는 User-Daemon 통신을 테스트하기 위해 수정사항을 포함하여 빌드하려면 ON으로 설정
WITH\_DLT\_UNIT\_TESTS            | OFF            | 유닛 테스트 바이너리를 빌드하기 위해 ON으로 설정
WITH\_GPROF                       | OFF            | flag를 컴파일하려면 \-pg 설정

## 실험 기능 옵션 (Dragons ahead!)

옵션 | 값 | 코멘트
:--- | :--- | :---
WITH\_DLT\_SHM\_ENABLE            | OFF            | IPC로 공유 메모리를 활성화하려면 ON으로 설정
WITH\_DLT\_CXX11\_EXT             | OFF            | C++11 확장을 빌드하기 위해 ON으로 설정
WITH\_DLT\_COREDUMPHANDLER        | OFF            | src/core\_dump\_handler 바이너리를 빌드하기 위해 ON으로 설정.
