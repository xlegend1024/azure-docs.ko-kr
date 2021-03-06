---
title: "가상 네트워크에서 Azure API 관리를 사용하는 방법"
description: "Azure API 관리에서 가상 네트워크 연결을 설정하고 웹 서비스에 액세스하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: cf2a063acb2a36af2ff71f45159b2e23c9971b32
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>가상 네트워크에서 Azure API 관리를 사용하는 방법
Azure VNET(가상 네트워크)을 사용하면 인터넷에서 사용할 수 없고 라우팅할 있는 네트워크(액세스를 제어하는)에 다수의 Azure 리소스를 배치할 수 있습니다. 이러한 네트워크는 다양한 VPN 기술을 사용하여 온-프레미스 네트워크에 연결될 수 있습니다. Azure 가상 네트워크에 대해 자세히 알아보려면 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)부터 참조하세요.

Azure API Management가 네트워크 내의 백 엔드 서비스에 액세스할 수 있도록 VNET(가상 네트워크) 내에 배포할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다.

> [!NOTE]
> Azure API Management는 클래식 및 Azure Resource Manager Vnet을 모두 지원합니다.
>
>

## <a name="enable-vpn"> </a>VNET 연결 사용
> [!NOTE]
> VNET 연결은 **프리미엄** 및 **개발자** 계층에서 사용할 수 있습니다. 계층 간에 전환하려면 Azure Portal 포털에서 API Management 서비스를 열고 **크기 조정 및 가격 책정** 탭을 엽니다. **가격 책정 계층** 섹션에서 프리미엄 계층을 선택하고 저장을 클릭합니다.
>

VNET 연결을 사용하려면 Azure Portal 포털에서 API Management 서비스를 열고 **가상 네트워크** 페이지를 엽니다.

![API 관리의 가상 네트워크 메뉴][api-management-using-vnet-menu]

원하는 액세스 유형을 선택합니다.

* **외부**: 외부 부하 분산 장치를 통해 공용 인터넷에서 API 관리 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

![공용 피어링][api-management-vnet-public]

* **내부**: 내부 부하 분산 장치를 통해 가상 네트워크 내에서만 API 관리 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

![개인 피어링][api-management-vnet-private]

이제 API 관리 서비스가 프로비전되는 모든 지역 목록이 보입니다. VNET 및 모든 지역에 대한 서브넷을 선택합니다. 이 목록은 사용자가 구성하고 있는 하위 지역에 설정된 Azure 구독에서 사용할 수 있는 클래식 및 Resource Manager 가상 네트워크로 채워집니다.

> [!NOTE]
> 위 다이어그램에서 **서비스 끝점**에는 게이트웨이/프록시, 게시자 포털, 개발자 포털, GIT 및 직접 관리 끝점이 포함되어 있습니다.
> 위 다이어그램에서 **관리 끝점**은 Azure Portal 및 Powershell을 통해 구성을 관리하기 위해 서비스에서 호스팅하는 끝점입니다.
> 또한 이 다이어그램은 다양한 끝점에 대한 IP 주소를 보여주지만, API Management 서비스**만** 이 구성된 호스트 이름에서 응답합니다.

> [!IMPORTANT]
> Resource Manager VNET에 Azure API Management 인스턴스를 배포할 때 서비스는 Azure API Management 인스턴스를 제외한 다른 리소스가 포함되어 있는 전용 서브넷에 있어야 합니다. 다른 리소스가 포함된 Resource Manager VNET 서브넷에 Azure API Management 인스턴스를 배포하려고 하면 배포가 실패합니다.
>
>

![VPN 선택][api-management-setup-vpn-select]

화면 위쪽에서 **저장**을 클릭합니다.

> [!NOTE]
> API Management 인스턴스의 VIP 주소는 VNET이 활성화되거나 비활성화될 때마다 변경됩니다.  
> VIP 주소는 API Management를 **외부**에서 **내부**로 또는 그 반대로 이동할 때도 변경됩니다.
>


