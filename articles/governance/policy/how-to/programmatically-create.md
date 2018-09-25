---
title: Skapa principer och visa data för kompatibilitetsinställningar med Azure Policy programmässigt
description: Den här artikeln beskriver hur du programmässigt kan skapa och hantera principer för Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: dd7ec4f1d0c018a3c7eed19bea523f7c09bfea3e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985324"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programmässigt skapa principer och visa data för kompatibilitetsinställningar

Den här artikeln beskriver hur du programmässigt kan skapa och hantera principer. Principdefinitioner tillämpar olika regler och effekterna på resurserna. Tvingande ser till att resurserna kompatibla med företagets standarder och serviceavtal.

Information om efterlevnad finns i [komma kompatibilitetsdata](getting-compliance-data.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du kontrollera att följande krav är uppfyllda:

1. Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.

1. Uppdatera din AzureRM PowerShell-modul till den senaste versionen. Mer information om den senaste versionen finns i [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registrera resursprovidern Policy Insights med Azure PowerShell för att säkerställa att din prenumeration fungerar med resursprovidern. När du ska registrera en resursleverantör måste du ha behörighet att utföra registeringsåtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

   ```azurepowershell-interactive
   Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Läs mer om att registrera och visa resursproviders [Resursproviders och resurstyper](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Om du inte redan gjort installerar du Azure CLI. Du kan hämta den senaste versionen på [installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Skapa och tilldela en principdefinition

Det första steget mot bättre överblick över dina resurser är att skapa och tilldela principer över dina resurser. Nästa steg är att lära dig hur du programmässigt skapar och tilldelar du en princip. Exempel-principen granskar lagringskonton som är öppna för alla offentliga nätverk med hjälp av PowerShell, Azure CLI och HTTP-begäranden.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Skapa och tilldela en principdefinition med PowerShell

1. Använd följande JSON-kodfragment för att skapa en JSON-fil med namnet AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Läs mer om redigering av en principdefinition [Azure Policy-Definitionsstruktur](../concepts/definition-structure.md).

1. Kör följande kommando för att skapa en principdefinition med hjälp av AuditStorageAccounts.json-filen.

   ```azurepowershell-interactive
   New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Kommandot skapar en principdefinition med namnet _Audit Storage-konton öppen för offentliga nätverk_. Läs mer om andra parametrar som du kan använda [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

1. När du skapar principdefinitionen har kan skapa du en principtilldelning genom att köra följande kommandon:

   ```azurepowershell-interactive
   $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Ersätt _ContosoRG_ med namnet på din avsedda resursgrupp.

Mer information om hur du hanterar resursprinciper med Azure Resource Managers PowerShell-modulen finns i [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Skapa och tilldela en principdefinition med ARMClient

Använd följande procedur för att skapa en principdefinition.

1. Kopiera följande JSON-kodfragment för att skapa en JSON-fil. Du ringer upp filen i nästa steg.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Skapa principdefinitionen med någon av följande anrop:

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Ersätt det föregående {subscriptionId} med ID: T för din prenumeration eller {managementGroupId} med ID: T för din [hanteringsgruppen](../../management-groups/overview.md).

   Mer information om strukturen i frågan finns i [Principdefinitioner – skapa eller uppdatera](/rest/api/resources/policydefinitions/createorupdate) och [Principdefinitioner – skapa eller uppdatera vid hantering av grupp](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Använd följande procedur för att skapa en principtilldelning och tilldelar principdefinitionen på resursgruppsnivå.

1. Kopiera följande JSON-kodfragment för att skapa en JSON-fil enligt principen tilldelning. Ersätt exempelinformation i &lt; &gt; symboler med dina egna värden.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Skapa principtilldelning med hjälp av följande anrop:

   ```
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Ersätt exempelinformation i &lt; &gt; symboler med dina egna värden.

   Mer information om HTTP-anrop till REST API finns i [Azure REST API-resurserna](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Skapa och tilldela en principdefinition med Azure CLI

Följ anvisningarna nedan om du vill skapa en principdefinition:

1. Kopiera följande JSON-kodfragment för att skapa en JSON-fil enligt principen tilldelning.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

1. Kör följande kommando för att skapa en principdefinition:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

1. Använd följande kommando för att skapa en principtilldelning. Ersätt exempelinformation i &lt; &gt; symboler med dina egna värden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

Du kan hämta Principdefinitionens ID med hjälp av PowerShell med följande kommando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Principdefinitionens ID för principdefinitionen som du skapade bör likna följande exempel:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Mer information om hur du hanterar resursprinciper med Azure CLI finns i [resursprinciper för Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Granska följande artiklar för mer information om kommandon och frågor i den här artikeln.

- [Azure REST API-resurser](/rest/api/resources/)
- [Azure RM PowerShell-moduler](/powershell/module/azurerm.resources/#policies)
- [Azure CLI-kommandon för principen](/cli/azure/policy?view=azure-cli-latest)
- [Princip för resursprovidern för Insights REST API-referens](/rest/api/policy-insights)
- [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)