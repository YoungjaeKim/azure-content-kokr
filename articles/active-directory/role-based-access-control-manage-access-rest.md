<properties
	pageTitle="REST API를 사용하여 역할 기반 액세스 제어 관리"
	description="REST API를 사용하여 역할 기반 액세스 제어 관리"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/13/2016"
	ms.author="kgremban"/>

# REST API를 사용하여 역할 기반 액세스 제어 관리

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

Azure 포털 및 Azure Resource Manager API의 RBAC(역할 기반 액세스 제어)를 사용하면 세밀한 수준에서 구독과 리소스에 대한 액세스를 관리할 수 있습니다. 이 기능을 통해 특정 범위에서 Active Directory 사용자, 그룹 또는 서비스 사용자에게 일부 역할을 할당하여 액세스 권한을 부여할 수 있습니다.


## 모든 역할 할당 나열

지정된 범위 및 하위 범위에서 모든 역할 할당을 나열합니다.

역할 할당을 나열하려면 범위에서 `Microsoft.Authorization/roleAssignments/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **GET** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 역할 할당을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{api-version}*을 2015-07-01로 바꿉니다.

*{filter}*를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다. 다음과 같은 조건을 지원합니다.


| 조건 | *{Filter}* | Replace |
|-----------|------------|---------|
| 하위 범위에서는 역할 할당을 포함시키지 않고 지정된 범위에 대해서만 역할 할당을 나열하려면 | `atScope()` | |
| 특정 사용자, 그룹 또는 응용 프로그램에 대해서만 역할 할당을 나열하려면 | `principalId%20eq%20'{objectId}'` | *{objectId}*를 사용자, 그룹 또는 서비스 사용자의 Azure AD objectId로 바꿉니다. 예: `&$filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| 사용자가 멤버인 그룹에 할당된 역할 할당을 포함하여 특정 사용자에 대해서만 역할 할당을 나열하려면 | `assignedTo('{objectId}')` | *{objectId}*를 사용자의 Azure AD objectId로 바꿉니다. 예: `&$filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |



### 응답

상태 코드: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## 역할 할당에 대한 정보 가져오기

역할 할당 식별자에서 지정한 단일 역할 할당에 대한 정보를 가져옵니다.

역할 할당에 대한 정보를 가져오려면 `Microsoft.Authorization/roleAssignments/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **GET** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 역할 할당을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-assignment-id}*를 역할 할당의 GUID 식별자로 바꿉니다.

*{api-version}*을 2015-07-01로 바꿉니다.

### 응답

상태 코드: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## 역할 할당 만들기

지정된 역할을 부여하는 지정된 보안 주체에 대해 지정된 범위에서 역할 할당을 만듭니다.

역할 할당을 만들려면 `Microsoft.Authorization/roleAssignments/write` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **PUT** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 역할 할당을 만들려는 범위로 바꿉니다. 부모 범위에서 역할 할당을 만들 때 모든 자식 범위는 같은 역할 할당을 상속합니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-assignment-id}*를 새 GUID로 바꿉니다. 이는 새 역할 할당의 GUID 식별자로 사용됩니다.

*{api-version}*을 2015-07-01로 바꿉니다.

요청 본문에 대해 다음과 같은 형식으로 값을 제공합니다.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 요소 이름 | 필수 | 형식 | 설명 |
|------------------|----------|--------|-------------|
| roleDefinitionId | 예 | String | 할당할 역할의 식별자입니다. 식별자의 형식은 `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}`입니다. |
| principalId | 예 | 문자열 | 역할을 할당할 Azure AD 보안 주체(사용자, 그룹 또는 서비스 사용자)의 objectId입니다. |

### 응답

상태 코드: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## 역할 할당 삭제

지정된 범위에서 역할 할당을 삭제합니다.

역할 할당을 삭제하려면 `Microsoft.Authorization/roleAssignments/delete`작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **DELETE** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 역할 할당을 만들려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-assignment-id}*를 역할 할당 id GUID로 바꿉니다.

*{api-version}*을 2015-07-01로 바꿉니다.

### 응답

상태 코드: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## 모든 역할 나열

지정된 범위에서 할당에 사용할 수 있는 모든 역할을 나열합니다.

