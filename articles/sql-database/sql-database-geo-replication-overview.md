<properties
	pageTitle="Azure SQL 데이터베이스에 대한 활성 지역 복제"
	description="활성 지역 복제를 사용하면 Azure 데이터 센터 중에서 데이터베이스의 복제본을 4개 설정할 수 있습니다."
	services="sql-database"
	documentationCenter="na"
	authors="stevestein"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="06/14/2016"
	ms.author="sstein" />

# 개요: SQL 데이터베이스 활성 지역 복제

> [AZURE.SELECTOR]
- [개요](sql-database-geo-replication-overview.md)
- [Azure 포털](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

활성 지역 복제를 사용하면 동일하거나 다른 데이터 센터 위치(하위 지역)에 최대 4개의 읽기 기능한 보조 데이터베이스를 구성할 수 있습니다. 보조 데이터베이스를 데이터 센터 정전 또는 주 데이터베이스에 연결하지 못하는 경우 사용할 수 있습니다.

>[AZURE.NOTE] 현재 활성 지역 복제(읽기 가능한 보조)는 모든 서비스 계층에 있는 모든 데이터베이스에 대해 사용 가능합니다. 2017년 4월, 읽을 수 없는 보조 유형은 사용 중지되며 기존의 읽을 수 없는 데이터베이스는 읽을 수 있는 보조로 자동으로 업그레이드됩니다.

어떠한 이유로 주 데이터베이스가 실패하거나 단순히 오프라인으로 전환해야 하는 경우 보조 데이터베이스로*장애 조치*할 수 있습니다. 장애 조치가 보조 데이터베이스 중 하나로 활성화된 경우 모든 다른 보조가 새 보조로 자동으로 연결됩니다.

활성 지역 복제 기능은 매커니즘을 구현하여 동일한 Microsoft Azure 지역이나 다른 지역(지역 중복)에서 데이터베이스 중복을 제공합니다. 활성 지역 복제는 데이터베이스에서 커밋된 트랜잭션을 서로 다른 서버의 최대 4개의 데이터베이스 복사본으로 비동기적으로 복제합니다. 활성 지역 복제가 구성되면 지정된 서버에 보조 데이터베이스가 생성됩니다. 원본 데이터베이스는 주 데이터베이스가 됩니다. 주 데이터베이스는 커밋된 트랜잭션을 각 보조 데이터베이스로 비동기적으로 복제합니다. 지정된 지점에서 보조 데이터베이스는 주 데이터베이스보다 약간 뒤에 있을 수 있는 반면 보조 데이터는 주 데이터베이스에 커밋된 변경 내용과 트랜잭션 측면에서 항상 일관되도록 보장됩니다.

활성 지역 복제의 주요 이점 중 하나는 복구 시간이 매우 짧은 데이터베이스 수준 재해 복구 솔루션을 제공한다는 것입니다. 다른 지역의 서버에 보조 데이터베이스를 배치하면 응용 프로그램의 복원력이 극대화됩니다. 지역 간 중복을 사용하면 자연 재해, 치명적인 사람의 실수 또는 악의적 행동으로 인해 데이터 센터의 일부 또는 전체가 영구적으로 손실되더라도 응용 프로그램을 복구할 수 있습니다. 다음 그림은 미국 중북부 지역에 주 데이터베이스, 미국 중남부 지역에 보조 데이터베이스가 있는 프리미엄 데이터베이스에 구성된 활성 지역 복제의 예입니다.

![지역에서 복제 관계](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

또 다른 주요 이점으로, 보조 데이터베이스를 읽고 작업 보고와 같은 읽기 전용 작업을 오프로드하는 데 사용할 수 있습니다. 보조 데이터베이스를 부하 분산에만 사용하려는 경우 동일한 지역에 주 데이터베이스로 만들면 됩니다. 그러나 이 방법은 치명적인 오류에 대한 응용 프로그램의 복원력이 향상되지 않습니다.

활성 지역 복제를 사용할 수 있는 다른 시나리오는 다음을 포함할 수 있습니다.

- **데이터베이스 마이그레이션**: 활성 지역 복제를 사용하여 최소 가동 중지 시간으로 하나의 서버에서 다른 온라인으로 데이터베이스를 마이그레이션할 수 있습니다.
- **응용 프로그램 업그레이드**: 응용 프로그램을 업그레이드하는 동안 추가 보조 데이터베이스를 장애 복구 복사본으로 만들 수 있습니다.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 응용 프로그램(서비스) 종단 간 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 저장소 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 응용 프로그램의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## 활성 지역 복제 기능
활성 지역 복제 기능은 다음과 같은 필수 기능을 제공합니다.

- **자동 비동기 복제**: 보조 데이터베이스는 기존 데이터베이스에 추가하는 방법으로만 만들 수 있습니다. 보조 데이터베이스는 다른 Azure SQL 데이터베이스 서버에만 만들 수 있습니다. 한번 만들면 보조 데이터베이스는 주 데이터베이스에서 복사된 데이터로 채워집니다. 이 프로세스를 시드라고 합니다. 보조 데이터베이스가 생성 및 시드되면 주 데이터베이스에 대한 업데이트가 보조 데이터베이스에 자동으로 비동기적으로 복사됩니다. 즉, 트랜잭션은 보조 데이터베이스에 복제되기 전에 주 데이터베이스에 커밋됩니다. SQL 데이터베이스는 시드 작업이 완료되면 보조 데이터베이스가 트랜잭션 측면에서 항상 일관성을 유지하도록 보장합니다. 보조 데이터베이스를 만드는 방법에 대한 자세한 내용은 [Transact-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제](sql-database-geo-replication-transact-sql.md) 및 [PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제](sql-database-geo-replication-powershell.md)를 참조하세요. Azure 포털을 사용하여 보조 데이터베이스를 만드는 방법에 대한 자세한 내용은 [Azure 포털로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-portal.md)을 참조하세요.

- **여러 보조 데이터베이스**: 두 개 이상의 보조 데이터베이스는 주 데이터베이스와 응용 프로그램의 중복성 및 보호 수준을 높입니다. 보조 데이터베이스가 여러 개 있는 경우 보조 데이터베이스 중 하나가 실패하더라도 응용 프로그램이 계속해서 보호됩니다. 보조 데이터베이스가 하나밖에 없는 경우 새 보조 데이터베이스를 만들 때까지 응용 프로그램이 더 높은 위험에 노출됩니다.

- **읽을 수 있는 보조 데이터베이스**: 응용 프로그램은 주 데이터베이스에 액세스하는데 사용된 동일한 보안 주체를 사용하여 읽기 전용 작업에 대한 보조 데이터베이스에 액세스할 수 있습니다. 보조 데이터베이스에서 실행되는 쿼리 때문에 주 데이터베이스(로그 재생)의 업데이트 복제가 지연되지 않도록 보장하기 위해 보조 데이터베이스는 스냅숏 격리 모드에서 작동합니다.

>[AZURE.NOTE] 주 데이터베이스에서 수신하는 스키마 업데이트가 있는 경우 보조 데이터베이스에 스키마 잠금이 필요하므로 보조 데이터베이스의 로그 재생이 지연됩니다.

- **탄력적 풀 데이터베이스의 활성 지역 복제**: 모든 탄력적 데이터베이스 풀의 모든 데이터베이스에 대해 활성 지역 복제를 구성할 수 있습니다. 보조 데이터베이스는 또 다른 탄력적 데이터베이스 풀에 있어도 됩니다. 일반 데이터베이스의 경우 서비스 계층이 같은 한, 보조 데이터베이스가 탄력적 데이터베이스 풀일 수 있고 그 반대도 가능합니다. 탄력적 풀 데이터베이스에 대해 지역에서 복제를 구성하는 방법에 대한 자세한 내용은 [Transact-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제](sql-database-geo-replication-transact-sql.md) 및 [PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제](sql-database-geo-replication-powershell.md)를 참조하세요.  

- **보조 데이터베이스에서 구성 가능한 성능 수준**: 보조 데이터베이스는 주 데이터베이스보다 낮은 성능 수준에서 만들 수 있습니다. 동일한 서비스 계층을 확보하려면 주 데이터베이스와 보조 데이터베이스 모두 필요합니다. 데이터베이스 쓰기 활동이 많은 응용 프로그램에는 이 방법을 권장하지 않습니다. 복제 지연이 증가하고, 그로 인해 장애 조치(failover) 후 후속 데이터 손실의 위험이 높기 때문입니다. 뿐만 아니라 장애 조치(failover) 후 새로운 주 데이터베이스가 더 높은 성능 수준으로 업그레이드될 때까지 응용 프로그램 성능이 영향을 받습니다. Azure 포털의 로그 IO 백분율 차트는 복제 부하를 유지하는 데 필요한 보조 데이터베이스의 최소 성능 수준을 예상하는 데 유용하게 사용할 수 있습니다. 예를 들어 주 데이터베이스가 P6(1000 DTU)이면 해당 로그 IO 백분율은 50%이고 보조 데이터베이스는 최소한 P4(500 DTU) 이상이어야 합니다. [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) or [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) 데이터베이스 뷰를 사용하여 로그 IO 데이터를 검색할 수도 있습니다. SQL 데이터베이스 성능 수준에 대한 자세한 내용은 [SQL 데이터베이스 옵션 및 성능](sql-database-service-tiers.md)을 참조하세요. 보조 데이터베이스의 성능 수준을 구성하는 방법에 대한 자세한 내용은 [Transact-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제](sql-database-geo-replication-transact-sql.md) 및 [PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제](sql-database-geo-replication-powershell.md)를 참조하세요.

- **사용자 제어 장애 조치(failover) 및 장애 복구**: 응용 프로그램 또는 사용자의 명시적인 작업을 통해 언제든지 보조 데이터베이스를 주 데이터베이스 역할로 전환할 수 있습니다. 실제로 가동이 중단되면 보조 데이터베이스를 즉시 주 데이터베이스로 승격하는 "계획되지 않음" 옵션을 사용해야 합니다. 중지된 주 데이터베이스가 복구되어 작동 가능한 상태가 되면 시스템에서는 복구된 주 데이터베이스를 자동으로 보조 데이터베이스로 표시하고 새로운 주 데이터베이스와 함께 최신 상태로 전환합니다. 주 데이터베이스가 가장 최근의 변경 사항을 보조 데이터베이스로 복제하기 전에 중단될 경우 비동기 복제의 특성상 계획되지 않은 장애 조치(failover)가 진행되는 동안 소량의 데이터가 손실될 수 있습니다. 보조 데이터베이스가 여러 개 있는 주 데이터베이스가 중단되면 시스템에서 사용자의 개입 없이 자동으로 복제 관계를 다시 구성하고 남아 있는 보조 데이터베이스를 새로 승격되는 주 데이터베이스에 연결합니다. 장애 조치(failover)를 일으킨 작동 중단 상황이 완화된 후에는 응용 프로그램을 주 지역으로 반환하는 것이 바람직할 수 있습니다. 장애 조치(failover)를 수행하려면 “계획됨” 옵션을 사용하여 명령을 호출해야 합니다. T-SQL을 사용하여 장애 조치(failover)를 활성화하는 방법에 대한 자세한 내용은 [Transact-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-transact-sql.md)을 참조하세요. PowerShell을 사용하여 장애 조치(failover)를 활성화하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-powershell.md)을 참조하세요.

- **자격 증명과 방화벽 규칙의 동기화 유지**: 지역에서 복제된 데이터베이스에 [데이터베이스 방화벽 규칙](sql-database-firewall-configure.md)을 사용할 것을 권장합니다. 데이터베이스 방화벽 규칙을 데이터베이스와 함께 복제하면 모든 보조 데이터베이스가 주 데이터베이스와 동일한 방화벽 구성을 갖기 때문입니다. 이렇게 하면 고객이 주 데이터베이스 및 보조 데이터베이스를 호스팅하는 서버에서 수동으로 방화벽 규칙을 구성하고 유지 관리할 필요가 없습니다. 마찬가지로, 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하면 주 데이터베이스와 보조 데이터베이스의 사용자 자격 증명이 항상 똑같기 때문에 장애 조치(failover) 시에 로그인과 암호가 불일치하여 중단되는 일이 없습니다. [Azure Active Directory](../active-directory/active-directory-whatis.md)가 추가되면서 고객은 주 데이터베이스 및 보조 데이터베이스에 대한 사용자 액세스를 관리할 수 있으므로 데이터베이스의 자격 증명을 모두 관리할 필요가 없습니다.

- **Azure Resource Manager API 및 역할 기반 보안**: 활성 지역 복제는 [ARM 기반 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt163571.aspx)을 포함하여 관리용 [ARM(Azure Resource Manager) API](sql-database-geo-replication-powershell.md)를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.

>[AZURE.NOTE] 활성 지역 복제의 여러 새로운 기능은 [ARM(Azure Resource Manager)](../resource-group-overview.md) 기반 [Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) 및 [Azure SQL 데이터베이스 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)에서만 지원됩니다. 이전 버전과의 호환성을 위해 기존 [Azure SQL Service Management(클래식) REST API](https://msdn.microsoft.com/library/azure/dn505719.aspx) 및 [Azure SQL 데이터베이스(클래식) cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx)이 지원되므로 ARM 기반 API를 사용하는 것이 좋습니다.

## 중요한 데이터 손실 방지
광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 이렇게 하면 오류가 발생하는 경우 일부 데이터 손실은 불가피합니다. 그러나 일부 응용 프로그램은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 응용 프로그램 개발자는 트랜잭션을 커밋한 후 즉시 [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) 시스템 프로시저를 호출할 수 있습니다. **sp\_wait\_for\_database\_copy\_sync** 호출은 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 복제될 때까지 호출 스레드를 차단합니다. 프로시저는 보조 데이터베이스에서 대기 중인 모든 트랜잭션을 승인할 때까지 기다립니다. **sp\_wait\_for\_database\_copy\_sync** 범위는 특정 연속 복사 링크로 지정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

>[AZURE.NOTE] **sp\_wait\_for\_database\_copy\_sync** 프로시저 호출로 인해 발생한 지연은 상당히 클 수 있습니다. 지연 시간은 현재 트랜잭션 로그 길이에 따라 달라지며 전체 로그가 복제될 때까지 반환되지 않습니다. 반드시 필요하지 않는 한 이 프로시저 호출을 피합니다.

## 참고 항목

- [지역에서 복제를 위한 보안 구성](sql-database-geo-replication-security-config.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0615_2016-->