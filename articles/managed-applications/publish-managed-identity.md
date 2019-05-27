---
title: Azure-hanterade program med hanterad identitet
description: Lär dig hur du konfigurerar ett hanterat program med en hanterad identitet. Hanterad identitet kan användas för att distribuera Managed Applications länkad till befintliga resurser, ge Managed Applications till att hantera Azure-resurser utanför den hanterade resursgruppen och ange en operational identiteten för hanterade program för aktivitetsloggen och andra tjänster i Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003453"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure-hanterade program med hanterad identitet

> [!NOTE]
> Stöd för hanterade identiteter för Managed Applications förhandsvisas just nu. Använd api-versionen 2018-09-01-preview för att använda hanterade identiteter.

Lär dig hur du konfigurerar ett hanterat program som innehåller en hanterad identitet. Hanterad identitet kan användas till att kunden kan ge det hanterade programmet åtkomsten till ytterligare befintliga resurser. Identiteten hanteras av Azure-plattformen och kräver inte att etablera eller rotera hemligheter. Mer information om hanterade identiteter i Azure Active Directory (AAD) finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

Ditt program kan beviljas två typer av identiteter:

- En **systemtilldelade identiteter** är kopplad till ditt program och tas bort om din app har tagits bort. En app kan bara ha en systemtilldelad identitet.
- En **Användartilldelad identitet** är ett fristående Azure-resurs som kan tilldelas till din app. En app kan ha flera användartilldelade identiteter.

## <a name="how-to-use-managed-identity"></a>Hur du använder hanterade identiteter

Hanterad identitet möjliggör många scenarier för hanterade program. Några vanliga scenarier som går att lösa är:

- Distribuera ett hanterat program som är länkad till befintliga Azure-resurser. Ett exempel är att distribuera en Azure-dator (VM) i det hanterade programmet som är kopplad till en [befintliga nätverksgränssnittet](../virtual-network/virtual-network-network-interface-vm.md).
- Bevilja åtkomst för hanterade program och utgivare till Azure-resurser utanför den **hanterad resursgrupp**.
- Att tillhandahålla Managed Applications operativa identitet för aktivitetsloggen och andra tjänster i Azure.

## <a name="adding-managed-identity"></a>Att lägga till hanterad identitet

