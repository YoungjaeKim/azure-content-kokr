   <properties
   pageTitle="Azure SQL 데이터 웨어하우스에 데이터 로드 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에 데이터 로드를 위한 일반적인 시나리오에 대해 알아봅니다. 여기에는 PolyBase, Azure Blob 저장소, 플랫 파일 및 디스크 배송 사용이 포함됩니다. 타사 도구를 사용할 수도 있습니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/17/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스에 데이터 로드

SQL 데이터 웨어하우스로 데이터를 로드하기 위한 시나리오 옵션 및 권장 사항 요약입니다.

데이터 로드에서 가장 어려운 부분은 일반적으로 로드할 데이터를 준비하는 것입니다. Azure는 Azure Blob 저장소를 다양한 서비스에 대한 공용 데이터 저장소로 사용하고 Azure 서비스 간의 통신 및 데이터 이동을 오케스트레이션하는 데 Azure Data Factory를 사용하여 로드를 간소화합니다. 이러한 프로세스는 MPP(대규모 병렬 처리)를 사용하는 PolyBase 기술과 통합하여 Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 병렬로 로드합니다.

샘플 데이터베이스를 로드하는 자습서의 경우 [샘플 데이터베이스 로드][]를 참조하세요.

## Azure Blob 저장소에서 로드
SQL 데이터 웨어하우스로 데이터를 가져오는 가장 빠른 방법은 PolyBase를 사용하여 Azure Blob 저장소에서 데이터를 로드하는 것입니다. PolyBase는 SQL 데이터 웨어하우스의 MPP(대규모 병렬 처리) 디자인을 사용하여 Azure Blob 저장소에서 병렬로 데이터를 로드합니다. PolyBase를 사용하려면 T-SQL 명령이나 Azure Data Factory 파이프라인을 사용할 수 있습니다.

### 1\. PolyBase 및 T-SQL 사용

로드 프로세스 요약:

2. PolyBase는 현재 UTF-16을 지원하지 않으므로 데이터를 UTF-8 형식으로 지정합니다.
2. Azure Blob 저장소로 데이터를 이동하고 텍스트 파일에 저장합니다.
3. SQL 데이터 웨어하우스의 외부 개체를 데이터의 위치 및 형식을 정의하도록 구성합니다.
4. 새 데이터베이스 테이블에 병렬로 데이터를 로드하기 위해 T-SQL 명령을 실행합니다.

<!-- 5. Schedule and run a loading job. --> 

자습서의 경우 [Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터 로드(PolyBase)][]를 참조하세요.

### 2\. Azure Data Factory 사용

PolyBase를 사용하는 더 간단한 방법으로, PolyBase를 사용하여 Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 로드하는 Azure Data Factory 파이프라인을 만들 수 있습니다. 이 방법은 T-SQL 개체를 정의할 필요가 없으므로 구성이 빠릅니다. 외부 데이터를 가져오지 않고 쿼리를 해야 하는 경우 T-SQL을 사용합니다.

로드 프로세스 요약:

2. PolyBase는 현재 UTF-16을 지원하지 않으므로 데이터를 UTF-8 형식으로 지정합니다.
2. Azure Blob 저장소로 데이터를 이동하고 텍스트 파일에 저장합니다.
3. 데이터를 수집할 Azure Data Factory 파이프라인을 만듭니다. PolyBase 옵션을 사용합니다.
4. 파이프라인을 예약 및 실행합니다.

자습서의 경우 [Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터 로드(Azure Data Factory)][]를 참조하세요.


## SQL Server에서 로드
Integration Services(SSIS)를 사용하거나, 플랫 파일을 전송하거나, Microsoft로 디스크를 배송하여 SQL Server에서 SQL 데이터 웨어하우스로 데이터를 로드할 수 있습니다. 다른 로드 프로세스 요약 및 자습서에 대한 링크를 참조하세요.

SQL Server에서 SQL 데이터 웨어하우스로 전체 데이터 마이그레이션을 계획하려면 [마이그레이션 개요][]를 참조하세요.

### Integration Services(SSIS) 사용
이미 Integration Services(SSIS) 패키지를 사용하여 SQL Server에 로드하고 있다면 패키지를 업데이트하여 SQL Server를 원본으로, SQL 데이터 웨어하우스를 대상으로 사용할 수 있습니다. 이 방법은 빠르고 수행하기 쉬우며, 클라우드에서 데이터를 사용하기 위한 로드 프로세스 마이그레이션을 아직 시도하지 않은 경우에 좋은 방법입니다. 단점은 이 SSIS는 병렬 로드를 수행하지 않으므로 PolyBase를 사용하는 것보다 속도가 느릴 수 있습니다.

