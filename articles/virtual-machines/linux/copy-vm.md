--- 
title: "Azure CLI 2.0을 사용하여 Linux VM 복사 | Microsoft Docs"
description: "Azure CLI 2.0 및 Managed Disks를 사용하여 Azure Linux VM의 복사본을 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/10/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 006123a4ab7d1c0208a7e5f33b6f05f9374511fc
ms.lasthandoff: 04/03/2017


---                    
               
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Azure CLI 2.0 및 Managed Disks를 사용하여 Linux VM의 복사본 만들기


이 문서에서는 Azure CLI 2.0 및 Azure Resource Manager 배포 모델을 사용하여 Linux를 실행하는 Azure VM(Virtual Machine)의 복사본을 만드는 방법에 대해 설명합니다. [Azure CLI 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 이러한 단계를 수행할 수도 있습니다.

[VHD에서 VM을 업로드하고 만들](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)수도 있습니다.

## <a name="prerequisites"></a>필수 조건


-   [Azure CLI 2.0](/cli/azure/install-az-cli2) 설치

-   [az login](/cli/azure/#login)을 사용하여 Azure 계정으로 로그인

-   복사본에 대한 원본으로 사용할 Azure VM 보유

## <a name="step-1-stop-the-source-vm"></a>1단계: 원본 VM 중지


[az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 원본 VM의 할당을 취소합니다.
다음 예제에서는 리소스 그룹 **myResourceGroup**에서 **myVM**이라는 VM의 할당을 취소합니다.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>2단계: 원본 VM 복사


VM을 복사하려면 기본 가상 하드 디스크의 복사본을 만듭니다. 이 프로세스를 통해 원본 VM과 동일한 구성 및 설정을 포함하는 특수한 VHD 및 관리 디스크를 만들 수 있습니다.

Azure Managed Disks에 대한 자세한 내용은 [Azure Managed Disks 개요](../../storage/storage-managed-disks-overview.md)를 참조하세요. 

1.  [az vm list](/cli/azure/vm#list)를 사용하여 각 VM 및 해당 OS 디스크의 이름을 나열합니다. 다음 예제에서는 리소스 그룹 **myResourceGroup**의 모든 VM을 나열합니다.
    
    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  [az disk create](/cli/azure/disk#create)를 사용하여 새로운 관리되는 디스크를 만들어 디스크를 복사합니다. 다음 예제에서는 관리되는 디스크 **myDisk**에서 디스크 **myCopiedDisk**를 만듭니다.

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ``` 

1.  이제 [az disk list](/cli/azure/disk#list)를 사용하여 리소스 그룹의 Managed Disks를 확인합니다. 다음 예제에서는 리소스 그룹 **myResourceGroup**의 관리되는 디스크를 나열합니다.

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

1.  ["3단계: 가상 네트워크 설정"](#step-3-set-up-a-virtual-network)으로 건너뜁니다.


## <a name="step-3-set-up-a-virtual-network"></a>3단계: 가상 네트워크 설정


다음 선택적 단계는 새 가상 네트워크, 서브넷, 공용 IP 주소 및 가상 NIC(네트워크 인터페이스 카드)를 만듭니다.

문제 해결 목적 또는 추가 배포를 위해 VM을 복사하는 경우 기존 가상 네트워크의 VM을 사용하지 않으려 할 수 있습니다.

복사된 VM에 대한 가상 네트워크 인프라를 만들려는 경우 다음 몇 단계를 수행합니다. 가상 네트워크를 만들지 않으려면 [4단계: VM 만들기](#step-4-create-a-vm)로 건너뜁니다.

1.  [az network vnet create](/cli/azure/network/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 **myVnet**이라는 가상 네트워크와 **mySubnet**이라는 서브넷을 만듭니다.

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

1.  [az network public-ip create](/cli/azure/network/public-ip#create)를 사용하여 공용 IP를 만듭니다. 다음 예제는 **mypublicdns**라는 DNS 이름으로 **myPublicIP**라는 공용 IP를 만듭니다. DNS 이름은 고유해야 하므로 고유한 이름을 제공하세요.

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

1.  [az network nic create](/cli/azure/network/nic#create)를 사용하여 NIC를 만듭니다.
    다음 예제에서는 **mySubnet** 서브넷에 연결된 **myNic**라는 NIC를 만듭니다.

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westus --name myNic \
        --vnet-name myVnet --subnet mySubnet --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>4단계: VM 만들기

이제 [az vm create](/cli/azure/vm#create)를 사용하여 VM을 만들 수 있습니다.

다음과 같이 복사된 관리되는 디스크를 OS 디스크(--attach-os-disk)로 사용하도록 지정합니다.

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 새 VM을 관리하는 방법을 알아보려면 [Azure Resource Manager의 Azure CLI 명령](../azure-cli-arm-commands.md)을 참조하세요.

