---
title: "OMS(Operations Management Suite)의 서비스 맵 구성 | Microsoft Azure"
description: "서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑하는 OMS(Operations Management Suite) 솔루션입니다.  이 문서에서는 사용자 환경에 서비스 맵을 배포하고 다양한 시나리오에서 사용하는 것에 대해 자세히 설명합니다."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: e0c74ef9fa2fa2e0b69a3730d7af30969d60e3f8
ms.lasthandoff: 04/20/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>OMS(Operations Management Suite)의 서비스 맵 솔루션 구성
서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다.  서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다.

이 문서에서는 서비스 맵 및 에이전트 등록에 대해 자세히 설명합니다.  서비스 맵 사용에 대한 자세한 내용은 [OMS(Operations Management Suite)에서 서비스 맵 솔루션 사용](operations-management-suite-service-map.md)을 참조하세요.

## <a name="dependency-agent-downloads"></a>종속성 에이전트 다운로드
| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |


## <a name="connected-sources"></a>연결된 소스
서비스 맵은 Microsoft 종속성 에이전트에서 해당 데이터를 가져옵니다.  종속성 에이전트는 OMS 연결에 사용된 OMS 에이전트에 따라 달라집니다.  이는 서버에 먼저 OMS 에이전트를 설치하고 구성한 다음, 종속성 에이전트를 설치할 수 있다는 의미입니다.  다음 표는 서비스 맵 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원됨 | 설명 |
|:--|:--|:--|
| Windows 에이전트 | 예 | 서비스 맵은 Windows 에이전트 컴퓨터에서 데이터를 분석하고 수집합니다.  <br><br>[OMS 에이전트](../log-analytics/log-analytics-windows-agents.md) 외에도 Windows 에이전트에는 Microsoft 종속성 에이전트가 필요합니다.  전체 운영 체제 목록 버전은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| Linux 에이전트 | 예 | 서비스 맵은 Linux 에이전트 컴퓨터에서 데이터를 분석하고 수집합니다.  <br><br>[OMS 에이전트](../log-analytics/log-analytics-linux-agents.md) 외에도 Linux 에이전트에는 Microsoft 종속성 에이전트가 필요합니다.  전체 운영 체제 목록 버전은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| SCOM 관리 그룹 | 예 | 서비스 맵은 연결된 [SCOM(System Center Operations Manager) 관리 그룹](../log-analytics/log-analytics-om-agents.md)의 Windows 및 Linux 에이전트에서 데이터를 분석하고 수집합니다. <br><br>SCOM 에이전트 컴퓨터에서 OMS로 직접 연결이 필요합니다. 데이터는 관리 그룹에서 OMS 리포지토리로 직접 전달됩니다.|
| Azure 저장소 계정 | 아니요 | 서비스 맵은 에이전트 컴퓨터에서 데이터를 수집하므로 Azure Storage에서 수집할 데이터는 없습니다. |

서비스 맵은 64비트 플랫폼만 지원합니다.

Windows에서는 SCOM 및 OMS에서 모니터링 데이터를 수집하고 전송하기 위해 MMA(Microsoft Monitoring Agent)가 사용됩니다.  (컨텍스트에 따라 이 에이전트를 SCOM 에이전트, OMS 에이전트, MMA 또는 직접 에이전트라고 합니다.)  SCOM 및 OMS는 MMA의 즉시 사용 가능한 다양한 버전을 제공하지만 이러한 버전으로 SCOM, OMS 각각 또는 둘 다에 보고할 수 있습니다.  Linux에서는 Linux용 OMS 에이전트가 모니터링 데이터를 수집하여 OMS에 보냅니다.  SCOM 관리 그룹을 통해 OMS에 연결된 서버에서 또는 OMS 직접 에이전트가 있는 서버에서 서비스 맵을 사용할 수 있습니다.  이 설명서에서는 컨텍스트에 따라 에이전트의 특정 배포 이름이 필요하지 않다면, Linux 또는 Windows용인지, SCOM MG에 연결되었는지 또는 OMS에 직접 연결되었는지 관계없이 모든 에이전트를 "OMS 에이전트"라고 합니다.

