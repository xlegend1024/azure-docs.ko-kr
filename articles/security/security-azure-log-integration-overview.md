---
title: "Azure 리소스에서 SIEM 시스템으로 로그 통합 | Microsoft Docs"
description: "Azure 로그 통합, 주요 기능 및 작동 원리에 대해 알아봅니다."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2017
ms.author: TomSh
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 616a29b3f7db817e63a3a3673b845cc86752af77
ms.contentlocale: ko-kr
ms.lasthandoff: 05/23/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 로그 통합 소개
Azure 로그 통합, 주요 기능 및 작동 원리에 대해 알아봅니다.

## <a name="overview"></a>개요

Azure 로그 통합은 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있게 해주는 무료 솔루션입니다.

Azure 로그 통합은 Azure 리소스에서 Windows 이벤트 뷰어 채널, [Azure 활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center 경고](../security-center/security-center-intro.md) 및 [Azure 진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)로부터 Windows 이벤트를 수집합니다. 이 통합을 통해 SIEM 솔루션에서 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공할 수 있습니다.

>[!NOTE]
현재 지원되는 유일한 클라우드는 Azure Commercial 및 Azure Government입니다. 현재 다른 클라우드는 지원되지 않습니다.

![Azure 로그 통합][1]

## <a name="what-logs-can-i-integrate"></a>어떤 로그와 통합할 수 있나요?
Azure에서는 모든 Azure 서비스에 대해 광범위한 로깅을 생성합니다. 이러한 로그는 세 가지 유형의 로그를 나타냅니다.

* **컨트롤/관리 로그**는 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다. Azure 활동 로그가 이 로그 유형에 속합니다.
* **데이터 평면 로그**는 Azure 리소스를 사용할 때 발생하는 이벤트에 대한 가시성을 제공합니다. 이 로그 유형의 예는 Windows 가상 컴퓨터의 Windows 이벤트 뷰어에 있는 **시스템**, **보안** 및 **응용 프로그램** 채널이 있습니다. 또 다른 예는 Azure Monitor를 통해 구성된 진단 로깅입니다.
* **처리된 이벤트**는 사용자 대신 처리된 경고 정보 및 분석된 이벤트를 제공합니다. 이 이벤트 유형의 예로 Azure Security Center 경고가 있습니다. Azure Security Center에서는 현재 보안 상태와 관련된 경고를 제공하기 위해 구독을 처리하고 분석합니다.

Azure 로그 통합 기능은 현재 Azure 활동 로그, Azure 구독의 Windows 가상 컴퓨터에서 제공된 Windows 이벤트 로그, Azure Security Center 경고, Azure 진단 로그 및 Azure Active Directory 감사 로그를 통합하도록 지원합니다.

다음 표에서는 로그 범주 및 SIEM 통합 세부 설명을 제공합니다.

| 로그 형식  |JSON(Splunk, ELK)을 지원하는 Log Analytics| ArcSight  | QRadar  |   
|---|---|---|---|
|  AAD 감사 로그 |  예 | FlexConnector JSON 파서 파일을 만들어야 합니다. 자세한 내용은 ArcSight 설명서를 참조하세요.  |  로그 원본 확장을 만들어야 합니다. 자세한 내용은 QRadar 설명서를 참조하세요. |  
| 활동 로그  | 예  |  Azure 로그 통합 다운로드와 함께 다운로드 센터에서 사용할 수 있는 FlexConnector JSON 파서 파일 |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)(Syslog를 통해 전송) |  
| ASC 경고  | 예  |  FlexConnector JSON 파서 파일을 만들어야 합니다. 자세한 내용은 ArcSight 설명서를 참조하세요. | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)(Syslog를 통해 전송)   |   
| 진단 로그(리소스 로그) | 예 | 최종 사용자는 FlexConnector JSON 파서 파일을 만들어야 합니다. 이 작업을 수행하는 방법은 ArcSight 설명서를 참조하세요. | 로그 원본 확장을 만들어야 합니다. 자세한 내용은 QRadar 설명서를 참조하세요. |
| VM 로그 | 예(JSON이 아닌 전달 이벤트를 통해) | 예(전달 이벤트를 통해) | 예(전달 이벤트를 통해) |

>[!NOTE]
Azure 로그 통합은 무료 솔루션이지만 로그 파일 정보 저장소로 인한 Azure Storage 비용이 발생합니다.

[Azure 로그 통합 MSDN 포럼](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)을 통해 커뮤니티의 지원을 받을 수 있습니다. 이 포럼은 Azure 로그 통합을 최대한 활용하는 방법에 대한 질문, 답변, 팁, 요령 등 서로 지원할 수 있는 기능을 AzLog 커뮤니티에 제공합니다. 또한 Azure 로그 통합 팀이 이 포럼을 모니터링하며 가능한 한 언제든지 도움을 드릴 것입니다.

[지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 열 수도 있습니다. 지원 요청을 열려면 지원을 요청하는 서비스로 **로그 통합**을 선택합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 로그 통합을 소개했습니다. Azure 로그 통합 및 지원되는 로그 유형에 대한 자세한 내용은 다음을 참조하세요.

* [Microsoft Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 다운로드할 수 있습니다.
* [Azure 로그 통합 시작](security-azure-log-integration-get-started.md) - 이 자습서에서는 Azure 로그 통합을 설치하고 Azure WAD 저장소, Azure 활동 로그, Azure Security Center 경고 및 의 Azure Active Directory 감사 로그의 로그를 통합하는 방법을 안내합니다.
* [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다.
* [Syslog를 통해 QRadar로 활동 및 ASC 경고 전송](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) - 이 블로그 게시물은 syslog를 통해 QRadar로 활동 및 Azure Security Center 경고를 보내는 단계를 제공합니다.
* [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
* [Azure 로그 통합과 Security Center 경고 통합](../security-center/security-center-integrating-alerts-with-log-integration.md) - 이 문서에서는 Azure Security Center 경고와 Azure 로그 통합을 동기화하는 방법을 보여 줍니다.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