> [!IMPORTANT]
> VNET에서 API Management를 제거하거나 배포된 것을 변경할 경우 이전에 사용한 VNET는 최대 4시간 동안 잠긴 상태를 유지할 수 있습니다. 이 기간에는 VNET를 삭제하거나 새 리소스를 배포할 수 없게 됩니다.

## <a name="enable-vnet-powershell"> </a>PowerShell cmdlet을 사용하여 VNET 연결 사용
PowerShell cmdlet을 사용하여 VNET 연결을 사용하도록 설정할 수도 있습니다.

* **VNET 내에서 API Management 서비스 만들기**: cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement)를 사용하여 VNET 내에서 Azure API Management 서비스를 만듭니다.

* **VNET 내에서 기존 API Management 서비스 배포**: cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment)를 사용하여 가상 네트워크 내에서 Azure API Management 서비스를 이동합니다.

## <a name="connect-vnet"> </a>가상 네트워크 내에서 호스트되는 웹 서비스에 연결
API 관리 서비스가 VNET에 연결된 후에는 공용 서비스에 액세스하는 것과 동일하게 VNET 내에서 백 엔드 웹 서비스에 액세스할 수 있습니다. 새 API를 만들거나 기존 API를 편집할 때 **웹 서비스 URL** 필드에 웹 서비스의 로컬 IP 주소 또는 호스트 이름(DNS 서버가 VNET에 대해 구성된 경우)을 입력하면 됩니다.

