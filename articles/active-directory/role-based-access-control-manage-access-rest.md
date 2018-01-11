---
title: "Rollbaserad åtkomstkontroll med REST - Azure AD | Microsoft Docs"
description: "Hantera rollbaserad åtkomstkontroll med REST API"
services: active-directory
documentationcenter: na
author: andredm7
manager: mtillman
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: andredm
ms.openlocfilehash: 9ec64dc3ce95de9c29331699ad2140e5a3c25673
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Hantera rollbaserad åtkomstkontroll med REST API
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

Rollbaserad åtkomstkontroll (RBAC) i Azure-portalen och Azure Resource Manager API hjälper dig att hantera åtkomst till din prenumeration och resurser på en detaljerad nivå. Med den här funktionen kan du bevilja åtkomst för Active Directory-användare, grupper eller tjänstens huvudnamn genom att tilldela vissa roller till dem för ett visst område.

## <a name="list-all-role-assignments"></a>Visa en lista med alla rolltilldelningar
Listar alla rolltilldelningar i definitionsområdet och subscopes.

Att lista rolltilldelningar, måste du ha tillgång till `Microsoft.Authorization/roleAssignments/read` åtgärden definitionsområdet. Inbyggda roller beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **hämta** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill visa en lista över rolltilldelningarna. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{api-version}* med 2015-07-01.
3. Ersätt *{filter}* med villkor som du vill tillämpa för att filtrera listan rollen tilldelning:

   * Lista rolltilldelningar för endast det angivna omfånget, exklusive rolltilldelningar på subscopes:`atScope()`    
   * Lista rolltilldelningar för en viss användare, grupp eller ett program:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Visa en lista med rolltilldelningar för en viss användare, inklusive de som ärvts från grupper |`assignedTo('{objectId of user}')`

### <a name="response"></a>Svar
Statuskod: 200

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

## <a name="get-information-about-a-role-assignment"></a>Hämta information om en rolltilldelning
Hämtar information om en enda rolltilldelning som anges av rollen tilldelning identifierare.

Om du vill få information om en rolltilldelning måste du ha tillgång till `Microsoft.Authorization/roleAssignments/read` igen. Inbyggda roller beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **hämta** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill visa en lista över rolltilldelningarna. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-tilldelning-id}* med GUID-identifierare för rolltilldelningen.
3. Ersätt *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar
Statuskod: 200

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

## <a name="create-a-role-assignment"></a>Skapa en rolltilldelning
Skapa en rolltilldelning i det specificerade omfånget för det angivna huvudnamnet bevilja den angivna rollen.

Om du vill skapa en rolltilldelning måste du ha tillgång till `Microsoft.Authorization/roleAssignments/write` igen. I de inbyggda rollerna, endast *ägare* och *administratör för användaråtkomst* beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **PLACERA** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill skapa rolltilldelningen. När du skapar en rolltilldelning på en överordnad omfattning, ärver alla underordnade omfång samma rolltilldelning. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1   
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-tilldelning-id}* med en ny GUID som blir GUID-identifierare för ny rolltilldelning.
3. Ersätt *{api-version}* med 2015-07-01.

För begärantext, anger du värden i följande format:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Elementnamn | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| roleDefinitionId |Ja |Sträng |Identifierare för rollen. Formatet på identifieraren är:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Ja |Sträng |objectId av Azure AD är säkerhetsobjekt (användare, grupp eller tjänstens huvudnamn) som har tilldelats rollen. |

### <a name="response"></a>Svar
Statuskod: 201

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

## <a name="delete-a-role-assignment"></a>Ta bort en rolltilldelning
Ta bort en rolltilldelning i det specificerade omfånget.

Om du vill ta bort en rolltilldelning måste du ha åtkomst till den `Microsoft.Authorization/roleAssignments/delete` igen. I de inbyggda rollerna, endast *ägare* och *administratör för användaråtkomst* beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **ta bort** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill skapa rolltilldelningen. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-tilldelning-id}* med rollen tilldelnings-id GUID.
3. Ersätt *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar
Statuskod: 200

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

## <a name="list-all-roles"></a>Visa en lista över alla roller
Listar de roller som är tillgängliga för tilldelning i det specificerade omfånget.

