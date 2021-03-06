<properties
   pageTitle="Azure SQL 데이터 웨어하우스에 대한 모범 사례 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스에 대한 솔루션을 개발하면서 알아야 할 권장 사항 및 모범 사례입니다. 이 내용은 성공적인 개발에 도움이 됩니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/02/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL 데이터 웨어하우스에 대한 모범 사례

이 문서는 Azure SQL 데이터 웨어하우스에서 최적의 가격/성능을 달성할 수 있는 여러 가지 모범 사례에 대한 모음입니다. 이 문서에 설명된 개념 중 일부는 매우 기본적이고 설명하기 쉬우며 일부 개념은 보다 고급 내용으로 전체적인 내용을 간략히 훑어봅니다. 이 문서의 목적은 몇 가지 기본 지침을 제공하고 데이터 웨어하우스를 구축할 때 고려할 중요한 사항에 대한 인지도를 높이는 것입니다. 각 섹션에서는 개념을 소개한 후 보다 심도 있게 개념을 다루는 자세한 문서를 안내합니다.

Azure SQL 데이터 웨어하우스를 이제 시작하는 사용자라면 이 문서가 어려울 수 있습니다. 항목은 주로 중요한 순서로 배치되어 있습니다. 우선은 처음 세 항목에 집중하는 것이 부담이 없을 것입니다. SQL 데이터 웨어하우스 사용에 익숙해지면 돌아와서 더 많은 항목을 살펴봅니다. 금방 모든 항목을 이해할 수 있습니다. 처음에는 부담스럽게 느껴질 수 있지만 시간이 지나고 이러한 항목을 경험하면서 익숙해집니다.

## 일시 중지 및 규모 조정으로 비용 절감
SQL 데이터 웨어하우스의 핵심 기능은 사용 중이 아닐 때 일시 중지하는 기능으로 계산 리소스의 비용이 발생되지 않도록 하는 것입니다. 또 다른 주요 기능은 리소스 규모를 조정하는 기능입니다. 일시 중지 및 크기 조정은 Azure 포털 또는 PowerShell 명령을 통해 수행할 수 있습니다. 이러한 기능으로 사용 중이 아닐 때 데이터 웨어하우스의 비용을 크게 절감할 수 있으므로 이러한 기능을 익혀 보세요. 항상 데이터 웨어하우스에 액세스할 수 있도록 하고 싶은 경우 일시 중지보다는 가장 작은 크기인 DW100으로 규모를 축소하는 것이 좋습니다.

참고 항목 [계산 리소스 일시 중지][], [계산 리소스 다시 시작][], [계산 리소스 크기 조정][]


## 일시 중지 또는 크기 조정 전 트랜잭션 비우기 
SQL 데이터 웨어하우스를 일시 중지하거나 규모를 조정할 경우 배후에서 데이터베이스 인스턴스가 중지됩니다. 즉, 모든 진행 중인 쿼리가 취소됩니다. 간단한 SELECT 쿼리를 취소하는 것은 빠른 작업이며 인스턴스를 일시 중지하거나 규모를 조정하는 데 소요되는 시간에 거의 영향을 주지 않습니다. 하지만 트랜잭션 쿼리는 데이터 또는 데이터 구조를 수정하므로 신속하게 중지되지 않을 수 있습니다. **트랜잭션 쿼리는 전체를 완료하거나 변경 사항을 롤백해야 합니다.** 트랜잭션 쿼리로 완료된 작업을 롤백하는 데는 쿼리가 적용된 원래 변경 사항보다 시간이 훨씬 더 오래 걸릴 수 있습니다. 예를 들어 행을 삭제하고 1시간 동안 실행 중인 쿼리를 취소하는 경우 시스템에서 삭제된 행을 다시 삽입하는 데 1시간이 소요될 수 있습니다. 트랜잭션이 진행 중인 동안 일시 중지 또는 크기 조정을 실행할 경우 일시 중지 및 크기 조정 시 롤백이 완료될 때까지 기다린 후 진행할 수 있으므로 시간이 오래 소요되는 것처럼 보일 수 있습니다.