서비스 맵 에이전트는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다.  서비스 맵의 데이터는 OMS 에이전트에 의해 직접 또는 OMS 게이트웨이를 통해 OMS로 항상 전송됩니다.

![서비스 맵 에이전트](media/oms-service-map/agents.png)

관리 그룹이 OMS에 연결된 SCOM 고객인 경우:

- SCOM 에이전트가 인터넷에 액세스하여 OMS에 연결할 수 있는 경우 추가 구성은 필요 하지 않습니다.  
- SCOM 에이전트가 인터넷을 통해 OMS에 액세스할 수 없는 경우 SCOM을 사용하도록 OMS 게이트웨이를 구성해야 합니다.
  
OMS 직접 에이전트를 사용하는 경우 OMS 에이전트 자체가 OMS 게이트웨이 또는 OMS에 직접 연결되도록 구성해야 합니다.  OMS 게이트웨이는 [https://www.microsoft.com/download/details.aspx?id=52666](https://www.microsoft.com/download/details.aspx?id=52666)에서 다운로드할 수 있습니다.


### <a name="avoiding-duplicate-data"></a>중복 데이터 방지

SCOM 고객인 경우 OMS와 직접 통신하도록 SCOM 에이전트를 구성해서는 안 됩니다. 이 경우 데이터가 두 번 보고됩니다.  서비스 맵에서 컴퓨터 목록에 컴퓨터가 두 번 나타납니다.

다음 위치 중 하나에만 OMS의 구성이 수행됩니다.

- 관리되는 컴퓨터에 대한 SCOM 콘솔 Operations Management Suite 패널
- MMA 속성에서 Azure Operational Insights 구성

*동일한* 작업 영역에서 두 구성을 각각 사용할 경우 데이터 중복이 발생합니다. *다른* 작업 영역의 두 구성을 모두 사용할 경우 구성 충돌이 발생하여(한 쪽에서는 서비스 맵 솔루션을 사용하고 다른 쪽에서는 사용하지 않음) 데이터가 서비스 맵으로 완전히 전달되지 않을 수 있습니다.  

SCOM 콘솔의 OMS 구성에 컴퓨터 자체가 지정되지 않고 "Windows Server Instances Group"와 같은 인스턴스 그룹이 활성 상태이면 컴퓨터는 계속 SCOM을 통해 OMS 구성을 수신할 수 있습니다.



## <a name="management-packs"></a>관리 팩
서비스 맵이 OMS 작업 영역에서 활성화되면 300KB 관리 팩이 해당 작업 영역의 모든 Microsoft Monitoring Agent로 전송됩니다.  [연결된 관리 그룹](../log-analytics/log-analytics-om-agents.md)에서 SCOM 에이전트를 사용하는 경우 서비스 맵 관리 팩은 SCOM에서 배포됩니다.  에이전트가 직접 연결되는 경우 OMS에 의해 MP가 전달됩니다.

The MP는 Microsoft.IntelligencePacks.ApplicationDependencyMonitor*라고 합니다.  *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\*에 기록됩니다.  관리 팩에 사용된 데이터 원본은 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*입니다.



## <a name="configuration"></a>구성
에이전트가 설치되어 OMS에 연결된 Windows 및 Linux 컴퓨터 외에도 종속성 에이전트 설치 관리자를 서비스 맵 솔루션에서 다운로드한 다음 관리되는 각 서버에서 루트 또는 관리자로 설치해야 합니다.  서비스 맵 에이전트가 OMS에 보고되는 서버에 설치되면 서비스 맵 종속성 맵이 10분 내에 나타납니다.


### <a name="migrating-from-bluestripe-factfinder"></a>BlueStripe FactFinder에서 마이그레이션
서비스 맵은 단계적으로 OMS에 BlueStripe 기술을 제공합니다. FactFinder는 기존 고객을 위해 계속 지원되지만 더 이상 개별 구매는 불가능합니다.  이 미리 보기 버전의 종속성 에이전트만 OMS와 통신할 수 있습니다.  현재 FactFinder 고객인 경우 서비스 맵에 대한 테스트 서버 집합이 FactFinder로 관리되지 않는지 확인해 주세요. 

### <a name="download-the-dependency-agent"></a>종속성 에이전트 다운로드
컴퓨터와 OMS 간의 연결을 제공하는 MMA(Microsoft Management Agent) 및 OMS Linux 에이전트 외에도 서비스 맵으로 분석된 모든 컴퓨터에 종속성 에이전트가 설치되어 있어야 합니다.  Linux에서는 종속성 에이전트 이전에 Linux용 OMS 에이전트를 설치해야 합니다. 

![서비스 맵 타일](media/oms-service-map/additional_configuration.png)

종속성 에이전트를 다운로드하려면 **서비스 맵** 타일의 **솔루션 구성**을 클릭하여 **종속성 에이전트** 블레이드를 엽니다.  종속성 에이전트 블레이드에는 Windows 및 Linux 에이전트에 대한 링크가 있습니다. 서로 다른 시스템에 에이전트 설치에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치

#### <a name="microsoft-windows"></a>Microsoft Windows
에이전트를 설치 또는 제거하려면 관리자 권한이 필요합니다.

종속성 에이전트는 InstallDependencyAgent-Windows.exe를 사용하여 Windows 컴퓨터에 설치됩니다. 옵션 없이 이 실행 파일을 실행하는 경우 마법사가 시작되고 대화형으로 설치를 수행할 수 있습니다.  

각 Windows 컴퓨터에서 종속성 에이전트를 설치하려면 다음 단계를 따르세요.

1.    OMS에 직접 컴퓨터 연결의 지침을 사용하여 OMS 에이전트가 설치되어 있는지 확인합니다.
2.    Windows 에이전트를 다운로드하고 다음 명령을 사용하여 실행합니다. <br>*InstallDependencyAgent-Windows.exe*
3.    마법사에 따라 에이전트를 설치합니다.
4.    종속성 에이전트를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Windows 에이전트에서 로그 디렉터리는 *C:\Program Files\Microsoft Dependency Agent\logs*입니다. 

제어판을 통해 관리자가 Windows용 종속성 에이전트를 제거할 수 있습니다.


#### <a name="linux"></a>Linux
에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.

종속성 에이전트는 셀프 추출 이진이 포함된 셸 스크립트인 InstallDependencyAgent-Linux64.bin을 사용하여 Linux 컴퓨터에 설치됩니다. sh로 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.
 
각 Linux 컴퓨터에서 종속성 에이전트를 설치하려면 다음 단계를 따르세요.

1.    [Linux 컴퓨터에서 데이터 수집 및 관리의 지침을 사용하여 OMS 에이전트가 설치되어 있는지 확인합니다.  Linux 종속성 에이전트](https://technet.microsoft.com/library/mt622052.aspx) 이전에 설치해야 합니다.
2.    다음 명령을 사용하여 루트로 Linux 종속성 에이전트를 설치합니다.<br>*sh InstallDependencyAgent-Linux64.bin*.
3.    종속성 에이전트를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 */var/opt/microsoft/dependency-agent/log*입니다.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Linux에서 종속성 에이전트 제거
Linux에서 종속성 에이전트를 완전히 제거하려면 에이전트 자체와 에이전트와 함께 자동으로 설치된 커넥터를 제거해야 합니다.  다음 단일 명령으로 둘 다 제거할 수 있습니다.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>명령줄에서 설치
이전 섹션에서는 기본 옵션을 사용하여 Dependency Monitor 에이전트를 설치하는 지침을 살펴보았습니다.  다음 섹션에서는 사용자 지정 옵션을 사용하여 명령줄에서 에이전트를 설치하는 지침을 설명합니다.

#### <a name="windows"></a>Windows
다음 표의 옵션을 사용하여 명령줄에서 설치합니다. 설치 플래그 목록을 보려면 다음과 같이 /? 플래그를 사용하여 설치 관리자를 실행합니다.

    InstallDependencyAgent-Windows.exe /?

| 플래그 | 설명 |
|:--|:--|
| /S | 사용자 프롬프트 없이 자동 설치를 수행합니다. |

Windows 종속성 에이전트에 대한 파일은 기본적으로 *C:\Program Files\Microsoft Dependency Agent*에 있습니다.


#### <a name="linux"></a>Linux
다음 표의 옵션을 사용하여 설치합니다. 설치 플래그 목록을 보려면 다음과 같이 -help 플래그로 설치 프로그램을 실행합니다.

    InstallDependencyAgent-Linux64.bin -help

| 플래그 | 설명 |
|:--|:--|
| -s | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| --check | 사용 권한 및 운영 체제를 확인하지만 에이전트를 설치하지 않습니다. |

종속성 에이전트에 대한 파일은 다음 디렉터리에 있습니다.

| 파일 | 위치 |
|:--|:--|
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 저장소 파일 | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>문제 해결
서비스 맵을 설치하거나 실행하는 데 문제가 발생하면 이 섹션을 통해 시작하고 실행할 수 있습니다.  그래도 문제를 해결할 수 없으면 Microsoft 기술 지원에 문의해 주세요.

### <a name="dependency-agent-installation-issues"></a>종속성 에이전트 설치 문제
#### <a name="installer-asks-for-a-reboot"></a>설치 관리자에서 다시 부팅을 요청함
종속성 에이전트는 *일반적으로* 설치 또는 제거 시 재부팅하지 않아도 됩니다.  그러나 드문 경우이지만 설치를 계속하기 위해 Windows Server를 다시 부팅해야 합니다.  이 문제는 일반적으로 Microsoft VC++ 재배포 가능 패키지의 종속성이 잠긴 파일로 인해 다시 부팅해야 하는 경우에 발생합니다.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber"></a>메시지: "종속성 에이전트를 설치할 수 없습니다: Visual Studio 런타임 라이브러리를 설치하지 못했습니다(코드 = [코드 번호])."

Microsoft 종속성 에이전트는 Microsoft Visual Studio 런타임 라이브러리를 기반으로 합니다. 라이브러리를 설치하는 동안 문제가 발생했습니다. 런타임 라이브러리 설치 관리자는 %LOCALAPPDATA%\temp 폴더에 로그를 만듭니다. 파일은 dd_vcredist_arch_yyyymmddhhmmss.log가 되며, 여기서 arch는 "x86" 또는 "amd64"이고, yyyymmddhhmmss는 로그를 만든 날짜 및 시간(24시간제)입니다. 로그는 설치를 차단하는 문제에 대한 세부 정보를 제공합니다.

먼저 [최신 런타임 라이브러리](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)를 직접 설치하는 것이 유용할 수 있습니다.

다음은 몇 가지 코드 번호 및 권장되는 해결 방법입니다.

| 코드 | 설명 | 해결 방법 |
|:--|:--|:--|
| 0x17 | 라이브러리 설치 관리자에는 설치하지 않은 Windows 업데이트가 필요합니다. | 가장 최근의 라이브러리 설치 관리자 로그를 확인합니다(위 참조).<br><br>“Windows8.1-KB2999226-x64.msu”에 대한 참조 뒤에 “오류 0x80240017: MSU 패키지를 실행하지 못했습니다.”라는 줄이 있으면 KB2999226을 설치하기 위한 필수 구성 요소가 없는 것입니다.  https://support.microsoft.com/kb/2999226에 있는 필수 구성 요소 섹션의 지침을 따릅니다.  Windows Update를 실행하고 필수 구성 요소를 설치하기 위해 여러 번 다시 부팅해야 할 수 있습니다.<br><br>Microsoft 종속성 에이전트 설치 관리자를 다시 실행합니다. |

### <a name="post-installation-issues"></a>사후 설치 문제
#### <a name="server-doesnt-show-in-service-map"></a>서비스 맵에 서버가 표시되지 않음
종속성 에이전트 설치가 성공했지만 서비스 맵 솔루션에 서버가 표시되지 않습니다.
1. 종속성 에이전트가 성공적으로 설치되었나요?  서비스가 설치되어 실행 중인지 확인하여 성공적인 설치 여부를 검사할 수 있습니다.<br><br>
**Windows**: "Microsoft 종속성 에이전트"라는 서비스를 찾아봅니다.<br>
**Linux**: "microsoft-dependency-agent" 실행 프로세스를 찾아봅니다.

2. [OMS/Log Analytics의 무료 가격 책정 계층](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)에 있나요?  무료 계획에서는 고유한 서비스 맵 서버를 5개까지 허용합니다.  따라서 이전의 5개 서비스 맵 서버에서 데이터를 더 이상 보내지 않더라도 이후의 모든 서버는 서비스 맵에 표시되지 않습니다.

3. 서버에서 OMS로 로그 및 perf 데이터를 보내고 있나요?  [로그 검색]으로 이동하여 컴퓨터에 대해 다음 쿼리를 실행합니다. 

        * Computer="<your computer name here>" | measure count() by Type
        
결과에서 다양한 이벤트를 얻었나요?  최근 데이터인가요?  그렇다면 OMS 에이전트가 올바르게 작동하고 OMS 서비스와 통신하고 있습니다. 그렇지 않으면 [Windows용 OMS 에이전트 문제 해결](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)을 참조하여 서버의 OMS 에이전트를 확인합니다.  [Linux용 OMS 에이전트 문제 해결](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md)

#### <a name="server-shows-in-service-map-but-has-no-processes"></a>서비스 맵에서 서버가 표시되지만 프로세스가 없음
서비스 맵에서 서버가 표시되지만 프로세스 또는 연결 데이터가 없는 경우 종속 에이전트가 설치되어 실행 중이지만 커널 드라이버가 로드되지 않았음을 나타냅니다.  드라이버가 로드되지 않은 이유를 확인하려면 wrapper.log 파일(Windows) 또는 service.log 파일(Linux)을 검사합니다.  파일의 마지막 줄에 커널이 로드되지 않은 이유(예: 커널이 지원되지 않습니다. 이는 커널을 업데이트한 경우 Linux에서 발생할 수 있습니다.)가 표시됩니다.

Windows: C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log

Linux: /var/opt/microsoft/dependency-agent/log/service.log

## <a name="data-collection"></a>데이터 수집
각 에이전트는 시스템 종속성이 얼마나 복잡한지에 따라 일일 약 25MB를 전송할 것으로 기대할 수 있습니다.  서비스 맵 종속성 데이터는 15초마다 각 에이전트에서 전송됩니다.  

서비스 맵 에이전트는 일반적으로 0.1%의 시스템 메모리와 0.1%의 시스템 CPU를 사용합니다.


## <a name="supported-operating-systems"></a>지원되는 운영 체제
다음 섹션에서는 종속성 에이전트에 대해 지원되는 운영 체제를 나열합니다.   운영 체제에 대해 32비트 아키텍처는 지원되지 않습니다.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows Desktop
- WIndows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux 및 Oracle Linux(RHEL 커널 포함)
- 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
- PAE 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 "2.6.16.21-0.8-xen"의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
- 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.
- Centos Plus 커널은 지원되지 않습니다.
- Oracle UEK(Unbreakable Kernel)는 아래 다양한 섹션에서 설명합니다.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| OS 버전 | 커널 버전 |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| OS 버전 | 커널 버전 |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| OS 버전 | 커널 버전 |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>UEK(Unbreakable Kernel) 포함 Oracle Enterprise Linux

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| OS 버전 | 커널 버전
|:--|:--|
| 6.2 | Oracle 2.6.32-300(UEK R1) |
| 6.3 | Oracle 2.6.39-200(UEK R2) |
| 6.4 | Oracle 2.6.39-400(UEK R2) |
| 6.5 | Oracle 2.6.39-400(UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400(UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| OS 버전 | 커널 버전
|:--|:--|
| 5.8 | Oracle 2.6.32-300(UEK R1) |
| 5.9 | Oracle 2.6.39-300(UEK R2) |
| 5.10 | Oracle 2.6.39-400(UEK R2) |
| 5.11 | Oracle 2.6.39-400(UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| OS 버전 | 커널 버전
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| OS 버전 | 커널 버전
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>진단 및 사용 현황 데이터
Microsoft는 서비스 맵 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스 맵 서비스의 품질, 보안 및 무결성을 제공 및 개선합니다. 데이터에는 운영 체제 및 버전과 같은 소프트웨어 구성에 대한 정보가 포함되며 정확하고 효율적인 문제 해결 기능을 제공하기 위해 IP 주소, DNS 이름 및 워크스테이션 이름도 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인 정보 취급 방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.



## <a name="next-steps"></a>다음 단계
- 서비스 맵이 배포 및 구성된 경우 [서비스 맵을 사용](operations-management-suite-service-map.md)하는 방법을 알아봅니다.

