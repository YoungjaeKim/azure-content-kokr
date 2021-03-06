<properties
   pageTitle="로컬 서비스 패브릭 클러스터 설정 문제 해결 | Microsoft Azure"
   description="이 문서에서는 로컬 개발 클러스터 문제 해결을 위한 여러 제안 사항을 다룹니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/06/2016"
   ms.author="seanmck"/>

# 로컬 개발 클러스터 설정 문제 해결

로컬 Azure 서비스 패브릭 개발 클러스터와 상호 작용하는 동안 문제가 발생할 경우 다음 제안 사항에서 잠재적인 해결 방법이 있는지 검토하세요.

## 클러스터 설정 오류

### 서비스 패브릭 로그를 정리할 수 없습니다.

#### 문제

DevClusterSetup 스크립트를 실행하는 동안 다음과 같은 오류가 나타날 수 있습니다.

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### 해결 방법

현재 Powershell 창을 닫고 관리자 권한으로 새 Powershell 창을 엽니다. 이제 성공적으로 스크립트를 실행할 수 있어야 합니다.

## 클러스터 연결 오류

### 서비스 패브릭 PowerShell cmdlet이 Azure PowerShell에서 인식되지 않습니다.

#### 문제

Azure PowerShell 창에서 `Connect-ServiceFabricCluster`와 같은 서비스 패브릭 PowerShell cmdlet을 실행하려고 하면 cmdlet이 인식되지 않는다는 메시지를 표시하면서 실행되지 않습니다. Azure PowerShell에서는 32비트 버전의 Windows PowerShell을 사용(64비트 OS 버전에서도 동일)하는 반면 서비스 패브릭 cmdlet은 64비트 환경에서만 작동하기 때문입니다.

#### 해결 방법

항상 서비스 패브릭 cmdlet을 Windows PowerShell에서 직접 실행합니다.

>[AZURE.NOTE] 최신 버전의 Azure PowerShell에서 특수 바로 가기를 만들지 않으므로 더 이상 발생하지 않습니다.

### 형식 초기화 예외

#### 문제

PowerShell에서 클러스터에 연결할 때 System.Fabric.Common.AppTrace에 대해 TypeInitializationException 오류가 표시됩니다.

#### 해결 방법

설치하는 동안 경로 변수가 올바르게 설정되지 않았습니다. Windows에서 로그아웃하고 다시 로그인하세요. 그러면 경로가 완전히 새고 고침됩니다.

### “개체 닫힘"으로 인해 클러스터 연결 실패

#### 문제

다음과 같은 오류와 함께 Connect-ServiceFabricCluster 호출에 실패합니다.

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### 해결 방법

현재 Powershell 창을 닫고 관리자 권한으로 새 Powershell 창을 엽니다. 이제 성공적으로 연결할 수 있어야 합니다.

### 패브릭 연결 거부 예외

#### 문제

Visual Studio에서 디버그 시 FabricConnectionDeniedException 오류가 나타납니다.

#### 해결 방법

이 오류는 일반적으로 서비스 패브릭 런타임이 자동으로 시작되게 하는 대신 서비스 호스트 프로세스를 수동으로 시작하려고 할 때 발생합니다.

솔루션에서 시작 프로젝트로 설정된 서비스 프로젝트가 없어야 합니다. 서비스 패브릭 응용프로그램 프로젝트만 시작 프로젝트로 설정되어야 합니다.


## 다음 단계

- [시스템 상태 보고서와 함께 클러스터 이해 및 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [서비스 패브릭 탐색기로 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

<!---HONumber=AcomDC_0413_2016-->