참고 항목 [SQL 데이터 웨어하우스의 트랜잭션][], [SQL 데이터 웨어하우스에 대한 트랜잭션 최적화][]

## 통계 유지 관리
도움이 되는 열에서 통계를 자동으로 감지하고 만들거나 업데이트하는 SQL Server와 달리, SQL 데이터 웨어하우스에서는 통계를 수동으로 유지 관리해야 합니다. 나중에 이를 변경하는 계획을 수행하지만 지금은 SQL 데이터 웨어하우스 계획이 최적화되도록 통계를 유지 관리하려고 합니다. 최적화 프로그램으로 만든 계획은 사용 가능한 통계만큼 훌륭합니다. **모든 열에 샘플링된 통계를 만드는 것이 통계로 시작하는 쉬운 방법입니다.** 데이터에 중요한 변경 내용이 있는 것 만큼 통계를 업데이트하는 것도 중요합니다. 보수적인 접근 방법은 매일 또는 각 로드 후 통계를 업데이트하는 것일 수 있습니다. 통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다. 모든 통계를 유지하는 데 시간이 너무 오래 소요되는 경우 어떤 열에 통계가 있고 어떤 열에서 자주 업데이트가 필요한지 더 선별적으로 시도해볼 수 있습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다. **조인에 포함된 열, WHERE 절에 사용된 열과 GROUP BY에 있는 열에서 통계를 유지하면 가장 큰 이점을 얻게 됩니다.**

참고 항목 [SQL 데이터 웨어하우스의 통계 관리][], [CREATE STATISTICS(Transact-SQL)][], [UPDATE STATISTICS(Transact-SQL)][]

## INSERT 문을 일괄 처리로 그룹화
INSERT 문을 이용한 작은 테이블에 대한 일회성 로드 또는 조회의 정기적인 다시 로드는 `INSERT INTO MyLookup VALUES (1, 'Type 1')`와 같은 문으로 사용자 요구에 대해 제대로 수행될 수 있습니다. 그러나 수천 또는 수백만 행을 하루 종일 로드해야 하는 경우 단일 항목 INSERTS만으로는 불가능할 수 있습니다. 대신 파일에 기록하는 프로세스를 개발하고 다른 프로세스를 주기적으로 함께 제공하여 이 파일을 로드합니다.

참고 항목 [Insert(Transact-SQL)][]
 
## PolyBase를 사용하여 데이터를 신속하고 로드하고 내보내기
SQL 데이터 웨어하우스는 Azure Data Factory, PolyBase, BCP 등의 여러 도구를 통해 데이터 로드 및 내보내기를 지원합니다. 성능이 중요하지 않은 소량의 데이터에는 어떤 도구를 사용해도 사용자 요구 사항을 충족할 수 있습니다. 그러나 대량의 데이터를 로드 또는 내보내거나 빠른 성능이 필요한 경우 PolyBase가 가장 좋습니다. PolyBase는 SQL 데이터 웨어하우스의 MPP(대규모 병렬 처리) 아키텍처를 활용하도록 디자인되었으므로 데이터 로드 및 내보내기 성능이 다른 도구보다 빠릅니다. PolyBase 로드는 CTAS 또는 INSERT INTO를 사용하여 실행할 수 있습니다. **CTAS를 사용하여 트랜잭션 로깅을 최소화하고 CTAS는 데이터를 로드하는 가장 빠른 방법입니다.** Azure Data Factory는 또한 PolyBase 로드를 지원합니다. PolyBase는 Gzip 파일을 포함한 다양한 파일 형식을 지원합니다. **gzip 텍스트 파일을 사용할 때 처리량을 최대화하려면 파일을 60개 이상의 파일로 나누어 로드의 병렬 처리를 최대화합니다.** 총 처리량을 더 빠르게 하기 위해 데이터를 동시에 로드하는 것이 좋습니다.

