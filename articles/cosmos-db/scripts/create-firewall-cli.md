---
title: "Azure CLI 스크립트-Azure Cosmos DB에 대한 방화벽 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Cosmos DB에 대한 방화벽 만들기"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sammvcple
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 710413f8bf0cd50abc5df9e117103fec5fc43e7c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---

# <a name="azure-cosmos-db-create-a-firewall-using-the-azure-cli"></a>Azure Cosmos DB: Azure CLI를 사용하여 방화벽 만들기

이 샘플 CLI 스크립트는 종류에 관계없이 Azure Cosmos DB 계정에 대해 방화벽 정책을 만듭니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-create-firewall/secure-cosmosdb-create-firewall.sh?highlight=38-42 "Azure Cosmos DB 방화벽 만들기")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az cosmosdb create](/cli/azure/cosmosdb/name#create) | Azure Cosmos DB 계정을 만듭니다. |
| [az cosmosdb update](/cli/azure/sql/server#create) | 방화벽 설정을 포함하도록 Azure CosmosDB 계정을 업데이트합니다. |
| [az group delete](/cli/azure/resource#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI 설명서](../cli-samples.md)에서 확인할 수 있습니다.

