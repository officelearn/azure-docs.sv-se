---
title: Exempel i Azure Policy – Använda tagg och dess standardvärde
description: Den här exempelprincipen lägger till ett angivet taggnamn och värde, om den taggen inte tillhandahålls.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 3f900d39b0260836013f3e42e147654e1d7a2226
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214485"
---
# <a name="apply-tag-and-its-default-value"></a>Använd taggen och dess standardvärde

Den här principen lägger till ett angivet taggnamn och värde, om den taggen inte tillhandahålls. Du anger taggnamn och värde som ska gälla.

Du kan distribuera det här exemplet med hjälp av:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST-API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Exempel-princip

### <a name="policy-definition"></a>Definition av princip

Den fullständigt beskrivna JSON-principdefinitionen, som används av REST API, ”Deploy to Azure”-knapparna (Distribuera till Azure) samt manuellt i portalen.

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

> [!NOTE]
> Om du skapar en princip i portalen manuellt använder du delarna **properties.parameters** och **properties.policyRule** i ovanstående. Omslut de två avsnitten med klammerparenteser `{}` så att det blir giltigt JSON.

### <a name="policy-rules"></a>Principregler

Det JSON som definierar principens regler, som används av Azure CLI och Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Principparametrar

Det JSON som definierar principparametrarna, som används av Azure CLI och Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Namn |Typ |Fält |Beskrivning |
|---|---|---|---|
|tagName |Sträng |tags |Namnet på taggen, till exempel costCenter (kostnadsställe)|
|tagValue |Sträng |tags |Taggens värde, till exempel headquarter (huvudkontor)|

När du skapar en tilldelning via PowerShell eller Azure CLI går det att skicka parametervärdena som JSON antingen i en sträng eller via en fil med hjälp av `-PolicyParameter` (PowerShell) eller `--params` (Azure CLI).
PowerShell stödjer även `-PolicyParameterObject`, vilket kräver att det till cmdlet skickas en Name/Value-hashtabell där **Name** (Namn) är parameternamnet och **Value** (Värde) är det enskilda värde eller den matris med värden som skickas under tilldelningen.

I den här exempelparametern definieras _tagName_ **costCenter** och _tagValue_ **headquarter**.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Distribuera till Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)
[![Distribuera till Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzureRmPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzureRmResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzureRmPolicyAssignment -Name 'apply-default-tag-value' -DisplayName 'Apply tag and its default value Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Ta bort med Azure PowerShell

Kör följande kommandon för att ta bort föregående tilldelning och definition:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzureRmPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzureRmPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell-förklaring

Skripten för distribution och borttagning använder följande kommandon. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) | Skapar en ny Azure Policy-definition. |
| [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | Hämtar en enskild resursgrupp. |
| [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) | Skapar en ny Azure Policy-tilldelning. I det här exemplet ges den en definition, men den kan också ta emot ett initiativ. |
| [Remove-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/remove-azurermpolicyassignment) | Tar bort en befintlig Azure Policy-tilldelning. |
| [Remove-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/remove-azurermpolicydefinition) | Tar bort en befintlig Azure Policy-definition. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Ta bort med Azure CLI

Kör följande kommandon för att ta bort föregående tilldelning och definition:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI-förklaring

| Kommando | Anteckningar |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Skapar en ny Azure Policy-definition. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Hämtar en enskild resursgrupp. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Skapar en ny Azure Policy-tilldelning. I det här exemplet ges den en definition, men den kan också ta emot ett initiativ. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Tar bort en befintlig Azure Policy-tilldelning. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Tar bort en befintlig Azure Policy-definition. |

Det finns flera verktyg som kan användas för att interagera med Resource Manager REST API, till exempel [ARMClient](https://github.com/projectkudu/ARMClient) och PowerShell. Ett exempel på anrop till REST API från PowerShell finns i avsnittet **Aliases** (Alias) i [Policy definition structure](../concepts/definition-structure.md#aliases) (Principdefinitionsstrukturen).

### <a name="deploy-with-rest-api"></a>Distribuera med REST API

- Skapa principdefinitionen (prenumerationsomfång). Använd [principdefinitionen](#policy-definition) för JSON för begärandetexten.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

- Skapa principtilldelningen (resursgruppsomfång)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

  Använd följande JSON-exempel för begärandetexten:

  ```json
  {
      "properties": {
          "displayName": "Apply tag and its default value Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Ta bort med REST API

- Ta bort principtilldelningen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

- Ta bort principdefinitionen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>REST API-förklaring

| Tjänst | Grupp | Åtgärd | Anteckningar |
|---|---|---|---|
| Resurshantering | Principdefinitioner | [Skapa](/rest/api/resources/policydefinitions/createorupdate) | Skapar en ny Azure Policy-definition vid en prenumeration. Alternativ: [Skapa vid hanteringsgrupp](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Resurshantering | Principtilldelningar | [Skapa](/rest/api/resources/policyassignments/create) | Skapar en ny Azure Policy-tilldelning. I det här exemplet ges den en definition, men den kan också ta emot ett initiativ. |
| Resurshantering | Principtilldelningar | [Ta bort](/rest/api/resources/policyassignments/delete) | Tar bort en befintlig Azure Policy-tilldelning. |
| Resurshantering | Principdefinitioner | [Ta bort](/rest/api/resources/policydefinitions/delete) | Tar bort en befintlig Azure Policy-definition. Alternativ: [Ta bort vid hanteringsgrupp](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Nästa steg

- Granska ytterligare [Azure Policy-exempel](index.md)
- Granska [Azure Policy-definitionsstruktur](../concepts/definition-structure.md)