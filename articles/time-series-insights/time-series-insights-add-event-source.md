---
title: "Azure Time Series Insights 환경에 이벤트 원본 추가 | Microsoft Docs"
description: "이 자습서에서는 Time Series Insights 환경에 이벤트 원본을 연결합니다."
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/26/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>Azure Portal을 사용하여 Time Series Insights 환경에 대한 이벤트 원본 만들기

Time Series Insights 이벤트 원본은 Azure Event Hubs 같은 이벤트 브로커에서 파생됩니다. Time Series Insights는 이벤트 원본에 직접 연결하여 데이터 스트림을 수집하므로 사용자가 코드를 작성할 필요가 전혀 없습니다. 현재 Time Series Insights는 Azure Event Hubs와 Azure IoT Hubs를 지원하며, 향후 더 많은 이벤트 원본이 추가될 예정입니다.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>환경에 이벤트 원본을 추가하는 단계

1.    [Azure 포털](https://portal.azure.com)에 로그인합니다.
2.    Azure Portal의 왼쪽에 있는 메뉴에서 “모든 리소스”를 클릭합니다.
3.    Time Series Insights 환경을 선택합니다.

  ![Time Series Insights 이벤트 원본 만들기](media/add-event-source/getstarted-create-eventsource1.png)

4.    “이벤트 원본”을 선택하고 “+ 추가”를 클릭합니다.

  ![Time Series Insights 이벤트 원본 만들기 - 세부 정보](media/add-event-source/getstarted-create-eventsource2.png)

5.    이벤트 원본의 이름을 지정합니다. 이 이름은 이 이벤트 원본에서 오는 모든 이벤트와 연결되어 쿼리 시 사용할 수 있습니다.
6.    현재 구독의 이벤트 허브 리소스 목록에서 이벤트 허브를 선택하거나 가져오기 옵션 "이벤트 허브 설정 수동으로 제공"을 선택하여 다른 구독에 있는 이벤트 허브를 지정할 수 있습니다. 이벤트는 JSON 형식으로 게시되어야 합니다.
7.    이벤트 허브에서 읽기 권한이 있는 정책을 선택합니다.
8.    이벤트 허브 소비자 그룹을 지정합니다.

  > [!IMPORTANT]
  > 이 소비자 그룹을 다른 서비스(예: Stream Analytics 작업 또는 다른 Time Series Insights 환경)에서 사용하지 못하게 합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스에 부정적인 영향을 미칩니다. 소비자 그룹으로 “$Default”를 사용할 경우 다른 읽기 권한자가 재사용할 수도 있습니다.

9.    “만들기”를 클릭합니다.

이벤트 원본이 생성되면 Time Series Insights가 자동으로 데이터를 환경으로 스트리밍하기 시작합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 원본으로 [이벤트 보내기](time-series-insights-send-events.md)
* [Time Series Insights 포털](https://insights.timeseries.azure.com)에서 환경 보기

