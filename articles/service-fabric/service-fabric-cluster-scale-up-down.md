<properties
   pageTitle="서비스 패브릭 클러스터 크기 조정 | Microsoft Azure"
   description="각 노드 형식/VM 크기 집합에 대한 자동 크기 조정 규칙을 설정하여 수요에 따라 서비스 패브릭 클러스터의 크기를 조정합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>


# 자동 크기 조정 규칙을 사용하여 서비스 패브릭 클러스터 크기 조정

가상 컴퓨터 크기 집합은 가상 컴퓨터의 컬렉션을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. 서비스 패브릭 클러스터에 정의된 모든 노드 유형은 별도의 VM 크기 집합으로 설치됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. [서비스 패브릭 노드 형식](service-fabric-cluster-nodetypes.md) 문서에서 자세히 알아보세요. 클러스터에서 서비스 패브릭 노드 형식은 백 엔드에서 VM 크기 집합으로 구성되므로 각 노드 형식/VM 크기 집합에 대한 자동 크기 조정 규칙을 설정해야 합니다.

>[AZURE.NOTE] 사용자의 구독에 이 클러스터를 형성할 새 VM을 추가하기에 충분한 코어가 있어야 합니다. 현재는 모델 유효성 검사가 없으므로 할당량 한도에 도달하면 배포 시간 오류가 발생합니다.

## 크기를 조정할 노드 형식/VM 크기 집합 선택

현재는 포털을 사용하여 VM 크기 집합에 대한 자동 크기 조정 규칙을 지정할 수 없으므로 Azure PowerShell(1.0+)을 사용하여 노드 형식을 나열한 후 자동 크기 조정 규칙을 추가하도록 합니다.

클러스터를 구성하는 VM 크기 집합 목록을 가져오려면 다음 cmdlet을 실행합니다.

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## 노드 형식/VM 크기 집합에 대한 자동 크기 조정 규칙 설정

클러스터에 여러 노드 형식이 있는 경우 크기를 조정(확장 또는 축소)할 각 노드 형식/VM 크기 집합에 대해 이 작업을 수행해야 합니다. 자동 크기 조정을 수행하기 전에 포함해야 할 노드 수를 고려합니다. 기본 노드 형식에 대해 포함해야 할 최소 노드 수는 선택한 안정성 수준에 따라 달라집니다. [안정성 수준](service-fabric-cluster-capacity.md)에 대해 자세히 알아보세요.

>[AZURE.NOTE]  기본 노드 형식을 최소 수보다 적게 축소하면 클러스터가 불안정해지거나 중단됩니다. 이 경우 응용 프로그램 및 시스템 서비스에 대한 데이터가 손실될 수 있습니다.

현재는 자동 크기 조정 기능이 응용 프로그램에서 서비스 패브릭에 보고할 수 있는 로드에 따라 결정되지 않습니다. 따라서 현재는 자동 크기 조정이 각 VM 크기 집합 인스턴스에서 내보낸 성능 카운터에 의해서만 결정됩니다.

[각 VM 크기 집합에 대해 자동 크기 조정을 설정](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)하는 지침을 따르세요.

>[AZURE.NOTE] 규모 축소 시나리오에서 노드 형식에 골드 또는 실버 내구성 수준이 없다면 적절한 노드 이름과 함께 [Remove-ServiceFabricNodeState cmdlet](https://msdn.microsoft.com/library/azure/mt125993.aspx)을 호출해야 합니다.

## Service Fabric Explorer에서 볼 수 있는 동작

클러스터를 강화하는 경우 Service Fabric Explorer는 클러스터의 일부로 노드 수(VM 크기 집합 인스턴스)를 반영합니다. 그러나 클러스터 규모를 축소할 때 적절한 노드 이름과 함께 [Remove-ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx)를 호출하지 않으면 제거된 노드/VM 인스턴스가 계속 비정상 상태로 표시됩니다.

다음은 이 동작에 대한 설명입니다.

Service Fabric Explorer에 나열된 노드는 서비스 패브릭 시스템 서비스(특히 FM)가 클러스터에 포함된 노드 수에 대해 알고 있는 내용이 반영된 것입니다. VM 크기 집합의 규모를 축소하면 VM이 삭제되지만 FM 시스템 서비스는 해당 노드(삭제된 VM에 매핑된 노드)가 돌아올 것으로 생각합니다. 따라서 Service Fabric Explorer는 성능 상태가 오류 또는 알 수 없음이어도 해당 노드를 계속 표시합니다.

VM이 제거될 때 노드가 제거되는지 확인하기 위한 두 가지 옵션이 있습니다.

1) 클러스터에서 노드 형식에 대해 골드 또는 실버(곧 사용 가능) 내구성 수준을 선택하면 인프라 통합이 제공됩니다. 그러면 규모를 축소할 때 시스템 서비스(FM)에서 해당 노드를 자동으로 제거합니다. [내구성 수준에 대한 세부 정보](service-fabric-cluster-capacity.md)를 참조하세요.

2) VM 인스턴스가 규모 축소되면 [Remove-ServiceFabricNodeState cmdlet](https://msdn.microsoft.com/library/mt125993.aspx)을 호출해야 합니다.

>[AZURE.NOTE] 가용성을 유지하고 상태를 보존하기 위해 서비스 패브릭 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업](service-fabric-reliable-services-backup-restore.md)을 처음으로 수행하는 경우 외에는 일반적으로 클러스터의 모든 컴퓨터를 종료하는 것은 안전하지 않습니다.

## 다음 단계
또한 클러스터 용량 계획, 클러스터 업그레이드 및 서비스 분할에 대해 자세히 알아 보려면 다음을 읽습니다.

- [클러스터 용량 계획](service-fabric-cluster-capacity.md)
- [클러스터 업그레이드](service-fabric-cluster-upgrade.md)
- [최대 크기를 위해 상태 저장 서비스 분할](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0608_2016-->