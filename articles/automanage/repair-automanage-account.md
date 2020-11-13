---
title: Reparera ett brutet Azure automanage-konto
description: Lär dig hur du åtgärdar ett trasigt automanage-konto
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557863"
---
# <a name="repair-a-broken-automanage-account"></a>Reparera ett trasigt automanage-konto
Kontot för automatisk [hantering](./automanage-virtual-machines.md#automanage-account) är säkerhets kontexten eller den identitet under vilken de automatiserade åtgärderna utförs. Om du nyligen har flyttat en prenumeration som innehåller ett automatiskt hanterat konto till en ny klient måste du konfigurera om kontot för autohantering. Om du vill konfigurera om kontot för autohantering måste du återställa identitets typen och tilldela lämpliga roller för kontot.

## <a name="step-1-reset-automanage-account-identity-type"></a>Steg 1: Återställ den automatiska hanterings kontots identitets typ
Återställ den automatiska hanterings kontots identitets typ med mallen Azure Resource Manager (ARM) nedan. Spara filen lokalt som `armdeploy.json` eller liknande. Anteckna namnet och platsen för autohantering av kontot, eftersom de är obligatoriska parametrar i ARM-mallen.

1. Skapa en ny ARM-distribution med mallen nedan och Använd `identityType = None`
    * Du kan göra detta med Azure CLI med hjälp av `az deployment sub create` . Läs mer om `az deployment sub` kommandot [här](https://docs.microsoft.com/cli/azure/deployment/sub).
    * Du kan också göra detta med PowerShell med hjälp av `New-AzDeployment` modulen. Lär dig mer om `New AzDeployment` modulen [här](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Kör samma ARM-mall igen med `identityType = SystemAssigned`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Steg 2: tilldela lämpliga roller för kontot för autohantering
Kontot för autohantering kräver deltagar-och resurs princip deltagar roller i prenumerationen som innehåller de virtuella datorer som hanteras av den automatiska hanteringen. Du kan tilldela dessa roller med hjälp av Azure Portal, ARM-mallar eller Azure CLI.

Om du använder en ARM-mall eller Azure CLI behöver du ägar-ID: t (även kallat objekt-ID) för kontot för autohantering (det är inte nödvändigt om du använder Azure Portal). Du kan hitta huvud kontots ID (objekt-ID) för kontot för autohantering med hjälp av följande metoder:

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp): Använd kommandot `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: navigera till **Azure Active Directory** och Sök efter ditt automanage-konto efter namn. Under **företags program** väljer du det hanterade konto namnet när det visas.

### <a name="azure-portal"></a>Azure-portalen
1. Under **prenumerationer** navigerar du till prenumerationen som innehåller dina autohanterade virtuella datorer.
1. Navigera till **åtkomst kontroll (IAM)**.
1. Klicka på **Lägg till roll tilldelningar**.
1. Välj **deltagar** rollen och skriv namnet på kontot för autohantering.
1. Tryck på **Spara**.
1. Upprepa steg 3-5, den här gången med rollen som **deltagar resurs princip** .

### <a name="arm-template"></a>ARM-mall
Kör följande ARM-mall. Du behöver huvud-ID: t för ditt automanage-konto – steg för att hämta huvudobjekts-ID: t ovan. Ange den när du uppmanas till det.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Kör följande kommandon:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer om Azure automanage [här](./automanage-virtual-machines.md).