참고 항목 [SQL 데이터 웨어하우스에 데이터 로드][], [SQL 데이터 웨어하우스의 PolyBase 사용을 위한 가이드][], [Azure SQL 데이터 웨어하우스 로딩 패턴 및 전략][], [Azure Data Factory를 사용하여 데이터 로드][], [Azure Data Factory를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터 이동][], [CREATE EXTERNAL FILE FORMAT(Transact-SQL)][], [SQL 데이터 웨어하우스의 CTAS(Create Table As Select)][]

## 해시 배포 대형 테이블
기본적으로 테이블은 라운드 로빈 분산됩니다. 따라서 사용자는 해당 테이블이 배포되는 방식을 결정하지 않고도 테이블 생성을 간편하게 시작할 수 있습니다. 라운드 로빈 테이블은 일부 워크로드에 대해서는 잘 작동하지만 대체로 배포 열을 선택하면 성능이 훨씬 향상됩니다. 열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다. 예를 들어 order\_id로 배포되는 주문 테이블과 order\_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order\_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 되어 데이터 이동 작업을 제거합니다. 단계가 적을수록 쿼리는 빨라집니다. 데이터 이동이 적을수록 쿼리는 빨라집니다. 이 설명에서는 간략한 내용만 다룹니다. 분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다. 성능을 향상시킬 수 있도록 배포 열을 선택하는 방법과 CREATE TABLES 문의 WITH 절에서 분산된 테이블을 정의하는 방법은 아래 링크를 참조하세요.

참고 항목 [해시 배포 및 SQL 데이터 웨어하우스의 쿼리 성능에 미치는 영향][], [해시 분산 테이블 및 라운드 로빈 분산 테이블 간에 선택][], [CREATE TABLE(Azure SQL 데이터 웨어하우스, 병렬 데이터 웨어하우스)][], [CREATE TABLE AS SELECT(Azure SQL 데이터 웨어하우스)][]

## 과도한 분할 피하기
데이터를 분할하면 파티션 전환 또는 파티션 제거로 스캔 최적화를 통해 데이터를 매우 효율적으로 유지 관리할 수 있지만 과도하게 분할할 경우 쿼리 속도가 느려질 수 있습니다. SQL Server에서 제대로 작동할 수 있는 높은 세분성 파티션 전략은 SQL 데이터 웨어하우스에서는 제대로 작동하지 않을 수 있습니다. 너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다. 배후에서 SQL 데이터 웨어하우스가 사용자 데이터를 60개 데이터베이스로 분할했다면 100개 파티션이 있는 한 개의 테이블을 만들면 실제로는 6000개의 파티션이 생기는 것입니다. 각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다. SQL Server에서 작업한 것보다 낮은 세분성을 고려합니다. 예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

참고 항목 [SQL 데이터 웨어하우스의 테이블 파티션][]

## 트랜잭션 크기 최소화
INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다. 긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다. INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다. 예를 들어 1시간 소요될 것으로 예상되는 INSERT가 있는 경우 가능하면 INSERT를 4부분까지 나누어 각각 15분만에 실행되도록 합니다. CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 로깅 사례를 최소한 활용하여 롤백 위험을 줄입니다. 롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다. 예를 들어 테이블에서 order\_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조). 분할되지 않은 테이블의 경우 DELETE를 사용하는 대신, 테이블에 유지할 데이터를 기록하도록 CTAS를 사용하는 것이 좋습니다. CTAS에 동일한 시간이 소요되는 경우 매우 최소한의 트랜잭션 로깅을 포함하므로 실행하기에 훨씬 더 안전한 작업이며 필요할 경우 신속하게 취소할 수 있습니다.

참고 항목 [SQL 데이터 웨어하우스의 트랜잭션][], [SQL 데이터 웨어하우스에 대한 트랜잭션 최적화][], [SQL 데이터 웨어하우스의 테이블 파티션][], [TRUNCATE TABLE(Transact-SQL)][], [ALTER TABLE(Transact-SQL)][], [SQL 데이터 웨어하우스의 CTAS(Create Table As Select)][]

## 가능한 가장 작은 열 크기 사용
DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다. 이것은 CHAR 및 VARCHAR 열에 대해 특히 중요합니다. 열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다. 모든 문자 열을 큰 기본 길이로 정의하지 마세요. 또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

