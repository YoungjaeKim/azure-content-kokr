<properties
	pageTitle="Azure 저장소 복제 | Microsoft Azure"
	description="description = Microsoft Azure 저장소 계정의 데이터는 항상 내구성 및 고가용성을 위해 복제됩니다. 복제 옵션은 (LRS) 로컬 중복 저장소(LRS), 영역 중복 저장소 (ZRS), 지역 중복 저장소 (GRS) 및 읽기 액세스 지역 중복 저장소 (RA-GRS)에 포함 됩니다."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2016"
	ms.author="tamram"/>

# Azure 저장소 복제

Microsoft Azure 저장소 계정의 데이터는 항상 내구성 및 고가용성을 보증하도록 복제되며 일시적인 하드웨어 오류가 발생 하는 경우에도 [Azure 저장소 SLA](https://azure.microsoft.com/support/legal/sla/storage)을 충족합니다.

저장소 계정을 만들면 다음 복제 옵션 중 하나를 선택해야 합니다.

- [LRS(로컬 중복 저장소)](#locally-redundant-storage)
- [ZRS(영역 중복 저장소)](#zone-redundant-storage)
- [GRS(지역 중복 저장소)](#geo-redundant-storage)
- [RA-GRS(읽기 액세스 지역 중복 저장소)](#read-access-geo-redundant-storage)

다음 표에서 후속 섹션에서는 각 유형의 복제를 더 자세히 다루고 LRS, ZRS, GRS, RA-GRS 간의 차이점에 대해 간략하게 설명합니다.


| 복제 전략 | LRS | ZRS | GRS | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| 데이터가 여러 시설에 걸쳐 복제됩니다. | 아니요 | 예 | 예 | 예 |
| 기본 위치와 보조 위치에서 데이터를 읽을 수 있습니다. | 아니요 | 아니요 | 아니요 | 예 |
| 별도 노드에서 유지 관리되는 데이터 복사본 수입니다. | 3 | 3 | 6 | 6 |

다른 이중화 옵션에 대한 가격 정보를 보려면 [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

## 로컬 중복 저장소

로컬 중복 저장소(LRS)는 저장소 계정을 만든 지역 내의 데이터를 복제합니다. 지속성을 최대화하려면 저장소 계정의 데이터에 대해 작성된 모든 요청에는 세 번 복제됩니다. 이러한 3개의 복제본은 각기 별도 오류 도메인 및 업그레이드 도메인에 상주합니다. 오류 도메인(FD)은 실패의 물리적 단위를 나타내는 노드 그룹이며 동일한 물리적 랙에 속하는 노드로 간주될 수 있습니다. UD(업그레이드 도메인)는 서비스 업그레이드(롤아웃) 중 함께 업그레이드되는 노드 그룹입니다. 세 개의 복제본이 UR 및 FD에 걸쳐 분포되어 하드웨어 오류가 단일 랙에 영향 주는 경우 및 롤아웃 중 노드가 업그레이드된 경우더라도 데이터를 사용할 수 있는지 확인합니다. 3개의 복제본 모두에 쓰여진 경우에만 요청은 성공적으로 반환합니다.

GRS(지역 중복 저장소)는 대부분의 응용 프로그램에 대해 권장되는 반면, 로컬 중복 저장소는 특정 시나리오에서 적합할 수 있습니다.

- LRS는 GRS보다 저렴하여 더 높은 처리량을 제공합니다. 응용 프로그램이 쉽게 재구성될 수 있는 데이터를 저장하는 경우 LRS를 선택할 수 있습니다.

- 일부 응용 프로그램은 데이터 관리 요구 사항으로 인해 단일 지역 내에서만 데이터를 복제하도록 제한됩니다.

- 응용 프로그램에 자체 지리적 복제 전략이 있는 경우 GRS이 필요하지 않을 수 있습니다.


## 영역 중복 저장소

영역 중복 저장소(ZRS)가 LRS보다 더 나은 경우, 단일 지역 내 또는 두 개 지역에 걸쳐 2~3개 시설에서 데이터를 복제하며 높은 영속성을 제공합니다. 저장소 계정에서 ZRS를 사용하도록 설정된 경우, 데이터가 시설 중 하나에서 장애가 발생 하더라도 지속됩니다.


>[AZURE.NOTE]  ZRS는 현재 블록 BLOB에만 사용할 수 있으며, 2014-02-14 이상 버전에서만 지원됩니다. 저장소 계정을 만들고 영역 중복 복제를 선택한 후에는 다른 복제 유형으로 또는 그 반대로 변환할 수 없습니다.


## 지역 중복 저장소

지역 중복 저장소(GRS)는 주 지역에서 수백 마일 떨어져 있는 보조 지역에 데이터를 복제합니다. 저장소 계정에서 GRS를 활성화하면, 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해이더라도 데이터는 지속됩니다.

GRS를 활성화 하는 저장소 계정의 경우, 먼저 업데이트가 기본 지역으로 커미트되며, 여기서 세 번 복제됩니다. 그런 다음 업데이트는 두 번째 지역에 복제하며 별도 오류 도메인 및 업그레이드 도메인에 걸쳐 세 번 복제됩니다.


> [AZURE.NOTE] GRS를 사용하여 데이터 쓰기 요청이 보조 지역에 비동기적으로 복제 됩니다. GRS에 대한 옵션은 기본 지역에 대해 작성된 요청의 대기 시간에 영향을 주지 않는다는 점이 중요합니다. 그러나 비동기 복제는 지연과 관련되므로, 지역 재해가 발생한 경우 기본 지역에서 데이터를 복구할 수 없는 것처럼 아직 보조 지역으로 복제되지 않은 변경 내용이 손실 될 수 있습니다.
 
저장소 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 보조 지역은 기본 지역에 따라 결정되며 변경할 수 없습니다. 기본 및 보조 지역 쌍에 대한 최신 정보를 보려면 [Azure 영역](https://azure.microsoft.com/regions/)을 참조하세요.
 
## 읽기 액세스 지역 중복 저장소

읽기 액세스 지역 중복 저장소(RA-GRS)는 GRS에서 제공한 두 지역에 걸쳐 복제하는 것 외에도 보조 위치에서 데이터에 대한 읽기 전용 액세스를 제공하여 저장소 계정의 가용성을 최대화합니다. 데이터를 사용할 수 없는 기본 지역에서 응용 프로그램은 보조 지역에서 데이터를 읽을 수 있습니다.

보조 지역에서 데이터를 읽기 전용 액세스를 설정할 때 데이터는 저장소 계정에 대한 기본 끝점 뿐만 아니라 보조 끝점에서 사용할 수 있습니다. 보조 끝점은 기본 끝점과 유사하지만 접미사`–secondary` 가 계정 이름에 추가됩니다. 예를 들어, Blob 서비스에 대한 기본 끝점이 `myaccount.blob.core.windows.net`인 경우, 보조 끝점은 `myaccount-secondary.blob.core.windows.net`입니다. 저장소 계정에 대한 액세스 키는 기본 및 보조 끝점에 대해 동일합니다.

## 다음 단계

- [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/)
- [Azure 저장소 계정 정보](storage-create-storage-account.md)
- [Azure 저장소 확장성 및 성능 목표](storage-scalability-targets.md)
- [Microsoft Azure 저장소 중복 옵션 및 읽기 액세스 지역 중복 저장소](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [SOSP 문서 - Azure 저장소: 일관성과 가용성이 뛰어난 클라우드 저장소 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

<!---HONumber=AcomDC_0615_2016-->