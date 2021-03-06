---
title: "Azure에서 Node.js 및 MongoDB 웹앱 작성 | Microsoft Docs"
description: "Azure에서 작동하며 MongoDB 연결 문자열로 Cosmos 데이터베이스에 연결되는 Node.js 응용 프로그램을 만드는 방법을 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 2a3d63b3829e750b62658d720522ae1abf89cd86
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Azure에서 Node.js 및 MongoDB 웹앱 작성
이 자습서에서는 Azure에서 Node.js 웹앱을 만들고 MongoDB 데이터베이스에 연결하는 방법을 보여 줍니다. 완료하고 나면 MEAN 응용 프로그램(MongoDB, Express, AngularJS 및 Node.js)이 [App Service Web Apps](app-service-web-overview.md)에서 실행됩니다. 간편하게 하기 위해 샘플 응용 프로그램은 [MEAN.js 웹 프레임워크](http://meanjs.org/)를 사용합니다.

![Azure App Service에서 실행 중인 MEAN.js 응용 프로그램](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 MongoDB 데이터베이스 만들기
> * Node.js 앱을 MongoDB에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음 필수 조건을 로컬로 설치합니다.

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [Node.js 및 NPM 다운로드 및 설치](https://nodejs.org/)
1. [Gulp.js 설치](http://gulpjs.com/)([MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started)에 필요)
1. [MongoDB Community Edition 다운로드, 설치 및 실행](https://docs.mongodb.com/manual/administration/install-community/). 
1. [Azure CLI 2.0 다운로드 및 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>로컬 MongoDB 테스트
이 단계에서는 로컬 MongoDB 데이터베이스가 실행 중인지 확인합니다.

터미널 창을 열고 `cd`를 사용하여 MongoDB 설치 위치의 `bin` 디렉터리로 이동합니다. 

터미널에서 `mongo`를 실행하여 로컬 MongoDB 서버에 연결합니다.

```bash
mongo
```

연결이 성공한다면 MongoDB 데이터베이스가 이미 실행 중입니다. 그렇지 않다면 [MongoDB Community Edition 다운로드, 설치 및 실행](https://docs.mongodb.com/manual/administration/install-community/)에 있는 단계를 수행하여 로컬 MongoDB 데이터베이스가 시작되었는지 확인합니다. MongoDB가 주로 설치되어 있지만 `mongod`를 실행하여 시작해야 합니다. 

MongoDB 데이터베이스를 테스트하고 나면 터미널에 `Ctrl`+`C`를 입력합니다. 

<a name="step2"></a>

## <a name="create-local-nodejs-app"></a>로컬 Node.js 앱 만들기
이 단계에서는 로컬 Node.js 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

터미널 창을 열고 `cd`를 사용하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

이 샘플 리포지토리에는 [MEAN.js 리포지토리](https://github.com/meanjs/mean) 복사본이 들어 있습니다. App Service에서 실행하도록 최소로 수정됩니다(자세한 내용은 [추가 정보](https://github.com/Azure-Samples/meanjs/blob/master/README.md) 참조).

### <a name="run-the-application"></a>응용 프로그램 실행

필요한 패키지를 설치하고 응용 프로그램을 시작합니다.

```bash
cd meanjs
npm install
npm start
```

응용 프로그램이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

브라우저에서 `http://localhost:3000`으로 이동합니다. 맨 위 메뉴에서 **등록**을 클릭하고 더미 사용자를 만들어 봅니다. 

MEAN.js 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 성공해서 MEAN.js가 생성된 사용자로 자동 로그인하면 응용 프로그램에서 로컬 MongoDB 데이터베이스에 정보를 씁니다.

![MEAN.js가 MongoDB 연결에 성공](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

**관리자** > **문서 관리**를 클릭하여 문서를 추가해 봅니다.

언제든지 Node.js를 중지하려면 터미널에 `Ctrl`+`C`를 입력합니다. 

## <a name="create-production-mongodb"></a>프로덕션 MongoDB 만들기

이 단계에서는 Azure에 MongoDB 데이터베이스를 만듭니다. 응용 프로그램이 Azure에 배포되어 있으면 프로덕션 워크로드에 이 데이터베이스를 사용합니다.

MongoDB의 경우 이 자습서에서는 [Azure Cosmos DB](/azure/documentdb/)를 사용합니다. Cosmos DB는 MongoDB 클라이언트 연결을 지원합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

이제 터미널 창에서 Azure CLI 2.0을 사용하여 Azure App Service에서 Node.js 응용 프로그램을 호스트하는 데 필요한 리소스를 만들 예정입니다.  [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create)를 사용하여 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 유럽 서부 지역의 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

`--location`에 사용할 수 있는 가능한 값을 보려면 `az appservice list-locations` Azure CLI 명령을 사용합니다.

### <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기

[az cosmosdb create](/cli/azure/cosmosdb#create) 명령을 사용하여 Cosmos DB 계정을 만듭니다.

다음 명령에서 _&lt;cosmosdb_name>_ 자리 표시자를 고유한 Cosmos DB 이름으로 바꿉니다. 이 고유한 이름은 Cosmos DB 끝점의 일부로 사용되므로(`https://<cosmosdb_name>.documents.azure.com/`), Azure의 모든 Cosmos DB 계정에서 고유해야 합니다. 

```azurecli
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

`--kind MongoDB` 매개 변수는 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

> [!NOTE]
> _&lt;cosmosdb_name>_은 소문자, 숫자 및 _-_ 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다.
>
>

Cosmos DB 계정을 만든 경우 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output has been truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>프로덕션 MongoDB에 앱 연결

이 단계에서는 MongoDB 연결 문자열을 사용하여 MEAN.js 샘플 응용 프로그램을 방금 만든 Cosmos DB 데이터베이스에 연결합니다. 

### <a name="retrieve-the-database-key"></a>데이터베이스 키 검색

Cosmos DB 데이터베이스에 연결하려면 데이터베이스 키가 필요합니다. [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) 명령을 사용하여 기본 키를 검색합니다.

```azurecli
az cosmosdb list-keys \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup
```

Azure CLI는 다음 예와 유사한 정보를 출력합니다.

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

`primaryMasterKey` 값을 텍스트 편집기에 복사합니다. 이 정보는 다음 단계에서 필요합니다.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js 응용 프로그램에 연결 문자열 구성

MEAN.js 리포지토리에서 _config/env/production.js_를 엽니다.

`db` 개체에서 다음 예제와 같이 `uri` 값을 바꿉니다. 2개의 _&lt;cosmosdb_name>_ 자리 표시자를 Cosmos DB 데이터베이스 이름으로 바꾸고 _&lt;primary_master_key>_ 자리 표시자를 이전 단계에서 복사한 키로 바꿔야 합니다.

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> `ssl=true` 옵션은 [Cosmos DB에서 SSL이 필요](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)하기 때문에 중요합니다. 
>
>

변경 내용을 저장합니다.

### <a name="test-the-application-in-production-mode"></a>프로덕션 모드에서 응용 프로그램 테스트 

다른 몇몇 Node.js 웹 프레임워크와 마찬가지로 MEAN.js에서도 `gulp`를 사용하여 프로덕션 환경의 스크립트를 최소화하고 번들로 만듭니다. 그러면 프로덕션 환경에 필요한 파일이 생성됩니다. 

이제 `gulp prod`를 실행합니다.

```bash
gulp prod
```

그 후에 다음 명령을 실행하여 _config/env/production.js_에 구성한 연결 문자열을 사용합니다.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production`에서는 Node.js가 프로덕션 환경에서 실행되게 하는 환경 변수를 설정하고, `node server.js`는 `server.js`가 있는 Node.js를 리포지토리 루트에서 시작합니다. 이 방법으로 Node.js 응용 프로그램을 Azure에 로드합니다. 

응용 프로그램이 로드될 때 프로덕션 환경에서 실행 중인지 확인해야 합니다.

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

브라우저에서 `http://localhost:8443`으로 이동합니다. 맨 위 메뉴에서 **등록**을 클릭하고 전과 같이 더미 사용자를 만들어 봅니다. 성공하면 응용 프로그램에서 데이터를 Azure의 Cosmos DB 데이터베이스에 씁니다. 

터미널에서 `Ctrl`+`C`를 입력하여 Node.js를 중지합니다. 

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포
이 단계에서는 MongoDB에 연결된 Node.js 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="create-an-app-service-plan"></a>앱 서비스 계획 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다. 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

다음 예제에서는 **무료** 가격 책정 계층을 사용하여 _myAppServicePlan_이라는 App Service 계획을 만듭니다.

```azurecli
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

App Service 계획을 만들면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>웹앱 만들기

이제 App Service 계획을 만들었으므로 _myAppServicePlan_ App Service 계획 내에서 웹앱을 만듭니다. 웹앱은 코드를 배포할 호스팅 공간을 제공하고, 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. [az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 웹앱을 만듭니다. 

다음 명령에서 _&lt;app_name>_ 자리 표시자를 고유한 응용 프로그램 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부로 사용되므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 웹앱에 사용자 지정 DNS 항목을 매핑할 수 있습니다. 

```azurecli
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>환경 변수 구성

자습서의 앞부분에서는 _config/env/production.js_에 데이터베이스 연결 문자열을 하드코딩했습니다. 최상의 보안을 유지하려면 이 중요한 데이터를 Git 리포지토리 외부에 두는 것이 좋습니다. Azure에서 실행되는 응용 프로그램의 경우는 대신 환경 변수를 사용합니다.

App Service에서는 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 명령을 사용하여 환경 변수를 _app settings_로 설정합니다. 

다음 예에서는 Azure 웹앱에 `MONGODB_URI` 응용 프로그램 설정을 구성할 수 있습니다. 전과 같이 자리 표시자를 바꿔야 합니다.

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Node.js 코드에서는 다른 환경 변수에 액세스할 때와 마찬가지로 `process.env.MONGODB_URI`를 사용하여 이 응용 프로그램 설정에 액세스합니다. 

이제 다음 명령을 사용하여 _config/env/production.js_의 변경 내용을 실행 취소합니다.

```bash
git checkout -- .
```

_config/env/production.js_를 다시 엽니다. 기본 MEAN.js 응용 프로그램은 이미 방금 만든 `MONGODB_URI` 환경 변수를 사용하도록 구성되어 있습니다.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성 

FTP, 로컬 Git, GitHub, Visual Studio Team Services 및 BitBucket과 같은 다양한 방법으로 응용 프로그램을 Azure App Service에 배포할 수 있습니다. FTP 및 로컬 Git의 경우 배포에 인증하기 위해 배포 사용자를 서버에 구성할 필요가 있습니다. 

[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 명령을 사용하여 계정 수준 자격 증명을 만듭니다. 

> [!NOTE] 
> App Service에 FTP 및 로컬 Git 배포를 하려면 배포 사용자가 필요합니다. 이 배포 사용자는 계정 수준입니다. 따라서 Azure 구독 계정과 다릅니다. 이 배포 사용자는 한 번만 구성하면 됩니다.

```azurecli
az appservice web deployment user set \
    --user-name <specify-a-username> \
    --password <minimum-8-char-capital-lowercase-number>
```

[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 명령을 사용하여 Azure 웹앱에 대한 로컬 Git 액세스 권한을 구성합니다. 

```azurecli
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

배포 사용자가 구성되면 Azure CLI에 다음과 같은 형식으로 Azure 웹앱의 배포 URL이 표시됩니다.

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

다음 단계에서 사용할 수 있는 터미널의 출력을 복사합니다. 

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 Git 리포지토리에 Azure 원격을 추가합니다. 

```bash
git remote add azure <paste_copied_url_here> 
```

Azure에 원격으로 푸시하여 Node.js 응용 프로그램을 배포합니다. 배포 사용자를 만드는 작업의 일부로 이전에 입력한 암호를 묻는 메시지가 나타납니다. 

```bash
git push azure master
```

배포하는 동안 Azure App Service는 진행 상황을 Git에 전합니다.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> 배포 프로세스에서 `npm install` 후에 [Gulp](http://gulpjs.com/)를 실행하는 것을 알 수 있습니다. App Service는 배포 중에 Gulp 또는 Grunt 작업을 실행하지 않으므로 이 샘플 리포지토리는 사용 설정에 사용되는 추가 파일 두 개가 루트 디렉터리에 들어 있습니다. 
>
> - _.deployment_ - 이 파일은 App Service에서 `bash deploy.sh`를 사용자 지정 배포 스크립트로 실행하게 만듭니다.
> - _deploy.sh_ - 사용자 지정 배포 스크립트입니다. 파일을 검토 하는 경우 실행 되도록 표시 됩니다 `gulp prod` 후 `npm install` 및 `bower install`합니다. 
>
> 이 방식으로 Git 기반 배포에 어떤 단계든 추가할 수 있습니다.
>
> 언제든지 Azure Web App을 다시 시작하면 App Service에서 이 자동화 작업을 다시 실행하지 않습니다.
>
>

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기 
웹 브라우저를 사용하여 배포된 웹앱으로 이동합니다. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

맨 위 메뉴에서 **등록**을 클릭하고 더미 사용자를 만들어 봅니다. 

성공해서 응용 프로그램이 생성된 사용자로 자동 로그인되면, Azure의 MEAN.js 응용 프로그램이 MongoDB(Cosmos DB) 데이터베이스에 연결된 것입니다. 

![Azure App Service에서 실행 중인 MEAN.js 응용 프로그램](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

**관리자** > **문서 관리**를 클릭하여 문서를 추가해 봅니다. 

**축하합니다.** Azure App Service에서 데이터 기반 Node.js 응용 프로그램을 실행합니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 `article` 데이터 모델을 약간 변경하고 변경 내용을 Azure에 게시합니다.

### <a name="update-the-data-model"></a>데이터 모델 업데이트

_modules/articles/server/models/article.server.model.js_를 엽니다.

`ArticleSchema`에서 `comment`라는 `String` 형식을 추가합니다. 완료된 후의 스키마 코드는 다음과 유사합니다.

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

모델 변경을 완료했습니다. 

### <a name="update-the-articles-code"></a>문서 코드 업데이트

다음으로 `comment`를 사용하도록 `articles` 코드의 나머지 부분을 업데이트합니다.

서버 컨트롤러 하나와 클라이언트 뷰 네 개, 합해서 5개의 파일을 수정해야 합니다. 

먼저 _modules/articles/server/controllers/articles.server.controller.js_를 엽니다.

`update` 함수에 `article.comment`의 할당을 추가합니다. 완료된 후의 `update` 함수는 다음과 비슷합니다.

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

그런 후 _modules/articles/client/views/view-article.client.view.html_을 엽니다.

닫는 `</section>` 태그 바로 위에 다음 줄을 추가하여 `comment`를 나머지 문서 데이터와 함께 표시합니다.

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

그런 후 _modules/articles/client/views/list-articles.client.view.html_을 엽니다.

닫는 `</a>` 태그 바로 위에 다음 줄을 추가하여 `comment`를 나머지 문서 데이터와 함께 표시합니다.

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

그런 후 _modules/articles/client/views/admin/list-articles.client.view.html_을 엽니다.

`<div class="list-group">` 태그 안과 닫는 `</a>` 태그 바로 위에 다음 줄을 추가하여 `comment`를 나머지 문서 데이터와 함께 표시합니다.

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

마지막으로 _modules/articles/client/views/admin/form-article.client.view.html_을 엽니다.

전송 단추가 포함된, 다음과 비슷한 `<div class="form-group">` 태그를 찾습니다.

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

사람들이 `comment` 필드를 편집할 수 있도록 이 태그 바로 위에 `<div class="form-group">` 태그를 하나 더 추가합니다. 새 태그는 다음과 비슷합니다.

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

변경 내용을 모두 저장합니다.

프로덕션 모드에서 변경 내용을 다시 테스트합니다.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> _config/env/production.js_이 원래대로 돌아갔으며 `MONGODB_URI` 환경 변수는 로컬 컴퓨터가 아닌 Azure 웹앱에만 설정되어 있다는 점에 유의해야 합니다. 구성 파일을 보면 프로덕션 구성에서 기본적으로 로컬 MongoDB 데이터베이스를 사용하는 것을 알 수 있습니다. 이렇게 하면 로컬에서 코드 변경 내용을 테스트할 때 프로덕션 데이터를 건드리지 않을 수 있습니다.
>
>

브라우저에서 `http://localhost:8443`으로 이동하여 로그인했는지 확인합니다.

**관리자** > **문서 관리**를 클릭한 다음 **+** 단추를 클릭하여 문서를 추가합니다.

이제 새 `Comment` 텍스트 상자가 표시됩니다.

![문서에 추가된 주석 필드](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

터미널에서 `Ctrl`+`C`를 입력하여 Node.js를 중지합니다. 

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

Git에서 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "added article comment"
git push azure master
```

`git push`가 완료되면 Azure 웹앱으로 이동하여 새 기능을 다시 시험해 봅니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> 이전에 문서를 추가했으면 그 문서를 지금도 볼 수 있습니다. Cosmos DB의 기존 데이터는 손실되지 않습니다. 또한 데이터 스키마가 업데이트되고 기존 데이터는 그대로 유지됩니다.
>
>

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림 

Node.js 응용 프로그램을 Azure App Service에서 실행하는 동안 콘솔 로그를 바로 터미널에 보낼 수 있습니다. 이 방법으로 응용 프로그램 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

로그 스트리밍을 시작하려면 [az appservice web log tail](/cli/azure/appservice/web/log#tail) 명령을 사용합니다.

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

로그 스트리밍이 시작되고 나면 브라우저에서 Azure 웹앱을 새로 고쳐 웹 트래픽을 만듭니다. 이제 콘솔 로그가 터미널에 표시됩니다.

언제든지 `Ctrl`+`C`를 입력하여 로그 스트리밍을 중지합니다. 

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

만든 웹앱을 보려면 Azure Portal로 이동합니다.

이 작업을 수행하려면 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

왼쪽 메뉴에서 **App Service**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

웹앱의 _블레이드_(가로로 열리는 포털 페이지)로 이동했습니다.

기본적으로 웹앱의 블레이드는 **개요** 페이지를 표시합니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 블레이드의 왼쪽에 있는 탭에서는 열 수 있는 다른 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 블레이드](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

블레이드의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.

* 사용자 지정 DNS 이름 매핑
* 사용자 지정 SSL 인증서 바인딩
* 지속적 배포 구성
* 수평 및 수직 확장
* 사용자 인증 추가

## <a name="clean-up-resources"></a>리소스 정리
 
다른 자습서에서 이러한 리소스가 필요하지 않으면([다음 단계](#next) 참조) 다음 명령을 실행하여 삭제할 수 있습니다. 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 MongoDB 데이터베이스 만들기
> * Node.js 앱을 MongoDB에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

사용자 지정 DNS 이름을 매핑하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"] 
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

