<properties 
   pageTitle="Azure CLI를 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치 만들기 시작 | Microsoft Azure"
   description="Azure CLI를 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Azure CLI에서 인터넷 연결 부하 분산 장치(클래식) 만들기 시작

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## CLI를 사용하여 인터넷 연결 부하 분산 장치 만들기 단계별 지침

이 가이드에서는 위의 시나리오에 따라 인터넷 부하 분산 장치를 만드는 방법을 보여줍니다.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../../articles/xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.

2. 아래와 같이 **azure config mode** 명령을 실행하여 클래식 모드로 전환합니다.

		azure config mode asm

	예상된 출력:

		info:    New mode is asm


## 끝점과 부하 분산 장치 집합 만들기 

시나리오는 가상 컴퓨터 "web1" 및 "web2"가 만들어졌다고 가정합니다. 이 가이드를 통해 공용 포트로 포트 80과 로컬 포트로 포트 80을 사용하여 부하 분산 장치 집합을 만듭니다. 또한 프로브 포트가 포트 80에 구성되고 부하 분산 장치 집합을 "lbset"로 지명합니다.


### 1단계 

가상 컴퓨터 "web1"을 위해 `azure network vm endpoint create`을 사용하여 첫 번째 끝점과 부하 분산 장치 집합을 만듭니다.

	azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset 

사용된 매개 변수:

**-k** - 로컬 가상 컴퓨터 포트<br> **-o** - 프로토콜<BR> **-t** - 프로브 포트<BR> **-b** - 부하 분산 장치 이름<BR>
 
## 2단계 

두 번째 가상 컴퓨터 "web2"를 부하 분산 장치 집합에 추가합니다.

	azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## 3단계 

`azure vm show`를 사용하여 부하 분산 장치 구성을 확인합니다.

	azure vm show web1

다음과 같이 출력됩니다.

	data:    DNSName "contoso.cloudapp.net"
	data:    Location "East US"
	data:    VMName "web1"
	data:    IPAddress "10.0.0.5"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
	6-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
	data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
	/vhds/joaomatest-web1-2015-09-25.vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
	r-2012-R2-20150916-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
	data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
	data:    Network Endpoints 0 localPort 80
	data:    Network Endpoints 0 name "tcp-80-80"
	data:    Network Endpoints 0 port 80
	data:    Network Endpoints 0 loadBalancerProbe port 80
	data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
	data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 5986
	data:    Network Endpoints 1 name "PowerShell"
	data:    Network Endpoints 1 port 5986
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 3389
	data:    Network Endpoints 2 name "Remote Desktop"
	data:    Network Endpoints 2 port 58081
	info:    vm show command OK

## 가상 컴퓨터를 위한 원격 데스크톱 끝점 만들기

`azure vm endpoint create`을 사용하여 특정 가상 컴퓨터의 공용 포트에서 로컬 포트로 네트워크 트래픽을 전달하는 원격 데스크톱 끝점을 만들 수 있습니다.

	azure vm endpoint create web1 54580 -k 3389 


## 부하 분산 장치에서 가상 컴퓨터 제거

가상 컴퓨터에서 부하 분산 장치 집합에 연결된 끝점을 삭제해야 합니다. 끝점이 제거되면 가상 컴퓨터는 더 이상 부하 분산 장치 집합에 속하지 않습니다.

 위의 예제를 통해 명령 `azure vm endpoint delete`를 사용하여 부하 분산 장치 "lbset"에서 가상 컴퓨터 "web1"을 위해 만들어진 끝점을 제거할 수 있습니다.

	azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] 명령 `azure vm endpoint --help`를 사용하여 끝점을 관리하는 더 많은 옵션을 탐색할 수 있습니다.


## 다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

 

<!---HONumber=AcomDC_0323_2016-->