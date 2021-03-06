<properties
	pageTitle="Windows VM에 대한 계획된 유지 관리 | Microsoft Azure"
	description="Azure 계획된 유지 관리의 정의와 계획된 유지 관리가 Azure를 실행하는 Windows 가상 컴퓨터에 주는 영향에 대해 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="drewm"/>

# Azure에서 가상 컴퓨터에 대한 계획된 유지 관리


Azure 계획된 유지 관리의 정의와 계획된 유지 관리가 Windows 가상 컴퓨터의 가용성에 주는 영향에 대해 알아봅니다. 이 문서는 [Linux 가상 컴퓨터](virtual-machines-linux-planned-maintenance.md)에도 적용됩니다.

이 문서는 Azure 계획 유지 보수 프로세스에 대한 배경을 제공합니다. VM이 다시 부팅되는 문제를 해결하려는 경우 [VM 재부팅 로그 보기에 대해 자세히 설명하는 이 블로그 게시물을 읽으십시오](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Azure에서 계획된 유지 관리를 수행하는 이유

Microsoft Azure는 가상 컴퓨터의 기반이 되는 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 전 세계적으로 주기적인 업데이트를 수행합니다. 이러한 많은 업데이트는 메모리 유지 업데이트를 비롯하여 가상 컴퓨터 또는 클라우드 서비스에 영향을 주지 않고 수행됩니다.

그렇지만 일부 업데이트는 인프라에 필요한 업데이트를 적용하기 위해 가상 컴퓨터를 다시 부팅해야 합니다. 가상 컴퓨터는 인프라를 패치하는 동안 종료되었다가 다시 시작됩니다.

가상 컴퓨터의 가용성에 영향을 미칠 수 있는 유지 관리 유형에는 계획된 유지 관리와 계획되지 않은 유지 관리가 있습니다. 이 페이지에서는 Microsoft Azure에서 계획된 유지 관리를 수행하는 방법을 설명합니다. 계획되지 않은 유지 관리에 대한 자세한 내용은 [계획된 유지 관리 및 계획되지 않은 유지 관리 이해](virtual-machines-windows-manage-availability.md)를 참조하세요.

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0504_2016-->