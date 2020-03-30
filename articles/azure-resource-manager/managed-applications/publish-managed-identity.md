---
title: Hanterad app med hanterad identitet
description: Konfigurera hanterat program med hanterad identitet för länkning till befintliga resurser, hantera Azure-resurser och tillhandahålla operativ identitet för aktivitetslogg.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651662"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure Managed Application med hanterad identitet

> [!NOTE]
> Stöd för hanterade program för hanterade program är för närvarande i förhandsversion. Använd api-versionen 2018-09-01 för att använda hanterad identitet.

Lär dig hur du konfigurerar ett hanterat program så att det innehåller en hanterad identitet. Hanterad identitet kan användas för att kunden ska kunna bevilja åtkomst till hanterade program till ytterligare befintliga resurser. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i Azure Active Directory (AAD) finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Din ansökan kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är knuten till ditt program och tas bort om appen tas bort. En app kan bara ha en systemtilldelad identitet.
- En **användartilldelad identitet** är en fristående Azure-resurs som kan tilldelas din app. En app kan ha flera användartilldelade identiteter.

## <a name="how-to-use-managed-identity"></a>Så här använder du hanterad identitet

Hanterad identitet möjliggör många scenarier för hanterade program. Några vanliga scenarier som kan lösas är:

- Distribuera ett hanterat program som är kopplat till befintliga Azure-resurser. Ett exempel är att distribuera en virtuell Azure-dator (VM) i det hanterade programmet som är kopplat till ett [befintligt nätverksgränssnitt](../../virtual-network/virtual-network-network-interface-vm.md).
- Bevilja managed application och utgivaren åtkomst till Azure-resurser utanför den **hanterade resursgruppen**.
- Tillhandahålla en operativ identitet för hanterade program för aktivitetslogg och andra tjänster i Azure.

## <a name="adding-managed-identity"></a>Lägga till hanterad identitet

Skapa ett hanterat program med en hanterad identitet kräver ytterligare en egenskap som ska anges på Azure-resursen. I följande exempel visas en **exempelidentitetsegenskap:**

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Det finns två vanliga sätt att skapa ett hanterat program med **identitet:** [CreateUIDefinition.json](./create-uidefinition-overview.md) och [Azure Resource Manager- mallar](../templates/template-syntax.md). För enkla scenarier för enkel enstaka skapade bör CreateUIDefinition användas för att aktivera hanterad identitet, eftersom det ger en rikare upplevelse. När du hanterar avancerade eller komplexa system som kräver automatiserade eller flera distribuerade program kan mallar användas.

### <a name="using-createuidefinition"></a>Använda CreateUIDefinition

