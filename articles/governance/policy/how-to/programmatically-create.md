---
title: Skapa principer programmässigt
description: Den här artikeln vägleder dig genom program mässigt skapa och hantera principer för Azure Policy med Azure CLI, Azure PowerShell och REST API.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982471"
---
# <a name="programmatically-create-policies"></a>Skapa principer programmässigt

Den här artikeln beskriver hur du programmässigt kan skapa och hantera principer. Azure Policy definitioner tillämpar olika regler och effekter över dina resurser. Tvingande ser till att resurserna kompatibla med företagets standarder och serviceavtal.

Information om efterlevnad finns i [komma kompatibilitetsdata](get-compliance-data.md).

## <a name="prerequisites"></a>Krav

Innan du börjar måste du kontrollera att följande krav är uppfyllda:

1. Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.

1. Uppdatera Azure PowerShell-modulen till den senaste versionen. Detaljerad information finns i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Mer information om den senaste versionen finns i [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registrera resurs leverantören Azure Policy Insights med Azure PowerShell för att kontrol lera att din prenumeration fungerar med resurs leverantören. Om du vill registrera en resursleverantör måste du ha behörighet att köra registeringsåtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Läs mer om att registrera och visa resursproviders i [Resource Providers and Types](../../../azure-resource-manager/management/resource-providers-and-types.md) (Resursproviders och typer).

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
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Kommandot skapar en principdefinition med namnet _Audit Storage-konton öppen för offentliga nätverk_.
   Mer information om andra parametrar som du kan använda finns i [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   När den anropas utan Platsparametrar, `New-AzPolicyDefinition` standard till sparar principdefinitionen i den valda prenumerationen av sessioner kontext. Om du vill spara definitionen till en annan plats, använder du följande parametrar:

   - **SubscriptionId** -spara till en annan prenumeration. Kräver en _GUID_ värde.
   - **ManagementGroupName** -spara till en hanteringsgrupp. Kräver en _sträng_ värde.

1. När du skapar principdefinitionen har kan skapa du en principtilldelning genom att köra följande kommandon:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Ersätt _ContosoRG_ med namnet på din avsedda resursgrupp.

   **Omfattnings** parametern på `New-AzPolicyAssignment` fungerar med hanterings grupp, prenumeration, resurs grupp eller en enskild resurs. Parametern använder en fullständig resurssökväg som den **ResourceId** egenskapen `Get-AzResourceGroup` returnerar. Mönster för **omfång** för varje behållare är på följande sätt. Ersätt `{rName}`, `{rgName}`, `{subId}`och `{mgName}` med resurs namnet, resurs gruppens namn, prenumerations-ID respektive namn för hanterings gruppen.
   `{rType}` ersätts med resurs **typen** för resursen, till exempel `Microsoft.Compute/virtualMachines` för en virtuell dator.

   - Resurs-`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resursgrupp – `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Prenumeration – `/subscriptions/{subId}/`
   - Hanteringsgrupp- `/providers/Microsoft.Management/managementGroups/{mgName}`

Mer information om hur du hanterar resurs principer med hjälp av Azure Resource Manager PowerShell-modulen finns i [AZ. Resources](/powershell/module/az.resources/#policies).

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

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Ersätt det föregående {subscriptionId} med ID: T för din prenumeration eller {managementGroupId} med ID: T för din [hanteringsgruppen](../../management-groups/overview.md).

   Mer information om frågans struktur finns i [Azure policy definitioner – skapa eller uppdatera](/rest/api/resources/policydefinitions/createorupdate) och [princip definitioner – skapa eller uppdatera i hanterings gruppen](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

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

   ```console
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

   Läs mer om redigering av en principdefinition [Azure Policy-Definitionsstruktur](../concepts/definition-structure.md).

1. Kör följande kommando för att skapa en principdefinition:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Kommandot skapar en principdefinition med namnet _Audit Storage-konton öppen för offentliga nätverk_.
   Mer information om andra parametrar som du kan använda finns i [AZ policy definition Create](/cli/azure/policy/definition#az-policy-definition-create).

   När den anropas utan Platsparametrar, `az policy definition creation` standard till sparar principdefinitionen i den valda prenumerationen av sessioner kontext. Om du vill spara definitionen till en annan plats, använder du följande parametrar:

   - **--prenumeration** – Spara till en annan prenumeration. Kräver ett _GUID_ -värde för prenumerations-ID eller ett _sträng_ värde för prenumerations namnet.
   - **--hantering-grupp** – Spara i en hanterings grupp. Kräver en _sträng_ värde.

1. Använd följande kommando för att skapa en principtilldelning. Ersätt exempelinformation i &lt; &gt; symboler med dina egna värden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Parametern **--scope** på `az policy assignment create` fungerar med hanterings grupp, prenumeration, resurs grupp eller en enskild resurs. Parametern använder en fullständig resurs Sök väg. Mönster för **--omfattning** för varje behållare är följande. Ersätt `{rName}`, `{rgName}`, `{subId}`och `{mgName}` med resurs namnet, resurs gruppens namn, prenumerations-ID respektive namn för hanterings gruppen. `{rType}` ersätts med resurs **typen** för resursen, till exempel `Microsoft.Compute/virtualMachines` för en virtuell dator.

   - Resurs-`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resursgrupp – `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Prenumeration – `/subscriptions/{subID}`
   - Hanteringsgrupp- `/providers/Microsoft.Management/managementGroups/{mgName}`

Du kan hämta Azure Policy Definitions-ID med hjälp av PowerShell med följande kommando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Principdefinitionens ID för principdefinitionen som du skapade bör likna följande exempel:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Mer information om hur du hanterar resursprinciper med Azure CLI finns i [resursprinciper för Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Granska följande artiklar för mer information om kommandon och frågor i den här artikeln.

- [Azure REST API-resurser](/rest/api/resources/)
- [Azure PowerShell moduler](/powershell/module/az.resources/#policies)
- [Azure CLI-kommandon för principen](/cli/azure/policy?view=azure-cli-latest)
- [Azure Policy insikter Resource Provider REST API referens](/rest/api/policy-insights)
- [Organisera dina resurser i Azure-hanteringsgrupper](../../management-groups/overview.md).