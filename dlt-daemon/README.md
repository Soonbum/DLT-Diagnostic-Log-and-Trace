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
1번째 애플리케이션에 개발에 몰두한 후에는 더 많은 것을 배우고 싶을 것입니다.
DLT의 [고급 주제](#고급-주제)에 대해 알아보고, DLT의 [구성, 제어 및 인터페이스](#구성,-제어-및-인터페이스)에 대해 배우거나 [설계 사양](./doc/dlt_design_specification.md)을 확인하여 내부를 연구해 보십시오.

### 고급 주제
COVESA DLT 구현은 로그 메시지를 "그저" 보낼 수 있는 것 이상의 능력을 갖추고 있습니다. 이 섹션에서는 고급 기능에 대한 개요를 볼 수 있습니다. 링크를 따라 각 개념에 대해 자세히 알아보십시오.

| 문서 | 설명 |
|----|----|
| [Build Options](./doc/dlt_build_options.md) | CMake 빌드 시스템은 대량의 빌드 옵션을 제공합니다. 특정 기능을 설정하거나 해제할 수 있으며 대체 구현 세부 정보를 제공합니다. |
| [LogStorage](doc/dlt_offline_logstorage.md) | DLT 데몬과 DLT 라이브러리는 소비자가 없을 때 로그 데이터를 캐싱하기 위한 버퍼를 제공합니다. 그러나 일부 사용 사례에서는 장기 저장을 위한 대량 저장, 또는 로그 데이터를 빼낼 수 있는 다른 방법이 없기 때문에 대량의 로그 메시지를 작성해야 합니다. |
| [MultiNode](doc/dlt_multinode.md) | DLT 데몬은 여러 패시브 노드를 연결하기 위한 게이트웨이로 실행될 수 있습니다. 각 패시브 노드는 고유의 DLT 애플리케이션을 가지고 있으며 고유의 데몬을 실행합니다. 게이트웨이 노드는 이들 모두에 연결되어 로그를 수집하여 DLT 클라이언트로 라우팅합니다. |
| [Extended Network Trace](doc/dlt_extended_network_trace.md) | 일반 DLT 메시지는 크기가 제한되어 있습니다. 이 제한을 극복하기 위해 피쳐 네트워크 트레이스 메시지를 사용하면, 사용자는 일반 DLT 메시지에 맞지 않는 메시지를 보내거나 잘라낼 수 있습니다. |
| [DLT Filetransfer](doc/dlt_filetransfer.md) | 원래 이를 위해 설계되지는 않았지만 DLT를 통해 파일을 전송할 수 있습니다. 해당 DLT 클라이언트(예: DLT 뷰어)는 그에 따라 파일을 수신하고 디코딩할 수 있습니다. |
| [DLT KPI](doc/dlt_kpi.md) | 모니터링되는 시스템에 대한 가치 있는 상태 정보는 DLT를 통해서도 읽을 수 있습니다. 대상 시스템의 '/proc' 아래의 정보를 쉽게 손에 넣을 수 있습니다. |
| [DLT Core Dump Handler](doc/dlt_cdh.md) | 이 도구는 디버그 정보를 수집하고 추출한 다음 [DLT Filetransfer](doc/dlt_filetransfer.md)를 사용하여 클라이언트에 정보를 전송합니다. |

### 구성, 제어 및 인터페이스

DLT에 대해서는 아직도 발견해야 할 것이 많습니다. cmake 옵션인 ```-DWITH_MAN=ON```으로 manpages 생성을 설정하면, DLT를 사용자의 요구에 정확히 맞게 구성하는 방법과 실행 중인 인스턴스의 동작을 제어하는 방법, 그리고 제공된 어댑터를 통해 DLT를 기존 시스템과 인터페이싱하는 방법을 배울 수 있을 것입니다.

*pandoc*으로 man pages를 생성할 때 *asciidoc* 역시 종속적으로 필요합니다.

(처음, 혹은 뭔가가 바뀌었을 경우) 예제를 이용하여 manpages를 빌드하십시오.
```bash
mkdir build
cd build
cmake -DWITH_MAN=ON ..
make generate_man
```

| 문서 | 설명 |
|----|----|
| *구성* ||
|[dlt-daemon(1)](doc/dlt-daemon.1.md) | DLT-Daemon을 시작하는 방법 |
|[dlt.conf(5)](doc/dlt.conf.5.md) | 사용 사례를 반영하기 위한 DLT 프레임워크 구성하기 |
| *실행 중인 DLT 인스턴스 제어* ||
|[dlt-receive(1)](doc/dlt-receive.1.md)| 데몬으로부터 DLT 메시지를 수신하고 로그 메시지를 출력하거나 저장합니다. |
|[dlt-control(1)](doc/dlt-control.1.md)| 데몬에게 제어 메시지를 전송합니다. |
|[dlt-logstorage-ctrl(1)](doc/dlt-logstorage-ctrl.1.md)| 특정 로그 저장 장치를 연결/연결 해제하기 위해 트리거를 데몬에 보내거나, 내부 버퍼 데이터를 로그 저장 파일에 동기화하기 위한 요구를 보냅니다. |
|[dlt-passive-node-ctrl(1)](doc/dlt-passive-node-ctrl.1.md)| 패시브 데몬을 연결/연결 해제하기 위해 데몬에 트리거를 보냅니다. |
| *DLT 인터페이싱* ||
|[dlt-system(1)](doc/dlt-system.1.md) | DLT-시스템은 DLT를 통해 시스템 로그에 직접 액세스할 수 있는 방법을 제공합니다. |
|[dlt-system.conf(5)](doc/dlt-system.conf.5.md) | DLT-시스템 구성하기 |
|[dlt-adaptor-stdin(1)](doc/dlt-adaptor-stdin.1.md)| stdin에서 데몬으로 입력을 전달하기 위한 어댑터 |
|[dlt-adaptor-udp(1)](doc/dlt-adaptor-udp.1.md)| 수신된 UDP 메시지를 데몬으로 전달하기 위한 어댑터 |
|[dlt-convert(1)](doc/dlt-convert.1.md)| DLT 파일을 사람이 읽을 수 있는 포맷으로 변환합니다. |
|[dlt-sortbytimestamp(1)](doc/dlt-sortbytimestamp.1.md)| DLT 파일에서 로그 메시지를 읽고, 타임스탬프 기준으로 정렬하고, 다시 저장합니다. |
|[dlt-qnx-system(1)](doc/dlt-qnx-system.md) | DLT를 사용하여 QNX에서 시스템 로그에 접근합니다. |

## 기여

작업을 시작합니다. 작업 중에는 나중에 처리하기 쉽도록 더 작고 컴파일 가능한 조각을 커밋(commit)하는 것이 최선의 방법입니다.

만약 당신의 변경사항을 커밋(commit)하고 싶으면 Github에 [Pull Request](https://github.com/covesa/dlt-daemon/pulls)를 생성하십시오. 반드시 [커밋 메시지에 대한 규칙](https://at.projects.covesa.org/wiki/display/PROJ/Rules+for+Commit+Messages)을 따르십시오.

### 코딩 규칙

이 프로젝트는 현재 uncrustify를 대체하기 위해 clang-format을 사용하고 있습니다.

편의상 모든 모드 변경사항은 hooks/pre-commit에 의해 commit 전에 조화가 이뤄질 것입니다.

- clang-format 설치하기

- 다음과 같이 pre-commit 스크립트 설치하기:

  ```bash
  cp scripts/pre-commit.sample .git/hooks/pre-commit
  ```

- 구성: .clang-format

clang-format에 대한 참조에 대해서는, 다음을 이용하여 확인할 수 있습니다:
[Configurator](https://zed0.co.uk/clang-format-configurator/)

## 알려진 이슈

알려진 이슈 목록은 다음에서 찾아볼 수 있습니다: [Github](https://github.com/COVESA/dlt-daemon/issues)

- DLT 라이브러리: dlt\_user\_log\_write\_float64()와 DLT\_FLOAT64()를 사용하면 ARM 대상인 경우 "불법 명렁어 (코어 덤프)"를 발생시킵니다.
- DLT 라이브러리: DLT\_LOG\_ ...에 대한 중첩된 호출을 지원하지 않습니다. 그리고 데드락을 야기하게 될 것입니다.
- 비-Linux 플랫폼의 경우 [예: QNX] 지원되는 IPC는 UNIX\_SOCKET입니다. Linux 플랫폼의 경우 IPC FIFO와 UNIX\_SOCKET 둘 다 지원됩니다.

## 소프트웨어/하드웨어

Ubuntu Linux 16 64-bit / Intel PC에서 개발하고 테스트하였습니다.

## 라이선스

이 소프트웨어에 대한 라이선스의 전체 정보는 "LICENSE" 파일에 있습니다.
cityhash 코드에 대한 라이선스의 전체 정보는 src/core\_dump\_handler/cityhash\_c에 있는 "COPYING" 파일에 있습니다.


## 연락처

Methner, Michael <mmethner@de.adit-jv.com>,
Le Van, Khanh <Khanh.LeVan@vn.bosch.com>

![alt text](https://github.com/COVESA/dlt-daemon/blob/master/doc/images/covesa-logo.png "COVESA logo")