로드 프로세스 요약:

1. 원본용으로 SQL Server 인스턴스를 가리키고 대상용으로 SQL 데이터 웨어하우스 데이터베이스를 가리키도록 Integration Services 패키지를 수정합니다.
2. SQL 데이터 웨어하우스에 아직 스키마가 없는 경우 스키마를 SQL 데이터 웨어하우스로 마이그레이션합니다.
3. SQL 데이터 웨어하우스에서 지원되는 데이터 형식만 사용하도록 패키지의 매핑을 변경합니다.
3. 패키지를 예약 및 실행합니다.

자습서의 경우 [SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(SSIS)][]를 참조하세요.

### AZCopy 사용(10TB 미만의 데이터에 권장)
데이터 크기가 10TB 미만일 경우 SQL Server에서 플랫 파일로 데이터를 내보내고 Azure Blob 저장소에 파일을 복사한 다음 PolyBase를 사용하여 SQL 데이터 웨어하우스로 데이터를 로드할 수 있습니다.

로드 프로세스 요약:

1. bcp 명령줄 유틸리티를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보냅니다.
2. AZCopy 명령줄 유틸리티를 사용하여 플랫 파일에서 Azure Blob 저장소로 데이터를 복사합니다.
3. PolyBase를 사용하여 SQL 데이터 웨어하우스로 로드합니다.

자습서의 경우 [Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터 로드(PolyBase)][]를 참조하세요.

### bcp 사용
데이터의 양이 적을 경우 bcp를 사용하여 Azure SQL 데이터 웨어하우스에 직접 로드할 수 있습니다.

로드 프로세스 요약:
1. bcp 명령줄 유틸리티를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보냅니다.
2. bcp를 사용하여 플랫 파일에서 SQL 데이터 웨어하우스로 데이터를 직접 로드합니다.

자습서의 경우 [SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(bcp)][]를 참조하세요.


### 가져오기/내보내기 사용(10TB를 초과하는 데이터에 권장)
10TB를 초과하는 크기의 데이터를 Azure로 이동하려는 경우 디스크 전달 서비스인 [가져오기/내보내기][]를 사용하는 것이 좋습니다.

로드 프로세스 요약
2. bcp 명령줄 유틸리티를 사용하여 SQL Server에서 전송 가능한 디스크에 있는 플랫 파일로 데이터를 내보냅니다.
3. Microsoft에 디스크를 배송합니다.
4. Microsoft는 SQL 데이터 웨어하우스에 데이터를 로드합니다.


## 추천

상당수의 파트너에게는 로드 솔루션이 있습니다. 자세한 내용을 알아보려면 [솔루션 파트너][] 목록을 참조하세요.


비관계형 원본에서 가져온 데이터를 SQL 데이터 웨어하우스로 로드하려는 경우 로드하기 전에 데이터를 행과 열로 변환해야 합니다. 변환된 데이터는 데이터베이스에 저장할 필요 없이 텍스트 파일에 저장할 수 있습니다.

새로 로드한 데이터에 대한 통계를 만듭니다. Azure SQL 데이터 웨어하우스는 자동 만들기 또는 통계 자동 업데이트를 아직 지원하지 않습니다. 쿼리에서 최상의 성능을 얻으려면, 데이터를 처음 로드하거나 데이터에 상당한 변화가 발생한 후에 모든 테이블의 모든 열에서 통계가 만들어지는 것이 중요합니다. 자세한 내용은 [통계][]를 참조하세요.


## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터 로드(PolyBase)]: sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터 로드(Azure Data Factory)]: sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(SSIS)]: sql-data-warehouse-load-from-sql-server-with-integration-services.md
[SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(bcp)]: sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: sql-data-warehouse-load-from-sql-server-with-azcopy.md

[샘플 데이터베이스 로드]: sql-data-warehouse-load-sample-databases.md
[마이그레이션 개요]: sql-data-warehouse-overview-migrate.md
[솔루션 파트너]: sql-data-warehouse-integrate-solution-partners.md
[개발 개요]: sql-data-warehouse-overview-develop.md
[통계]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[가져오기/내보내기]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

<!---HONumber=AcomDC_0615_2016-->