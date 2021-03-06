---
title: "Azure Virtual Network 피어링 - PowerShell | Microsoft Docs"
description: "Azure PowerShell을 사용하여 가상 네트워크 피어링을 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 57991a59feb784fd2e5277e893110de2a776a060
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>Azure PowerShell을 사용하여 가상 네트워크 피어링 만들기
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

PowerShell을 사용하여 VNet 피어링을 만들려면 다음 단계를 수행하세요.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.

    > [!NOTE]
    > VNet 피어링을 관리하기 위한 PowerShell cmdlet은 [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. 가상 네트워크 개체 읽기:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. VNet 피어링을 설정하려면 각 방향에 하나씩 두 개의 링크를 만들어야 합니다. 다음 단계에서는 먼저 VNet1-VNet2에 VNet 피어링 링크를 만듭니다.

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    반환되는 출력:
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. 이 단계에서는 VNet2-VNet1에 VNet 피어링 링크를 만듭니다.

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    반환되는 출력:

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. VNet 피어링 링크가 만들어지면 다음 명령을 입력하여 링크 상태를 확인합니다.

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    반환되는 출력:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    VNet 피어링에 대한 몇 가지 구성 가능한 속성이 있습니다.
   
   | 옵션 | 설명 | 기본값 |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |피어링 VNet의 주소 공간이 Virtual_network 태그의 일부로 포함되는지 여부입니다. |예 |
   | AllowForwardedTraffic |피어링된 VNet에서 시작하지 않는 트래픽이 허용되거나 삭제되는지의 여부입니다. |아니요 |
   | AllowGatewayTransit |피어링 VNet이 VNet 게이트웨이를 사용할 수 있습니다. |아니요 |
   | UseRemoteGateways |피어링한 VNet 게이트웨이를 사용합니다. 피어링 VNet에는 게이트웨이가 구성되어 있어야 하고 AllowGatewayTransit을 선택해야 합니다. 구성된 게이트웨이가 있는 경우 이 옵션을 사용할 수 없습니다. |아니요 |

    VNet 피어링의 링크 각각에는 이전 속성 집합이 있습니다. 예를 들어, `AllowVirtualNetworkAccess`를 VNet 피어링 링크 VNet2-VNet1에 대해 *True*로 설정하고 다른 방향의 VNet 피어링 링크에 대해 *False*로 설정할 수 있습니다.

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    변경 후에 `Get-AzureRmVirtualNetworkPeering`을 실행하여 속성 값을 다시 확인할 수 있습니다. 이전 cmdlet을 실행한 후에 `AllowForwardedTraffic`을 *True*로 설정한 것을 확인할 수 있습니다.

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    피어링이 설정되면 VM은 두 Vnet에서 서로 통신할 수 있어야 합니다. 기본적으로 `AllowVirtualNetworkAccess`는 *True*이며 VNet 피어링은 적절한 ACL을 프로비전하여 VNet 간의 통신을 허용합니다. 특정 서브넷 또는 가상 컴퓨터 간의 연결을 차단하도록 NSG(네트워크 보안 그룹) 규칙을 적용하여 두 VNet 간의 액세스를 세부적으로 제어할 수 있습니다. NSG에 대해 자세히 알아보려면 [네트워크 보안 그룹](virtual-networks-create-nsg-arm-ps.md)을 참조하세요.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

PowerShell을 사용하여 구독에 VNet 피어링을 만들려면 다음 단계를 완료하세요.

1. 구독-A에 대한 권한이 있는 사용자-A 계정으로 Azure에 로그인하고 다음 cmdlet을 실행합니다.

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    필수 요구 사항은 아닙니다. 요청이 일치하면 사용자가 해당하는 VNet에 대한 피어링 요청을 개별적으로 요구하는 경우에도 피어링을 설정할 수 있습니다. 다른 VNet의 권한있는 사용자를 로컬 VNet의 사용자로 추가하면 설치가 수월해집니다.
2. 구독-B에 대한 권한이 있는 사용자-B 계정으로 Azure에 로그인하고 다음 cmdlet을 실행합니다.

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > 만드는 피어링이 Azure Resource Manager 배포 모델을 통해 만든 두 VNet 사이에 있는 경우 이 섹션의 나머지 단계를 계속 진행합니다. 두 VNet이 다른 배포 모델을 통해 만들어진 경우 이 섹션의 나머지 단계를 생략하고 이 문서의 [다양한 배포 모델을 통해 생성된 가상 네트워크 피어링](#x-model) 섹션에 나오는 단계를 따르세요.

3. 사용자-A의 로그인 세션에서 다음 명령을 실행합니다.

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. 사용자-B의 로그인 세션에서 다음 cmdlet을 실행합니다.

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. 피어링을 설정한 후에 VNet3의 VM은 VNet5의 모든 VM과 통신할 수 있어야 합니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 이 시나리오에서는 다음 PowerShell cmdlet을 실행하여 VNet 피어링을 설정합니다. `AllowForwardedTraffic` 속성을 *True*로 설정하고 VNET1을 HubVnet에 연결하여 피어링 VNet 주소 공간 외부에서 인바운드 트래픽을 허용해야 합니다.

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. 피어링을 설정하면 이 [문서](virtual-network-create-udr-arm-ps.md) 를 참조하고 UDR(사용자 정의 경로)를 정의하여 그 기능을 사용하는 가상 어플라이언스를 통해 VNet1 트래픽을 리디렉션할 수 있습니다. 경로에 다음 홉 주소를 지정하면 피어링된 VNet HubVNet에서 가상 어플라이언스의 IP 주소를 설정할 수 있습니다. 다음은 샘플입니다.

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. *동일한* 구독의 다양한 배포 모델을 통해 배포된 VNet 간에 피어링을 만드는 경우 2단계로 건너뜁니다. *다른* 구독에 있는 다른 배포 모델을 통해 배포된 VNet 간에 VNet 피어링을 만드는 기능은 **미리 보기** 릴리스에 제공됩니다. 미리 보기 릴리스의 기능은 일반 릴리스 기능과 동일한 수준의 안정성 및 서비스 수준 계약을 제공하지 않습니다. 다른 구독의 다른 배포 모델을 통해 배포된 VNet 간에 피어링을 만드는 경우 먼저 다음 단계를 완료해야 합니다.
    - PowerShell에서 `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` 및 `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network` 명령을 입력하여 Azure 구독에 미리 보기 기능을 등록합니다.
    - [구독간 피어링](#x-sub) 섹션에 나오는 1-2단계를 완료합니다.
2. 다음 명령을 입력하여 Azure Resource Manager 가상 네트워크인 **VNET1**의 가상 네트워크 개체를 읽습니다.

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. 이 시나리오에서 VNet 피어링을 설정하려면 하나의 링크, 특히 **VNET1**에서 **VNET2**로의 링크만 필요합니다. 이 단계에서는 클래식 VNet의 리소스 ID를 알아야 합니다. 리소스 그룹 ID 형식은 다음 예제와 같습니다.

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    SubscriptionID, ResourceGroupName 및 VirtualNetworkName을 적절한 이름으로 바꿔야 합니다.

    이 작업은 다음 명령을 입력하여 수행할 수 있습니다.

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. VNet 피어링 링크가 만들어지면 다음 아래 출력에 표시된 대로 링크 상태를 확인할 수 있습니다.
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>VNet 피어링 제거
1. VNet 피어링을 제거하려면 다음과 같은 cmdlet을 실행해야 합니다.

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    두 링크를 제거하려면 다음 명령을 입력합니다.

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. VNET 피어링에서 한 링크만 제거하면 피어링 링크 상태가 *연결 끊김*이 됩니다. 이 상태에서는 피어링 링크 상태가 *시작됨*으로 변경될 때까지 링크를 다시 만들 수 없습니다. VNet 피어링을 다시 만들기 전에 링크를 모두 제거하는 것이 좋습니다.


