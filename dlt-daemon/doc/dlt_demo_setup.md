# DLT 데모 설정
[README.md](../README.md)로 돌아가기

이 문서에서는 dlt-daemon 인스턴스를 실행해 볼 것입니다. 1명 이상의 DLT 사용자가 생성한 로그 메시지를 수집하고 버퍼링할 때까지 자동으로 기다립니다. DLT 사용자 중 하나를 실행하여 데몬으로 전송되는 로그 메시지를 생성하도록 합니다. 마지막에는 이러한 메시지를 수집하고 표시하는 클라이언트를 실행합니다.

*주의: DLT를 설치했다고 가정합시다. (예: [빌드 후 2가지 선택적 단계](../README.md#build-and-install)가 실행됨) 그렇지 않으면 실행 경로를 관리하고 라이브러리 경로를 명시적으로 기술해야 합니다.*

## DLT 데몬 실행하기
DLT 데몬은 구성 가능성이 매우 높지만 이 경우에는 기본 설정만 사용해도 괜찮습니다. 경고 메시지로 인해 지체하지 마십시오:
```bash
$ dlt-daemon
[1886222.668006]~DLT~32290~NOTICE   ~Starting DLT Daemon; DLT Package Version: 2.18.0 STABLE, Package Revision: v2.18.1, build on Dec  8 2020 11:11:51
-SYSTEMD -SYSTEMD_WATCHDOG -TEST -SHM

[1886222.668651]~DLT~32290~INFO     ~FIFO size: 65536
[1886222.668764]~DLT~32290~INFO     ~Activate connection type: 5
[1886222.668897]~DLT~32290~INFO     ~dlt_daemon_socket_open: Socket created
[1886222.669047]~DLT~32290~INFO     ~dlt_daemon_socket_open: Listening on ip 0.0.0.0 and port: 3490
[1886222.669159]~DLT~32290~INFO     ~dlt_daemon_socket_open: Socket send queue size: 16384
[1886222.669355]~DLT~32290~INFO     ~Activate connection type: 1
[1886222.669509]~DLT~32290~INFO     ~Activate connection type: 9
[1886222.669644]~DLT~32290~INFO     ~Ringbuffer configuration: 500000/10000000/500000
[1886222.669924]~DLT~32290~NOTICE   ~Failed to open ECU Software version file.
[1886222.670034]~DLT~32290~INFO     ~Activate connection type: 6
[1886222.670188]~DLT~32290~INFO     ~Switched to buffer state for socket connections.
[1886222.670365]~DLT~32290~WARNING  ~dlt_daemon_applications_load: cannot open file /tmp/dlt-runtime-application.cfg: No such file or directory
```
데몬은 로그 메시지를 읽고 버퍼링할 준비가 된 네임드 파이프를 열었습니다. 또한 클라이언트가 메시지를 수집하기 위해 TCP 포트 3490에서 연결을 수락합니다.

## 로그 메시지 생성하기
이제 모의 ECU - DLT 사용자 - 는 DLT 라이브러리를 사용하여 로그 메시지를 생성하고 데몬이 수집할 네임드 파이프를 통해 보냅니다. 2번째 터미널을 열고 실행합니다.
```bash
$ dlt-example-user -n 5 -l 3 "This is my first log message"
Send 0 This is my first log message
Log level changed of context TEST, LogLevel=4, TraceState=0
Log level changed of context TS1, LogLevel=4, TraceState=0
Log level changed of context TS2, LogLevel=4, TraceState=0
Send 1 This is my first log message
Client disconnected!
Send 2 This is my first log message
Send 3 This is my first log message
Send 4 This is my first log message
```
이것은 문자열 페이로드를 포함하는 Log-Level WARNING ```(-l 3)```의 5개(```-n 5```) 동일한 로그 메시지를 보낼 것입니다.

## 로그 읽기
DLT 데몬은 이제 메시지를 버퍼에 저장하고 가져올 때까지 해당 메시지를 보관합니다. 로그 메시지를 수신하고 처리하는 강력한 도구는 그래픽 UI도 제공하는 [DLT-Viewer](https://github.com/COVESA/dlt-viewer)입니다. 현재로서는 간단한 커맨드 라인 클라이언트로 충분합니다:
```bash
$ dlt-receive -a localhost
2020/04/30 12:27:14.976731   17134987 000 ECU1 DA1- DC1- control response N 1 [service(3842), ok, 02 00 00 00 00]
2020/04/30 12:27:14.976779   17067139 000 ECU1 DA1- DC1- control response N 1 [service(3842), ok, 01 00 00 00 00]
2020/04/30 12:27:14.976787   17067139 004 ECU1 DLTD INTM log info V 1 [Client connection #7 closed. Total Clients : 0]
2020/04/30 12:27:14.976794   17104625 005 ECU1 DLTD INTM log info V 1 [ApplicationID 'LOG' registered for PID 5241, Description=Test Application for Logging]
2020/04/30 12:27:14.976802   17104625 000 ECU1 DA1- DC1- control response N 1 [get_log_info, 07, 01 00 4c 4f 47 00 01 00 54 45 53 54 ff ff 18 00 54 65 73 74 20 43 6f 6e 74 65 78 74 20 66 6f 72 20 4c 6f 67 67 69 6e 67 1c 00 54 65 73 74 20 41 70 70 6c 69 63 61 74 69 6f 6e 20 66 6f 72 20 4c 6f 67 67 69 6e 67 72 65 6d 6f]
2020/04/30 12:27:14.976823   17104625 000 ECU1 DA1- DC1- control response N 1 [get_log_info, 07, 01 00 4c 4f 47 00 01 00 54 53 31 00 ff ff 1b 00 54 65 73 74 20 43 6f 6e 74 65 78 74 31 20 66 6f 72 20 69 6e 6a 65 63 74 69 6f 6e 1c 00 54 65 73 74 20 41 70 70 6c 69 63 61 74 69 6f 6e 20 66 6f 72 20 4c 6f 67 67 69 6e 67 72 65 6d 6f]
2020/04/30 12:27:14.976844   17104625 000 ECU1 DA1- DC1- control response N 1 [get_log_info, 07, 01 00 4c 4f 47 00 01 00 54 53 32 00 ff ff 1b 00 54 65 73 74 20 43 6f 6e 74 65 78 74 32 20 66 6f 72 20 69 6e 6a 65 63 74 69 6f 6e 1c 00 54 65 73 74 20 41 70 70 6c 69 63 61 74 69 6f 6e 20 66 6f 72 20 4c 6f 67 67 69 6e 67 72 65 6d 6f]
2020/04/30 12:27:14.976866   17104588 000 ECU1 LOG- TEST log warn V 2 [0 This is my first log message]
2020/04/30 12:27:14.976872   17109592 001 ECU1 LOG- TEST log warn V 2 [1 This is my first log message]
2020/04/30 12:27:14.976880   17114599 002 ECU1 LOG- TEST log warn V 2 [2 This is my first log message]
2020/04/30 12:27:14.976884   17119607 003 ECU1 LOG- TEST log warn V 2 [3 This is my first log message]
2020/04/30 12:27:14.976889   17124611 004 ECU1 LOG- TEST log warn V 2 [4 This is my first log message]
2020/04/30 12:27:14.976894   17134988 006 ECU1 DLTD INTM log info V 1 [New client connection #8 established, Total Clients : 1]
2020/04/30 12:27:15.442016   17139641 000 ECU1 DA1- DC1- control response N 1 [service(3841), ok, 4c 4f 47 00 54 45 53 54 72 65 6d 6f]
2020/04/30 12:27:15.442044   17139642 007 ECU1 DLTD INTM log info V 1 [Unregistered ApID 'LOG']
```
클라이언트는 localhost의 기본 포트 3490에 연결하여 모든 메시지를 수집하고 페이로드를 ASCII 텍스트(```-a```)로 해석합니다. 많은 추가 메시지를 볼 수 있습니다. 클라이언트와 데몬 간의 흐름을 제어하기 위한 제어 메시지입니다. 이에 대해서는 나중에 알게 될 것입니다. 지금은 DLT가 작동하는 것을 보여주는 기본적인 예제를 설정했습니다.

이제 이 설정을 실험해 볼 수 있습니다. DLT 사용자를 먼저 시작하고 (DLT 사용자가 계속 실행 중인 상태에서) 데몬을 실행하면 어떻게 됩니까?