참고 항목 [CREATE TABLE(Azure SQL 데이터 웨어하우스, 병렬 데이터 웨어하우스)][]

## 임시 데이터에 대해 임시 힙 테이블 사용
SQL 데이터 웨어하우스에서 일시적으로 데이터를 방문하는 경우 힙 테이블을 사용하면 전체 프로세스가 더 빨라지는 것을 알 수 있습니다. 변환을 추가로 실행하기 전에 준비하기 위해서만 데이터를 방문하는 경우 테이블을 힙 테이블에 로드하면 데이터를 클러스터형 columnstore 테이블에 로드할 때보다 훨씬 빨라집니다. 또한 데이터를 임시 테이블에 로드하면 테이블을 영구 저장소에 로드할 때보다 훨씬 빠르게 로드됩니다. 임시 테이블은 "#"으로 시작되며 테이블을 생성한 세션에서만 액세스할 수 있으므로 일부 시나리오에서는 작동하지 않을 수 있습니다. 힙 테이블은 CREATE TABLE의 WITH 절에 정의됩니다. 임시 테이블을 사용하는 경우 해당 임시 테이블에서도 통계를 작성해야 합니다.

참고 항목 [SQL 데이터 웨어하우스의 임시 테이블][], [CREATE TABLE (Azure SQL 데이터 웨어하우스, 병렬 데이터 웨어하우스)][], [CREATE TABLE AS SELECT(Azure SQL 데이터 웨어하우스)][]

## 클러스터형 Columnstore 테이블 최적화
클러스터형 columnstore 인덱스는 Azure SQL 데이터 웨어하우스에 데이터를 저장할 수는 가장 효율적인 방법 중 하나입니다. 기본적으로 SQL 데이터 웨어하우스의 테이블은 클러스터형 ColumnStore로 만들어집니다. columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다. 메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다. 세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다. 클러스터형 columnstore 테이블에 대해 세그먼트 품질을 감지 및 개선하는 단계별 지침은 [문제 해결][]의 **클러스터형 Columnstore 세그먼트 품질** 섹션을 참조하세요. 좋은 품질의 columnstore 세그먼트를 얻는 것이 매우 중요하므로 중형 또는 대형 리소스 클래스를 활용하는 로드에 특수한 사용자 ID를 만드는 것이 일반적으로 좋습니다. 사용하는 DWU 수가 적을수록 사용자 로드에 할당할 리소스 클래스가 커집니다.

columnstore 테이블은 일반적으로 테이블당 100개 이상의 행이 있고 각 SQL 데이터 웨어하우스 테이블이 60개 테이블로 파티션될 때까지 데이터를 압축된 columnstore 세그먼트에 푸시하지 않으므로 경험에 따르면 테이블에서 행 수가 6천만 개를 초과하지 않는다면 columnstore 테이블은 쿼리에 이점을 제공하지 않습니다. 6천만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 포함하는 것이 적절하지 않을 수 있습니다. 오히려 상황을 악화시킬 수 있습니다. 또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다. 테이블에 파티션 수가 100개라면 클러스터형 columnstore에서 이점을 얻으려면 60억 개 이상의 행을 포함해야 합니다(60개 배포 * 100개 파티션 * 1백만 행). 이 예에서 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다. 또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블로 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다. Columnstore 테이블은 보조 인덱스를 아직 지원하지 않습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.

참고 항목 [문제 해결][], [Azure SQL 데이터 웨어하우스의 columnstore 인덱스 관리][], [Columnstore 인덱스 가이드][]

## 더 큰 리소스 클래스를 사용하여 쿼리 성능 향상
SQL 데이터 웨어하우스는 메모리를 쿼리에 할당하는 방법으로 리소스 그룹을 사용합니다. 기본적으로 모든 사용자가 배포당 100MB의 메모리를 부여하는 소형 리소스 클래스에 할당됩니다. 항상 60개의 배포가 있고 각 배포에는 최소 100MB가 부여되므로 시스템 전체에서 총 메모리 할당량은 6,000MB이거나 6GB가 약간 못됩니다. 큰 조인 또는 클러스터형 columnstore 테이블에 로드와 같은 특정 쿼리는 큰 메모리를 할당하는 것이 도움이 됩니다. 순수 검색과 같은 일부 쿼리에는 어떤 이점도 보이지 않습니다. 한편 더 큰 리소스 클래스를 활용하면 동시성에 영향을 주므로 모든 사용자를 큰 리소스 클래스로 이동하기 전에 이를 고려하려고 합니다.
 
