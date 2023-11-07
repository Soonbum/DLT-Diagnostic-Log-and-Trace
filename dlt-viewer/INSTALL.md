# DLT 뷰어 - 설치

Alexander Wenzel <Alexander.AW.Wenzel@bmw.de>

## DLT 뷰어 설치 방법 (GUI)

* Qt Creator를 포함한 Qt5 SDK와 Microsoft Visual Studio Build Tools 2017를 설치합니다.
* QT 5.15.2 (Windows)와 Qt 5.12.8 (Ubuntu 20.04LTS Linux)에서 테스트했습니다.
* Qt Creator에서 프로젝트 BuildDltViewer.pro를 엽니다.
* 빌드 옵션이 올바른 QT와 MSVC를 가리키는지 확인하십시오.
* Qt Creator에서 또는 CMake를 통해 빌드 릴리즈. (마지막을 보십시오)
* Linux 선택사항: Qt Creator에서 라이브러리 경로를 설정하십시오: Projects/Build Settings/Build Environment에 변수 추가: LD_LIBRARY_PATH = .

## DLT 뷰어 설치 방법 (Linux command line)

* sudo apt install build-essential
* sudo apt install qtcreator
* sudo apt install qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools
* sudo apt install libqt5serialport5-dev
* mkdir build
* cd build
* qmake ../BuildDltViewer.pro
* make
* sudo make install
* sudo ldconfig
* 선택사항: 라이브러리 경로를 설정하십시오: LD_LIBRARY_PATH = .

## Windows에서 DLT 뷰어 빌드하기

* Qt5 SDK와 Microsoft Visual Studio Build Tools 2019를 설치합니다.
* 필요한 경우 build_sdk_windows_qt5_MSVC.bat에서 구성을 조정합니다.
* Run build_sdk_windows_qt5_MSVC.bat을 실행합니다.

DLT 뷰어는 기본적으로 C:\DltViewerSDK에 설치될 것입니다.

## 선택사항: Qwt 설치 준비 (Windows)

일부 DLT 뷰어 플러그인은 그래프를 표시하기 위해 Qwt 라이브러리를 사용할 것입니다.
Qwt 라이브러리를 사용하려면 반드시 컴파일하고 설치해야 합니다.

(Qwt 6.1.6를 가지고 테스트했음)

* Qt5 SDK와 Microsoft Visual Studio Build Tools 2019를 설치합니다.
* Qwt zip 패키지 qwt-6.1.6.zip을 DLT 뷰어 폴더에 복사하십시오.
* build_qwt_windows_qt5_MSVC.bat을 실행하십시오.

## SDK 생성하기 (Linux)

* "Build DLT-viewer Linux"에 설명한 대로 DLT 뷰어를 컴파일하십시오.
* SDK를 위한 라이브러리와 헤더를 포함하여 dlt-viewer를 설치하기 위해 "sudo make install"을 호출하십시오.

## SDK 문서 생성하기 (Windows)

* doxygen과 graphviz를 설치하십시오.
* project 디렉토리로 변경하십시오.
* doxygen sdk\doxygen_dlt_viewer_plugininterface.cfg
* (선택사항) doxygen sdk\doxygen_dlt_viewer.cfg
* (선택사항) doxygen sdk\doxygen_dlt_viewer_qdlt.cfg
* doc 디렉토리에서 문서를 찾을 수 있습니다.

## SDK 문서 생성하기 (Linux)

* doxygen과 graphviz를 설치하십시오.
* project 디렉토리로 변경하십시오.
* doxygen sdk/doxygen_dlt_viewer_plugininterface.cfg
* (선택사항) doxygen sdk/doxygen_dlt_viewer.cfg
* (선택사항) doxygen sdk/doxygen_dlt_viewer_qdlt.cfg
* doc 디렉토리에서 문서를 찾을 수 있습니다.

## 매뉴얼 문서 생성하기

플러그인 프로그래밍 매뉴얼은 asciidoc으로 생성됩니다.
html 출력물을 생성하기 위해 다음을 호출하십시오.
    asciidoc dlt_viewer_user_manual.txt
    asciidoc dlt_viewer_plugins_programming_guide.txt
pdf를 생성하려면 doc 디렉토리 안에서 다음을 호출하십시오.
    sh convert.sh

사용자 매뉴얼은 2018년 6월 이후로 Latex 입력 포맷으로 만들어졌습니다.
다음은 Ubuntu 18.04 버전에서 검증된 빌드입니다.
sudo apt-get install texlive texlive-latex-extra

pdf 포맷 출력을 생성하려면 다음을 사용하십시오:
pdflatex dlt-viewer_user_manual.tex

## CMake로 DLT 뷰어 빌드하는 방법 (Linux 및 MacOS command line)

CMake는 "메타" make 시스템입니다. 이것은 CMake가 "Unix Makefiles"와 같은 커맨드 라인 빌드 도구나 "Visual Studio" 같은 IDE 도구를 포함하는 다양한 make 솔루션에 대한 make 시스템을 생성한다는 것을 의미합니다.
다음을 보십시오: https://cmake.org/cmake/help/v3.0/manual/cmake-generators.7.html

CMake는 "소스 외부" 빌드 패러다임을 권장합니다. DLT 뷰어 코드가 src_path/에 언팩되었다고 가정하면, 해당 디렉토리에서 빌드하지 않는 것이 좋습니다.

* mkdir build
* cd build
* cmake "GENERATOR OF CHOICE" src_path/

이 단계에서, CMake는 "Unix Makefiles", "Visual Studio 6" 또는 다른 make 시스템에서 사용할 수 있는 파일 집합을 생성합니다. 빌드 디렉토리에는 DLT-Viewer.EXTENSION 파일이 있는 경우가 많습니다.

"Unix Makefiles"의 경우, 빌드하는 것은 다음과 같이 간단합니다.

* sudo make install
* sudo ldconfig
* 선택사항: set the library path: LD_LIBRARY_PATH = .

필요한 구성 요소에 대한 비-표준 설치 경로에 대해 알려줄 수 있는 cmake 구성을 위한 다양한 솔루션이 있습니다. 자세한 내용은 CMake 문서를 보십시오. (힌트: QTDIR)

MacOS의 경우, Homebrew를 가지고 Qt를 설치할 수 있습니다:
brew install qt

그리고 나서 Qt 디렉토리를 CMake에게 제공하고 빌드하십시오:
* Qt5_DIR="/usr/local/opt/qt" cmake src_path/
* make

애플리케이션은 "bin/DLT Viewer.app"에 빌드되어 있으며, Finder 또는 커맨드 라인에서 실행할 수 있습니다:
open -a $DLT_BUILD_DIR/bin/DLT\ Viewer.app

## MacOS에서 Qmake로 DLT 뷰어 릴리즈 버전 빌드하기

때때로 Cmake를 가지고, 혹은 Qt Creator에서 빌드하면 DLT 뷰어에서 플러그인이 작동하지 않기도 합니다.
릴리즈 버전에서 qmake로 빌드하면 이 문제를 해결할 수 있습니다.
다음 단계를 따르십시오:

* mkdir build
* cd build
* <path to Qt folder>/Qt/5.X/gcc_64/bin/qmake <path to BuildDltViewer.pro>/BuildDltViewer.pro -r
* make

이 시점에서 "build" 폴더 안에 "release" 폴더를 생성해야 합니다.

* cd release
* export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:./

실행하려면:

*./dlt_viewer
