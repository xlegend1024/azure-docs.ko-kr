---
title: "AWS VM을 Azure로 마이그레이션 | Microsoft Docs"
description: "AWS(Amazon Web Services) EC2 인스턴스를 Azure Virtual Machines으로 마이그레이션합니다. 이 시나리오에서는 클라우드 저장소를 단순화하기 위해 Managed Disks를 사용합니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e940a5653d81852c6440829010ec86bcadeadca7
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---

# <a name="migrate-from-amazon-web-services-aws-to-azure-managed-disks"></a>Amazon Web Services(AWS)에서 Azure Managed Disks로 마이그레이션

VHD(가상 하드 디스크)를 업로드하여 AWS(Amazon Web Services) EC2 인스턴스를 Azure로 마이그레이션할 수 있습니다. 같은 이미지로 Azure에서 여러 VM(가상 컴퓨터)을 만들려면 먼저 VM을 일반화한 후 일반화된 VHD를 로컬 디렉터리로 내보내야 합니다. VHD가 업로드되면 저장소에 [Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하는 새 Azure VM을 만들 수 있습니다. Azure Managed Disks를 사용하면 Azure IaaS VM의 저장소 계정을 관리할 필요가 없습니다. 필요한 디스크의 유형(프리미엄 또는 표준)과 크기를 지정해야 합니다. 그러면 Azure가 알아서 디스크를 만들고 관리해줍니다. 

이 프로세스를 시작하기 전에 [Managed Disks로 마이그레이션하기 위한 계획](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토하세요.

Azure에 VHD를 업로드하기 전에 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.


## <a name="generalize-the-vm"></a>VM 일반화

Sysprep을 사용하여 VM을 일반화하면 VHD에서 모든 시스템별 정보 및 개인 계정 정보가 제거되고 이미지로 사용할 컴퓨터가 준비됩니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Azure에 VHD를 업로드하기 전에 Sysprep을 처음으로 실행하는 경우 Sysprep을 실행하기 전에 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

1. Windows 가상 컴퓨터에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
   
    ![Sysprep 시작](./media/aws-to-azure/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 컴퓨터를 종료합니다. VM을 다시 시작하지 마세요.



## <a name="export-the-vhd-from-aws"></a>AWS에서 VHD를 내보내기

1.    AWS(Amazon Web Services)를 사용하는 경우 EC2 인스턴스를 Amazon S3 버킷의 VHD로 내보냅니다. Amazon 설명서에 제공된 Amazon EC2 인스턴스 내보내기 단계에 따라 Amazon EC2 CLI(명령줄 인터페이스) 도구를 설치하고 create-instance-export-task 명령을 실행하여 EC2 인스턴스를 VHD 파일로 내보냅니다. create-instance-export-task 명령 실행 시 DISK_IMAGE_FORMAT 변수에 VHD를 사용해야 합니다. 내보낸 VHD 파일은 해당 프로세스 중 지정된 Amazon S3 버킷에 저장됩니다.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT '
    --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2.    S3 버킷에서 VHD 파일을 다운로드 합니다. VHD 파일을 선택한 다음 **작업** > **다운로드**를 선택합니다.



## <a name="upload-the-vhd"></a>VHD 업로드

이미지를 만들려면 먼저 Azure에 로그인하고 저장소 계정을 만든 후 VHD를 저장소 계정에 업로드해야 합니다. 

### <a name="log-in-to-azure"></a>Azure에 로그인

아직 PowerShell을 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

1. Azure PowerShell을 열고 Azure 계정에 로그인합니다. Azure 계정 자격 증명을 입력하기 위한 팝업 창이 열립니다.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 사용 가능한 구독에 대한 구독을 ID를 가져옵니다.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 구독 ID를 사용하여 올바른 구독을 설정합니다. `<subscriptionID>`를 올바른 구독의 ID로 바꿉니다.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>저장소 계정 가져오기
업로드한 VM 이미지를 저장할 Azure 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다. 

VHD를 사용하여 VM의 Managed Disk를 만드는 경우 저장소 계정 위치가 VM을 만들 위치와 동일해야 합니다.

사용 가능한 저장소 계정을 표시하려면 다음을 입력합니다.

```powershell
Get-AzureRmStorageAccount
```

기존 저장소 계정을 사용하려면 [VM 이미지 업로드](#upload-the-vm-vhd-to-your-storage-account) 섹션을 진행합니다.

저장소 계정을 만들어야 하는 경우 다음 단계를 따릅니다.

1. 저장소 계정을 만들어야 하는 리소스 그룹의 이름을 알아야 합니다. 구독의 모든 리소스 그룹을 찾으려면 다음을 입력합니다.
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    **미국 서부** 지역에 **myResourceGroup** 이라는 이름의 리소스 그룹을 만들려면 다음을 입력합니다.

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet을 사용하여 이 리소스 그룹에 **mystorageaccount**라는 이름의 저장소 계정을 만듭니다.
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName에 대한 유효한 값은 다음과 같습니다.
   
   * **Standard_LRS** - 로컬 중복 저장소 
   * **Standard_ZRS** - 영역 중복 저장소
   * **Standard_GRS** - 지역 중복 저장소 
   * **Standard_RAGRS** - 읽기 액세스 지역 중복 저장소 
   * **Premium_LRS** - 프리미엄 로컬 중복 저장소 

### <a name="upload-the-vhd"></a>VHD 업로드 

[Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet을 사용하여 저장소 계정의 컨테이너에 VHD를 업로드합니다. 이 예제에서는 `"C:\Users\Public\Documents\Virtual hard disks\"`에서 **myResourceGroup** 리소스 그룹의 **mystorageaccount**라는 저장소 계정에 파일 **myVHD.vhd**를 업로드합니다. 파일은 **mycontainer**라는 컨테이너에 배치되고 새 파일 이름은 **myUploadedVHD.vhd**가 됩니다.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


성공하면 다음과 유사한 응답을 얻게 됩니다.

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

이 명령은 네트워크 연결 및 VHD 파일의 크기에 따라 완료하는 데 다소 시간이 걸립니다.

업로드된 VHD를 사용하여 Managed Disk 또는 새 VM을 만들려면 나중에 사용할 수 있도록 **대상 URI** 경로를 저장합니다.

### <a name="other-options-for-uploading-a-vhd"></a>VHD를 업로드하기 위한 기타 옵션

또한 다음 방법 중 하나를 사용하여 저장소 계정에 VHD를 업로드할 수 있습니다.

-   [Azure 저장소 Blob 복사 API](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Azure Storage 탐색기 Blob 업로드](https://azurestorageexplorer.codeplex.com/)

-   [저장소 가져오기/내보내기 서비스 REST API 참조](https://msdn.microsoft.com/library/dn529096.aspx)

    예상 업로드 시간이 7일보다 긴 경우 가져오기/내보내기 서비스를 사용하는 것이 좋습니다. [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html)를 사용하여 데이터 크기 및 전송 단위로 시간을 예측할 수 있습니다. 

    가져오기/내보내기를 사용하여 표준 저장소 계정을 복사할 수 있습니다. Premium Storage를 사용하려면 AzCopy와 같은 도구를 사용하여 표준 저장소에서 Premium Storage 계정으로 복사해야 합니다.

## <a name="create-an-image"></a>이미지 만들기 

일반화된 OS VHD를 사용하여 관리 이미지를 만듭니다.


1.  먼저, 공통 매개 변수를 설정합니다.

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  일반화된 OS VHD를 사용하여 이미지를 만듭니다.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="create-vm-from-image"></a>이미지에서 VM 만들기

먼저 이미지에 대한 기본 정보를 수집하고 이미지의 변수를 만들어야 합니다. 이 예제에서는 **미국 중서부** 위치의 **myResourceGroup** 리소스 그룹에 있는 **myImage**라는 관리되는 VM 이미지를 사용합니다. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 vNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 이 예제에서는 주소 접두사로 **10.0.0.0/24**를 사용하는 **mySubnet**이라는 서브넷을 만듭니다.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 가상 네트워크 만들기 이 예제에서는 주소 접두사로 **10.0.0.0/16**을 사용하는 **myVnet**이라는 가상 네트워크를 만듭니다.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-and-nic"></a>공용 IP 및 NIC 만들기

가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP 주소 만들기. 이 예에서는 **myPip**라는 공용 IP 주소를 만듭니다. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 만들기. 이 예에서는 **myNic**라는 NIC를 만듭니다. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-nsg"></a>NSG 만들기

RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 NSG(네트워크 보안 규칙)가 필요합니다. 

이 예에서는 포트 3389를 통한 RDP 트래픽을 허용하는 **myRdpRule**이라는 규칙을 포함하는 **myNsg**로 명명된 NSG를 만듭니다. NSG에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-network-variables"></a>네트워크 변수 만들기

완료된 가상 네트워크에 대한 변수를 만듭니다. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials"></a>자격 증명 가져오기 

다음 cmdlet을 사용하면 VM에 원격으로 액세스하기 위해 로컬 관리자 계정으로 사용할 새 사용자 이름 및 암호를 입력할 수 있는 창이 열립니다. 

```powershell
$cred = Get-Credential
```

### <a name="set-vm-variables"></a>VM 변수 설정 

1. VM 이름 및 컴퓨터 이름에 대한 변수를 만듭니다. 이 예제에서는 VM 이름을 **myVM**으로 설정하고 컴퓨터 이름을 **myComputer**로 설정합니다.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 가상 컴퓨터의 크기를 설정합니다. 이 예제에서는 **Standard_DS1_v2** 크기의 VM을 만듭니다. 자세한 내용은 [VM 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) 설명서를 참조하세요.

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. VM 구성에 VM의 이름과 크기를 추가합니다.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image"></a>VM 이미지 설정 

관리되는 VM 이미지의 ID를 사용하여 원본 이미지를 설정합니다.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration"></a>OS 구성 설정 

저장소 유형(PremiumLRS 또는 StandardLRS) 및 OS 디스크의 크기를 입력합니다. 이 예제에서는 계정 유형을 **PremiumLRS**로, 디스크 크기를 **128GB**로, 디스크 캐싱을 **ReadWrite**로 설정합니다.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>VM 만들기

이전에 작성하여 **$vm** 변수에 저장한 구성을 사용하여 새 VM을 만듭니다.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-the-vm"></a>VM 확인
완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>다음 단계

새 가상 컴퓨터에 로그인하려면 [포털](https://portal.azure.com)에서 VM으로 이동한 다음 **연결**을 클릭하고 원격 데스크톱 RDP 파일을 엽니다. 원본 가상 컴퓨터의 계정 자격 증명을 사용하여 새 가상 컴퓨터에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 
 