참고 항목 [SQL 데이터 웨어하우스의 동시성 및 워크로드 관리][]

## 더 작은 리소스 클래스를 사용하여 동시성 증가
사용자 쿼리에 긴 지연이 있는 것처럼 보이는 경우 사용자가 큰 리소스 클래스에서 실행 중이고 많은 동시성 슬롯을 사용 중이어서 다른 쿼리가 큐에 대기될 수 있습니다. 사용자 쿼리가 큐에 대기되었는지 확인하려면 `SELECT * FROM sys.dm_pdw_waits`를 실행하여 모든 행이 반환되었는지 확인합니다.

참고 항목 [SQL 데이터 웨어하우스의 동시성 및 워크로드 관리][], [sys.dm\_pdw\_waits(Transact-SQL)][]

## DMV를 사용하여 쿼리 모니터링 및 최적화
SQL 데이터 웨어하우스에는 쿼리 실행을 모니터링하는 데 사용할 수 있는 여러 DMV가 있습니다. 다음 모니터링 문서는 쿼리 실행의 세부 정보를 확인하는 방법에 대한 단계별 지침을 안내합니다. 이러한 DMV에서 쿼리를 신속하게 찾으려면 쿼리에 LABEL 옵션을 사용하는 것이 도움이 될 수 있습니다.

참고 항목 [DMV를 사용하여 워크로드 모니터링][], [SQL 데이터 웨어하우스에서 레이블을 사용하여 쿼리 계측][], [OPTION 절(Transact-SQL)][], [sys.dm\_exec\_sessions(Transact-SQL)][], [sys.dm\_pdw\_exec\_requests(Transact-SQL)][], [sys.dm\_pdw\_request\_steps(Transact-SQL)][], [sys.dm\_pdw\_sql\_requests(Transact-SQL)][], [sys.dm\_pdw\_dms\_workers(Transact-SQL)], [DBCC PDW\_SHOWEXECUTIONPLAN(Transact-SQL)][], [sys.dm\_pdw\_waits(Transact-SQL)][]

## 기타 리소스
여러 위치에서 Azure SQL 데이터 웨어하우스를 사용하는 방법에 대한 정보를 확인할 수 있습니다. 이 문서는 Azure 설명서의 일부이며 MSDN 문서뿐만 아니라 다른 Azure 문서에 대한 많은 링크를 포함합니다. 이러한 모든 문서에서 고객의 피드백을 모니터링하고 자주 업데이트합니다. 문서가 도움이 되었다고 생각된다면 "이 페이지가 도움이 되었나요?"라는 질문에 답하여 알려주세요. 예 또는 아니요 중에 어떻게 대답했든 의견을 제공할 수 있습니다. 문서가 도움이 되었다고 생각되지만 피드백이 있는 경우 예를 클릭하고 문서를 개선할 수 있는 의견을 어떤 것이든 추가해 주세요. 이 질문에 대한 메시지가 표시되지 않으면 모든 Azure 문서 끝에서 항상 찾을 수 있으며 MSDN 문서의 경우 모든 MSDN 페이지 오른쪽 위 모서리에서 "제안 사항이 있나요?" 링크를 사용합니다. 고객의 피드백을 가치 있게 사용하며 대부분의 피드백에 대해 조치를 취합니다.

SQL 데이터 웨어하우스에 대한 **기능 제안**이 있는 경우 [Azure SQL 데이터 웨어하우스 피드백][] 페이지를 사용하세요. 요청을 추가하거나 다른 요청을 투표해 주시면 기능의 순위를 지정하는 데 도움이 됩니다.

