---
title: Azure Metadata Service - Scheduled Events | Microsoft Docs
description: "Virtual Machine에 강력한 영향을 미치는 이벤트가 발생하기 전에 조치를 취합니다."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 627aa117ded0aaa519052d4ea1a1995ba2e363ee
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Azure 메타데이터 서비스 - 예정된 이벤트(미리 보기)

> [!NOTE] 
> 사용 약관에 동의하게 되면 미리 보기를 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)을 참조하세요.
>

Azure 메타데이터 서비스의 하위 서비스 중 하나인 예정된 이벤트는 예정된 이벤트(예: 다시 부팅)에 대한 정보를 나타내므로 응용 프로그램에서 이벤트를 준비하고 중단을 제한할 수 있습니다. 이 기능은 PaaS 및 IaaS를 포함한 모든 Azure Virtual Machine 유형에 사용할 수 있습니다. 예정된 이벤트는 Virtual Machine에 예방 작업을 수행하고 이벤트의 영향을 최소화할 수 있는 시간을 제공합니다. 


## <a name="introduction---why-scheduled-events"></a>소개 - Scheduled Events가 필요한 이유

예정된 이벤트를 사용하여 사용자 서비스에 미치는 영향을 제한하는 단계를 수행할 수 있습니다. 복제 기술을 사용하여 상태를 유지 관리하는 다중 인스턴스 워크로드는 여러 인스턴스에서 자주 발생하는 중단에 취약할 수 있습니다. 이러한 중단으로 인해 비용이 많이 드는 작업(예: 인덱스 다시 빌드) 또는 복제본 손실이 발생할 수 있습니다. 다른 많은 경우에서 정상 종료 시퀀스를 사용하면 전체 서비스 가용성이 향상됩니다. 예를 들어 메모리 내 트랜잭션을 완료(또는 취소)하고 다른 작업을 클러스터의 다른 VM에 다시 할당(수동 장애 조치)하고, 부하 분산 장치 풀에서 Virtual Machine을 제거합니다. 예정된 이벤트에 대해 관리자에게 알리거나 그러한 이벤트를 로깅하는 것만으로도 클라우드에 호스팅된 응용 프로그램의 서비스 효율성을 개선하는 데 도움이 되는 경우가 있습니다.
Azure 메타데이터 서비스는 다음 사용 사례에서 예정된 이벤트를 나타냅니다.
-    플랫폼 시작 유지 관리(예: 호스트 OS 롤아웃)
-    사용자 시작 호출(예: 사용자에 의한 VM 다시 시작 또는 다시 배포)


## <a name="scheduled-events---the-basics"></a>Scheduled Events - 기본 사항  

Azure Metadata Service는 VM 내에서 REST 끝점을 사용하여 Virtual Machines 실행에 대한 정보를 공개합니다. 이 정보는 라우팅할 수 없는 IP를 통해 사용할 수 있으므로 VM 외부에 공개되지 않습니다.

### <a name="scope"></a>범위
Scheduled Events는 클라우드 서비스의 모든 Virtual Machines 또는 가용성 집합의 모든 Virtual Machines에 나타납니다. 따라서 이벤트의 **리소스** 필드를 확인하여 영향을 받을 VM을 식별해야 합니다. 

