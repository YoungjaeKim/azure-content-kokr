<properties
   pageTitle="PowerShell을 통해 배포 작업 보기 | Microsoft Azure"
   description="Azure PowerShell을 사용하여 리소스 관리자 배포의 문제를 감지하는 방법에 대해 설명합니다."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="05/19/2016"
   ms.author="tomfitz"/>

# Azure PowerShell을 통해 배포 작업 보기

> [AZURE.SELECTOR]
- [포털](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure에 리소스를 배포할 때 오류가 발생하는 경우 실행된 배포 작업에 대한 더욱 자세한 정보가 필요할 수 있습니다. Azure PowerShell은 쉽게 오류를 찾고 잠재적 해결 방법을 확인할 수 있는 cmdlet을 제공합니다.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

배포하기 전에 템플릿 및 인프라의 유효성을 검사하여 몇 가지 오류를 방지할 수 있습니다. 배포 중에 이후 문제 해결에 도움이 될 만한 추가 요청과 응답 정보를 기록할 수 있습니다. 유효성 검사와 요청 및 응답 정보 기록에 관한 자세한 내용은 [Azure Resource Manager 템플릿으로 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

## 감사 로그를 사용하여 문제 해결

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

배포 오류를 확인하려면 다음 단계를 사용합니다.

1. 로그 항목을 검색하려면 **Get-AzureRmLog** 명령을 실행합니다. **ResourceGroup** 및 **Status** 매개 변수를 사용하여 단일 리소스 그룹에 대해 실패한 이벤트만 반환할 수 있습니다. 시작 및 종료 시간을 지정하지 않으면 지난 시간에 대한 항목이 반환됩니다. 예를 들어 지난 시간 동안 실패한 작업을 검색하려면 다음을 실행합니다.

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    특정 시간 범위를 지정할 수 없습니다. 다음 예제에서는 지난 14일 동안 실패한 작업을 찾아봅니다.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    또는 실패한 작업의 정확한 시작 시간 및 종료 시간을 설정할 수 있습니다.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. 이 명령이 너무 많은 항목 및 속성을 반환하는 경우 **Properties** 속성을 검색하여 감사에 집중할 수 있습니다. 또한 **DetailedOutput** 매개 변수를 포함하여 오류 메시지를 참조합니다.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    로그 항목의 속성은 다음과 같은 형식으로 반환됩니다.
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. 특정 단일 항목에 대한 상태 메시지를 보고 결과를 더욱 구체화할 수 있습니다.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    상태 메시지는 다음과 같은 형식으로 반환됩니다.
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## 배포 작업을 사용하여 문제 해결

1. 배포의 전반적인 상태를 가져오려면 **Get-AzureRmResourceGroupDeployment** 명령을 사용합니다. 실패한 배포에 대해서만 결과를 필터링할 수 있습니다.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    실패한 배포는 다음과 같은 형식으로 반환됩니다.
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. 각 배포는 일반적으로 배포 프로세스의 단계를 나타내는 각 작업을 사용하여 여러 작업으로 구성됩니다. 배포에서 무엇이 잘못 되었는지 검색하려면 일반적으로 배포 작업에 대한 세부 정보를 확인해야 합니다. **Get-AzureRmResourceGroupDeploymentOperation**을 사용하여 작업의 상태를 확인할 수 있습니다.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
        
    작업은 다음과 같은 형식으로 반환됩니다.
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. 작업에 대한 자세한 정보를 보려면 **Properties** 개체를 검색합니다.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    작업 속성은 다음과 같은 형식으로 반환됩니다.
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. 실패한 작업에 대한 상태 메시지를 중점적으로 확인하려면 다음 명령을 사용합니다.

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    상태 메시지는 다음과 같은 형식으로 반환됩니다.
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## 다음 단계

- 특정 배포 오류에 대한 도움말은 [ Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
- 감사 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법을 알아보려면 [Resource Manager를 사용하여 작업 감사](resource-group-audit.md)를 참조하세요.
- 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0615_2016-->