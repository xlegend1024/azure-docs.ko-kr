---
title: "Azure Cosmos DB: Java 및 MongoDB API에서 콘솔 앱 작성 | Microsoft Docs"
description: "Azure Cosmos DB MongoDB API에 연결 및 쿼리하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 1683afd842294b3b45ae4d0e53bbecdccadc1ed5
ms.contentlocale: ko-kr
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: Java 및 Azure Portal에서 MongoDB API 콘솔 앱 빌드

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정, 문서 데이터베이스 및 컬렉션을 만드는 방법을 보여 줍니다. 그런 다음, [MongoDB Java 드라이버](https://docs.mongodb.com/ecosystem/drivers/java/)에서 작성한 콘솔 앱을 빌드 및 배포합니다. 

## <a name="prerequisites"></a>필수 조건

* 이 샘플을 실행하기 전에 다음 필수 조건이 있어야 합니다.
   * JDK 1.7+(JDK가 없는 경우 `apt-get install default-jdk` 실행)
   * Maven(Maven이 없는 경우 `apt-get install maven` 실행)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>컬렉션 추가

새 사용자 데이터베이스 이름을 **db**로 지정하고 새로운 컬렉션 이름을 **coll**로 지정합니다.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 MongoDB API 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 그런 다음 Visual Studio에서 솔루션을 엽니다. 

## <a name="review-the-code"></a>코드 검토

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. `Program.cs` 파일을 열어 보면 이들 코드 줄에서 Azure Cosmos DB 리소스를 만드는 것을 알 수 있습니다. 

* DocumentClient가 초기화됩니다.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 새 데이터베이스 및 컬렉션이 만들어집니다.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* `MongoCollection.insertOne`을 사용하여 일부 문서가 삽입됩니다.

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* `MongoCollection.find`을 사용하여 일부 쿼리가 수행됩니다.

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());        
    ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. 계정에서 **빠른 시작**을 선택하고 Java를 선택한 다음 연결 문자열을 클립보드에 복사합니다.

2. `Program.java` 파일을 열고 MongoClientURI 생성자에 대한 인수를 연결 문자열로 바꿉니다. 이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 
    
## <a name="run-the-console-app"></a>콘솔 앱 실행

1. 터미널에서 `mvn package`를 실행하여 필요한 npm 모듈을 설치합니다.

2. 터미널에서 `mvn exec:java -D exec.mainClass=GetStarted.Program`을 실행하여 Java 응용 프로그램을 시작합니다.

이제 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md)를 사용하여 이 새 데이터를 쿼리, 수정 및 사용할 수 있습니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 컬렉션을 만들고, 콘솔 앱을 실행하는 방법을 알아보았습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md)



