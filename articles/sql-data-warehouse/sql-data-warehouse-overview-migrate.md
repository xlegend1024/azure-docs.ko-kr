---
title: "SQL Data Warehouse에 솔루션 마이그레이션| Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스 플랫폼에 솔루션을 가져오기 위한 마이그레이션 지침"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.contentlocale: ko-kr
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>SQL 데이터 웨어하우스에 솔루션 마이그레이션
SQL 데이터 웨어하우스는 요구 사항에 맞게 탄력적으로 확장되는 분산된 데이터베이스 시스템입니다. 성능과 확장을 모두 유지 관리할 수 있도록 SQL Server 내의 모든 기능이 SQL 데이터 웨어하우스 내에서 구현되는 것은 아닙니다. 다음 마이그레이션 토픽은 솔루션을 SQL 데이터 웨어하우스에 마이그레이션하기 위한 일부 주요 요소에 대해 다룹니다. 확장을 위한 데이터 웨어하우스의 디자인은 다양한 디자인 패턴을 도입하므로 일반적인 접근 방식이 항상 최선은 아닙니다. 따라서 기존 솔루션을 조정하여 SQL 데이터 웨어하우스에서 제공하는 분산 플랫폼의 활용도를 최대한 높일 수도 있습니다.

SQL 데이터 웨어하우스는 Microsoft Azure를 기반으로 하는 플랫폼이라는 사실을 기억하는 것도 중요합니다. 따라서 마이그레이션의 일부에 데이터를 클라우드로 전송하는 것이 포함될 수도 있습니다. 데이터 전송은 그 자체로 하나의 주제이며 특히 볼륨이 증가하면 신중하게 고려해야 합니다. 데이터 전송 및 데이터 로드는 개별 주제에 해당합니다.

## <a name="migration-guidance"></a>마이그레이션 지침
마이그레이션을 시작하기 전에 다음 문서들을 끝까지 읽고 일부 제품의 차이점과 기본 개념을 파악하세요.

* [스키마 마이그레이션][Migrate your schema]
* [데이터 마이그레이션][Migrate your data]
* [코드 마이그레이션][Migrate your code]

## <a name="next-steps"></a>다음 단계
CAT(고객 자문 팀)에서 블로그를 통해 게시하는 몇 가지 유용한 SQL Data Warehouse 관련 지침도 확인할 수 있습니다.  [Azure SQL Data Warehouse로의 데이터 마이그레이션 실습][Migrating data to Azure SQL Data Warehouse in practice] 문서에서 마이그레이션 관련 추가 지침을 확인해 보세요.

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

