---
title: "Azure 활동 로그 개요 | Microsoft Docs"
description: "Azure 활동 로그란 무엇이고 Azure 구독 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: johnkem
ms.translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: c123b76b0e4c95cfebcc79063fb1c3a27efc8646
ms.contentlocale: ko-kr
ms.lasthandoff: 03/31/2017


---
# <a name="overview-of-the-azure-activity-log"></a>Azure 활동 로그 개요
**Azure 활동 로그** 는 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공하는 로그입니다. 활동 로그는 구독에 대한 제어 평면 이벤트를 보고하므로 이전에 "감사 로그" 또는 "작업 로그"로 알려져 있습니다. 활동 로그를 통해 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 '무엇을, 누가, 언제'를 판단할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기(GET) 작업 또는 Classic/"RDFE" 모델을 사용하는 리소스에 대한 작업이 포함되지 않습니다.

![활동 로그 및 다른 종류의 로그 ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

그림 1: 활동 로그 및 다른 종류의 로그

활동 로그는 [진단 로그](monitoring-overview-of-diagnostic-logs.md)와 다릅니다. 활동 로그는 외부에서 리소스의 작업에 대한 데이터를 제공합니다. 진단 로그는 리소스에 의해 발생하고 해당 리소스의 작업에 대한 정보를 제공합니다.

Azure 포털, CLI, PowerShell cmdlet 및 Azure Monitor REST API를 사용하여 활동 로그에서 이벤트를 검색할 수 있습니다.


> [!WARNING]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. 클래식/RDFE 모델을 사용하여 리소스를 추적하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. 클래식 포털의 클래식 리소스 유형이나 Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 클래식 포털에서만 액세스될 수 있습니다.
>
>

활동 로그를 소개하는 다음 동영상을 봅니다.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="what-you-can-do-with-the-activity-log"></a>활동 로그에서 수행할 수 있는 작업
활동 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

![Azure 활동 로그](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* [활동 로그 이벤트를 트리거 해제하는 경고를 만듭니다.](monitoring-activity-log-alerts.md)
* [타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 **Event Hub**](monitoring-stream-activity-logs-event-hubs.md)로 스트림합니다.
* [**PowerBI 콘텐츠 팩**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 PowerBI에서 분석합니다.
* [보관 또는 수동 검사를 위해 **저장소 계정**에 저장합니다](monitoring-archive-activity-log.md). **로그 프로필**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* **Azure 포털**에서 쿼리하고 봅니다.
* PowerShell Cmdlet, CLI 또는 REST API를 통해 쿼리합니다.


로그를 내보내는 것과 동일한 구독에 위치하지 않는 저장소 계정 또는 Event Hub 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자에게는 두 구독에 대한 적절한 RBAC 액세스가 있어야 합니다.

## <a name="export-the-activity-log-with-log-profiles"></a>로그 프로필과 함께 활동 로그 내보내기
**로그 프로필** 은 활동 로그를 내보내는 방식을 제어합니다. 로그 프로필을 사용하여 다음을 구성할 수 있습니다.

* 활동 로그를 보낼 위치(저장소 계정 또는 이벤트 허브)
* 보낼 이벤트 범주(쓰기, 삭제, 작업) *로그 프로필 및 활동 로그 이벤트에서 "범주"의 의미는 다릅니다. 로그 프로필에서 "범주"는 작업 유형(쓰기, 삭제 작업)을 나타냅니다. 활동 로그 이벤트에서 "범주" 속성은 원본 또는 이벤트의 유형(예: 관리, ServiceHealth, 경고 등)을 나타냅니다.*
* 내보낼 하위 지역(위치)
* 활동 로그를 저장소 계정에 유지해야 하는 기간
    - 보존이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.
    - 보존 정책이 설정되었지만 저장소 계정에 로그를 저장할 수 없는 경우(예를 들어 Event Hubs 또는 OMS 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.
    - 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

이러한 설정은 포털의 활동 로그 블레이드에서 "내보내기" 옵션을 통해 구성할 수 있습니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell cmdlet 또는 CLI를 사용하여 프로그래밍 방식으로 구성할 수도 있습니다. 하나의 구독에는 하나의 로그 프로필만 포함할 수 있습니다.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Azure 포털을 사용하여 로그 프로필 구성
활동 로그를 이벤트 허브로 스트림하거나 Azure 포털의 "내보내기" 옵션을 사용하여 저장소 계정에 저장할 수 있습니다.

1. 포털 왼쪽에 있는 메뉴를 사용하여 **활동 로그** 블레이드로 이동합니다.

    ![포털에서 활동 로그로 이동](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 블레이드 맨 위에서 **내보내기** 단추를 클릭합니다.

    ![포털에서 내보내기 단추](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 표시되는 블레이드에서 다음을 선택할 수 있습니다.  
  * 이벤트를 내보내려는 지역
  * 이벤트를 저장하려는 저장소 계정
  * 저장소에서 이러한 이벤트를 유지하려는 기간(일). 0일로 설정하면 로그를 계속 유지합니다.
  * 이러한 이벤트를 스트리밍하기 위해 Event Hub를 만들 Service Bus 네임스페이스입니다.

     ![활동 로그 내보내기 블레이드](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. **저장** 을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet을 사용하여 로그 프로필 구성
#### <a name="get-existing-log-profile"></a>기존 로그 프로필 가져오기
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>로그 프로필 추가
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 이름 |예 |로그 프로필의 이름입니다. |
| StorageAccountId |아니요 |활동 로그를 저장할 저장소 계정의 리소스 ID입니다. |
| serviceBusRuleId |아니요 |이벤트 허브를 만들 서비스 버스 네임스페이스의 서비스 버스 규칙 ID입니다. `{service bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다. |
| 위치 |예 |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
| RetentionInDays |예 |이벤트를 유지해야 하는 일 수는 1에서 2147483647 사이입니다. 0 값은 로그를 무기한(영원히) 저장합니다. |
| 범주 |아니요 |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |

#### <a name="remove-a-log-profile"></a>로그 프로필 제거
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Azure 플랫폼 간 CLI를 사용하여 로그 프로필 구성
#### <a name="get-existing-log-profile"></a>기존 로그 프로필 가져오기
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` 속성은 로그 프로필의 이름이어야 합니다.

#### <a name="add-a-log-profile"></a>로그 프로필 추가
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| name |예 |로그 프로필의 이름입니다. |
| storageId |아니요 |활동 로그를 저장할 저장소 계정의 리소스 ID입니다. |
| serviceBusRuleId |아니요 |이벤트 허브를 만들 서비스 버스 네임스페이스의 서비스 버스 규칙 ID입니다. `{service bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다. |
| 위치 |예 |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
| RetentionInDays |예 |이벤트를 유지해야 하는 일 수는 1에서 2147483647 사이입니다. 0 값은 로그를 무기한(영원히) 저장합니다. |
| 범주 |아니요 |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |

#### <a name="remove-a-log-profile"></a>로그 프로필 제거
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>이벤트 스키마
활동 로그의 각 이벤트는 다음과 같은 JSON Blob을 포함합니다.

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| 요소 이름 | 설명 |
| --- | --- |
| authorization |이벤트의 RBAC 속성 Blob입니다. 일반적으로 "action", "role" 및 "scope" 속성이 포함됩니다. |
| caller |가용성을 기반으로 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. |
| channels |"Admin", "Operation" 값 중 하나여야 합니다. |
| CorrelationId |일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 correlationId를 공유하는 이벤트입니다. |
| 설명 |이벤트의 정적 텍스트 설명입니다. |
| eventDataId |이벤트의 고유 식별자입니다. |
| eventSource |이 이벤트를 생성한 Azure 서비스 또는 인프라의 이름입니다. |
| httpRequest |Http 요청을 설명하는 Blob입니다. 일반적으로 "clientRequestId", "clientIpAddress" 및 "method"(PUT 등의 HTTP 메서드) 포함. |
| 최소 수준 |이벤트의 수준입니다. 다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자 이름입니다. |
| resourceUri |영향을 받는 리소스의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| status |작업의 상태를 설명하는 문자열. 일반적인 값: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |일반적으로 해당 REST 호출의 HTTP 상태 코드이지만 다음과 같이 하위 상태를 설명하는 다른 문자열을 포함할 수 있습니다. 예를 들어 이러한 일반적인 값은 다음과 같습니다. OK(HTTP 상태 코드: 200), Created(HTTP 상태 코드: 201), Accepted(HTTP 상태 코드: 202), No Content(HTTP 상태 코드: 204), Bad Request(HTTP 상태 코드: 400), Not Found(HTTP 상태 코드: 404), Conflict(HTTP 상태 코드: 409), Internal Server Error(HTTP 상태 코드: 500), Service Unavailable(HTTP 상태 코드:503), Gateway Timeout(HTTP 상태 코드: 504). |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |
| nextLink |결과가 여러 응답으로 세분화되는 경우 다음 결과 집합을 가져올 연속 토큰입니다. 일반적으로 200개가 넘는 레코드가 있는 경우 필요합니다. |

## <a name="next-steps"></a>다음 단계
* [활동 로그(이전의 감사 로그)에 대해 자세히 알아보기](../azure-resource-manager/resource-group-audit.md)
* [Azure 활동 로그를 이벤트 허브로 스트림](monitoring-stream-activity-logs-event-hubs.md)

