---
title: "명령줄 인터페이스를 사용하여 Azure Data Lake Store 시작 | Microsoft Docs"
description: "Azure 플랫폼 간 명령줄을 사용하여 데이터 레이크 저장소 계정을 만들고 기본 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/17/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: f7748dba30c6e0332c166feda25f4aaa93c06efa
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Azure 명령줄을 사용하여 Azure 데이터 레이크 저장소 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure 명령줄 인터페이스를 사용하여 Azure 데이터 레이크 저장소 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake Store에 대한 자세한 내용은 [Data Lake Store 개요](data-lake-store-overview.md)를 참조하세요.

Azure CLI는 Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다. Azure CLI는 오픈 소스입니다. 소스 코드는 <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>의 GitHub에서 관리됩니다. 이 문서에서는 데이터 레이크 저장소에서 Azure CLI를 사용하는 방법만 설명합니다. Azure CLI를 사용하는 방법에 대한 일반 가이드는 [Azure CLI를 사용하는 방법][azure-command-line-tools]을 참조하세요.


> [!NOTE]
> 대량의 데이터(큰 파일, 많은 수의 파일 또는 둘 다)를 업로드 및 다운로드하는 경우 [Python SDK](data-lake-store-get-started-python.md), [.NET SDK](data-lake-store-get-started-net-sdk.md) 또는 [Azure PowerShell](data-lake-store-get-started-powershell.md)을 사용하는 것이 좋습니다. 이러한 옵션은 여러 스레드를 사용하여 데이터 이동을 병렬화할 때 더 나은 성능을 제공합니다.
> 
>

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure CLI** - 설치 및 구성 정보는 [Azure CLI 설치 및 구성](../cli-install-nodejs.md) 을 참조하세요. CLI를 설치한 후 컴퓨터를 다시 부팅해야 합니다.

## <a name="authentication"></a>인증

이 문서는 최종 사용자로 로그인하는 Data Lake Store에 보다 간단한 인증 방식을 사용합니다. Data Lake Store 계정 및 파일 시스템에 대한 액세스 수준은 로그인한 사용자의 액세스 수준을 따릅니다. 하지만 Data Lake Store에 인증하는 다른 방법인 **최종 사용자 인증** 또는 **서비스간 인증**도 있습니다. 인증 하는 방법에 대한 지침 및 자세한 내용은 [Azure Active Directory를 사용하여 Data Lake Store로 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="login-to-your-azure-subscription"></a>Azure 구독에 로그인

1. [Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md)에 설명된 단계에 따라 `azure login` 메서드를 사용하여 구독에 연결합니다.

2. `azure account list` 명령을 사용하여 계정과 연결된 구독을 나열합니다.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    위의 출력에서 **Azure-sub-1**은 현재 활성화되어 있으며 다른 구독은 **Azure-sub-2**입니다. 
3. 작업에 사용하려는 구독을 선택합니다. Azure-sub-2 구독 하에서 작업하려면 `azure account set` 명령을 사용합니다.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 만들기
명령 프롬프트, 셸 또는 터미널 세션을 열고 다음 명령을 실행합니다.

1. 다음 명령을 사용하여 Azure 리소스 관리자 모드로 전환합니다.
   
        azure config mode arm
2. 새 리소스 그룹을 만듭니다. 다음 명령에서 사용하려는 매개 변수 값을 제공합니다.
   
        azure group create <resourceGroup> <location>
   
    위치 이름이 공백을 포함하는 경우 이중 따옴표로 묶습니다. 예를 들어 "East US 2"입니다.
3. 데이터 레이크 저장소 계정을 만듭니다.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>데이터 레이크 저장소에서 폴더 만들기
Azure 데이터 레이크 저장소 계정에서 폴더를 만들어 데이터를 관리하고 저장할 수 있습니다. 다음 명령을 사용하여 데이터 레이크 저장소의 루트에 있는 "mynewfolder"라는 폴더를 만듭니다.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

예:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>데이터 레이크 저장소에 데이터 업로드
루트 수준에서 데이터 레이크 저장소에 직접 데이터를 업로드하거나 계정 내에서 만든 폴더에 업로드할 수 있습니다. 아래 코드 조각은 이전 섹션에서 만든 폴더(**mynewfolder**)에 일부 샘플 데이터를 업로드하는 방법을 보여 줍니다.

업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다. 파일을 다운로드하고 컴퓨터의 로컬 디렉터리(예: C:\sampledata\.)에 저장합니다.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

예:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>데이터 레이크 저장소의 파일 나열
다음 명령을 사용하여 데이터 레이크 저장소 계정의 파일을 나열합니다.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

예:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

이 명령의 출력은 다음과 유사합니다.

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>데이터 레이크 저장소에서 데이터 이름 바꾸기, 다운로드 및 삭제
* **파일의 이름을 바꾸려면**다음 명령을 사용합니다.
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    예:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **파일을 다운로드하려면**다음 명령을 사용합니다. 이미 지정한 대상 경로가 있는지 확인합니다.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    예:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **파일을 삭제하려면**다음 명령을 사용합니다.
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    예:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    메시지가 표시되면 **Y** 를 입력하여 항목을 삭제합니다.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>데이터 레이크 저장소의 폴더에 대한 액세스 제어 목록 보기
다음 명령을 사용하여 데이터 레이크 저장소 폴더에 ACL을 봅니다. 현재 릴리스에서 ACL 데이터 레이크 저장소의 루트에 대해서만 설정할 수 있습니다. 따라서 아래 경로 매개 변수는 항상 루트(/)가 됩니다.

    azure datalake store permissions show <dataLakeStoreName> <path>

예:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>데이터 레이크 저장소 계정 삭제
다음 명령을 사용하여 데이터 레이크 저장소 계정을 삭제합니다.

    azure datalake store account delete <dataLakeStoreAccountName>

예:

    azure datalake store account delete mynewdatalakestore

메시지가 표시되면 **Y** 를 입력하여 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../cli-install-nodejs.md

