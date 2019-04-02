---
title: Exempel – Tillåtna platser
description: Den här exempelprincipdefinitionen kräver att alla resurser distribueras till de godkända platserna.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 0dc93b171fb610d901d2f13f3521b87aa91b1fd6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804725"
---
# <a name="sample---allowed-region-locations"></a>Exempel – Tillåtna regionplatser

Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. Den används för att genomdriva kraven på geo-efterlevnad. Resursgrupper, Microsoft.AzureActiveDirectory/b2cDirectories och resurser som använder regionen ”global” undantas. Du anger en matris med tillåtna platser.

Du kan distribuera det här exemplet med hjälp av:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST-API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Exempel-princip

### <a name="policy-definition"></a>Definition av princip

Den fullständigt beskrivna JSON-principdefinitionen, som används av REST API, ”Deploy to Azure”-knapparna (Distribuera till Azure) samt manuellt i portalen.

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Om du skapar en princip i portalen manuellt använder du delarna **properties.parameters** och **properties.policyRule** i ovanstående. Omslut de två avsnitten med klammerparenteser `{}` så att det blir giltigt JSON.

### <a name="policy-rules"></a>Principregler

Det JSON som definierar principens regler, som används av Azure CLI och Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Principparametrar

Det JSON som definierar principparametrarna, som används av Azure CLI och Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametrar

|Namn |Type |Fält |Beskrivning |
|---|---|---|---|
|listOfAllowedLocations |Matris |platser |Listan över tillåtna platser|

När du skapar en tilldelning via PowerShell eller Azure CLI går det att skicka parametervärdena som JSON antingen i en sträng eller via en fil med hjälp av `-PolicyParameter` (PowerShell) eller `--params` (Azure CLI).
PowerShell stödjer även `-PolicyParameterObject`, vilket kräver att det till cmdlet skickas en Name/Value-hashtabell där **Name** (Namn) är parameternamnet och **Value** (Värde) är det enskilda värde eller den matris med värden som skickas under tilldelningen.

I den här exempelparametern tillåts endast platserna _USA, östra 2_ eller _USA, västra_.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Distribuera princip för exemplet till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![distribuera princip för exemplet till Azure Government](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Ta bort med Azure PowerShell

Kör följande kommandon för att ta bort föregående tilldelning och definition:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell-förklaring

Skripten för distribution och borttagning använder följande kommandon. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Skapar en ny Azure Policy-definition. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Hämtar en enskild resursgrupp. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Skapar en ny Azure Policy-tilldelning. I det här exemplet ges den en definition, men den kan också ta emot ett initiativ. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Tar bort en befintlig Azure Policy-tilldelning. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Tar bort en befintlig Azure Policy-definition. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

## <a name="rest-api"></a>REST-API

Det finns flera verktyg som kan användas för att interagera med Resource Manager REST API, till exempel [ARMClient](https://github.com/projectkudu/ARMClient) och PowerShell.

### <a name="deploy-with-rest-api"></a>Distribuera med REST API

- Skapa principdefinitionen (prenumerationsomfång). Använd [principdefinitionen](#policy-definition) för JSON för begärandetexten.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Skapa principtilldelningen (resursgruppsomfång)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  Använd följande JSON-exempel för begärandetexten:

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Ta bort med REST API

- Ta bort principtilldelningen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- Ta bort principdefinitionen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
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