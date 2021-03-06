---
title: "Azure CLI 1.0을 사용하여 Linux VM의 크기를 조정하는 방법 | Microsoft Docs"
description: "VM 크기를 변경하여 Linux 가상 컴퓨터의 규모를 확장하거나 축소하는 방법"
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 72f5a3cd6463befd5108040ed166984281bfc5f0
ms.lasthandoff: 04/03/2017


---

# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Azure CLI 1.0을 사용하여 Linux VM의 크기 조정

## <a name="overview"></a>개요

VM(가상 컴퓨터)을 프로비전한 후 [VM 크기][vm-sizes]를 변경하여 VM의 크기를 확장 또는 축소할 수 있습니다. 경우에 따라 먼저 VM의 할당을 취소해야 합니다. 이는 VM을 호스트하는 하드웨어 클러스터에서 새 크기를 사용할 수 없는 경우에 발생할 수 있습니다.

이 문서에서는 [Azure CLI][azure-cli]를 사용하여 Linux VM의 크기를 조정하는 방법을 보여 줍니다.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#resize-a-linux-vm) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="resize-a-linux-vm"></a>Linux VM 크기 조정
VM의 크기를 조정하려면 다음 단계를 수행합니다.

1. 다음 CLI 명령을 실행합니다. 이 명령은 VM이 호스트되는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 나열합니다.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. 원하는 크기가 목록에 나열된 경우 다음 명령을 실행하여 VM 크기를 조정합니다.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    이 과정에서 VM이 다시 시작됩니다. 다시 시작한 후 기존의 OS 및 데이터 디스크는 다시 매핑됩니다. 임시 디스크에 있는 모든 내용이 손실됩니다.
   
    `--enable-boot-diagnostics` 옵션을 사용하면 [진단 부팅][boot-diagnostics]으로 시작과 관련된 모든 오류를 기록할 수 있습니다.
3. 그러지 않고 원하는 크기가 나열되지 않은 경우에는 다음 명령을 실행하여 VM의 할당을 취소하고 크기를 조정한 다음 VM을 다시 시작합니다.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > VM의 할당이 취소되면 VM에 할당된 모든 동적 IP 주소도 해제됩니다. OS 및 데이터 디스크는 영향을 받지 않습니다.
   > 
   > 

## <a name="next-steps"></a>다음 단계
확장성을 높이기 위해서는 여러 VM 인스턴스를 실행하고 규모를 확장합니다. 자세한 내용은 [가상 컴퓨터 크기 집합에서 Linux 컴퓨터 자동 확장][scale-set]을 참조하세요. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md