Ett hanterat program kan konfigureras med hanterad identitet via [CreateUIDefinition.json](./create-uidefinition-overview.md). I [avsnittet utdata](./create-uidefinition-overview.md#outputs)kan `managedIdentity` nyckeln användas för att åsidosätta identitetsegenskapen för mallen Hanterat program. Exempelbälgen aktiverar **systemtilldelade** identitet i det hanterade programmet. Mer komplexa identitetsobjekt kan bildas med hjälp av CreateUIDefinition-element för att be konsumenten om indata. Dessa indata kan användas för att konstruera hanterade program med **användartilldelade identitet**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>När du ska använda CreateUIDefinition för hanterad identitet

Nedan följer några rekommendationer om när du ska använda CreateUIDefinition för att aktivera hanterad identitet på hanterade program.

- Skapandet av hanterade program går via Azure-portalen eller marknadsplatsen.
- Den hanterade identiteten kräver komplexa konsumentindata.
- Den hanterade identiteten behövs när det hanterade programmet skapas.

#### <a name="systemassigned-createuidefinition"></a>SystemAssignerade CreateUIDefinition

En grundläggande CreateUIDefinition som aktiverar den SystemAssigned-identiteten för det hanterade programmet.

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

En grundläggande CreateUIDefinition som tar en **användartilldelad identitetsresurs** som indata och aktiverar den UserAssigned-identiteten för det hanterade programmet.

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

CreateUIDefinition.json ovan genererar en skapa användarupplevelse som har en textruta för en konsument att ange **den användartilldelade identiteten** Azure-resurs-ID. Den genererade upplevelsen skulle se ut:

![Exempel på användartilldelade identitet CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

> [!NOTE]
> Marketplace-mallar för hanterade program genereras automatiskt för kunder som går igenom Azure-portalen skapa erfarenhet.
> För dessa scenarier `managedIdentity` måste utdatanyckeln på CreateUIDefinition användas för att aktiverad identitet.

Den hanterade identiteten kan också aktiveras via Azure Resource Manager-mallar. Exempelbälgen aktiverar **systemtilldelade** identitet i det hanterade programmet. Mer komplexa identitetsobjekt kan bildas med hjälp av Azure Resource Manager-mallparametrar för att tillhandahålla indata. Dessa indata kan användas för att konstruera hanterade program med **användartilldelade identitet**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>När ska Azure Resource Manager-mallar användas för hanterad identitet

Nedan följer några rekommendationer om när du ska använda Azure Resource Manager-mallar för att aktivera hanterad identitet på hanterade program.

- Hanterade program kan distribueras programmässigt baserat på en mall.
- Anpassade rolltilldelningar för den hanterade identiteten behövs för att etablera det hanterade programmet.
- Det hanterade programmet behöver inte azure-portalen och marketplace skapa flöde.

#### <a name="systemassigned-template"></a>Mall för systemtilldelade

En grundläggande Azure Resource Manager-mall som distribuerar ett hanterat program med **systemtilldelad** identitet.

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

### <a name="userassigned-template"></a>Mall för UserAssignerad

En grundläggande Azure Resource Manager-mall som distribuerar ett hanterat program med en **användartilldelad identitet**.

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

När ett hanterat program har tilldelats en identitet kan det beviljas åtkomst till befintliga azure-resurser. Den här processen kan göras via IAM-gränssnittet (Access Control) i Azure-portalen. Namnet på det hanterade programmet eller **den användartilldelade identiteten** kan sökas för att lägga till en rolltilldelning.

![Lägga till rolltilldelning för hanterat program](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Länka befintliga Azure-resurser

> [!NOTE]
> En **användartilldelad identitet** måste [konfigureras](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) innan det hanterade programmet distribueras. Dessutom stöds länkad resursdistribution av hanterade program endast för **marketplace-typen.**

Hanterad identitet kan också användas för att distribuera ett hanterat program som kräver åtkomst till befintliga resurser under distributionen. När det hanterade programmet etableras av kunden kan **användartilldelade identiteter** läggas till för att tillhandahålla ytterligare auktoriseringar till **mainTemplate-distributionen.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Skapa CreateUIDefinition med en länkad resurs

När du länkar distributionen av det hanterade programmet till befintliga resurser måste både den befintliga Azure-resursen och en **användartilldelad identitet** med tillämplig rolltilldelning på den resursen tillhandahållas.

 Ett exempel på CreateUIDefinition som kräver två indata: ett nätverksgränssnittsresurs-ID och ett användartilldelade identitetsresurs-ID.

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

Den här CreateUIDefinition.json genererar en skapa användarupplevelse som har två fält. Det första fältet gör det möjligt för användaren att ange i Azure-resurs-ID för den resurs som länkas till distributionen hanterat program. Den andra är för en konsument att ange **den användartilldelade identiteten** Azure-resurs-ID, som har åtkomst till den länkade Azure-resursen. Den genererade upplevelsen skulle se ut:

![Exempel på CreateUIDefinition med två indata: ett nätverksgränssnittsresurs-ID och ett användartilldelade identitetsresurs-ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Skapa huvudmetoden med en länkad resurs

Förutom att uppdatera CreateUIDefinition måste huvudmallen också uppdateras för att acceptera det skickade i länkat resurs-ID. Huvudmallen kan uppdateras för att acceptera den nya utdata genom att lägga till en ny parameter. Eftersom `managedIdentity` utdata åsidosätter värdet på den genererade mallen För hanterade program skickas det inte till huvudmallen och ska inte inkluderas i parameteravsnittet.

Ett exempel på huvudmallen som anger nätverksprofilen till ett befintligt nätverksgränssnitt som tillhandahålls av CreateUIDefinition.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Använda det hanterade programmet med en länkad resurs

När paketet Managed Application har skapats kan det hanterade programmet förbrukas via Azure-portalen. Innan den kan förbrukas finns det flera nödvändiga steg.

- En instans av den länkade Azure-resurs som krävs måste skapas.
- Den **användartilldelade identiteten** måste [skapas och ges rolltilldelningar](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) till den länkade resursen.
- Det befintliga länkade resurs-ID:t och **det användartilldelade identitets-ID:et** finns i CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Komma åt token för hanterad identitet

Token för det hanterade programmet kan nu `listTokens` nås via api:et från utgivarens klientorganisation. En exempelbegäran kan se ut som:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parametrar för begäranden:

Parameter | Krävs | Beskrivning
---|---|---
tillståndPubliken | *nej* | Målresursens app-ID-IURI. Det är `aud` också (publiken) anspråk på den utfärdade token. Standardvärdet ärhttps://management.azure.com/" "
userAssignedId entiteter | *nej* | Listan över användartilldelade hanterade identiteter som du vill hämta en token för. Om det inte `listTokens` anges returneras token för den systemtilldelade hanterade identiteten.


Ett exempelsvar kan se ut så här:

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

Svaret innehåller en matris med token `value` under egenskapen:

Parameter | Beskrivning
---|---
access_token | Den begärda åtkomsttoken.
expires_in | Antalet sekunder som åtkomsttoken är giltig.
expires_on | Tidsintervallet när åtkomsttoken upphör att gälla. Detta representeras som antalet sekunder från epoken.
not_before | Tidsintervallet när åtkomsttoken börjar gälla. Detta representeras som antalet sekunder från epoken.
tillståndPubliken | Den `aud` (målgrupp) som åtkomsttoken var begäran om. Detta är samma som vad `listTokens` som angavs i begäran.
resourceId | Azure-resurs-ID för den utfärdade token. Detta är antingen det hanterade program-ID:t eller det användartilldelade identitets-ID: t.
token_type | Typen av token.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ett hanterat program med en anpassad leverantör](../custom-providers/overview.md)
