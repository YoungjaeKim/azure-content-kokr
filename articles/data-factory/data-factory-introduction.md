<properties 
	pageTitle="Azure 데이터 팩터리 소개" 
	description="Azure Data Factory 서비스를 통해 데이터 처리, 데이터 저장 및 데이터 이동 서비스를 구성하여 신뢰할 수 있는 정보를 생성하는 파이프라인을 만드는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="04/26/2016" 
	ms.author="spelluru"/>

# Azure Data Factory 서비스 소개

## 개요
데이터 팩터리는 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 원자재를 가져다가 완제품으로 만들기 위해 장비를 실행하는 제조 공장처럼 데이터 팩터리는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다.

데이터 팩터리는 온-프레미스 및 클라우드 데이터 원본 및 SaaS 모두에서 작업하면서 데이터를 수집, 준비, 변환, 분석 및 게시합니다. 빅 데이터 컴퓨팅 요구를 위해 [Azure HDInsight(Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) 및 [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) 같은 서비스를 사용하여 데이터를 변환하기 위해, [Azure 기계 학습](https://azure.microsoft.com/documentation/services/machine-learning/)으로 분석 솔루션을 운영할 수 있도록 하기 위해 데이터 팩터리를 사용하여 서비스를 관리되는 데이터 흐름 파이프라인으로 작성합니다. 테이블 형식의 모니터링 뷰를 능가하는 데이터 팩터리의 풍성한 시각화를 사용하여 데이터 파이프라인 간의 종속성과 계보를 신속하게 나타냅니다. 단일의 통합된 보기에서 모든 데이터 흐름 파이프라인을 모니터링하여 쉽게 문제를 파악하고 모니터링 경고를 설정합니다.

![개요](./media/data-factory-introduction/data-factory-overview.png)

**그림 1.** 다양한 온-프레미스 데이터 원본으로부터 데이터를 수집하고 받아들여서 준비하고 다양한 변환으로 데이터를 구성하고 분석한 후 소비를 위해 바로 사용할 수 있는 데이터를 게시합니다.

깊을 통찰력을 끌어내도록 다양한 형태와 크기의 데이터를 신뢰할만한 일정으로 수집하고 변환하고 게시하기 위해 필요할 때마다 데이터 팩터리를 사용할 수 있습니다. 데이터 팩터리는 분석 파이프라인이 필요한 다양한 산업의 여러 시나리오에서 가용성이 높은 데이터 흐름 파이프라인을 만드는 데 사용됩니다. 온라인 소매 업체는 데이터 팩터리를 사용하여 고객의 탐색 활동을 기반으로 맞춤형 [제품 권장 목록](data-factory-product-reco-usecase.md)을 만듭니다. 게임 제작사는 [자사 마케팅](data-factory-customer-profiling-usecase.md) 캠페인의 효율을 파악하는 데 데이터 팩터리를 사용합니다. [고객 사례 연구](data-factory-customer-case-studies.md)를 검토하여 고객이 데이터 팩터리를 사용하는 방법과 이유를 직접 들어보세요.

> [AZURE.VIDEO azure-data-factory-overview]

## 주요 개념

Azure Data Factory에는 입력 및 출력 데이터, 처리 이벤트, 필요한 데이터 흐름을 실행하는데 필요한 일정과 리소스를 정의하기 위해 함께 작용하는 몇 가지 주요 엔터티가 있습니다.

![주요 개념](./media/data-factory-introduction/key-concepts.png)

**그림 2.** 데이터 집합, 활동, 파이프라인 및 연결된 서비스 간의 관계


### 활동
활동은 데이터에 수행할 작업을 정의합니다. 각 활동은 0개 이상의 [데이터 집합](data-factory-create-datasets.md)을 입력으로 받아 하나 이상의 데이터 집합을 출력합니다. 활동은 Azure Data Factory의 오케스트레이션 단위입니다. 예를 들어 하나의 데이터 집합에서 다른 데이터 집합으로의 데이터 복사를 오케스트레이션하기 위해 [복사 활동](data-factory-data-movement-activities.md)을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 [Hive 활동](data-factory-data-transformation-activities.md)을 사용할 수 있습니다. Azure Data Factory는 다양한 데이터 변환, 분석 및 데이터 이동 활동을 제공합니다.

### 파이프라인
[파이프라인](data-factory-create-pipelines.md)은 활동의 논리적 그룹화입니다. 파이프라인은 여러 활동을 함께 작업을 수행하는 하나의 단위로 그룹화하기 위해 사용됩니다. 예를 들어 몇 개의 변환 활동 시퀀스에서 로그 파일 데이터를 정리해야 하는 경우가 있습니다. 이 시퀀스에는 복잡한 일정과 종속성이 있어서 오케스트레이션과 자동화가 필요할 수 있습니다. 이런 모든 활동이 “CleanLogFiles”라는 단일의 파이프라인으로 그룹화될 수 있습니다. “CleanLogFiles”는 각각의 개별적인 활동을 독립적으로 관리하는 대신 하나의 단위로 배포하고, 예약하고, 삭제할 수 있습니다.

### 데이터 집합
[데이터 집합](data-factory-create-datasets.md)은 활동의 입력 또는 출력으로 사용하려는 데이터에 대해 명명된 참조/포인터입니다. 데이터 집합은 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터 구조를 식별합니다.

### 연결된 서비스
연결된 서비스는 데이터 팩터리가 외부 리소스에 연결하기 위해 필요한 정보를 정의합니다. 연결된 서비스는 데이터 팩터리 내에서 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server, Oracle DB, 파일 공유 또는 Azure Blob 저장소 계정을 포함하(지만 여기에 국한되지 않)는 데이터 저장소를 나타내기 위해 사용됩니다. 위의 설명대로 데이터 집합은 연결된 서비스를 통해 데이터 팩터리에 연결된 데이터 저장소 내의 구조를 나타냅니다.
- 활동의 실행을 호스팅할 수 있는 계산 리소스를 나타내기 위해 사용됩니다. 예를 들어, “HDInsightHive Activity”는 HDInsight Hadoop 클러스터에서 실행됩니다.

데이터 집합, 활동, 파이프라인, 연결된 서비스라는 네 가지의 단순한 개념만으로 시작할 준비가 되었습니다. 처음부터 시작하여 [첫 번째 파이프라인을 빌드](data-factory-build-your-first-pipeline.md)하거나 [Data Factory 샘플](data-factory-samples.md)에 있는 지침에 따라 즉시 사용 가능한 샘플을 배포할 수 있습니다.

## 지원되는 지역
이번에는 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서 데이터 팩터리를 만들 수 있습니다. 그러나 데이터 팩터리는 계산 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 계산 서비스에 액세스할 수 있습니다.

Azure 데이터 팩터리 자체는 데이터를 저장하지 않습니다. 데이터 기반 흐름을 만들어서 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores) 간의 데이터 이동을 조정하고 다른 지역 또는 온-프레미스 환경에서 [계산 서비스](data-factory-compute-linked-services.md)를 사용하여 데이터의 처리를 조정할 수 있습니다. 또한 프로그래밍 방식 및 UI 메커니즘을 모두 사용하여 [워크플로를 모니터링하고 관리](data-factory-monitor-manage-pipelines.md)할 수 있습니다.

자체 Azure 데이터 팩터리가 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서만 사용할 수 있지만, 여러 지역에서 데이터 팩터리의 데이터 이동을 지원하는 서비스를 [전역적으로](data-factory-data-movement-activities.md#global) 사용할 수 있습니다. 데이터 저장소가 방화벽 뒤에 있는 경우 온-프레미스 환경에 설치된 [데이터 관리 게이트웨이](data-factory-move-data-between-onprem-and-cloud.md)가 대신 데이터를 이동시킵니다.

예를 들어 Azure HDInsight 클러스터 및 Azure 기계 학습과 같은 계산 환경이 서유럽 지역 외부에서 실행되고 있다고 가정해보겠습니다. 북유럽에서 Azure 데이터 팩터리 인스턴스를 만들고 활용할 수 있으며 이를 사용하여 서유럽의 계산 환경에 작업을 예약할 수 있습니다. 데이터 팩터리 서비스에서 계산 환경에 작업을 트리거하는 데는 몇 밀리초가 걸리지만 사용자의 계산 환경에서 작업을 실행하는 데는 걸리는 시간은 변경되지 않습니다.

나중에 Azure에서 지원하는 모든 지리적 위치에서 Azure 데이터 팩터리를 사용하게 될 예정입니다.
  

<!---HONumber=AcomDC_0518_2016-->