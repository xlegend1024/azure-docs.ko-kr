---
title: "PostgreSQL용 Azure Database에서 서비스 매개 변수 구성 | Microsoft Docs"
description: "PostgreSQL용 Azure Database의 서비스 매개 변수를 구성하는 방법을 설명합니다."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Azure CLI를 사용하여 서버 구성 매개 변수 사용자 지정
Azure CLI(명령줄 인터페이스)를 사용하여 Azure PostgreSQL 서버의 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다. 그러나 엔진 구성의 하위 집합만 서버 수준에서 노출하고 수정할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 서버 및 데이터베이스 [PostgreSQL용 Azure Database 만들기](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 명령줄 유틸리티 설치

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Azure PostgreSQL 서버에 대한 서버 구성 매개 변수 나열
Azure PostgreSQL 서버 및 해당 값의 모든 수정 가능한 매개 변수 목록을 가져오려면 다음 **az postgres server configuration** 명령을 다음과 같이 실행합니다.
> az postgres server configuration list --resource-group <resource group name> --server <server name>

예를 들어 리소스 그룹 **myresourcegroup**에 있는 Azure PostgreSQL 서버 **mypgserver.postgres.database.azure.com**에 대한 서버 구성 매개 변수를 나열할 수 있습니다.
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>서버 구성 매개 변수 세부 정보 표시
Azure PostgreSQL 서버에 대한 특정 구성 매개 변수의 세부 정보를 표시하려면 **az postgres server configuration** 명령을 실행합니다.
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
예를 들어 리소스 그룹 **myresourcegroup**에 있는 Azure PostgreSQL 서버 **mypgserver.postgres.database.azure.com**에 대한 **log\_min\_messages** 서버 구성 매개 변수를 나열할 수 있습니다. **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>서버 구성 매개 변수 값 수정
Azure PostgreSQL 서버의 특정 구성 매개 변수의 값을 수정할 수 있습니다. 그러면 PostgreSQL 서버 엔진에 대한 기본 구성 값이 업데이트됩니다. 구성 값을 업데이트하려면 **az postgres server configuration** 명령을 실행합니다. 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
예를 들어 리소스 그룹 **myresourcegroup**에 있는 Azure PostgreSQL 서버 **mypgserver.postgres.database.azure.com**에 대한 **log\_min\_messages** 서버 구성 매개 변수를 업데이트할 수 있습니다. **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
구성 매개 변수 값을 다시 설정하려는 경우 선택 사항인 --value 매개 변수를 유지하도록 선택하기만 하면 서비스에서 기본 값을 적용합니다. 위의 예제에서는 다음과 같이 표시됩니다.
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
이렇게 하면 log\_min\_messages 구성이 기본 값 WARNING으로 다시 설정됩니다. 서버 구성 및 허용되는 값에 자세한 내용은 [서버 구성](https://www.postgresql.org/docs/9.6/static/runtime-config.html)의 PostgreSQL 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계
- 서버 로그를 구성하고 액세스하려면 [PostgreSQL용 Azure Database의 서버 로그](concepts-server-logs.md)를 참조하세요.
