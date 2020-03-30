---
title: Distribuera resurser till hanteringsgrupp
description: Beskriver hur du distribuerar resurser i hanteringsgruppens omfattning i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460321"
---
# <a name="create-resources-at-the-management-group-level"></a>Skapa resurser på hanteringsgruppsnivå

När din organisation mognar kan du behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [rollbaserade åtkomstkontroller](../../role-based-access-control/overview.md) för en hanteringsgrupp. Med mallar på hanteringsgruppsnivå kan du deklarativt tillämpa principer och tilldela roller på hanteringsgruppsnivå.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurstyper på hanteringsgruppsnivå:

* [distributioner](/azure/templates/microsoft.resources/deployments) – för kapslade mallar som distribueras till prenumerationer eller resursgrupper.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitioner](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [rollTilldelningar](/azure/templates/microsoft.authorization/roleassignments)
* [rollDefinitioner](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Schemat som du använder för hanteringsgruppsdistributioner skiljer sig från schemat för resursgruppsdistributioner.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Schemat för en parameterfil är detsamma för alla distributionsomfattningar. Använd för parameterfiler:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Distributionskommandon

Kommandona för hanteringsgruppdistributioner skiljer sig från kommandona för resursgruppsdistributioner.

För Azure CLI använder du [az deployment mg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Använd [New-AzManagementGroupDeployment för](/powershell/module/az.resources/new-azmanagementgroupdeployment)Azure PowerShell .

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

För REST API använder du [Distributioner - Skapa i hanteringsgruppscope](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Distributionsplats och namn

För distributioner på hanteringsgruppsnivå måste du ange en plats för distributionen. Platsen för distributionen är skild från platsen för de resurser som du distribuerar. Distributionsplatsen anger var distributionsdata ska lagras.

Du kan ange ett namn för distributionen eller använda standarddistributionsnamnet. Standardnamnet är namnet på mallfilen. Om du till exempel distribuerar en mall med namnet **azuredeploy.json** skapas ett standarddistributionsnamn för **azuredeploy**.

För varje distributionsnamn är platsen oföränderlig. Du kan inte skapa en distribution på en plats när det finns en befintlig distribution med samma namn på en annan plats. Om du får `InvalidDeploymentLocation`felkoden använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mallfunktioner

För distributioner av hanteringsgrupper finns det några viktiga överväganden när du använder mallfunktioner:

* Funktionen [resourceGroup()](template-functions-resource.md#resourcegroup) stöds **inte.**
* Funktionen [subscription()](template-functions-resource.md#subscription) stöds **inte.**
* Funktionerna [reference()](template-functions-resource.md#reference) och [list()](template-functions-resource.md#list) stöds.
* Funktionen [resourceId()](template-functions-resource.md#resourceid) stöds. Använd den för att hämta resurs-ID för resurser som används på hanteringsgruppsnivådistributioner. Ange inget värde för parametern resursgrupp.

  Om du till exempel vill hämta resurs-ID:t för en principdefinition använder du:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Det returnerade resurs-ID:et har följande format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Skapa principer

### <a name="define-policy"></a>Definiera princip

I följande exempel visas hur [du definierar](../../governance/policy/concepts/definition-structure.md) en princip på hanteringsgruppsnivå.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>Tilldela princip

I följande exempel tilldelas en befintlig principdefinition till hanteringsgruppen. Om principen tar parametrar anger du dem som ett objekt. Om principen inte tar parametrar använder du standardobjektet för tomma objekt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Exempel på mall

* [Skapa en resursgrupp, en princip och en principtilldelning](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med hjälp av RBAC- och Azure Resource Manager-mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på distribution av arbetsyteinställningar för Azure Security Center finns i [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du kan också distribuera mallar på [prenumerationsnivå](deploy-to-subscription.md) och [klientnivå](deploy-to-tenant.md).
