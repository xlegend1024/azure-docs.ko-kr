---
title: "Azure Virtual Network 피어링 - 포털 | Microsoft Docs"
description: "Azure Portal을 사용하여 가상 네트워크 피어링을 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: a4a770ec6f258c62864cd3fcc79b33b5cac38ace
ms.openlocfilehash: 8f05957e8c40dee8ceb7fb1355d8d8776935cf5a
ms.lasthandoff: 02/24/2017


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Azure 포털을 사용하여 가상 네트워크 피어링 만들기
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Azure Portal을 사용하여 시나리오에 따라 VNet 피어링을 만들려면 다음 단계를 완료합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. VNet 피어링을 설정하려면 두 개의 VNet 간에 각 방향에 하나씩 두 개의 링크를 만들어야 합니다. 먼저 VNet1-VNet2에 VNet 피어링 링크를 만들 수 있습니다. Azure Portal에서 **찾아보기** > **가상 네트워크 선택**을 클릭합니다.

    ![Azure 포털에서 VNet 피어링 만들기](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. **가상 네트워크** 블레이드에서 다음 그림에 표시된 것처럼 *VNET1*을 선택하고 **피어링**을 클릭한 다음 **추가**를 클릭합니다.

    ![피어링 선택](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. **피어링 추가** 블레이드에서 **이름**에 *LinkToVnet2*를 입력하고 구독 및 피어 **가상 네트워크** *VNET2*를 선택한 다음 **확인**을 클릭합니다.

    ![VNet에 링크](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. VNet 피어링 링크가 만들어지면 다음 그림에 표시된 대로 링크 상태가 표시됩니다.

    ![링크 상태](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. VNET2-VNet1에 VNet 피어링 링크를 만듭니다. **가상 네트워크** 블레이드에서 다음 그림에 표시된 것처럼 *VNET2*을 선택하고 **피어링**을 클릭한 다음 **추가**를 클릭합니다.

    ![다른 VNet에서 피어링](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. **피어링 추가** 블레이드에서 **이름**에 *LinkToVnet1*을 입력하고 구독을 선택하고 **가상 네트워크**에 *VNET1*을 선택한 다음 **확인**을 클릭합니다.

    ![가상 네트워크 만들기 타일](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. VNet 피어링 링크가 만들어지면 다음 그림에 표시된 대로 링크 상태가 표시됩니다.

    ![마지막 링크 상태](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. **LinkToVnet2**에 대한 상태를 확인하면 이제 *연결됨*으로 변경되었습니다.  
    
    ![마지막 링크 상태 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [!NOTE]
    > 링크를 모두 연결해야만 VNET 피어링이 설정됩니다.
    > 
    > 

각 링크에 대한 몇 가지 구성 가능한 속성이 있습니다.

| 옵션 | 설명 | 기본값 |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |피어링 VNet의 주소 공간이 Virtual_network 태그의 일부로 포함되는지 여부입니다. |예 |
| AllowForwardedTraffic |피어링된 VNet에서 시작하지 않는 트래픽이 허용되거나 삭제되는지의 여부입니다. |아니요 |
| AllowGatewayTransit |피어링 VNet이 VNet 게이트웨이를 사용할 수 있습니다. |아니요 |
| UseRemoteGateways |피어링한 VNet 게이트웨이를 사용합니다. 피어링 VNet에는 게이트웨이가 구성되어 있어야 하고 AllowGatewayTransit을 선택해야 합니다. 구성된 게이트웨이가 있는 경우 이 옵션을 사용할 수 없습니다. |아니요 |

VNet 피어링의 링크 각각에는 이전 속성 집합이 있습니다. 포털에서 **VNet 피어링** 링크를 클릭하고 사용할 수 있는 옵션을 변경한 다음 **저장**을 클릭하여 변경을 적용할 수 있습니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 이 예제에서 사용자A에는 구독A에 대한 관리 권한이 있으며 사용자B에는 구독B에 대한 관리 권한이 있습니다. 두 구독은 동일한 Azure Active Directory 테넌트에 연결되어 있습니다. 서로 다른 Azure Active Directory 테넌트에 연결된 구독 간에는 피어링을 만들 수 없습니다.
3. 포털에서 **찾아보기**를 클릭하고 **가상 네트워크**를 선택합니다. 피어링을 설정하려는 VNet을 클릭합니다.
4. 선택한 VNet에 대한 블레이드에서 다음 그림에 표시된 것처럼 **액세스 제어**를 클릭한 다음 **추가**를 클릭합니다.

    ![시나리오 2 찾아보기](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. **액세스 추가** 블레이드에서 역할을 클릭하고 **네트워크 참가자**를 선택하고 **사용자 추가**를 클릭하고 사용자B 로그인 이름을 입력한 다음 확인을 클릭합니다.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

5. 구독B에 대한 권한 있는 사용자인 사용자B로 Azure Portal에 로그인합니다. 다음 그림에 표시된 것처럼 이전 단계를 따라 사용자A를 네트워크 참여자 역할에 추가합니다.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [!NOTE]
    > 브라우저에서 두 사용자 세션에 로그오프하고 로그온하여 권한 부여가 성공적으로 사용되었는지 확인할 수 있습니다.
    >
    >

    > [!IMPORTANT]
    > 만드는 피어링이 Azure Resource Manager 배포 모델을 통해 만든 두 VNet 사이에 있는 경우 이 섹션의 나머지 단계를 계속 진행합니다. 두 VNet이 다른 배포 모델을 통해 만들어진 경우 이 섹션의 나머지 단계를 생략하고 이 문서의 [다양한 배포 모델을 통해 생성된 가상 네트워크 피어링](#x-model) 섹션에 나오는 단계를 따르세요.

6. 포털에 사용자A로 로그인하고 VNET3 블레이드로 이동하여 **피어링**을 클릭한 다음 **내 리소스 ID를 알고 있습니다** 확인란을 선택하고 다음 예제에 표시된 형식으로 VNET5에 대한 리소스 ID를 입력합니다.
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/virtualNetworks/{VNETname}
   
    ![리소스 ID](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. 포털에 사용자B로 로그인하고 이전 단계에 따라 VNET5에서 VNet3에 피어링 링크를 만듭니다.
   
    ![리소스 ID 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. 피어링이 설정됩니다. VNet3에 연결된 모든 VM은 VNet5에 연결된 모든 VM과 통신할 수 있어야 합니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 첫 번째 단계로 VNET 피어링 링크가 HubVnet에서 VNET1으로 연결됩니다. 링크에 전달 트래픽 허용 옵션을 선택하지 않았습니다.
   
    ![기본 피어링](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. 다음 단계로 VNET1에서 HubVnet으로 피어링 링크를 만들 수 있습니다. 전달 트래픽 허용 옵션을 선택하지 않았습니다.
   
    ![기본 피어링](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. 피어링을 설정하면 이 [문서](virtual-network-create-udr-arm-ps.md) 를 참조하고 UDR(사용자 정의 경로)를 정의하여 그 기능을 사용하는 가상 어플라이언스를 통해 VNet1 트래픽을 리디렉션할 수 있습니다. 경로에 다음 홉 주소를 지정하면 피어링된 VNet HubVNet에서 가상 어플라이언스의 IP 주소를 설정할 수 있습니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. *동일한* 구독의 다양한 배포 모델을 통해 배포된 VNet 간에 피어링을 만드는 경우 3단계로 건너뜁니다. *다른* 구독에 있는 다른 배포 모델을 통해 배포된 VNet 간에 VNet 피어링을 만드는 기능은 **미리 보기** 릴리스에 제공됩니다. 미리 보기 릴리스의 기능은 일반 릴리스 기능과 동일한 수준의 안정성 및 서비스 수준 계약을 제공하지 않습니다. 다른 구독의 다른 배포 모델을 통해 배포된 VNet 간에 피어링을 만드는 경우 먼저 다음 단계를 완료해야 합니다.
    - PowerShell에서 `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` 및 `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network` 명령을 입력하여 Azure 구독에서 미리 보기 기능을 등록합니다. 이 단계는 포털에서 완료될 수 없습니다.
    - 이 문서의 [구독간 피어링](#x-sub) 섹션에 나오는 1-6단계를 완료합니다.
3. 이 시나리오에서 VNET 피어링을 설정하려면 Azure Resource Manager에서 가상 네트워크에서 클래식의 가상 네트워크에 하나의 링크를 만들어야 합니다. 즉 **VNET1**에서 **VNET2**입니다. 포털에서 **찾아보기** >를 클릭하고 **Virtual Networks**를 선택합니다.
4. 가상 네트워크 블레이드에서 **VNET1**을 선택합니다. **피어링**을 클릭한 다음 **추가**를 클릭합니다.
5. **피어링 추가** 블레이드에서 링크의 이름을 지정합니다. 여기에서는 **LinkToVNet2**라고 합니다. 피어 세부 정보에서 **클래식**을 선택합니다.
6. 구독 및 피어 Virtual Network **VNET2**를 선택합니다. 그런 후 확인을 클릭합니다.

    ![Vnet1-VNet2 연결](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
7. 이 VNet 피어링 링크가 만들어지면 두 가상 네트워크는 피어링되며 다음을 볼 수 있습니다.

    ![피어링 연결 확인](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>VNet 피어링 제거
1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 가상 네트워크 블레이드로 이동하여 피어링을 클릭하고 제거하려는 링크를 클릭한 다음 **삭제**를 클릭합니다.

    ![삭제1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. VNET 피어링에서 한 링크만 제거하면 피어링 링크 상태의 연결이 끊깁니다.

    ![삭제2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. 이 상태에서는 피어링 링크 상태가 시작됨으로 변경될 때까지 링크를 다시 만들 수 없습니다. VNET 피어링을 다시 만들기 전에 링크를 모두 제거하는 것이 좋습니다.


