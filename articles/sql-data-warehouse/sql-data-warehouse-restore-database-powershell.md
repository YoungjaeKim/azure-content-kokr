<properties
   pageTitle="Azure SQL 데이터 웨어하우스 복원(PowerShell) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 복원을 위한 PowerShell 작업."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스 복원(PowerShell)

> [AZURE.SELECTOR]
- [개요][]
- [포털][]
- [PowerShell][]
- [REST (영문)][]

이 문서에서는 PowerShell을 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## 시작하기 전에

### SQL 데이터베이스의 DTU 용량을 확인합니다. 

각 SQL 데이터 웨어하우스는 SQL Server 논리 서버에 의해 호스팅됩니다. 이 논리 서버는 DTU로 측정된 용량 제한을 가집니다. SQL 데이터 웨어하우스를 복원하기 전에 데이터베이스를 호스팅하는 SQL Server 논리 서버에 복원 중인 데이터베이스에 대한 충분한 DTU 용량이 있는지 확인하십시오. [DTU 할당량을 보고 늘리는 방법][]에 대한 자세한 내용은 이 블로그 게시물을 참조하세요.

### PowerShell 설치

SQL 데이터 웨어하우스에서 Azure PowerShell을 사용하려면 Azure PowerShell 버전 1.0 이상을 설치해야 합니다. **Get-Module -ListAvailable -Name Azure**를 실행하여 버전을 확인할 수 있습니다. 최신 버전은 [Microsoft 웹 플랫폼 설치 관리자][]를 통해 설치할 수 있습니다. 최신 버전 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법][]을 참조하세요.

## 활성 또는 일시 중지된 데이터베이스 복원

스냅숏에서 데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][] PowerShell cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 데이터베이스의 복원 지점을 나열합니다.
5. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.
6. 데이터베이스를 원하는 복원 지점으로 복원합니다.
7. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.

## 삭제된 데이터베이스 복원

삭제된 데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 삭제된 데이터베이스가 포함된 구독을 선택합니다.
4. 삭제된 특정 데이터베이스를 가져옵니다.
5. 삭제된 데이터베이스를 복원합니다.
6. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.

## Azure 지역에서 복원

데이터베이스를 복구하려면 [Restore-AzureRmSqlDatabase][] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 복구하려는 데이터베이스를 가져옵니다.
5. 데이터베이스 복구 요청을 만듭니다.
6. 지역에서 복원된 데이터베이스의 상태를 확인합니다.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

### 지역 복원을 수행한 후 데이터베이스 구성

복구된 데이터베이스 프로덕션을 준비하는 데 유용한 검사 목록입니다.

1. **연결 문자열 업데이트**: 클라이언트 도구의 연결 문자열이 새로 복구된 데이터베이스를 가리키는지 확인합니다.
2. **방화벽 규칙 수정**: 대상 서버에서 방화벽 규칙을 확인하고 서버 및 새로 복구된 데이터베이스와 클라이언트 컴퓨터 또는 Azure의 연결이 설정되어 있는지 확인합니다.
3. **서버 로그인 및 데이터베이스 사용자 확인**: 응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스트하는 서버에 존재하는지 확인합니다. 누락된 로그인을 다시 만들고 복구된 데이터베이스에 대한 적절한 사용 권한을 부여합니다. 
4. **감사 사용**: 데이터베이스에 액세스하기 위해 감사가 필요한 경우 데이터베이스 복구 후에 감사 사용을 설정해야 합니다.

원본 데이터베이스가 TDE를 사용할 수 있는 경우 복구된 데이터베이스도 TDE를 사용할 수 있습니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: sql-database-business-continuity.md
[복구된 데이터베이스 마무리]: sql-database-recovered-finalize.md
[Azure PowerShell 설치 및 구성 방법]: powershell-install-configure.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST (영문)]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[DTU 할당량을 보고 늘리는 방법]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft 웹 플랫폼 설치 관리자]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0615_2016-->