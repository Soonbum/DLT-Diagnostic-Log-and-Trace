# DLT 코어 덤프 핸들러

[README.md](../README.md)로 돌아가기

## 개요

시스템에서 프로그램 충돌이 발생하면 코어 덤프 핸들러가 트리거되어 시스템에서 생성된 코어 덤프에서 관련 정보를 추출합니다. 이 추출된 정보를 ECU의 파일 시스템에 코어 덤프 핸들러 파일로 저장합니다. 이러한 파일은 [DLT Filetransfer](dlt_filetransfer.md) 메커니즘을 통해 전송됩니다. 전송된 정보를 결합하고 개발자 툴체인(gdb, Release SW, 기타)에 통합할 수 있습니다.

![alt text](https://github.com/COVESA/dlt-daemon/doc/images/dlt_core_dump_handler.png "DLT CDH")

## 통합

### 코어 덤프 핸들러를 빌드하려면

`-DWITH_DLT_COREDUMPHANDLER=ON -DTARGET_CPU_NAME={i686|x86_64}` 옵션을 cmake에 추가하십시오.

코어 덤프 핸들러 코드는 현재 i686과 x86\_64 아키텍처를 지원합니다.

### 다음 재부팅까지 기본 충돌 핸들러를 대체하는 임시 활성화

*root*(sudo 아님)는 다음을 실행합니다:

`echo "|/usr/local/bin/dlt-cdh %t %p %s %e" > /proc/sys/kernel/core_pattern`

주의: */usr/local/bin*을(를) dlt-cdh가 설치된 경로로 대체합니다. 이는 커널에게 코어 덤프를 dlt-cdh의 표준 입력으로 다음 파라미터와 함께 pipe하도록 지시합니다:

- %t : 덤프 시간
- %p : 덤프된 프로세스의 PID
- %s : 덤프를 유발하는 시그널 개수
- %e : 실행가능한 파일 이름

자세한 것은 `man core`를 보십시오.

### 기본 충돌 핸들러를 대체하는 영구적 활성화

*/usr/lib/sysctl.d/*에서 파일 *50-coredump.conf*는 `make install`에 의해 자동으로 만들어져야 합니다.

안타깝게도 - 적어도 Fedora 시스템에서는 - abrt는 `yum remove abrtd*`를 이용하여 제거해야 합니다.

왜냐하면 그것이 부팅할 때마다 변경사항을 무자비하게 덮어쓰기 때문입니다. `sysctl -p /usr/lib/sysctl.d/50-coredump.conf`를 실행하여 코어 덤프 핸들러가 재부팅 없이 활성화될 수 있습니다.

### dlt-cdh와 함께 사용하기 위한 [DLT Filetransfer](dlt_filetransfer.md) 구성

*/etc/dlt-system.conf*의 "Filetransfer Manager" 섹션에 다음과 같이 설정되어 있는지 확인해 보십시오:

```
...
FiletransferEnable = 1
...
FiletransferDirectory = /var/core
...
```

### 코어 덤프 생성

충돌이 발생하면 커널은 dlt-cdh를 호출하여 코어 덤프를 표준 입력으로 전달합니다. dlt-cdh는 다음 작업을 수행합니다:

- 남은 디스크 공간이 충분한지 확인함
- 만약 존재하지 않는다면 대상 디렉토리들을 생성함:
  - /var/core
  - /var/core\_tmp
  - /tmp/.core\_locks
- /var/core\_tmp를 비움
- 충돌한 프로세스의 /proc fs로부터 주로 컨텍스트 데이터를 텍스트 포맷 형태인 임시 컨텍스트 파일로 가져옴
- 코어 덤프 초기화
- ELF 헤더와 노트를 읽어서 임시 코어 덤프 출력 파일을 작성함
- 컨텍스트 파일과 코어 덤프를 /var/core로 이동시킴
- 충돌을 식별하는 id를 생성함

파일을 /var/core로 이동시킨 후에, [DLT Filetransfer](dlt_filetransfer.md) 메커니즘을 사용하면 연결된 클라이언트로 파일을 전송합니다.

## 작성자

Lutz Helwing <Lutz_Helwing (at) mentor (dot) com>

## 저작권

Copyright (C) 2011 - 2015 BMW AG. License MPL-2.0: Mozilla Public License version 2.0 <http://mozilla.org/MPL/2.0/>.