Lista roller måste du ha tillgång till `Microsoft.Authorization/roleDefinitions/read` åtgärden definitionsområdet. Inbyggda roller beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **hämta** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill visa en lista över roller. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resursen /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{api-version}* med 2015-07-01.
3. Ersätt *{filter}* med villkor som du vill använda för att filtrera listan över roller:

   * Lista roller som är tillgängliga för tilldelning i det specificerade omfånget och alla dess underordnade omfattningar:`atScopeAndBelow()`
   * Sök efter en roll med exakt visningsnamn: `roleName%20eq%20'{role-display-name}'`. Använd URL-kodade formatet exakt visningsnamnet för rollen. Till exempel`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Svar
Statuskod: 200

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

## <a name="get-information-about-a-role"></a>Hämta information om en roll
Hämtar information om en enda roll som anges av rollen definition identifierare. För information om en roll med hjälp av dess namn, se [lista över alla roller för](role-based-access-control-manage-access-rest.md#list-all-roles).

Om du vill få information om en roll måste du ha tillgång till `Microsoft.Authorization/roleDefinitions/read` igen. Inbyggda roller beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **hämta** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill visa en lista över rolltilldelningarna. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-definition-id}* med GUID-identifierare för rolldefinitionen.
3. Ersätt *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar
Statuskod: 200

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

## <a name="create-a-custom-role"></a>Skapa en anpassad roll
Skapa en anpassad roll.

Om du vill skapa en anpassad roll måste du ha tillgång till `Microsoft.Authorization/roleDefinitions/write` igen på alla de `AssignableScopes`. I de inbyggda rollerna, endast *ägare* och *administratör för användaråtkomst* beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **PLACERA** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med först *AssignableScope* av den anpassade rollen. Följande exempel visar hur du kan ange omfång för olika nivåer.

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-definition-id}* med en ny GUID som blir GUID-identifierare för den nya anpassa rollen.
3. Ersätt *{api-version}* med 2015-07-01.

För begärantext, anger du värden i följande format:

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

| Elementnamn | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| namn |Ja |Sträng |GUID-identifierare för den anpassade rollen. |
| properties.roleName |Ja |Sträng |Visningsnamn för den anpassade rollen. Maximal storlek 128 tecken. |
| Properties.Description |Nej |Sträng |Beskrivning av anpassad roll. Maximal storlek 1024 tecken. |
| Properties.Type |Ja |Sträng |Ange till ”CustomRole”. |
| Properties.permissions.Actions |Ja |String] |En strängmatris åtgärd anger de åtgärder som tilldelats av den anpassade rollen. |
| properties.permissions.notActions |Nej |String] |En strängmatris åtgärd anger åtgärderna som ska undantas från de åtgärder som tilldelats av den anpassade rollen. |
| properties.assignableScopes |Ja |String] |En matris med scope där den anpassade rollen som kan användas. |

### <a name="response"></a>Svar
Statuskod: 201

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

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll
Ändra en anpassad roll.

Om du vill ändra en anpassad roll måste du ha tillgång till `Microsoft.Authorization/roleDefinitions/write` igen på alla de `AssignableScopes`. I de inbyggda rollerna, endast *ägare* och *administratör för användaråtkomst* beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **PLACERA** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med först *AssignableScope* av den anpassade rollen. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-definition-id}* med GUID-identifierare för den anpassade rollen.
3. Ersätt *{api-version}* med 2015-07-01.

För begärantext, anger du värden i följande format:

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

| Elementnamn | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| namn |Ja |Sträng |GUID-identifierare för den anpassade rollen. |
| properties.roleName |Ja |Sträng |Visningsnamn för den anpassade rollen som är uppdaterade. |
| Properties.Description |Nej |Sträng |Beskrivning av den anpassade rollen som är uppdaterade. |
| Properties.Type |Ja |Sträng |Ange till ”CustomRole”. |
| Properties.permissions.Actions |Ja |String] |En strängmatris åtgärd anger de åtgärder som den uppdaterade anpassade rollen som ger åtkomst. |
| properties.permissions.notActions |Nej |String] |En strängmatris åtgärd anger åtgärderna som ska undantas från de åtgärder som den anpassade rollen som uppdaterade ger. |
| properties.assignableScopes |Ja |String] |En matris med scope där uppdaterade anpassad roll kan användas. |

### <a name="response"></a>Svar
Statuskod: 201

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

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll
Ta bort en anpassad roll.

Om du vill ta bort en anpassad roll måste du ha tillgång till `Microsoft.Authorization/roleDefinitions/delete` igen på alla de `AssignableScopes`. I de inbyggda rollerna, endast *ägare* och *administratör för användaråtkomst* beviljas åtkomst till den här åtgärden. Mer information om rolltilldelningar och hantera åtkomst till Azure-resurser finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

### <a name="request"></a>Förfrågan
Använd den **ta bort** metoden med följande URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Gör följande ersättningar att anpassa din begäran inom URI:

1. Ersätt *{scope}* med den omfattning som du vill ta bort rolldefinitionen. Följande exempel visar hur du kan ange omfång för olika nivåer:

   * Prenumerationen: /subscriptions/ {prenumerations-id}  
   * Resursgrupp: /subscriptions/ {prenumerations-id} / resursgrupper/myresourcegroup1  
   * Resurs: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Ersätt *{roll-definition-id}* med ID: t för GUID rollen definitionen av den anpassade rollen.
3. Ersätt *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar
Statuskod: 200

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

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
