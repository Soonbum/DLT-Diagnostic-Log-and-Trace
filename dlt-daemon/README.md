# Diagnostic Log and Trace


[![Build Status](https://github.com/COVESA/dlt-daemon/actions/workflows/cmake-ctest.yml/badge.svg)]( https://github.com/COVESA/dlt-daemon/actions/workflows/cmake-ctest.yml)
[![CodeQL](https://github.com/COVESA/dlt-daemon/actions/workflows/codeql-analysis.yml/badge.svg?branch=master)](https://github.com/COVESA/dlt-daemon/actions/workflows/codeql-analysis.yml)
[![Page-build-deployment](https://github.com/COVESA/dlt-daemon/actions/workflows/pages/pages-build-deployment/badge.svg)](https://github.com/COVESA/dlt-daemon/actions/workflows/pages/pages-build-deployment)

**Code coverage reports online** 📄 [LCOV - code coverage report](https://COVESA.github.io/dlt-daemon/dlt_lcov_report/index.html)

# Diagnostic Log and Trace

COVESA Diagnostic Log and Trace (DLT)를 보러 오신 것을 환영합니다. 만약 DLT가 익숙하고 새로 바뀐 것이 궁금하다면 [릴리스 노트](ReleaseNotes.md)를 확인하십시오.

**DLT는 처음입니까? 좋아요! 잘 오셨습니다.** 당신을 위한 간단한 [개요](#개요)와 즉시 [시작](#시작하기)하는 방법에 대한 정보를 준비했습니다.
DLT에 대한 기본 메커니즘에 익숙해진 후에는 고급 개념과 기능에 대해 [더 배울](#더-배우기) 수 있습니다.

## 개요

COVESA DLT는 [AUTOSAR standard 4 DLT](https://www.autosar.org/fileadmin/standards/R22-11/CP/AUTOSAR_SWS_DiagnosticLogAndTrace.pdf)에 명시된 표준화된 프로토콜 기반의 로그 및 추적 인터페이스를 제공합니다.
다른 COVESA 컴포넌트에서 사용되지만 COVESA와 관련 없는 기타 애플리케이션을 위한 로깅 프레임워크 역할도 할 수 있습니다.

가장 중요한 용어 및 파트는 아래 그림에 설명되어 있습니다. DLT 관련 용어에 대한 전체 개요는 [용어집](doc/dlt_glossary.md)을 참조하십시오.

![alt text](https://github.com/COVESA/dlt-daemon/blob/master/doc/images/dlt_overview.png)

- **DLT User**는 본질적으로 (DLT와 관련되지 않은) 각각의 목적을 달성하고 DLT 로그 메시지를 생성하는 어플리케이션으로, DLT 라이브러리를 활용하여 이러한 메시지를 제작하고 전송합니다.
- **DLT Library**는 DLT 사용자(예. 애플리케이션)에게 편리한 API를 제공하여 DLT 로그 메시지를 생성하고 DLT 데몬에게 전달할 수 있게 해줍니다. 후자가 불가능한 경우, 라이브러리는 메시지가 즉시 손실되지 않도록 링 버퍼에 캐시합니다.
- **DLT Daemon**은 ECU의 DLT 통신 인터페이스입니다. ECU 상에서 동작하는 하나 이상의 DLT 사용자로부터 로그 메시지를 수집 및 버퍼링하여 요청 시 DLT 클라이언트에게 제공하고, 또한 데몬은 클라이언트로부터의 제어 메시지를 받아 데몬 또는 애플리케이션의 동작을 조정합니다.
- **DLT Client**는 DLT 데몬으로부터 로그 메시지를 가져와 DLT 사용자로부터 수신 및 소비합니다. 또한 DLT 데몬 또는 연결된 DLT 사용자의 동작을 제어하기 위해 제어 메시지를 발행할 수도 있습니다. DLT 클라이언트는 사용자 정의 데이터를 소위 주입(injection) 메시지를 통해 DLT 사용자에게 전송할 수도 있습니다.

이는 [시작하기](#시작하기) 섹션에서 계속해서 살펴볼 모든 사용 사례 중 가장 간단한 것에 불과합니다. [더 배우기](#더-배우기)를 원하신다면 여러 어댑터와 콘솔 유틸리티 및 테스트 애플리케이션을 활용하는 고급 기능이 포함되어 있는 리포지토리를 찾아 보십시오.

## 시작하기
이 섹션에서는 DLT [빌드 및 설치](#빌드-및-설치) 방법을 배울 수 있습니다.
그리고 나서 [DLT 데모 실행](#DLT-데모-실행) 설정 또는 [나만의 DLT 기능이 포함된 애플리케이션 개발하기](#나만의-DLT-기능이-포함된-애플리케이션-개발하기)를 시작할 수 있습니다.

### 빌드 및 설치

DLT 데몬을 빌드 및 설치하려면 다음 패키지들을 설치해야 합니다:

- cmake
- zlib
- dbus
- json-c (dlt-receives 확장 필터링에만 필요함)

Ubuntu의 경우, 다음 커맨드를 이용하여 위의 종속성을 설치할 수 있습니다:

```bash
sudo apt-get install cmake zlib1g-dev libdbus-glib-1-dev build-essential
선택사항: sudo apt-get install libjson-c-dev # dlt-receives 확장 필터링을 사용하고 싶을 경우
```

그 다음에 아직 DLT를 다운로드하지 않았다면 다운로드를 진행하십시오. 리포지토리를 복제하는 것을 추천하지만 zip-archive를 다운로드하여 추출하는 것도 좋습니다.
```bash
cd /path/to/workspace
git clone https://github.com/COVESA/dlt-daemon.git
```

DLT 데몬을 빌드 및 설치하려면 다음 단계를 따르십시오:

```bash
cd /path/to/workspace/dlt-daemon
mkdir build
cd build
cmake ..
make
선택사항: sudo make install
선택사항: sudo ldconfig # make install을 실행한 경우
```
CMake는 당신의 필요에 맞게 빌드를 구성하기 위한 수많은 [빌드 옵션](doc/dlt_build_options.md)을 허용합니다.

### DLT 데모 실행
만약 간략한 [개요](#개요)를 보지 않았다면, 지금이야말로 가장 중요한 용어에 대해 알아보고, 데이터가 버퍼링되는 위치에 대한 아이디어를 얻을 수 있는 완벽한 기회가 될 것입니다. 그 다음에 [DLT 데모 설정 방법](doc/dlt_demo_setup.md)에 대한 가이드를 진행할 것입니다.

### 나만의 DLT 기능이 포함된 애플리케이션 개발하기

DLT가 실행되고 있는 것을 보았으므로, DLT를 사용하여 자신만의 애플리케이션을 개발하고 싶을 것입니다. ["애플리케이션 개발자를 위한 DLT" 가이드](doc/dlt_for_developers.md)에서 필요한 모든 것을 찾을 수 있습니다.

힌트: API 문서를 읽고 싶으면 현재 로컬에서 빌드해야 합니다. API 문서는 _doxygen_로 생성됩니다. 빌드하려면 cmake에 옵션 ```-DWITH_DOC=ON```을 붙여서 실행하십시오. 다음은 예시입니다:

```bash
mkdir build
cd build
cmake -DWITH_DOC=ON ..
make doc
```

### DLT 데비안 패키지 빌드하기

당신만의 목적을 위해 DLT 데비안 패키지를 빌드하려면 다음 단계를 따르십시오:

```bash
dpkg-buildpackage -us -uc
```

## 더 배우기
Once you got your feet wet with developing your first application, you might
want to learn more. Find out about DLT's [advanced topics](#advanced-topics),
learn how to [configure, control and interface](#configure-control-and-interface)
DLT or study its internals by checking out the [design
specifications](./doc/dlt_design_specification.md).

### Advanced Topics
The COVESA DLT implementation is capable of by far more than to "just" send log
message. You will get an overview of advanced features in this section. Follow
the links to learn more about the respective concept.

| Document | Description |
|----|----|
| [Build Options](./doc/dlt_build_options.md) | The CMake build system provides a large amount of build options. They let you turn on or off certain features and provide alternative implementation details. |
| [LogStorage](doc/dlt_offline_logstorage.md) | The DLT Daemon as well as the DLT libary provide buffers for caching log data during absence of a consumer. However, some use cases require to write large amounts of log message e.g. to mass storages for long term storage or because no other means of exfiltrating the log data is available. |
| [MultiNode](doc/dlt_multinode.md) | A DLT Daemon can run as a gateway to connect multiple passive nodes. Each passive node has its owns DLT Applications and runs its own daemon. The gateway node connects to all of them, collects the logs and routes them to the DLT Client. |
| [Extended Network Trace](doc/dlt_extended_network_trace.md) | Normal DLT messages are limited in size. To overcome this limitation the feature network trace message allows the user to send or truncate messages which would not fit into a normal DLT message. |
| [DLT Filetransfer](doc/dlt_filetransfer.md) | Although not originally designed for this, files can be transmitted over DLT. A corresponding DLT Client (e.g. DLT Viewer) can receive and decode them accordingly. |
| [DLT KPI](doc/dlt_kpi.md) | Valueable status information about the monitored system can be read via DLT as well. The information under `/proc` of the target system is at your hands easily. |
| [DLT Core Dump Handler](/doc/dlt_cdh.md) | This tool collects and extracts debug information then utilize [DLT Filetransfer](doc/dlt_filetransfer.md) to transfer the information to client. |

### Configure, Control and Interface

There is still lots to discover about DLT. If you turn on the generation of
manpages with the cmake option ```-DWITH_MAN=ON``` you can learn how to
configure DLT to exactly suit your needs, how to control the behvaiour of
running instances and how to interface DLT with existing system through
provided adaptors.

The man pages are generated with *pandoc*, which also needs *asciidoc* as dependency.

Build manpages (initally or because something changed) with e.g.
```bash
mkdir build
cd build
cmake -DWITH_MAN=ON ..
make generate_man
```

| Document | Description |
|----|----|
| *Configuration* ||
|[dlt-daemon(1)](doc/dlt-daemon.1.md) | How to start DLT-Daemon |
|[dlt.conf(5)](doc/dlt.conf.5.md) | Configure the DLT framework to reflect your use case|
| *Control running instances of DLT*||
|[dlt-receive(1)](doc/dlt-receive.1.md)| Receive DLT messages from daemon and print or store the log messages. |
|[dlt-control(1)](doc/dlt-control.1.md)| Send control messages to daemon. |
|[dlt-logstorage-ctrl(1)](doc/dlt-logstorage-ctrl.1.md)| Send a trigger to daemon to connect/disconnect certain logstorage device, or send a demand to sync data the internal buffer into logstorage file. |
|[dlt-passive-node-ctrl(1)](doc/dlt-passive-node-ctrl.1.md)| Send a trigger to daemon to connect/disconnect passive daemon. |
| *Interfacing DLT* ||
|[dlt-system(1)](doc/dlt-system.1.md) | DLT-System provides a way to directly access system logs via DLT |
|[dlt-system.conf(5)](doc/dlt-system.conf.5.md) | Configure DLT-System |
|[dlt-adaptor-stdin(1)](doc/dlt-adaptor-stdin.1.md)| Adaptor for forwarding input from stdin to daemon. |
|[dlt-adaptor-udp(1)](doc/dlt-adaptor-udp.1.md)| Adaptor for forwarding received UDP messages to daemon. |
|[dlt-convert(1)](doc/dlt-convert.1.md)| Convert DLT files into human readable format. |
|[dlt-sortbytimestamp(1)](doc/dlt-sortbytimestamp.1.md)| Read log messages from DLT file, sort by timestamp, and store them again. |
|[dlt-qnx-system(1)](doc/dlt-qnx-system.md) | Access system logs in QNX with DLT |

## Contribution

Start working, best practice is to commit smaller, compilable pieces during the
work that makes it easier to handle later on.

If you want to commit your changes, create a
[Pull Request](https://github.com/covesa/dlt-daemon/pulls) in Github. Please
make sure to follow the
[Rules for commit messages](https://at.projects.covesa.org/wiki/display/PROJ/Rules+for+Commit+Messages)

### Coding Rules

This project is now using clang-format as replacement of uncrustify.

For convenience, any code changes will be harmonized before commit by hooks/pre-commit.

- Install clang-format

- Install pre-commit script by:

  ```bash
  cp scripts/pre-commit.sample .git/hooks/pre-commit
  ```

- Configuration: .clang-format

For reference to clang-format, you can check with:
[Configurator](https://zed0.co.uk/clang-format-configurator/)

## Known issues

List of open issues can be found on
[Github](https://github.com/COVESA/dlt-daemon/issues)

- DLT library: Usage of dlt\_user\_log\_write\_float64() and DLT\_FLOAT64()
  leads to "Illegal instruction (core dumped)" on ARM target.
- DLT library: Nested calls to DLT\_LOG\_ ... are not supported, and will lead
  to a deadlock.
- For Non linux platforms [eg: QNX] IPC supported is UNIX\_SOCKET. For Linux
  Platforms both IPC FIFO and UNIX\_SOCKET are supported

## Software/Hardware

Developed and tested with Ubuntu Linux 16 64-bit / Intel PC

## License

Full information on the license for this software is available in the "LICENSE"
file.
Full information on the license for the cityhash code is available in "COPYING"
file in src/core\_dump\_handler/cityhash\_c.


## Contact

Methner, Michael <mmethner@de.adit-jv.com>,
Le Van, Khanh <Khanh.LeVan@vn.bosch.com>

![alt text](doc/images/covesa-logo.png "COVESA logo")
