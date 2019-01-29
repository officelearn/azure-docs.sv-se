---
title: Exempel – Godkända VM-avbildningar
description: Den här exempelprincipen kräver att endast godkända anpassade avbildningar distribueras i din miljö.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 9fe9e0021d4c2c0ebccf8f0fdb123e2fa413a1aa
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856612"
---
# <a name="approved-vm-images"></a>Godkända VM-avbildningar

Den här principen kräver att endast godkända anpassade avbildningar distribueras i din miljö. Du anger en matris med godkända avbildnings-ID.

Du kan distribuera det här exemplet med hjälp av:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST-API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Exempel-princip

### <a name="policy-definition"></a>Definition av princip

Den fullständigt beskrivna JSON-principdefinitionen, som används av REST API, ”Deploy to Azure”-knapparna (Distribuera till Azure) samt manuellt i portalen.

[!code-json[full](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> Om du skapar en princip i portalen manuellt använder du delarna **properties.parameters** och **properties.policyRule** i ovanstående. Omslut de två avsnitten med klammerparenteser `{}` så att det blir giltigt JSON.

### <a name="policy-rules"></a>Principregler

Det JSON som definierar principens regler, som används av Azure CLI och Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Principparametrar

Det JSON som definierar principparametrarna, som används av Azure CLI och Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametrar

|Namn |Typ |Fält |Beskrivning |
|---|---|---|---|
|imageIds |Matris |Microsoft.Compute/imageIds |Listan över godkända avbildningar av virtuella datorer|

När du skapar en tilldelning via PowerShell eller Azure CLI går det att skicka parametervärdena som JSON antingen i en sträng eller via en fil med hjälp av `-PolicyParameter` (PowerShell) eller `--params` (Azure CLI).
PowerShell stödjer även `-PolicyParameterObject`, vilket kräver att det till cmdlet skickas en Name/Value-hashtabell där **Name** (Namn) är parameternamnet och **Value** (Värde) är det enskilda värde eller den matris med värden som skickas under tilldelningen.

I den här exempelparametern tillåts endast _ContosoStdImage_ i resursgruppen _YourResourceGroup_ eller avbildningsversionen för Windows Server 2016 Datacenter från maj 2018 som finns i ”USA, centrala”.

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Distribuera till Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![Distribuera till Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Det finns flera verktyg som kan användas för att interagera med Resource Manager REST API, till exempel [ARMClient](https://github.com/projectkudu/ARMClient) och PowerShell. Ett exempel på anrop till REST API från PowerShell finns i avsnittet **Aliases** (Alias) i [Policy definition structure](../concepts/definition-structure.md#aliases) (Principdefinitionsstrukturen).

### <a name="deploy-with-rest-api"></a>Distribuera med REST API

- Skapa principdefinitionen (prenumerationsomfång). Använd [principdefinitionen](#policy-definition) för JSON för begärandetexten.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Skapa principtilldelningen (resursgruppsomfång)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  Använd följande JSON-exempel för begärandetexten:

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Ta bort med REST API

- Ta bort principtilldelningen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- Ta bort principdefinitionen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
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
- Azure Policy-exempel för virtuella datorer finns på [Tillämpa principer för virtuella Windows-datorer](../../../virtual-machines/windows/policy.md)