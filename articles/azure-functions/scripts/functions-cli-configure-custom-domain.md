---
title: "Azure CLI 스크립트 샘플 - 함수 앱에 사용자 지정 도메인 매핑 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure의 함수 앱에 사용자 지정 도메인을 매핑합니다."
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cd7ab0bbe92fa32d23a841b0b17bee8510f6b406
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>함수 앱에 사용자 지정 도메인 매핑

이 샘플 스크립트는 관련된 리소스를 사용하여 함수 앱을 만든 다음 여기에 `www.<yourdomain>`을 매핑합니다. 사용자 지정 도메인에 매핑하려면 함수 앱이 소비 계획이 아니라 App Service 계획에서 생성되어야 합니다. Azure Functions는 A 레코드를 사용한 사용자 지정 도메인 매핑을 지원합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "함수 앱에 사용자 지정 도메인 매핑")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 함수 앱에 필요한 저장소 계정을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 사용자 지정 도메인을 매핑하는 데 필요한 App Service 계획을 만듭니다. |
| [az functionapp create]() | 함수 앱을 만듭니다. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 함수 앱에 사용자 지정 도메인을 매핑합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Functions CLI 스크립트 샘플은 [Azure Functions 설명서]()에서 확인할 수 있습니다.