![VPN에서 API 추가][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>일반적인 네트워크 구성 문제
다음은 가상 네트워크로 API Management 서비스를 배포하는 동안 발생할 수 있는 일반적인 구성 오류의 목록입니다.

* **사용자 지정 DNS 서버 설치**: API Management 서비스는 여러 API 서비스에 따라 달라집니다. API Management가 사용자 지정 DNS 서버를 사용하는 VNET에서 호스트되는 경우 해당 Azure 서비스의 호스트 이름을 확인해야 합니다. 사용자 지정 DNS 설정에 대한 [이](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 지침을 따르세요. 아래의 포트 테이블 및 기타 네트워크 요구 사항을 참조하세요.

> [!IMPORTANT]
> VNET에 사용자 지정 DNS 서버를 사용하고 있는 경우에는 API Management 서비스를 배포하기 **전에** 설정하는 것이 좋습니다. 그렇지 않으면 [네트워크 구성 작업 적용](https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementservices#ApiManagementServices_ApplyNetworkConfigurationUpdates)을 실행하여 DNS 서버를 변경할 때마다 API Management 서비스를 업데이트해야 합니다.

* **API Management에 필요한 포트**: API Management가 배포된 인바운드 및 아웃바운드 트래픽은 [네트워크 보안 그룹][Network Security Group]을 사용하여 제어할 수 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNET에서 API Management를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.

API 관리 서비스 인스턴스가 VNET에 호스트된 경우 다음 표의 포트가 사용됩니다.

| 소스/대상 포트 | 방향 | 전송 프로토콜 | 목적 | 원본 / 대상 | 액세스 유형 |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |인바운드 |TCP |API 관리에 대한 클라이언트 통신 |인터넷 / VIRTUAL_NETWORK |외부 |
| * / 3443 |인바운드 |TCP |Azure Portal 및 Powershell용 관리 끝점 |인터넷 / VIRTUAL_NETWORK |외부 및 내부 |
| * / 80, 443 |아웃바운드 |TCP |Azure 저장소 및 Azure Service Bus에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| * / 1433 |아웃바운드 |TCP |Azure SQL에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| * / 11000 - 11999 |아웃바운드 |TCP |Azure SQL V12에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| * / 14000 - 14999 |아웃바운드 |TCP |Azure SQL V12에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| * / 5671 |아웃바운드 |AMQP |이벤트 허브 정책 및 모니터링 에이전트에 대한 로그의 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| 6381 - 6383 / 6381 - 6383 |인바운드 및 아웃바운드 |UDP |Redis 캐시에 대한 종속성 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |외부 및 내부 |-
| * / 445 |아웃바운드 |TCP |GIT의 Azure 파일 공유에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| * / * | 인바운드 |TCP |Azure 인프라 부하 분산 장치 | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK |외부 및 내부 |

* **SSL 기능**: SSL 인증서 체인 작성 및 유효성 검사를 사용하도록 설정하려면 API Management에서 ocsp.msocsp.com, mscrl.microsoft.com 및 crl.microsoft.com으로의 아웃바운드 네트워크 연결이 필요합니다.

* **메트릭 및 상태 모니터링**: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net 도메인에서 해결하는 Azure 모니터링 끝점에 아웃바운드 네트워크 연결.

* **Express Route 설정**: 고객의 일반적인 구성은 온-프레미스 흐름 대신 아웃바운드 인터넷 트래픽을 강제하는 기본 경로(0.0.0.0/0)로 정의되어 있습니다. 이 트래픽 흐름은 변함없이 Azure API Management와의 연결을 끊습니다. 그 이유는 아웃바운드 트래픽이 온-프레미스에서 막히거나 다양한 Azure 끝점에서 더 이상 작동하지 않는 인식 불가능한 주소 집합으로 NAT되기 때문입니다. 해결책은 하나의(또는 그 이상) [UDR][UDRs](사용자 정의 경로)을 Azure API Management를 포함하는 서브넷에 정의하는 것입니다. UDR이 정의한 특정 서브넷 경로는 기본 경로대신 적용됩니다.
  가능하면 다음 구성을 사용하는 것이 좋습니다.
 * Express 경로 구성은 0.0.0.0/0을 보급하고 기본적으로 모든 아웃바운드 트래픽 온-프레미스를 강제로 터널링합니다.
 * Azure API Management를 포함하는 서브넷에 적용된 UDR은 다음 홉 형식을 갖는 인터넷으로 0.0.0.0/0을 정의합니다.
 이러한 단계의 결합된 효과는 서브넷 수준 UDR이 강제된 터널링에 ExpressRoute를 담당하고 Azure API Management에서 아웃바운드 인터넷 액세스를 보장합니다.

>[!WARNING]  
>**공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 잘못**한 ExpressRoute 구성에서는 Azure API Management가 지원되지 않습니다. 구성된 공용 피어링이 있는 Express 경로 구성은 다양한 Microsoft Azure IP 주소 범위 집합에 대해 Microsoft에서 경로 보급을 받습니다. 이러한 주소 범위의 교차 보급을 개인 피어링 경로에 잘못한 경우 Azure API Management 인스턴스의 서브넷에서 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라에 강제 터널링되는 잘못된 최종 결과를 발생시킵니다. 이 네트워크 흐름은 Azure API Management를 중단합니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로 이어진 교차 보급 경로를 중지합니다.

## <a name="limitations"> </a>제한 사항
* API 관리 인스턴스가 포함된 서브넷은 다른 Azure 리소스 종류를 포함할 수 없습니다.
* 서브넷과 API 관리 서비스는 동일한 구독에 있어야 합니다.
* API 관리 인스턴스가 포함된 서브넷은 구독 간에 이동할 수 없습니다.
* 내부 가상 네트워크를 사용할 때는 [RFC 1918](https://tools.ietf.org/html/rfc1918)에 명시된 범위의 내부 IP 주소만 사용할 수 있으며 공용 IP 주소를 제공할 수 없습니다.
* 내부 가상 네트워크가 구성된 다중 하위 지역 API Management 배포의 경우 사용자가 DNS를 소유하므로 자신의 부하 분산을 직접 관리해야 합니다.


## <a name="related-content"> </a>관련 콘텐츠
* [VPN Gateway를 사용하여 가상 네트워크를 백 엔드에 연결](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-ipsecike-vpn-tunnel)
* [다양한 배포 모델에서 가상 네트워크 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md