[Azure SQL 데이터 웨어하우스 MSDN 포럼][]은 다른 사용자 및 SQL 데이터 웨어하우스 제품 그룹에 질문을 할 수 있는 장소로 마련되었습니다. Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다. Stack Overflow에 질문하는 것을 선호하는 경우 [Azure SQL 데이터 웨어하우스 Stack Overflow 포럼][]도 제공합니다.

<!--Image references-->

<!--Article references-->
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[SQL 데이터 웨어하우스의 동시성 및 워크로드 관리]: sql-data-warehouse-develop-concurrency.md
[SQL 데이터 웨어하우스의 CTAS(Create Table As Select)]: sql-data-warehouse-develop-ctas.md
[SQL 데이터 웨어하우스의 PolyBase 사용을 위한 가이드]: sql-data-warehouse-load-polybase-guide.md
[해시 배포 및 SQL 데이터 웨어하우스의 쿼리 성능에 미치는 영향]: sql-data-warehouse-develop-hash-distribution-key.md
[SQL 데이터 웨어하우스에 데이터 로드]: sql-data-warehouse-overview-load.md
[Azure Data Factory를 사용하여 데이터 로드]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase in SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md
[Azure SQL 데이터 웨어하우스의 columnstore 인덱스 관리]: sql-data-warehouse-manage-columnstore-indexes.md
[SQL 데이터 웨어하우스의 통계 관리]: sql-data-warehouse-develop-statistics.md
[DMV를 사용하여 워크로드 모니터링]: sql-data-warehouse-manage-monitor.md
[Azure Data Factory를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터 이동]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[SQL 데이터 웨어하우스에 대한 트랜잭션 최적화]: sql-data-warehouse-develop-best-practices-transactions.md
[계산 리소스 일시 중지]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[계산 리소스 다시 시작]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[계산 리소스 크기 조정]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Table design in SQL Data Warehouse]: sql-data-warehouse-develop-table-design.md
[SQL 데이터 웨어하우스의 테이블 파티션]: sql-data-warehouse-develop-table-partitions.md
[SQL 데이터 웨어하우스의 임시 테이블]: sql-data-warehouse-develop-temporary-tables.md
[SQL 데이터 웨어하우스의 트랜잭션]: sql-data-warehouse-develop-transactions.md
[문제 해결]: sql-data-warehouse-troubleshoot.md
[SQL 데이터 웨어하우스에서 레이블을 사용하여 쿼리 계측]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE(Transact-SQL)]: https://msdn.microsoft.com/library/ms190273.aspx
[Columnstore 인덱스 가이드]: https://msdn.microsoft.com/library/gg492088.aspx
[CREATE TABLE (Azure SQL 데이터 웨어하우스, 병렬 데이터 웨어하우스)]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE(Azure SQL 데이터 웨어하우스, 병렬 데이터 웨어하우스)]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE EXTERNAL FILE FORMAT(Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT(Azure SQL 데이터 웨어하우스)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE STATISTICS(Transact-SQL)]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN(Transact-SQL)]: https://msdn.microsoft.com/library/mt204017.aspx
[Insert(Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION 절(Transact-SQL)]: https://msdn.microsoft.com/library/ms190322.aspx
[sys.dm\_exec\_sessions(Transact-SQL)]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm\_pdw\_exec\_requests(Transact-SQL)]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_request\_steps(Transact-SQL)]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests(Transact-SQL)]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm\_pdw\_dms\_workers(Transact-SQL)]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_waits(Transact-SQL)]: https://msdn.microsoft.com/library/mt203893.aspx
[TRUNCATE TABLE(Transact-SQL)]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS(Transact-SQL)]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->
[해시 분산 테이블 및 라운드 로빈 분산 테이블 간에 선택]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL 데이터 웨어하우스 피드백]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL 데이터 웨어하우스 MSDN 포럼]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL 데이터 웨어하우스 Stack Overflow 포럼]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL 데이터 웨어하우스 로딩 패턴 및 전략]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/06/azure-sql-data-warehouse-loading-patterns-and-strategies

<!---HONumber=AcomDC_0608_2016-->