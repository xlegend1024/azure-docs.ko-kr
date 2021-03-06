---
title: "OMS의 IT Service Management Connector | Microsoft Docs"
description: "IT Service Management Connector를 사용하여 중앙에서 OMS의 ITSM 작업 항목을 모니터링 및 관리하고 문제를 빠르게 해결합니다."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: f8e668f89eb1e508c61fec6565e93667d3d0478e
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>IT Service Management Connector(미리 보기)를 사용하여 ITSM 작업 항목을 중앙에서 관리

OMS Log Analytics에서 IT Service Management Connector를 사용하여 ITSM 제품/서비스에서 작업 항목을 중앙에서 모니터링하고 관리할 수 있습니다.

IT Service Management Connector는 기존 ITSM(IT Service Management) 제품 및 서비스를 OMS Log Analytic에 통합합니다.  이 솔루션은 ITSM 제품/서비스와 양방향으로 통합되어 OMS 사용자가 ITSM 솔루션에서 인시던트, 경고 또는 이벤트를 만드는 옵션을 제공합니다. 또한 이 커넥터는 ITSM 솔루션의 인시던트 및 변경 요청과 같은 데이터를 OMS Log Analytics로 가져옵니다.

IT Service Management Connector를 사용하여 다음을 수행할 수 있습니다.

  - 조직에서 사용되는 ITSM 제품/서비스에 대한 작업 항목을 중앙에서 모니터링 및 관리합니다.
  - OMS 경고 및 로그 검색을 통해 ITSM에서 ITSM 작업 항목(예: 경고, 이벤트, 인시던트)을 만듭니다.
  - ITSM 솔루션에서 인시던트 및 변경 요청을 읽고, Log Analytics 작업 영역에서 관련 로그 데이터와의 상관 관계를 파악합니다.
  - 최종 사용자가 지원 부서에 전화를 해서 보고하기 전에 예기치 않은 비정상적인 이벤트를 찾아 해결합니다.
  - 작업 항목 데이터를 Log Analytics로 가져오고 KPI(핵심 성과 지표) 보고서를 만듭니다.  이러한 보고서를 사용하여 중요한 일부 항목을 식별하고 평가한 후 맬웨어 평가와 같은 조치를 수행할 수 있습니다.
  - 엄선된 대시보드를 통해 인시던트, 변경 요청 및 영향 받은 시스템에 대한 심층 정보를 볼 수 있습니다.
  - Log Analytics 작업 영역에서 다른 관리 솔루션과 상호 연결하여 더 빠르게 문제를 해결합니다.


## <a name="prerequisites"></a>필수 조건

ITSM 작업 항목을 OMS Log Analytics로 가져오기 위해 솔루션에서 OMS의 IT Service Management Connector와 작업 항목을 가져오는 ITSM 제품/서비스가 서로 연결되어 있어야 합니다.


## <a name="configuration"></a>구성

[솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 IT Service Management Connector 솔루션을 추가합니다.

솔루션 갤러리에 표시되는 IT Service Management Connector 타일:

![커넥터 타일](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

성공적으로 추가한 후 **OMS** > **설정** > **연결된 원본** 아래에 IT Service Management Connector가 표시됩니다.

![ITSMC가 연결되어 있습니다.](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

## <a name="management-packs"></a>관리 팩
이 솔루션에는 관리 팩이 필요하지 않습니다.

## <a name="connected-sources"></a>연결된 소스

다음 ITSM 제품/서비스는 IT Service Management Connector를 통해 지원됩니다.

- [SCSM(System Center Service Manager)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>솔루션 사용

OMS IT Service Management Connector를 ITSM 서비스에 연결하면 Log Analytics 서비스가 연결된 ITSM 제품/서비스로부터 데이터를 수집하기 시작합니다.

> [!NOTE]
> - IT Service Management Connector 솔루션에서 가져온 데이터는 **ServiceDesk_CL**이라는 이벤트로 Log Analytics에 표시됩니다.
- 이벤트에는 **ServiceDeskWorkItemType_s**라는 필드가 포함됩니다. 이 필드는 **ServiceDesk_CL** 이벤트에 포함된 작업 항목 데이터에 따라 인시던트 또는 변경 요청으로 값을 사용할 수 있습니다.

다음 정보는 IT Service Management Connector에서 수집된 데이터 예제를 보여 줍니다.

> [!NOTE]
> Log Analytics로 가져온 작업 항목 형식에 따라 **ServiceDesk_CL**에는 다음 필드가 포함됩니다.

**작업 항목:** **인시던트**  
ServiceDeskWorkItemType_s="Incident"

**필드**

- ServiceDeskConnectionName
- 서비스 데스크 ID
- 시스템 상태
- 긴급도
- 영향
- 우선 순위
- 에스컬레이션
- 만든 사람
- 해결한 사람
- 종결한 사람
- 원본
- 할당 대상
- Category
- 제목
- 설명
- 만든 날짜
- 종결한 날짜
- 해결한 날짜
- 마지막으로 수정한 날짜
- 컴퓨터


**작업 항목:** **변경 요청**

ServiceDeskWorkItemType_s="ChangeRequest"

**필드**
- ServiceDeskConnectionName
- 서비스 데스크 ID
- 만든 사람
- 종결한 사람
- 원본
- 할당 대상
- 제목
- 형식
- Category
- 시스템 상태
- 에스컬레이션
- 충돌 상태
- 긴급도
- 우선 순위
- 위험
- 영향
- 할당 대상
- 만든 날짜
- 종결한 날짜
- 마지막으로 수정한 날짜
- 요청한 날짜
- 예상된 시작 날짜
- 예상된 종료 날짜
- 작업 시작 날짜
- 작업 종료 날짜
- 설명
- 컴퓨터

ITSM 데이터에 대한 샘플 Log Analytics 화면:

![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector - 다른 OMS 솔루션과의 통합

현재 IT Service Management Connector는 서비스 맵 솔루션과의 통합을 지원합니다.

서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다. 추가 정보: [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)

이 통합을 사용하면 다음 예제와 같이 ITSM 솔루션에서 만든 서비스 데스크 항목을 볼 수 있습니다.

![통합된 솔루션 ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>OMS 경고에 대한 ITSM 작업 항목 만들기

OMS 경고의 경우 연결된 ITSM 원본에서 연결된 작업 항목을 만들 수 있습니다.  이렇게 하려면 다음 절차를 따릅니다.

1. **로그 검색** 창에서 로그 검색 쿼리를 실행하여 데이터를 봅니다. 쿼리 결과는 작업 항목의 원본입니다.
2. **로그 검색**에서 **경고**를 클릭하여 **경고 규칙 추가** 페이지를 엽니다.

    ![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. **경고 규칙 추가** 창에서 **이름**, **심각도**, **검색 쿼리** 및 **경고 조건**(기간/메트릭 측정)에 대한 필수 정보를 제공합니다.
4. **ITSM 작업**에 대해 **예**를 선택합니다.
5. **연결 선택** 목록에서 ITSM 연결을 선택합니다.
6. 필요에 따라 세부 정보를 제공합니다.
7. 이 경고의 각 로그 항목에 대해 별도 작업 항목을 만들려면 **각 로그 항목에 대해 개별 작업 항목 만들기** 확인란을 선택합니다.

    또는

    이 경고에서 제한없는 수의 로그 항목에 대해 하나의 작업 항목만 만들려면 이 확인란을 선택 취소합니다.

7. **Save**를 클릭합니다.

**경고** 아래에 OMS 경고가 만들어집니다. 지정된 경고 조건이 충족되면 해당 ITSM 연결의 작업 항목이 만들어집니다.

## <a name="create-itsm-work-items-from-oms-logs"></a>OMS 로그에서 ITSM 작업 항목 만들기

OMS 로그 검색을 사용하여 연결된 ITSM 원본에서 작업 항목을 만들 수 있습니다. 이렇게 하려면 다음 절차를 따릅니다.

1. **로그 검색**에서 필요한 데이터를 검색하고 세부 정보를 선택한 후 **작업 항목 만들기**를 클릭합니다.

    **ITSM 작업 항목 만들기** 창이 나타납니다.

    ![Log Analytics 화면](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   다음 세부 정보를 추가합니다.

  - **작업 항목 제목**: 작업 항목의 제목입니다.
  - **작업 항목 설명**: 새 작업 항목에 대한 설명입니다.
  - **영향을 받는 컴퓨터**: 이 로그 데이터가 있는 컴퓨터의 이름입니다.
  - **연결 선택**: 이 작업 항목을 만들려는 ITSM 연결입니다.
  - **작업 항목**: 작업 항목의 유형입니다.

3. 인시던트에 대한 기존 작업 항목 템플릿을 사용하려면 **템플릿을 기준으로 작업 항목 생성** 옵션에서 **예**를 클릭한 다음 **만들기**를 클릭합니다.

    또는

    사용자 지정한 값을 제공하려는 경우 **아니요**를 클릭합니다.

4. **연락처 유형**, **영향**, **긴급도**, **범주** 및 **하위 범주** 텍스트 상자에 적절한 값을 입력하고 **만들기**를 클릭합니다.

ITSM에 작업 항목이 생성됩니다. 이 항목은 OMS에서도 볼 수 있습니다.

## <a name="contact-us"></a>문의처

IT Service Management Connector에 대해 질문이나 의견이 있는 경우 [omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계
[ITSM 제품/서비스를 IT Service Management Connector에 추가](log-analytics-itsmc-connections.md).

