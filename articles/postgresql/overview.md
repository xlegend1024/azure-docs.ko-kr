---
title: "PostgreSQL 관계형 데이터베이스 서비스에 대한 Azure Database 개요 | Microsoft Docs"
description: "PostgreSQL 관계형 데이터베이스 서비스에 대한 Azure Docs 개요를 제공합니다."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: overview
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8aae9994f8fc6bb8e4e5368a1495dd257be0417f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="what-is-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database란?

PostgreSQL용 Azure Database는 커뮤니티 버전의 오픈 소스 [PostgreSQL](https://www.postgresql.org/) 데이터베이스 엔진을 기반으로 개발자용으로 빌드된 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. 이 서비스는 공개 미리 보기 상태입니다. PostgreSQL용 Azure Database는 다음과 같은 기능을 제공합니다.
- 여러 서비스 수준에서 예측 가능한 성능
- 응용 프로그램 가동 중지 시간 없는 동적 확장성
- 기본 제공되는 고가용성
- 데이터 보호

이러한 모든 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 이러한 기능을 사용하면 가상 컴퓨터와 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 응용 프로그램 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 또한 오픈 소스 도구 및 선택한 플랫폼을 사용하여 응용 프로그램을 개발하고 새로운 기술을 배울 필요 없이 비즈니스에서 요구하는 속도 및 효율성을 계속 제공할 수 있습니다. 

이 문서에서는 성능, 확장성 및 관리 효율성과 관련된 PostgreSQL용 Azure Database의 핵심 개념과 기능을 소개합니다. 이러한 빠른 시작을 참조하여 다음 항목을 시작하세요.

- [Azure Portal을 사용하여 PostgreSQL용 Azure Database 만들기](quickstart-create-server-database-portal.md)
- [Azure CLI를 사용하여 PostgreSQL용 Azure Database 만들기](quickstart-create-server-database-azure-cli.md)

일련의 Azure CLI 및 PowerShell 샘플은 다음을 참조하세요.

- [PostgreSQL용 Azure Database에 대한 Azure CLI 샘플](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>가동 중지 시간 없이 성능 및 규모 조정

현재 PostgreSQL용 Azure Database 서비스는 기본 및 표준 서비스 계층을 제공합니다. 각 서비스 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 [다양한 수준의 성능, IOPS 보장 및 기능](concepts-service-tiers.md)을 제공합니다. 한 달에 몇 달러의 작은 서버에 첫 번째 앱을 빌드한 다음 언제든지 수동 또는 프로그래밍 방식으로 서비스 계층 내의 [성능 수준을 변경](scripts/sample-scale-server-up-or-down.md)하여 솔루션의 요구 사항을 충족시킬 수 있습니다. 응용 프로그램이나 고객에게 가동 중지 시간 없이 이를 수행할 수 있습니다. 동적 확장성을 통해 데이터베이스는 급변하는 리소스 요구 사항에 투명하게 대응할 수 있으며, 필요할 때 필요한 리소스에 대해서만 비용을 지불할 수 있습니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

기능을 사용하고 해제할 때를 어떻게 결정하나요? 계산 단위에 따라 성능 등급을 함께 결합한 기본 제공 성능 모니터링 및 경고 기능을 사용합니다. 이 도구를 사용하면 현재 또는 프로젝트된 성능 요구에 기반하여 계산 단위를 확장 또는 축소하는 경우 미치는 영향을 신속하게 평가할 수 있습니다. 자세한 내용은 [PostgreSQL용 Azure Database 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 항목 이해](./concepts-service-tiers.md)를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지

Azure의 업계 선도적인 99.99% 가용성(미리 보기 상태에서 사용할 수 없음) SLA(서비스 수준 약정)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 모든 PostgreSQL 서버용 Azure Database에서 다른 방법으로 구입 또는 설계, 구축 및 관리해야 하는 기본 제공 보안, 내결함성 및 데이터 보호를 활용합니다. PostgreSQL용 Azure Database에서 각 서비스 계층은 해당 방식으로 시작하고 실행하고 계속하는 데 사용할 수 있는 일련의 포괄적인 비즈니스 연속성 기능 및 옵션을 제공합니다. [특정 시점 복원](howto-restore-server-portal.md)을 사용하면 데이터베이스를 최대 35일 전의 상태로 복원할 수 있습니다. 또한 데이터베이스를 호스팅하는 데이터 센터에서 중단이 발생하면 최신 백업의 지역 중복 복사본에서 데이터베이스를 복원할 수 있습니다.

## <a name="secure-your-data"></a>데이터 보호

Azure Database 서비스는 PostgreSQL용 Azure Database가 액세스를 제한하고, 미사용 및 사용 중인 데이터를 보호하고, 작업을 모니터링하는 데 도움이 되는 기능을 사용하여 보관하도록 데이터 보안을 유지해왔습니다. Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://www.microsoft.com/TrustCenter/Security/default.aspx)를 방문하세요.

## <a name="next-steps"></a>다음 단계
- 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
- [첫 번째 PostgreSQL용 Azure Database를 만들](./quickstart-create-server-database-portal.md)어서 시작합니다.
- Python, PHP, Ruby, C\#, Java, Node.js에서 첫 번째 앱 빌드: [연결 라이브러리](./concepts-connection-libraries.md)