역할을 나열하려면 범위에서 `Microsoft.Authorization/roleDefinitions/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **GET** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 역할을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{api-version}*을 2015-07-01로 바꿉니다.

*{filter}*를 역할 목록을 필터링하기 위해 적용할 조건으로 바꿉니다. 다음과 같은 조건을 지원합니다.

| 조건 | *{Filter}* | Replace |
|-----------|------------|---------|
| 지정된 범위 및 해당 자식 범위에서 할당에 사용할 수 있는 역할을 나열하려면 | `atScopeAndBelow()` | |
| 정확한 표시 이름을 사용하여 역할을 검색하려면 | `roleName%20eq%20'{role-display-name}'` | *{role-display-name}*을 역할의 정확한 표시 이름의 URL 인코딩 형식으로 바꿉니다. 예: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |



### 응답

상태 코드: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## 역할에 대한 정보 가져오기

역할 정의 식별자에서 지정한 단일 역할에 대한 정보를 가져옵니다. 해당 표시 이름을 사용하여 단일 역할에 대한 정보를 가져오려면 모든 역할 및 roleName 필터 나열을 참조하세요.

역할에 대한 정보를 가져오려면 `Microsoft.Authorization/roleDefinitions/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **GET** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 역할 할당을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}*를 역할 정의의 GUID 식별자로 바꿉니다. *{api-version}*을 2015-07-01로 바꿉니다.

### 응답

상태 코드: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## 사용자 지정 역할 만들기
사용자 지정 역할을 만듭니다.

사용자 지정 역할을 만들려면 해당하는 모든 `AssignableScopes`에서 `Microsoft.Authorization/roleDefinitions/write` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **PUT** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 사용자 지정 역할의 첫 번째 *AssignableScope*으로 바꿉니다. 다음 예제는 서로 다른 수준에 대한 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}*를 새 GUID로 바꿉니다. 이는 새 사용자 지정 역할 할당의 GUID 식별자로 사용됩니다.

*{api-version}*을 2015-07-01로 바꿉니다.

요청 본문에 대해 다음과 같은 형식으로 값을 제공합니다.

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 요소 이름 | 필수 | 형식 | 설명 |
|--------------|----------|------|-------------|
| name | 예 | String | 사용자 지정 역할의 GUID 식별자입니다. |
| properties.roleName | 예 | String | 사용자 지정 역할의 표시 이름입니다. 최대 128자입니다. |
| properties.description | 아니요 | String | 사용자 지정 역할에 대한 설명입니다. 최대 1024자입니다. |
| properties.type | 예 | String | 이를 "CustomRole"로 설정합니다. |
| properties.permissions.actions | 예 | String | 사용자 지정 역할이 액세스 권한을 부여하는 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.permissions.notActions | 아니요 | String | 사용자 지정 역할이 액세스 권한을 부여하는 작업에서 제외할 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.assignableScopes | 예 | 문자열 | 사용자 지정 역할이 액세스 관리에 사용될 수 있는 범위의 배열입니다. |

### 응답

상태 코드: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## 사용자 지정 역할 업데이트

사용자 지정 역할을 수정합니다.

사용자 지정 역할을 수정하려면 해당하는 모든 `AssignableScopes`에서 `Microsoft.Authorization/roleDefinitions/write` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **PUT** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*을 사용자 지정 역할의 첫 번째 *AssignableScope*으로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}*를 업데이트할 사용자 지정 역할의 GUID 식별자로 바꿉니다.

*{api-version}*을 2015-07-01로 바꿉니다.

요청 본문에 대해 다음과 같은 형식으로 값을 제공합니다.

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 요소 이름 | 필수 | 형식 | 설명 |
|--------------|----------|------|-------------|
| name | 예 | String | 업데이트할 사용자 지정 역할의 GUID 식별자입니다. |
| properties.roleName | 예 | String | 업데이트된 사용자 지정 역할의 표시 이름입니다. |
| properties.description | 아니요 | 문자열 | 업데이트된 사용자 지정 역할의 설명입니다. |
| properties.type | 예 | String | 이를 "CustomRole"로 설정합니다. |
| properties.permissions.actions | 예 | 문자열 | 업데이트된 사용자 지정 역할이 액세스 권한을 부여하는 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.permissions.notActions | 아니요 | 문자열 | 업데이트된 사용자 지정 역할이 액세스 권한을 부여하는 작업에서 제외할 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.assignableScopes | 예 | String | 업데이트된 사용자 지정 역할이 액세스 관리에 사용될 수 있는 범위의 배열입니다. |

### 응답

상태 코드: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## 사용자 지정 역할 삭제

사용자 지정 역할을 삭제합니다.

사용자 지정 역할을 삭제하려면 해당하는 모든 `AssignableScopes`에서 `Microsoft.Authorization/roleDefinitions/delete` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

### 요청

다음 URI와 함께 **DELETE** 메서드를 사용합니다.

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

*{scope}*를 역할 정의를 삭제하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

| Level | *{Scope}* |
|-------|-----------|
| 구독 | /subscriptions/{subscription-id} |
| 리소스 그룹 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 리소스 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}*를 삭제할 사용자 지정 역할의 GUID 역할 정의 ID로 바꿉니다.

*{api-version}*을 2015-07-01로 바꿉니다.

### 응답

상태 코드: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0518_2016-->