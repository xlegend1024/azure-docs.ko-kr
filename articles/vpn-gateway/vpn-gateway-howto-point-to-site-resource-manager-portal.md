---
title: "지점 및 사이트 간을 사용하여 Azure Virtual Network에 컴퓨터 연결: Portal | Microsoft Docs"
description: "Resource Manager 및 Azure 포털을 사용하여 지점 및 사이트 간 VPN Gateway 연결을 만들어 Azure Virtual Network에 안전하게 연결합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 20c0db9c5f3f3586ac2df8657a7b2eb2114ed6b8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Azure 포털을 사용하여 VNet에 지점 및 사이트 간 연결 구성

이 문서에서는 Resource Manager 배포 모델에서 Azure Portal을 사용하여 지점 및 사이트 간 연결로 VNet을 만드는 방법을 보여줍니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure 포털](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure 포털(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. 지점 및 사이트 간 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다. P2S VPN 연결은 기본 Windows VPN 클라이언트를 사용하여 클라이언트 컴퓨터에서 시작됩니다. 클라이언트 연결은 인증서를 사용하여 인증합니다. 


![지점 및 사이트 간 다이어그램](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

P2S 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. P2S는 SSTP(Secure Socket Tunneling Protocol)를 통해 VPN 연결을 만듭니다. 서버 쪽에서 SSTP 버전 1.0, 1.1 및 1.2를 지원하며, 클라이언트에서 사용할 버전을 결정합니다. Windows 8.1 이상에서는 기본적으로 SSTP 버전 1.2를 사용합니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [지점 및 사이트 간 FAQ](#faq)를 참조하세요.

P2S 연결에는 다음이 필요합니다.

* RouteBased VPN 게이트웨이입니다.
* Azure에 업로드된 루트 인증서에 대한 공개 키(.cer 파일) - 신뢰할 수 있는 인증서로 간주되며 인증에 사용됩니다.
* 루트 인증서에서 생성되고 연결할 각 클라이언트 컴퓨터에 설치된 클라이언트 인증서 - 클라이언트 인증에 사용됩니다.
* 연결하는 모든 클라이언트 컴퓨터에 VPN 클라이언트 구성 패키지를 생성하여 설치해야 합니다. 클라이언트 구성 패키지는 VNet에 연결하는 데 필요한 정보와 함께 운영 체제에 이미 있는 기본 VPN 클라이언트를 구성합니다.

### <a name="example"></a>예제 값

다음 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

* **이름: VNet1**
* **주소 공간: 192.168.0.0/16**<br>이 예제에서는 하나의 주소 공간만 사용합니다. VNet에는 둘 이상의 주소 공간을 포함할 수 있습니다.
* **서브넷 이름: FrontEnd**
* **서브넷 주소 범위: 192.168.1.0/24**
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **GatewaySubnet: 192.168.200.0/24**
* **가상 네트워크 게이트웨이 이름: VNet1GW**
* **게이트웨이 유형: VPN**
* **VPN 유형: 경로 기반**
* **공용 IP 주소: VNet1GWpip**
* **연결 형식: 지점 및 사이트 간**
* **클라이언트 주소 풀: 172.16.201.0/24**<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 클라이언트 주소 풀에서 IP 주소를 받습니다.

## <a name="createvnet"></a>1 - 가상 네트워크 만들기

시작하기 전에 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다. 연습으로 이 구성을 만드는 경우 [예제 값](#example)을 참조하세요.

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>2 - 주소 공간 및 서브넷 지정

VNet이 만들어지면 여기에 다른 주소 공간 및 서브넷을 추가할 수 있습니다.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3 - 게이트웨이 서브넷 추가

가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서비스는 게이트웨이 서브넷에 지정된 IP 주소를 사용합니다. 향후 추가적인 구성 요구 사항을 수용하기에 충분한 IP 주소를 제공하도록 가능하면 /28 또는 /27 CIDR 블록을 사용하여 게이트웨이 서브넷을 만듭니다.

이 섹션의 스크린샷은 참조 예제로 제공됩니다. 구성에 필요한 값에 해당하는 GatewaySubnet 주소 범위를 사용해야 합니다.

### <a name="to-create-a-gateway-subnet"></a>게이트웨이 서브넷을 만들려면

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4 - DNS 서버 지정(선택 사항)

가상 네트워크를 만든 후에 DNS 서버의 IP 주소를 추가하여 이름 확인을 처리할 수 있습니다. 지정된 DNS 서버는 연결 중인 리소스에 대한 이름을 확인할 수 있는 DNS 서버여야 합니다. 이후 단계에서 생성하는 VPN 클라이언트 구성 패키지는 이 설정에서 지정한 DNS 서버의 IP 주소를 포함합니다. 나중에 DNS 서버 목록을 업데이트해야 하는 경우 새 목록을 반영하는 새 VPN 클라이언트 구성 패키지를 생성하고 설치할 수 있습니다.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>5 - 가상 네트워크 게이트웨이 만들기

지점 및 사이트 간 연결에는 다음 설정이 필요합니다.

* 게이트웨이 유형: VPN
* VPN 유형: 경로 기반

### <a name="to-create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이를 만들려면

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>6 - 인증서 생성

인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하기 위해 Azure에 의해 사용됩니다. Azure에 루트 인증서의 공개 키 정보를 업로드합니다. 그러면 해당 공개 키가 '신뢰할 수 있는 키'로 간주됩니다. 클라이언트 인증서는 신뢰할 수 있는 루트 인증서에서 생성한 다음 각 클라이언트 컴퓨터의 [인증서 - 현재 사용자/개인] 인증서 저장소에 설치해야 합니다. 인증서는 VNet에 대한 연결을 시작할 때 해당 클라이언트를 인증하는 데 사용됩니다. 인증서 생성 및 설치에 대한 자세한 내용은 [지점 및 사이트 간 인증서](vpn-gateway-certificates-point-to-site.md)를 참조하세요.

### <a name="getcer"></a>1단계 - 루트 인증서용 .cer 파일 가져오기

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2단계 - 클라이언트 인증서 생성

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>7 - 클라이언트 주소 풀 추가

클라이언트 주소 풀은 사용자가 지정한 개인 IP 주소 범위입니다. P2S를 통해 연결하는 클라이언트는 이 범위의 IP 주소를 받습니다. 연결 원본이 되는 온-프레미스 위치 또는 연결 대상이 되는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용합니다.

1. 가상 네트워크 게이트웨이가 생성된 후에는 가상 네트워크 게이트웨이 블레이드의 **설정** 섹션으로 이동합니다. **설정** 섹션에서 **지점 및 사이트 간 구성**을 클릭하여 **구성** 블레이드를 엽니다.

  ![지점 및 사이트 간 블레이드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. 자동으로 채워진 범위를 삭제한 다음 사용하려는 개인 IP 주소 범위를 추가할 수 있습니다. **저장**을 클릭하여 설정을 확인하고 저장합니다.

  ![클라이언트 주소 풀](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>8 - 루트 인증서 .cer 파일 업로드

게이트웨이를 만든 후에 신뢰할 수 있는 루트 인증서의 .cer 파일(공개 키 정보 포함)을 Azure로 업로드할 수 있습니다. a.cer 파일이 업로드되면 Azure는 이.cer 파일을 사용하여 신뢰할 수 있는 루트 인증서에서 생성된 클라이언트 인증서를 설치한 클라이언트를 인증합니다. 필요한 경우 나중에 신뢰할 수 있는 루트 인증서 파일을 최대 20개까지 추가로 업로드할 수 있습니다. 

1. **루트 인증서** 섹션의 **지점 및 사이트 간 구성** 블레이드에 인증서가 추가됩니다.  
2. 루트 인증서를 Base-64 인코딩된 X.509(.cer) 파일로 내보내야 합니다. 이 형식으로 내보내야 텍스트 편집기에서 인증서를 열 수 있습니다.
3. 메모장과 같은 텍스트 편집기에서 인증서를 엽니다. 인증서 데이터를 복사하는 경우 캐리지 리턴 또는 줄 바꿈 없이 하나의 연속 줄로 텍스트를 복사합니다. 캐리지 리턴 및 줄 바꿈을 보려면 ‘기호 표시/모든 문자 표시'에 대한 텍스트 편집기의 보기를 수정해야 할 수도 있습니다. 하나의 연속 줄로만 다음 섹션을 복사합니다.

  ![인증서 데이터](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. 인증서 데이터를 **공용 인증서 데이터** 필드에 붙여 넣습니다. 인증서의 **이름을 지정**한 다음 **저장**을 클릭합니다. 최대 20개의 신뢰할 수 있는 루트 인증서를 추가할 수 있습니다.

  ![인증서 업로드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>9 - VPN 클라이언트 구성 패키지 설치

지점 및 사이트 간 VPN을 사용하여 VNet에 연결하려면 각 클라이언트에서 기본 Windows VPN 클라이언트를 구성하는 패키지를 설치해야 합니다. 구성 패키지는 가상 네트워크에 연결하는 데 필요한 설정을 사용하여 네이티브 Windows VPN 클라이언트를 구성하고 VNet에 DNS 서버를 지정한 경우 클라이언트가 이름 확인을 위해 사용하는 DNS 서버 IP 주소를 포함합니다. 지정된 DNS 서버를 변경하는 경우에 클라이언트 구성 패키지를 생성한 후에 새 클라이언트 구성 패키지를 생성하여 클라이언트 컴퓨터에 설치하도록 합니다.

버전이 클라이언트의 아키텍처와 일치하는 한 각 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 이 문서 끝의 [지점 및 사이트 간 연결 FAQ](#faq)를 참조하세요.

### <a name="step-1---download-the-client-configuration-package"></a>1단계 - 클라이언트 구성 패키지 다운로드

1. **지점 및 사이트 간 구성** 블레이드에서 **VPN 클라이언트 다운로드**를 클릭하여 **VPN 클라이언트 다운로드** 블레이드를 엽니다. 패키지 생성에 1~2분이 걸립니다.

  ![VPN 클라이언트 다운로드 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. 클라이언트에 맞는 올바른 패키지를 선택한 다음 **다운로드**를 클릭합니다. 구성 패키지 파일을 저장합니다. 가상 네트워크에 연결하는 각 클라이언트 컴퓨터에 VPN 클라이언트 구성 패키지를 설치합니다.

  ![VPN 클라이언트 다운로드 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>2단계 - 클라이언트 구성 패키지 설치

1. 구성 파일을 가상 네트워크에 연결할 컴퓨터에 로컬로 복사합니다. 
2. .exe 파일을 두 번 클릭하여 클라이언트 컴퓨터에 패키지를 설치합니다. 구성 패키지를 만들었지만 이 패키지가 서명되지 않았기 때문에 경고가 표시될 수 있습니다. Windows SmartScreen 팝업이 나타나면 **자세한 정보**(왼쪽), **실행**을 차례로 클릭하여 패키지를 설치합니다.
3. 클라이언트 컴퓨터에 패키지를 설치합니다. Windows SmartScreen 팝업이 나타나면 **자세한 정보**(왼쪽), **실행**을 차례로 클릭하여 패키지를 설치합니다.
4. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.

## <a name="installclientcert"></a>10 - 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치하는 경우 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다. 일반적으로 이 인증서를 두 번 클릭하고 설치하기만 하면 됩니다. 자세한 내용은 [내보낸 클라이언트 인증서 설치](vpn-gateway-certificates-point-to-site.md#install)를 참조하세요.

## <a name="connect"></a>11 - Azure에 연결

1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. **계속**을 클릭하여 상승된 권한을 사용합니다.

2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.

  ![VPN 클라이언트에서 Azure에 연결](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. 연결이 설정되었습니다.

  ![설정된 연결](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

연결 문제가 있으면 다음 항목을 확인합니다.

- **사용자 인증서 관리**를 열고 **신뢰할 수 있는 루트 인증 기관\인증서**로 이동합니다. 루트 인증서가 나열되어 있는지 확인합니다. 인증이 작동하려면 루트 인증서가 있어야 합니다. [가능한 경우 인증 경로에 있는 인증서 모두 포함] 기본값을 사용하여 .pfx 클라이언트 인증서를 내보낼 때는 루트 인증서 정보도 내보내집니다. 클라이언트 인증서를 설치하면 루트 인증서도 클라이언트 컴퓨터에 설치됩니다. 

- 엔터프라이즈 CA 솔루션을 사용하여 발급된 인증서를 사용하고 인증에 문제가 발생하는 경우 클라이언트 인증서에서 인증 순서를 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**으로 이동하여 인증 목록 순서를 확인할 수 있습니다. 목록의 첫 번째 항목으로 '클라이언트 인증'이 표시되는지 확인합니다. 그렇지 않으면 목록에서 첫 번째 항목으로 클라이언트 인증을 가진 사용자 템플릿을 기반으로 하는 클라이언트 인증서를 발급해야 합니다.


## <a name="verify"></a>12 - 연결 확인

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음 예제와 비슷합니다.
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled


P2S를 통해 가상 시스템에 연결하는 데 문제가 있으면 'ipconfig'를 사용하여 연결하는 컴퓨터의 이더넷 어댑터에 할당된 IPv4 주소를 확인합니다. IP 주소가 연결하는 VNet의 주소 범위 또는 VPNClientAddressPool의 주소 범위 내에 있는 경우 이 주소를 겹치는 주소 공간이라고 합니다. 주소 공간이 이러한 방식으로 겹치면 네트워크 트래픽이 Azure에 도달하지 않고 로컬 네트워크에 남아 있습니다. 네트워크 주소 공간이 겹치지 않지만 여전히 VM에 연결할 수 없는 경우 [VM에 대한 원격 데스크톱 연결 문제 해결 ](../virtual-machines/windows/troubleshoot-rdp-connection.md)을 참조하세요.

## <a name="connectVM"></a>가상 컴퓨터에 연결

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>신뢰할 수 있는 루트 인증서 추가 또는 제거

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 루트 인증서를 제거하면 해당 루트에서 생성된 인증서가 있는 클라이언트를 인증하지 못하게 됩니다. 따라서 연결할 수도 없습니다. 클라이언트를 인증하고 연결하려는 경우 Azure에 (업로드된)신뢰할 수 있는 루트 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

### <a name="to-add-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 추가하려면

Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 자세한 내용은 이 문서에서 [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile) 섹션을 참조하세요.

### <a name="to-remove-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 제거하려면

1. 신뢰할 수 있는 루트 인증서를 제거하려면 가상 네트워크 게이트웨이에 대한 **지점 및 사이트 간 구성** 블레이드로 이동합니다.
2. 블레이드의 **루트 인증서** 섹션에서 제거할 인증서를 찾습니다.
3. 인증서 옆의 줄임표를 클릭한 다음 '제거'를 클릭합니다.

## <a name="revokeclient"></a>클라이언트 인증서 해지

클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지하면 루트 인증서에서 생성된 다른 인증서를 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### <a name="to-revoke-a-client-certificate"></a>클라이언트 인증서를 해지하려면

해지 목록에 지문을 추가하여 클라이언트 인증서를 해지할 수 있습니다.

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [인증서의 지문을 검색하는 방법](https://msdn.microsoft.com/library/ms734695.aspx)을 참조하세요.
2. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다.
3. 가상 네트워크 게이트웨이 **지점 및 사이트 간 구성** 블레이드로 이동합니다. [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile)하는 데 사용한 것과 동일한 블레이드입니다.
4. **해지된 인증서** 섹션에서 인증서에 대한 이름(인증서 CN이 아니어도 됨)을 입력합니다.
5. 지문 문자열을 **지문** 필드에 복사하여 붙여 넣습니다.
6. 지문의 유효성이 검사되고 해당 지문이 해지 목록에 자동으로 추가됩니다. 목록이 업데이트되고 있음을 알리는 메시지가 화면에 표시됩니다. 
7. 업데이트가 완료된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에서 인증서가 더 이상 유효하지 않다고 하는 메시지를 받습니다.

## <a name="faq"></a>지점 및 사이트 간 FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요. 네트워킹 및 가상 컴퓨터에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/linux/azure-vm-network-overview.md)를 참조하세요.