Skapa ett hanterat program med en hanterad identitet kräver ytterligare en egenskap på Azure-resursen. I följande exempel visas ett exempel **identitet** egenskapen:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Det finns två vanliga sätt att skapa ett hanterat program med **identitet**: [CreateUIDefinition.json](./create-uidefinition-overview.md) och [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Skapa scenarier för en enkel enskild, CreateUIDefinition bör användas för att aktivera hanterad identitet, eftersom det ger en rikare upplevelse. När du hanterar komplexa eller Avancerat system som kräver automatisk eller distribution av flera hanterade program, mallar kan användas.

### <a name="using-createuidefinition"></a>Med CreateUIDefinition

Ett hanterat program kan konfigureras med hanterad identitet via den [CreateUIDefinition.json](./create-uidefinition-overview.md). I den [matar ut avsnittet](./create-uidefinition-overview.md#outputs), nyckeln `managedIdentity` kan användas för att åsidosätta identitetsegenskapen för mall för hanterade program. Exemplet nedan gör **systemtilldelade** identitet på hanterade programmet. Mer komplexa identitetsobjekt kan skapas med hjälp av CreateUIDefinition element be konsumenten för indata. Dessa indata som kan användas för att konstruera Managed Applications med **Användartilldelad identitet**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>När du ska använda CreateUIDefinition för hanterad identitet

Nedan följer några rekommendationer för när du ska använda CreateUIDefinition för att aktivera hanterade identiteter på hanterade program.

- Skapa för hanterade program går igenom Azure-portalen eller marketplace.
- Hanterade identiteter kräver komplexa konsument indata.
- Hanterad identitet krävs vid skapandet av hanterade program.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

En grundläggande CreateUIDefinition som möjliggör SystemAssigned identiteten för det hanterade programmet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

En grundläggande CreateUIDefinition som tar en **Användartilldelad identitet** resurs som indata och gör det möjligt UserAssigned identiteten för det hanterade programmet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

CreateUIDefinition.json ovan genererar skapa användarna en upplevelse som har en textruta för en konsument att ange den **Användartilldelad identitet** Azure-resurs-ID. Den genererade upplevelsen ser ut som:

![Exempel på Användartilldelad identitet CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Med Azure Resource Manager-mallar

> [!NOTE]
> Marketplace-mallar för hanterade program genereras automatiskt för kunder som ska via Azure portal skapa upplevelse.
> Dessa scenarier kan de `managedIdentity` utdata nyckeln på CreateUIDefinition måste användas aktiverat identitet.

Hanterad identitet kan även aktiveras via Azure Resource Manager-mallar. Exemplet nedan gör **systemtilldelade** identitet på hanterade programmet. Mer komplexa identitetsobjekt kan skapas med Azure Resource Manager-mallens parametrar för att ange indata. Dessa indata som kan användas för att konstruera Managed Applications med **Användartilldelad identitet**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>När du ska använda Azure Resource Manager-mallar för hanterad identitet

Nedan följer några rekommendationer för när du ska använda Azure Resource Manager-mallar för att aktivera hanterade identiteter på hanterade program.

- Hanterade program kan distribueras via programmering baserad på en mall.
- Anpassade rolltilldelningar för hanterade identiteter behövs för att etablera hanterade programmet.
- Hanterat program behöver inte Azure portal och marketplace skapa flödet.

#### <a name="systemassigned-template"></a>SystemAssigned mall

En grundläggande Azure Resource Manager-mall som distribuerar ett hanterat program med **systemtilldelade** identitet.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned mall

En grundläggande Azure Resource Manager-mall som distribuerar ett hanterat program med en **Användartilldelad identitet**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Bevilja åtkomst till Azure-resurser

När ett hanterat program har beviljats en identitet, kan den beviljas åtkomst till befintliga azure-resurser. Den här processen kan göras via gränssnittet åtkomst åtkomstkontroll (IAM) i Azure-portalen. Namnet på den hanterade program eller **Användartilldelad identitet** kan sökas igenom för att lägga till en rolltilldelning.

![Lägg till rolltilldelning för hanterade program](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Länka befintliga Azure-resurser

> [!NOTE]
> En **Användartilldelad identitet** måste vara [konfigurerats](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) innan du distribuerar det hanterade programmet. Dessutom länkad resursdistribution av hanterade program stöds bara för den **marketplace** slag.

Hanterad identitet kan också användas för att distribuera ett hanterat program som kräver åtkomst till befintliga resurser under distributionen. När det hanterade programmet etableras av kunden, **användartilldelade identiteter** kan läggas till att tillhandahålla ytterligare tillstånd till den **mainTemplate** distribution.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Redigera CreateUIDefinition med en länkad resurs

När du länkar distribution av hanterade program i befintliga resurser, både befintliga Azure-resursen och en **Användartilldelad identitet** med rollen tillämpliga tilldelning av resursen måste anges.

 Ett sampel CreateUIDefinition som kräver två indatavärdena: ett nätverksgränssnitts resurs-ID och en Användartilldelad identitet resurs-id.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Den här CreateUIDefinition.json genererar en skapa användarvänligt gränssnitt som har två fält. Det första fältet gör att användaren kan ange i Azure-resurs-ID för den resurs som är länkad till programdistributionen som hanteras. Andra är för en konsument att ange den **Användartilldelad identitet** Azure-resurs-ID som har åtkomst till den länkade Azure-resursen. Den genererade upplevelsen ser ut som:

![Exempel på CreateUIDefinition med två indatavärdena: ett nätverksgränssnitt resurs-ID och en Användartilldelad identitet resurs-ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Redigera mainTemplate med en länkad resurs

Förutom att uppdatera CreateUIDefinition måste huvudsakliga mallen också uppdateras för att godkänna den överförda i länkade resurs-ID. Den huvudsakliga mallen kan uppdateras för att godkänna den nya utdatan genom att lägga till en ny parameter. Eftersom den `managedIdentity` utdata åsidosätter värdet på den genererade mallen för hanterade program, skickas inte till den huvudsakliga mallen och ska inte ingå i avsnittet Parametrar.

En huvudsakliga exempelmall som anger nätverksprofilen till en befintlig nätverksgränssnitt som tillhandahålls av CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Använder det hanterade programmet med en länkad resurs

När det hanterade programpaketet har skapats kan kan det hanterade programmet användas via Azure portal. Innan den kan användas, finns det flera nödvändiga steg.

- En instans av nödvändiga länkade Azure-resursen måste skapas.
- Den **Användartilldelad identitet** måste vara [skapas och de angivna rolltilldelningar](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) till den länkade resursen.
- Den befintliga länkade resurs-ID och **Användartilldelad identitet** ID tillhandahålls för att CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Åtkomst till den hanterade identiteter-token

Token för hanterade program kan nu kommas åt via den `listTokens` api från publisher-klient. En exempelbegäran kan se ut:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

En exempelsvaret kan se ut:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här konfigurerar du ett hanterat program med en anpassad Provider](./custom-providers-overview.md)