### <a name="discover-the-endpoint"></a>끝점 검색
Virtual Machine을 VNet(Virtual Network) 내에서 만든 경우 메타데이터 서비스는 169.254.169.254라는 라우팅할 수 없는 IP에서 사용할 수 있습니다. 아니면 클라우드 서비스 및 클래식 VM에 대한 기본 사례에서 사용할 끝점을 검색하는 데 추가 논리가 필요합니다. 이 샘플을 참조하여 [호스트 끝점을 검색]하는 방법을 알아봅니다(https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>버전 관리 
인스턴스 메타데이터 서비스에는 버전이 있습니다. 버전은 필수이며 최신 버전은 2017-03-01입니다.

> [!NOTE] 
> 예약된 이벤트의 이전 미리 보기 릴리스는 api-version으로 {최신 버전}을 지원했습니다. 이 형식은 더 이상 지원되지 않으며 향후 사용되지 않을 예정입니다.
>


### <a name="using-headers"></a>헤더 사용
Metadata Service를 쿼리할 때 *Metadata: true* 헤더를 제공해야 합니다. 

### <a name="enable-scheduled-events"></a>Scheduled Events 사용
처음으로 예약된 이벤트를 호출하면 Azure는 Virtual Machine의 기능을 암시적으로 사용하도록 설정합니다. 결과적으로 최대 2분인 첫 번째 호출에서 지연된 응답을 예상해야 합니다.

### <a name="testing-your-logic-with-user-initiated-operations"></a>사용자 시작 작업을 사용하여 논리 테스트
사용자 논리를 테스트하려면 Azure Portal, API, CLI 또는 PowerShell을 사용하여 예정된 이벤트가 발생하는 작업을 시작할 수 있습니다. 가상 컴퓨터를 다시 시작하면 다시 부팅과 같은 이벤트 형식으로 예정된 이벤트가 발생합니다. 가상 컴퓨터를 다시 배포하면 다시 배포와 같은 이벤트 형식으로 예정된 이벤트가 발생합니다.
예정된 이벤트를 사용하면 정상적으로 응용 프로그램을 종료하는 데 더 많은 시간이 걸리기 때문에 두 경우 모두 사용자 시작 작업을 완료하는 데에도 더 많은 시간이 걸립니다. 

## <a name="using-the-api"></a>API 사용

### <a name="query-for-events"></a>이벤트 쿼리
다음과 같이 호출하여 예약된 이벤트를 쿼리할 수 있습니다.

    curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01


응답에는 예약된 이벤트의 배열이 포함됩니다. 빈 배열은 현재 예약된 이벤트가 없음을 의미합니다.
예약된 이벤트가 있는 경우 응답에 다음과 같은 이벤트의 배열이 포함됩니다. 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }
    
### <a name="event-properties"></a>이벤트 속성
|속성  |  설명 |
| - | - |
| EventId |이벤트의 전역적으로 고유한 식별자. <br><br> 예제: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 이벤트로 인해 발생하는 결과입니다. <br><br> 값 <br><ul><li> <i>중지</i>: 몇 초 동안 Virtual Machine을 일시 중지하도록 예약합니다. 메모리, 열려 있는 파일 또는 네트워크 연결에는 영향을 미치지 않습니다. <li> <i>다시 부팅</i>: Virtual Machine을 다시 부팅하도록 예약합니다(메모리 초기화됨).<li> <i>다시 배포</i>: Virtual Machine을 다른 노드로 이동하도록 예약합니다(임시 디스크 손실됨). |
| ResourceType | 이벤트가 영향을 주는 리소스 형식입니다. <br><br> 값 <ul><li>VirtualMachine|
| 리소스| 이벤트가 영향을 주는 리소스 목록입니다. <br><br> 예제: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| 이벤트 상태 | 이벤트의 상태입니다. <br><br> 값 <ul><li><i>예약됨:</i> <i>NotBefore</i> 속성에 지정된 시간 이후 시작하도록 이벤트를 예약합니다.<li><i>시작됨</i>: 이벤트가 시작되었습니다.</i>
| NotBefore| 이 시간이 지난 후 이벤트가 시작될 수 있습니다. <br><br> 예제: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>이벤트 예약
각 이벤트는 이벤트 유형에 따라 향후 최소한의 시간으로 예약됩니다. 이 시간은 이벤트의 <i>NotBefore</i> 속성에 반영됩니다. 

|EventType  | 최소 공지 |
| - | - |
| 중지| 15분 |
| Reboot | 15분 |
| 재배포 | 10분 |

### <a name="starting-an-event-expedite"></a>이벤트 시작(신속 이동)

예정된 이벤트에 대해 알게 되고 정상 종료를 위한 논리를 완료하면 **POST** 호출을 통해 Azure에서 더 빨리 이동할 수 있음(가능한 경우)을 나타낼 수 있습니다. 
 

## <a name="powershell-sample"></a>PowerShell 샘플 

다음 샘플은 예약된 이벤트에 대한 메타데이터 서버를 읽고 이벤트를 승인합니다.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>C\# 샘플 
다음 샘플은 메타데이터 서비스와 통신하기 위한 클라이언트 쪽 API입니다
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
Scheduled Events는 다음 데이터 구조를 사용하여 구문 분석될 수 있습니다. 

```csharp
    public class ScheduledEventsDocument
    {
        public string DocumentIncarnation;
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime? NotBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public string DocumentIncarnation;
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

클라이언트를 사용하여 이벤트를 검색, 처리 및 확인하는 샘플 프로그램:   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
        {
            DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
        };
        
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Python 샘플 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>다음 단계 
[Azure에서 가상 컴퓨터에 대한 계획된 유지 관리](linux/planned-maintenance.md)
[인스턴스 메타데이터 서비스](virtual-machines-instancemetadataservice-overview.md)

