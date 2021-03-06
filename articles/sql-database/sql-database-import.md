---
title: "BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기 | Microsoft Docs"
description: "BACPAC 파일을 가져와 새 Azure SQL 데이터베이스를 만듭니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 42ac754ba24ba3e8f96d4fb6f3abb97169056c8e
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>새 Azure SQL Database로 BACPAC 파일 가져오기

이 문서에서는 새 Azure SQL 데이터베이스로 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일을 가져오는 방법을 살펴봅니다. 이 문서에서는 다음 방법에 대해 설명합니다.
- [Azure Portal](https://portal.azure.com)
- [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티
- [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet

## <a name="overview"></a>개요

아카이브에서 데이터베이스를 가져와야 할 때 또는 다른 플랫폼에서 마이그레이션할 때 BACPAC 파일에서 데이터베이스 스키마 및 데이터를 가져올 수 있습니다. BACPAC 파일은 SQL Server 데이터베이스의 메타데이터와 데이터를 포함하고 있는 ZIP 파일이며 확장명은 BACPAC입니다. BACPAC 파일은 Azure Blob Storage(표준 저장소만 해당)에서 또는 온-프레미스 위치의 로컬 저장소에서 가져올 수 있습니다. 가져오기 속도를 최대화하려면 P6처럼 더 높은 서비스 계층과 성능 수준을 지정한 다음 가져오기가 성공하면 적절하게 규모 감축하는 것이 좋습니다. 또한 가져오기 후의 데이터베이스 호환성 수준은 원본 데이터베이스의 호환성 수준에 따라 결정됩니다. 

> [!IMPORTANT] 
> Azure SQL Database로 데이터베이스를 마이그레이션한 후에는 데이터베이스를 현재 호환성 수준에서(AdventureWorks2008R2 데이터베이스에 대해 수준 100) 또는 더 높은 수준에서 작동하도록 선택할 수 있습니다. 특정 호환성 수준에서 데이터베이스를 운영하기 위한 옵션 및 그 영향에 대한 자세한 내용은 [ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요. 호환성 수준과 관련된 추가 데이터베이스 수준 설정에 대한 내용은 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 참조하세요.   >

> [!NOTE]
> 새 데이터베이스로 BACPAC을 가져오려면 먼저 Azure SQL Database 논리 서버를 만들어야 합니다. SQLPackage를 사용하여 Azure SQL Database로 SQL Server 데이터베이스를 마이그레이션하는 방법을 보여주는 자습서는 [SQL Server 데이터베이스 마이그레이션](sql-database-migrate-your-sql-server-database.md)을 참조하세요.
>

## <a name="azure-portal"></a>Azure 포털

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure Blob Storage에 저장된 BACPAC 파일로 Azure SQL Database를 만드는 지침을 제공합니다. Azure Portal을 사용하여 가져오기는 Azure Blob Storage에서 BACPAC 파일을 가져오는 것만 지원합니다.

Azure Portal을 사용하여 가져오려면 해당 데이터베이스의 페이지를 열고 도구 모음에서 **가져오기**를 클릭합니다. 저장소 계정 및 컨테이너를 지정하고 가져올 *.bacpac 파일을 선택합니다. 새 데이터베이스의 크기(일반적으로 원본과 동일)를 선택하고 대상 SQL Server 자격 증명을 제공합니다.  

   ![데이터베이스 가져오기](./media/sql-database-import/import.png)

가져오기 작업의 진행률을 모니터링하려면 가져올 데이터베이스가 포함된 논리 서버에 대한 페이지를 엽니다. 아래로 **작업**이 나올 때까지 스크롤한 다음 **가져오기/내보내기** 기록을 클릭합니다.

### <a name="monitor-the-progress-of-the-import-operation"></a>가져오기 작업의 진행률 모니터링
1. **SQL Server**를 클릭합니다.
2. 복원할 서버를 클릭합니다.
3. SQL Server 블레이드의 작업 영역에서 **가져오기/내보내기 기록**을 클릭합니다.
   
   ![가져오기](./media/sql-database-import/import.png)
   ![가져오기 상태](./media/sql-database-import/import-status.png)

4. 데이터베이스가 서버에서 라이브 상태인지 확인하려면 **SQL 데이터베이스**를 클릭하고 새 데이터베이스를 **온라인** 상태인지 확인합니다.

## <a name="sqlpackage"></a>SQLPackage

[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 SQL 데이터베이스를 가져오려면 [매개 변수 및 속성 가져오기](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties)를 참조하세요. SQLPackage 유틸리티는 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 [Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)가 함께 제공되며, Microsoft 다운로드 센터에서 직접 최신 버전의 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 다운로드할 수 있습니다.

대부분의 프로덕션 환경에서 규모 및 성능에 SQLPackage 유틸리티를 사용하는 것이 좋습니다. BACPAC 파일을 사용하는 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.

로컬 저장소에서 Azure SQL Database 논리 서버(이 예에서는 **mynewserver20170403**)로 **AdventureWorks2008R2** 데이터베이스를 가져오는 방법에 대한 샘플 스크립트는 다음 SQLPackage 명령을 참조하세요. 이 스크립트는 이름이 **myMigratedDatabase**이고, 서비스 계층이 **프리미엄**이고, 서비스 목표가 **P6**인 새 데이터베이스를 만드는 방법을 보여줍니다. 각자 환경에 맞게 적절한 값으로 변경하면 됩니다.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

   ![sqlpackage 가져오기](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Azure SQL Database 논리 서버는 포트 1433에서 수신 대기합니다. 회사 방화벽 내에서 Azure SQL Database 논리 서버로 연결을 시도하면 성공적인 연결을 위해 회사 방화벽에서 이 포트가 열려야 합니다.
>

이 예제는 Active Directory 유니버설 인증으로 SqlPackage.exe를 사용하여 데이터베이스를 가져오는 방법을 보여줍니다.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="powershell"></a>PowerShell

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet을 사용하여 Azure SQL Database 서비스에 데이터베이스 가져오기 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

가져오기 요청의 상태를 확인하려면 [Get AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet을 사용합니다. 요청 직후에 이 명령을 실행하면 **Status: InProgress**가 반환됩니다. **Status: Succeeded**가 표시되면 가져오기가 완료된 것입니다.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

## <a name="next-steps"></a>다음 단계
* 가져온 SQL 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리 수행](sql-database-connect-query-ssms.md)을 참조하세요.
* BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.
* 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.




