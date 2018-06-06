---
title: Skapa principer och visa kompatibilitetsdata med Azure princip programmässigt
description: Den här artikeln vägleder dig genom programmässigt skapa och hantera principer för Azure-principen.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: a83402316854b23fe85bff813dc9f5665bccd1fb
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794822"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Skapa principer och visa kompatibilitetsdata programmässigt

Den här artikeln vägleder dig genom att skapa och hantera principer programmässigt. Den visar hur du visar kompatibilitetstillstånd för resursen och principer. Principdefinitioner tillämpa olika regler och effekter över dina resurser. Tvingande säkerställer att resurser vara kompatibla med företagets standarder och servicenivåavtal.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att följande krav är uppfyllda:

1. Installera om du inte redan gjort det [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett verktyg som skickar HTTP-förfrågningar till Azure Resource Manager-baserade API: er.
2. Uppdatera din AzureRM PowerShell-modul till den senaste versionen. Mer information om den senaste versionen finns [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Registrera principen insikter resursprovidern med Azure PowerShell för att säkerställa att din prenumeration fungerar med resursprovidern. När du ska registrera en resursleverantör måste du ha behörighet att utföra registeringsåtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Läs mer om registrering och visa resursproviders [Resursproviders och typer](../azure-resource-manager/resource-manager-supported-services.md).
4. Om du inte redan gjort installera Azure CLI. Du kan hämta den senaste versionen på [installera Azure CLI 2.0 på Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Skapa och tilldela en principdefinition

Första steget mot bättre synlighet för dina resurser är att skapa och tilldela principer över dina resurser. Nästa steg är att lära dig hur du skapar och tilldelar du en princip programmässigt. Exempel principen granskningar storage-konton är tillgängliga för alla offentliga nätverk med PowerShell, Azure CLI och HTTP-begäranden.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Skapa och tilldela en principdefinition med PowerShell

1. Använd följande JSON-fragment för att skapa en JSON-fil med namnet AuditStorageAccounts.json.

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

  Läs mer om att redigera en principdefinition [Azure Definition principstruktur](policy-definition.md).
2. Kör följande kommando för att skapa en principdefinition med hjälp av filen AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  Kommandot skapar en principdefinition med namnet _Audit Storage-konton öppna till offentliga nätverk_. Mer information om andra parametrar som du kan använda finns [ny AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. När du har skapat din principdefinitionen kan du skapa en principtilldelning genom att köra följande kommandon:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Ersätt _ContosoRG_ med namnet på din avsedda resursgrupp.

Mer information om hur du hanterar principer för företagsresurser med hjälp av Azure Resource Manager PowerShell-modulen finns [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Skapa och tilldela en principdefinition med ARMClient

Använd följande procedur för att skapa en principdefinition.

1. Kopiera följande JSON-fragment för att skapa en JSON-fil. Du måste anropa filen i nästa steg.

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

2. Skapa principdefinitionen med någon av följande anrop:

  ```
  # For defining a policy in a subscription
  armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

  # For defining a policy in a management group
  armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Ersätt den föregående {subscriptionId} med ID för din prenumeration eller {managementGroupId} med ID: T för din [hanteringsgruppen](../azure-resource-manager/management-groups-overview.md).

  Mer information om strukturen i frågan finns [Principdefinitioner – skapa eller uppdatera](/rest/api/resources/policydefinitions/createorupdate) och [Principdefinitioner – skapa eller uppdatera vid hantering av grupp](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Använd följande procedur för att skapa en principtilldelning och tilldela principdefinitionen på resursgruppsnivå.

1. Kopiera följande JSON-fragment för att skapa en tilldelning principfil JSON. Ersätt exempelinformationen i &lt; &gt; symboler med egna värden.

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

2. Skapa principtilldelningen med hjälp av följande anrop:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Ersätt exempelinformationen i &lt; &gt; symboler med egna värden.

  Mer information om HTTP-anrop till REST-API finns [Azure REST API-resurser](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Skapa och tilldela en principdefinition med Azure CLI

Använd följande procedur för att skapa en principdefinition:

1. Kopiera följande JSON-fragment för att skapa en tilldelning principfil JSON.

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

2. Kör följande kommando för att skapa en principdefinition:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Använd följande kommando för att skapa en principtilldelning. Ersätt exempelinformationen i &lt; &gt; symboler med egna värden.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

Du kan få principen Definitions-ID med hjälp av PowerShell med följande kommando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Princip-ID för definition för principdefinitionen som du skapade bör likna följande exempel:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Mer information om hur du kan hantera resursprinciper med Azure CLI finns [Azure CLI resursprinciper](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Granska följande artiklar för mer information om kommandon och frågor i den här artikeln.

- [Azure REST API-resurser](/rest/api/resources/)
- [Azure RM PowerShell-moduler](/powershell/module/azurerm.resources/#policies)
- [Azure CLI-kommandona för principen](/cli/azure/policy?view=azure-cli-latest)
- [Principprovidern insikter resurs REST API-referens](/rest/api/policy-insights)
- [Ordna dina resurser med Azure-Hanteringsgrupper](../azure-resource-manager/management-groups-overview.md)