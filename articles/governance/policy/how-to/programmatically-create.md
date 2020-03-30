---
title: Skapa principer programmässigt
description: I den här artikeln får du hjälp med programmatiskt att skapa och hantera principer för Azure Policy med Azure CLI, Azure PowerShell och REST API.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264549"
---
# <a name="programmatically-create-policies"></a>Skapa principer programmässigt

I den här artikeln får du hjälp med programmatiskt skapa och hantera principer. Azure Policy definitioner genomdriva olika regler och effekter över dina resurser. Tvingande myndigheter ser till att resurserna förblir kompatibla med företagets standarder och servicenivåavtal.

Information om efterlevnad finns i [hämta efterlevnadsdata](get-compliance-data.md).

## <a name="prerequisites"></a>Krav

Innan du börjar kontrollerar du att följande förutsättningar är uppfyllda:

1. Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.

1. Uppdatera din Azure PowerShell-modul till den senaste versionen. Detaljerad information finns i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Mer information om den senaste versionen finns i [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registrera Azure Policy Insights-resursleverantören med Azure PowerShell för att verifiera att din prenumeration fungerar med resursleverantören. Om du vill registrera en resursprovider måste du ha behörighet att köra åtgärden registrera åtgärder för resursleverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Mer information om hur du registrerar och visar resursleverantörer finns i [Resursleverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Om du inte redan har gjort det installerar du Azure CLI. Du kan få den senaste versionen på [Installera Azure CLI i Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Skapa och tilldela en principdefinition

Det första steget mot bättre synlighet för dina resurser är att skapa och tilldela principer över dina resurser. Nästa steg är att lära sig att programmässigt skapa och tilldela en princip. Exempelprincipen granskar lagringskonton som är öppna för alla offentliga nätverk med PowerShell-, Azure CLI- och HTTP-begäranden.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Skapa och tilldela en principdefinition med PowerShell

1. Använd följande JSON-kodavsnitt för att skapa en JSON-fil med namnet AuditStorageAccounts.json.

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

   Mer information om hur du skapar en principdefinition finns i [Azure Policy Definition Structure](../concepts/definition-structure.md).

1. Kör följande kommando för att skapa en principdefinition med filen AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Kommandot skapar en principdefinition med namnet _Audit Storage Accounts Open to Public Networks_.
   Mer information om andra parametrar som du kan använda finns i [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   När det anropas `New-AzPolicyDefinition` utan platsparametrar kan du spara principdefinitionen i den valda prenumerationen för sessionskontexten som standard. Om du vill spara definitionen på en annan plats använder du följande parametrar:

   - **SubscriptionId** - Spara i en annan prenumeration. Kräver ett _GUID-värde._
   - **ManagementGroupName** - Spara i en hanteringsgrupp. Kräver ett _strängvärde._

1. När du har skapat principdefinitionen kan du skapa en principtilldelning genom att köra följande kommandon:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Ersätt _ContosoRG_ med namnet på den avsedda resursgruppen.

   Parametern **Scope** på `New-AzPolicyAssignment` fungerar med hanteringsgrupp, prenumeration, resursgrupp eller en enda resurs. Parametern använder en fullständig resurssökväg, `Get-AzResourceGroup` som **egenskapen ResourceId** på returer. Mönstret för **Scope** för varje behållare är följande. Ersätt `{rName}` `{rgName}`, `{subId}`, `{mgName}` och med resursnamn, resursgruppnamn, prenumerations-ID och hanteringsgruppnamn.
   `{rType}`skulle ersättas med **resurstypen** för resursen, till exempel `Microsoft.Compute/virtualMachines` för en virtuell dator.

   - Resurs -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resursgrupp -`/subscriptions/{subId}/resourceGroups/{rgName}`
   - Abonnemang -`/subscriptions/{subId}/`
   - Ledningsgrupp -`/providers/Microsoft.Management/managementGroups/{mgName}`

Mer information om hur du hanterar resursprinciper med Hjälp av Azure Resource Manager PowerShell-modulen finns i [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Skapa och tilldela en principdefinition med ARMClient

Använd följande procedur för att skapa en principdefinition.

1. Kopiera följande JSON-kodavsnitt för att skapa en JSON-fil. Du ringer filen i nästa steg.

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

1. Skapa principdefinitionen med något av följande anrop:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Ersätt föregående {subscriptionId} med ID:t för din prenumeration eller {managementGroupId} med ID:t för [din hanteringsgrupp](../../management-groups/overview.md).

   Mer information om frågans struktur finns i [Azure Policy Definitions – Skapa eller uppdatera](/rest/api/resources/policydefinitions/createorupdate) och [principdefinitioner – Skapa eller uppdatera i hanteringsgruppen](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Använd följande procedur för att skapa en principtilldelning och tilldela principdefinitionen på resursgruppsnivå.

1. Kopiera följande JSON-kodavsnitt för att skapa en JSON-principtilldelningsfil. Ersätt exempelinformation &lt; &gt; i symboler med dina egna värden.

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

1. Skapa principtilldelningen med följande anrop:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Ersätt exempelinformation &lt; &gt; i symboler med dina egna värden.

   Mer information om hur du ringer HTTP-anrop till REST API finns i [Azure REST API Resources](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Skapa och tilldela en principdefinition med Azure CLI

Om du vill skapa en principdefinition använder du följande procedur:

1. Kopiera följande JSON-kodavsnitt för att skapa en JSON-principtilldelningsfil.

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

   Mer information om hur du skapar en principdefinition finns i [Azure Policy Definition Structure](../concepts/definition-structure.md).

1. Kör följande kommando för att skapa en principdefinition:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Kommandot skapar en principdefinition med namnet _Audit Storage Accounts Open to Public Networks_.
   Mer information om andra parametrar som du kan använda finns i [az-principdefinition skapa](/cli/azure/policy/definition#az-policy-definition-create).

   När det anropas `az policy definition creation` utan platsparametrar kan du spara principdefinitionen i den valda prenumerationen för sessionskontexten som standard. Om du vill spara definitionen på en annan plats använder du följande parametrar:

   - **--prenumeration** - Spara i en annan prenumeration. Kräver ett _GUID-värde_ för prenumerations-ID eller ett _strängvärde_ för prenumerationsnamnet.
   - **--management-group** - Spara till en ledningsgrupp. Kräver ett _strängvärde._

1. Använd följande kommando för att skapa en principtilldelning. Ersätt exempelinformation &lt; &gt; i symboler med dina egna värden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Parametern **--scope** på `az policy assignment create` fungerar med hanteringsgrupp, prenumeration, resursgrupp eller en enda resurs. Parametern använder en fullständig resurssökväg. Mönstret för **--scope** för varje behållare är följande. Ersätt `{rName}` `{rgName}`, `{subId}`, `{mgName}` och med resursnamn, resursgruppnamn, prenumerations-ID och hanteringsgruppnamn. `{rType}`skulle ersättas med **resurstypen** för resursen, till exempel `Microsoft.Compute/virtualMachines` för en virtuell dator.

   - Resurs -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resursgrupp -`/subscriptions/{subID}/resourceGroups/{rgName}`
   - Abonnemang -`/subscriptions/{subID}`
   - Ledningsgrupp -`/providers/Microsoft.Management/managementGroups/{mgName}`

Du kan hämta Azure Policy Definition ID med hjälp av PowerShell med följande kommando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Principdefinitions-ID för principdefinitionen som du skapade bör likna följande exempel:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Mer information om hur du kan hantera resursprinciper med Azure CLI finns i [Azure CLI Resource Policies](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill ha mer information om kommandona och frågorna i den här artikeln.

- [Azure REST API-resurser](/rest/api/resources/)
- [Azure PowerShell-moduler](/powershell/module/az.resources/#policies)
- [Azure CLI-principkommandon](/cli/azure/policy?view=azure-cli-latest)
- [Azure Policy Insights-resursprovider REST API-referens](/rest/api/policy-insights)
- [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).