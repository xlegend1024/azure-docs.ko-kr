---
title: "Azure 공용 IP 주소 | Microsoft Docs"
description: "공용 IP 주소를 생성, 수정 및 삭제하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9e65a61b2b156611e998f266068ab5e1e306143d
ms.lasthandoff: 04/27/2017


---

# <a name="public-ip-addresses"></a>공용 IP 주소

공용 IP 주소 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소는 자체적으로 구성 가능한 설정을 사용하는 리소스입니다. 공용 IP 주소를 다른 Azure 리소스에 할당하면 다음을 수행할 수 있습니다.
- Azure Virtual Machines(VM), Azure Virtual Machine Scale Sets, Azure VPN Gateway 및 인터넷 연결 Azure Load Balancer와 같은 리소스에 대한 인바운드 인터넷 연결
- 네트워크 주소 변환(NAT)되지 않은 아웃바운드 인터넷 연결 - 예를 들어 VM은 할당된 공용 IP 주소 없이 인터넷으로 아웃바운드 통신할 수 있지만, 해당 주소는 Azure에서 변환된 네트워크 주소입니다. Azure 리소스의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

이 문서에서는 공용 IP 주소를 사용하는 방법에 대해 설명합니다. 이 문서는 Azure Resource Manager 배포 모델을 통해 배포된 리소스에 적용됩니다. 공용 IP 주소는 클래식 배포 모델을 통해 배포된 리소스에 할당될 수 있지만, 해당 주소는 Resource Manager를 통해 적용되는 주소와 다르게 적용됩니다. 두 모델의 차이점에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

이 문서의 나머지 섹션에서는 모든 공용 IP 주소 관련 작업을 수행하는 단계를 나열합니다. 각 섹션에서는 다음과 같이 나열합니다.
- Azure Portal에서 작업을 수행하는 단계 - 단계를 수행하려면 [Azure Portal](http://portal.azure.com)에 로그인해야 합니다. 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 등록합니다.
- 명령에 대한 명령 참조에 연결되는 링크가 있는 Azure PowerShell을 사용하여 작업을 수행하는 명령 - [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 문서의 단계를 수행하여 PowerShell을 설치하고 구성합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- 명령에 대한 명령 참조에 연결되는 링크가 있는 Azure CLI(명령줄 인터페이스)를 사용하여 작업을 수행하는 명령 - [Azure CLI 2.0 설치 및 구성 방법](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 수행하여 Azure CLI를 설치합니다. CLI 명령에 대한 도움말을 보려면 `az <command> -h`를 입력합니다.

공용 IP 주소에는 명목 요금이 부과됩니다. 가격을 보려면 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한됩니다. 제한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요. 

공용 IP 주소 리소스를 생성, 변경 또는 삭제하려면 다음 섹션의 단계를 수행합니다.

## <a name="create"></a>공용 IP 주소 만들기

공용 IP 주소를 만들려면 다음 단계를 수행합니다.
1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소**를 클릭합니다.
3. 표시되는 **공용 IP 주소** 블레이드에서 **+ 추가**를 클릭합니다.
4. 표시되는 **공용 IP 주소 만들기** 블레이드에서 다음 설정 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.

    |**설정**|Required?|**세부 정보**|
    |---|---|---|
    |**Name**|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
    |**IP 주소 할당**|예|**동적:** 동적 주소는 VM에 연결된 NIC에 공용 IP 주소를 연결하고 VM을 처음 시작한 후에만 할당됩니다. NIC가 연결된 VM이 중지(할당 취소)되면 동적 주소가 변경될 수 있습니다. VM을 다시 부팅하거나 중지하는 경우(하지만 할당 취소하지 않은 경우) 주소가 동일하게 유지됩니다. **고정:** 고정 IP 주소를 만들면 고정 주소가 할당됩니다. VM이 중지(할당 취소)된 상태에 있더라도 고정 주소는 변경되지 않습니다. 이 주소는 NIC를 삭제할 때만 해제됩니다. NIC를 만든 후에는 할당 방법을 변경할 수 있습니다.|
    |**유휴 제한 시간(분)**|아니요|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다.|
    |**DNS 이름 레이블**|아니요|이름을 만드는 Azure 위치 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure 공용 DNS 서비스는 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만드는 데 제공하는 이름에 *location.cloudapp.azure.com*(여기서 location은 선택한 위치임)을 추가합니다. |
    |**구독**|예|공용 IP 주소를 연결하려는 리소스와 동일한 구독에 있어야 합니다.|
    |**리소스 그룹**|예|공용 IP 주소를 연결하려는 리소스와 동일하거나 다른 리소스 그룹에 있을 수 있습니다.|
    |**위치**|예|공용 IP 주소를 연결하려는 리소스와 동일한 위치에 있어야 합니다.|

|**도구**|**명령**|
|---|---|
|**CLI**|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>공용 IP 주소 설정 변경 또는 삭제

공용 IP 주소를 변경하거나 삭제하려면 다음 단계를 수행합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소**를 클릭합니다.
3. 표시되는 **공용 IP 주소** 블레이드에서 설정을 변경하거나 삭제하려는 공용 IP 주소의 이름을 클릭합니다.
4. 공용 IP 주소에 대해 표시되는 블레이드에서 공용 IP 주소를 삭제할지 또는 변경할지에 따라 다음 옵션 중 하나를 수행합니다.
    - **삭제:** 공용 IP 주소를 삭제하려면 블레이드의 **개요** 섹션에서 **삭제**를 클릭합니다. 주소가 현재 IP 구성과 연결되어 있으면 삭제할 수 없습니다. 주소가 현재 구성과 연결되어 있으면 **분리**를 클릭하여 IP 구성에서 주소를 분리합니다.
    - **변경:** **구성**을 클릭합니다. 이 문서의 [공용 IP 주소 만들기](#create) 섹션의 4단계 정보를 사용하여 설정을 변경합니다. 할당을 고정에서 동적으로 변경하려면 먼저 연결된 IP 구성에서 공용 IP 주소를 분리해야 합니다. 그런 다음 할당 방법을 동적으로 변경하고 **연결**을 클릭하여 동일한 IP 구성이나 다른 구성으로 해당 IP 주소를 연결하거나 분리할 수 있습니다. 공용 IP 주소를 분리하려면 **개요** 섹션에서 **분리**를 클릭합니다.

>[!WARNING]
>할당 방법을 고정에서 동적으로 변경하면 공용 IP 주소에 할당된 IP 주소가 손실됩니다. Azure 공용 DNS 서버는 고정 또는 동적 주소와 모든 DNS 이름 레이블(사용자가 정의한 경우) 간의 매핑을 유지하지만, VM을 중지(할당 취소)된 상태에서 시작할 때 동적 IP 주소가 변경될 수 있습니다. 주소가 변경되지 않도록 하려면 고정 IP 주소를 할당합니다.

|**도구**|**명령**|
|---|---|
|**CLI**|업데이트: [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update), 삭제: [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|업데이트: [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json), 삭제: [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress)|

## <a name="next-steps"></a>다음 단계
다음 Azure 리소스를 만들 때 공용 IP 주소를 할당합니다.

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 컴퓨터
- [인터넷 연결 Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 응용 프로그램 게이트웨이](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway를 사용하여 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Set](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

