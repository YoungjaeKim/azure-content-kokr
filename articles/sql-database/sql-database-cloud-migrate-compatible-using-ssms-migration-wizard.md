<properties
   pageTitle="Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사를 사용하여 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, Microsoft Azure 데이터베이스 마법사"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사를 사용하여 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션


> [AZURE.SELECTOR]
- [SSMS 마이그레이션 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [BACPAC 파일에서 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [트랜잭션 복제자](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

SQL Server Management Studio의 Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사는 [호환되는 SQL Server 데이터베이스](sql-database-cloud-migrate.md)를 Azure SQL 데이터베이스 서버로 마이그레이션합니다.

## Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사 사용

> [AZURE.NOTE] 아래 단계에서는 [프로비전된 SQL 데이터베이스 서버](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)가 있다고 가정합니다.

1. 최신 버전의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

    > [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).

2. Management Studio를 열고 개체 탐색기에서 마이그레이션할 SQL Server 데이터베이스에 연결합니다.
3. 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **Deploy Database to Microsoft Azure SQL Database…**(Microsoft Azure SQL 데이터베이스에 데이터베이스 배포...)를 클릭합니다.

	![작업 메뉴에서 Azure에 배포 합니다.](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	배포 마법사에서 **다음**을 클릭한 후 **연결**을 클릭하여 SQL 데이터베이스 서버에 대한 연결을 구성합니다.

	![작업 메뉴에서 Azure에 배포 합니다.](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. 서버에 연결 대화 상자에서 SQL 데이터베이스 서버에 연결하기 위한 연결 정보를 입력합니다.

	![작업 메뉴에서 Azure에 배포 합니다.](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	새 데이터베이스 이름에 대해 **새 데이터베이스 이름**을 입력하고 **Microsoft Azure SQL 데이터베이스 버전**([서비스 계층](sql-database-service-tiers.md)), **최대 데이터베이스 크기**, **서비스 목표**(성능 수준), 마이그레이션 프로세스 중 이 마법사가 만드는 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일의 **임시 파일 이름**을 입력합니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	데이터베이스를 마이그레이션하도록 마법사를 완료합니다. 데이터베이스의 크기와 복잡성에 따라 배포는 몇 분에서 몇 시간이 걸릴 수 있습니다. 이 마법사에서 호환성 문제를 검색하면 오류가 화면에 표시되고 마이그레이션이 진행되지 않습니다. 데이터베이스 호환성 문제를 해결하는 방법에 대한 참고 자료는 [데이터베이스 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues.md)을 참조하세요.

7.	개체 탐색기를 사용하여 Azure SQL 데이터베이스 서버에서 마이그레이션된 데이터베이스에 연결합니다.
8.	Azure 포털을 사용하여 데이터베이스와 해당 속성을 봅니다.

## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->