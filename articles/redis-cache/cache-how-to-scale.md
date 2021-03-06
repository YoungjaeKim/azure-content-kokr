<properties 
	pageTitle="Azure Redis Cache 크기를 조정하는 방법 | Microsoft Azure" 
	description="Azure Redis Cache 인스턴스 크기를 조정하는 방법에 대해 알아봅니다." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/23/2016" 
	ms.author="sdanie"/>

# Azure Redis Cache 크기를 조정하는 방법

>[AZURE.NOTE] Azure Redis Cache 크기 조정 기능은 현재 미리 보기 상태입니다.

Azure Redis Cache에는 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다. 캐시를 만든 후 응용 프로그램 요구 사항이 변경되면 [Azure 포털](https://portal.azure.com)의 **가격 책정 계층 변경** 블레이드를 사용하여 캐시 크기를 조정할 수 있습니다.

## 크기를 조정하는 경우

Azure Redis Cache의 [모니터링](cache-how-to-monitor.md) 기능을 사용하여 캐시 응용 프로그램의 상태 및 성능을 모니터링하고 캐시 크기를 조정해야 하는지 결정하는데 도움을 줄 수 있습니다.

다음 메트릭을 모니터링하면 크기를 조정해야 하는지 결정하는데 도움이 될 수 있습니다.

-	Redis 서버 부하
-	메모리 사용량
-	네트워크 대역폭
-	CPU 사용량

캐시가 더 이상 응용 프로그램 요구 사항을 충족시키지 못한다고 판단되면 응용 프로그램에 적합하도록 더 크거나 더 작은 캐시 가격 책정 계층으로 변경할 수 있습니다. 사용할 캐시 가격 책정 계층 결정에 대한 자세한 내용은 [어떤 Redis Cache 제품 및 크기를 사용해야 하나요?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

## 캐시 크기 조정
캐시 크기를 조정하려면 [Azure 포털](https://portal.azure.com)에서 [캐시를 찾은](cache-configure.md#configure-redis-cache-settings) 다음 **설정**, **가격 책정 계층**을 클릭합니다.

**Redis Cache** 블레이드에서 **가격 책정 계층** 부분을 클릭할 수도 있습니다.

![가격 책정 계층][redis-cache-pricing-tier-part]

**가격 책정 계층** 블레이드에서 원하는 가격 책정 계층을 선택하고 **선택**을 클릭합니다.

![가격 책정 계층][redis-cache-pricing-tier-blade]

>[AZURE.NOTE] 다른 가격 책정 계층으로 크기를 조정할 수 있지만 다음과 같은 제한 사항이 있습니다.
>
>-	높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
>    -    **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
>    -    **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
>-	**기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 후속 크기 조정 작업을 통해 원하는 크기로 조정할 수 있습니다.
>-	**기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준**에서 **프리미엄**으로 확장해야 합니다.
>-	더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.

새로운 가격 책정 계층으로 캐시 크기를 조정하는 동안에는 **Scaling(크기 조정 중)** 상태가 **Redis Cache** 블레이드에 표시됩니다.

![확장][redis-cache-scaling]

크기 조정이 완료되면 상태가 **Scaling(크기 조정 중)**에서 **실행 중**으로 변경됩니다.

## 크기 조정 작업을 자동화하는 방법

Azure 포털에서 Azure Redis Cache 인스턴스의 크기를 조정할 뿐만 아니라 Azure Redis Cache PowerShell cmdlet, Azure CLI를 사용하거나 MAML(Microsoft Azure Management Libraries)을 사용하여 크기를 조정할 수 있습니다.

-	[PowerShell을 사용하여 크기 조정](#scale-using-powershell)
-	[Azure CLI를 사용한 크기 조정](#scale-using-azure-cli)
-	[MAML을 사용하여 크기 조정](#scale-using-maml)

### PowerShell을 사용하여 크기 조정

`Size`, `Sku`, 또는 `ShardCount` 속성은 수정할 때 [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) cmdlet를 사용하여 PowerShell을 통해 Azure Redis Cache 인스턴스를 확장할 수 있습니다. 다음 예제에서는 `myCache`라는 캐시를 2.5GB 캐시로 크기를 조정하는 방법을 보여줍니다.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

PowerShell을 통한 크기 조정에 대한 자세한 내용은 [Powershell을 사용하여 Redis Cache의 크기 조정](cache-howto-manage-redis-cache-powershell.md#scale)을 참조하세요.

### Azure CLI를 사용한 크기 조정

Azure CLI를 사용하여 Azure Redis Cache 인스턴스 크기를 조정하려면 원하는 크기 조정 작업에 따라 `azure rediscache set` 명령을 호출하고 새 크기, SKU, 또는 클러스터 크기를 포함하는 원하는 구성 변경에 전달합니다.

Azure CLI을 통한 크기 조정에 대한 자세한 내용은 [기존 Redis Cache의 설정 변경](cache-manage-cli.md#scale)을 참조하세요.

### MAML을 사용하여 크기 조정

[MAML(Microsoft Azure Management Libraries)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)를 사용하여 Azure Redis Cache 인스턴스의 크기를 조정하려면 `IRedisOperations.CreateOrUpdate` 메서드를 호출하고 `RedisProperties.SKU.Capacity`에 대해 새 크기를 전달합니다.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

자세한 내용은 [MAML로 Redis Cache 관리](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 샘플을 참조하세요.

## 크기 조정 FAQ

다음 목록에는 Azure Redis Cache 크기 조정에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

-	[프리미엄 캐시로 확장하거나, 이 캐시를 축소하거나 이 캐시 내에서 크기를 조정할 수 있나요?](#can-i-scale-to-from-or-within-a-premium-cache)
-	[크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
-	[크기 조정은 어떻게 수행되나요?](#how-does-scaling-work)
-	[크기를 조정하는 동안 캐시의 데이터가 손실되나요?](#will-i-lose-data-from-my-cache-during-scaling)
-	[사용자 지정 데이터베이스 설정이 크기 조정 하는 동안에 영향을 받나요?](#is-my-custom-databases-setting-affected-during-scaling)
-	[크기를 조정하는 동안 내 캐시를 사용할 수 있나요?](#will-my-cache-be-available-during-scaling)
-	[지원되지 않는 작업](#operations-that-are-not-supported)
-	[크기 조정은 시간이 얼마나 걸리나요?](#how-long-does-scaling-take)
-	[크기 조정이 완료되었는지 어떻게 알 수 있나요?](#how-can-i-tell-when-scaling-is-complete)
-	[이 기능이 미리 보기 상태인 이유는 무엇인가요?](#why-is-this-feature-in-preview)

### 프리미엄 캐시로 확장하거나, 이 캐시를 축소하거나 이 캐시 내에서 크기를 조정할 수 있나요?

-	**프리미엄** 캐시에서 **기본** 또는 **표준** 가격 책정 계층으로 축소할 수 없습니다.
-	하나의 **프리미엄** 캐시 가격 책정 계층에서 다른 프리미엄 캐시 가격 책정 계층으로 크기를 조정할 수 있습니다.
-	**기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준**에서 **프리미엄**으로 확장해야 합니다.
-	**프리미엄** 캐시를 만들 때 클러스터링을 사용하도록 설정했으면 [클러스터 크기를 변경](cache-how-to-premium-clustering.md#cluster-size)할 수 있습니다. 현재는 클러스터링 없이 생성된 기존 캐시에 클러스터링을 사용할 수 없습니다.

    자세한 내용은 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

### 크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?

아니요, 캐시 이름 및 키는 크기 조정 작업을 수행하는 동안 변경되지 않습니다.

### 크기 조정은 어떻게 수행되나요?

-	**기본** 캐시 크기를 다른 크기로 조정하는 경우 캐시가 종료되고 새 크기를 사용하여 새 캐시를 프로비전합니다. 이 시간 동안에는 캐시를 사용할 수 없으며 캐시의 모든 데이터가 손실됩니다.
-	**기본** 캐시를 **표준** 캐시로 확장하는 경우 복제본 캐시가 프로비전되며 데이터가 주 캐시에서 복제본 캐시로 복사됩니다. 크기를 조정하는 동안 캐시를 계속 사용할 수 있습니다.
-	**표준** 캐시 크기를 다른 크기 또는 **프리미엄** 캐시로 조정하는 경우 복제본 중 하나가 종료되고 새 크기로 다시 프로비전되며 데이터가 전송됩니다. 그런 다음 나머지 복제본이 장애 조치(failover)를 수행한 후 다시 프로비전됩니다. 캐시 노드 중 하나에 오류가 발생하면 수행되는 프로세스와 비슷합니다.

### 크기를 조정하는 동안 캐시의 데이터가 손실되나요?

-	**기본** 캐시 크기를 새 크기로 조정하는 경우 모든 데이터가 손실되고 크기 조정 작업을 수행하는 동안 캐시를 사용할 수 없습니다.
-	**기본** 캐시를 **표준** 캐시로 확장하는 경우 캐시의 데이터가 일반적으로 유지됩니다.
-	**표준** 캐시가 더 큰 크기나 계층으로 확장되거나, **프리미엄** 캐시가 더 크게 확장되는 경우에는 일반적으로 모든 데이터가 유지됩니다. **표준** 또는 **프리미엄** 캐시를 더 작게 축소하는 경우, 새로 조정된 크기 대비 캐시에 있는 데이터의 양에 따라 데이터가 손실될 수 있습니다. 크기를 축소하는 경우 데이터가 손실되면 [allkeys-lru](http://redis.io/topics/lru-cache) 제거 정책을 사용하여 키를 제거합니다. 

### 사용자 지정 데이터베이스 설정이 크기 조정 하는 동안에 영향을 받나요?

일부 가격 책정 계층에는 다른 [데이타 베이스 제한](cache-configure.md#databases)이 있으므로 캐시 만들기 중에 `databases` 설정에 대한 사용자 지정 값을 구성했다면 규모 축소를 할 때 고려 사항이 있습니다.

-	현재 계층보다 낮은 `databases` 제한을 가진 가격 책정 계층으로 크기를 조정할 때:
	-	모든 가격 계층에 대해 기본값이 16개인 `databases`을 사용하는 경우 데이터 손실은 전혀 없습니다.
	-	크기 조정하는 계층에 대한 제한내에 포함되는 `databases`의 사용자 지정 수를 사용하는 경우, 이 `databases` 설정은 유지되고 데이터 손실은 전혀 없습니다.
	-	새 계층의 제한을 초과하는 `databases`의 사용자 지정 수를 사용하는 경우, `databases` 설정은 새 계층의 제한까지 낮춰지고 제거된 데이터베이스의 모든 데이터는 손실됩니다.
-	현재 계층보다 같거나 높은 `databases` 제한을 가진 가격 책정 계층으로 크기를 조정할 때:`databases` 설정은 유지되고 데이터 손실은 전혀 없습니다.

표준 및 프리미엄 캐시의 가용성에 대한 SLA는 99.9%이나 데이터 손실에 대한 SLA는 없습니다.

### 크기를 조정하는 동안 내 캐시를 사용할 수 있나요?

-	**표준** 및 **프리미엄** 캐시는 크기 조정 작업을 수행하는 동안 사용할 수 있습니다.
-	**기본** 캐시는 다른 크기로 조정하는 동안 오프라인 상태이지만 **기본**에서 **표준**으로 크기를 조정하는 경우에는 계속 사용할 수 있습니다.

### 지원되지 않는 작업

-	높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
    -    **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
    -    **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
-	**기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 후속 크기 조정 작업을 통해 원하는 크기로 조정할 수 있습니다.
-	**기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준**에서 **프리미엄**으로 확장해야 합니다.
-	더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.

크기 조정 작업이 실패하면 서비스는 작업을 되돌리려고 하며 캐시는 원래 크기로 되돌아갑니다.

### 크기 조정은 시간이 얼마나 걸리나요?

크기 조정은 약 20분이 걸립니다. 캐시에 있는 데이터의 양에 따라 다를 수 있습니다.

### 크기 조정이 완료되었는지 어떻게 알 수 있나요?

Azure 포털에서 진행 중인 크기 조정 작업을 볼 수 있습니다. 크기 조정이 완료되면 캐시 상태가 **실행 중**으로 변경됩니다.

### 이 기능이 미리 보기 상태인 이유는 무엇인가요?

이 기능은 사용자 의견을 듣기 위해 출시되었습니다. 사용자 의견을 기반으로 곧 이 기능을 일반 공급으로 출시할 것입니다.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=AcomDC_0525_2016-->