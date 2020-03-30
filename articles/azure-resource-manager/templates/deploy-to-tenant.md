---
title: Distribuera resurser till klient
description: Beskriver hur du distribuerar resurser i klientomfattningen i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460270"
---
# <a name="create-resources-at-the-tenant-level"></a>Skapa resurser på klientnivå

När din organisation mognar kan du behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [rollbaserade åtkomstkontroller](../../role-based-access-control/overview.md) i din Azure AD-klientorganisation. Med mallar på klientnivå kan du deklarativt tillämpa principer och tilldela roller på global nivå.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurstyper på klientnivå:

* [distributioner](/azure/templates/microsoft.resources/deployments) – för kapslade mallar som distribueras till hanteringsgrupper eller prenumerationer.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitioner](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [rollTilldelningar](/azure/templates/microsoft.authorization/roleassignments)
* [rollDefinitioner](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Schemat som du använder för klientdistributioner skiljer sig från schemat för resursgruppsdistributioner.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Schemat för en parameterfil är detsamma för alla distributionsomfattningar. Använd för parameterfiler:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Nödvändig åtkomst

Huvudut distribuera mallen måste ha behörighet att skapa resurser i klientomfånget. Huvudansvarig måste ha behörighet att`Microsoft.Resources/deployments/*`köra distributionsåtgärderna ( ) och för att skapa de resurser som definierats i mallen. Om du till exempel vill skapa en hanteringsgrupp måste huvudmannen ha behörighet till deltagare i klientomfånget. Om du vill skapa rolltilldelningar måste huvudmannen ha ägarbehörighet.

Den globala administratören för Azure Active Directory har inte automatiskt behörighet att tilldela roller. Om du vill aktivera malldistributioner i klientomfattningen måste den globala administratören göra följande:

1. Öka kontoåtkomsten så att den globala administratören kan tilldela roller. Mer information finns i [Öka åtkomsten för att hantera alla Azure-prenumerationer och hanteringsgrupper](../../role-based-access-control/elevate-access-global-admin.md).

1. Tilldela ägare eller deltagare till huvudmannen som behöver distribuera mallarna.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Huvudansvarig har nu de behörigheter som krävs för att distribuera mallen.

## <a name="deployment-commands"></a>Distributionskommandon

Kommandona för klientdistributioner skiljer sig från kommandona för resursgruppsdistributioner.

För Azure CLI använder du [az deployment-klienten skapa:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Använd [Ny-AzTenantDeployment för](/powershell/module/az.resources/new-aztenantdeployment)Azure PowerShell .

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

För REST API använder du [distributioner - Skapa eller uppdatera i klientomfattning](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Distributionsplats och namn

För distribution på klientnivå måste du ange en plats för distributionen. Platsen för distributionen är skild från platsen för de resurser som du distribuerar. Distributionsplatsen anger var distributionsdata ska lagras.

Du kan ange ett namn för distributionen eller använda standarddistributionsnamnet. Standardnamnet är namnet på mallfilen. Om du till exempel distribuerar en mall med namnet **azuredeploy.json** skapas ett standarddistributionsnamn för **azuredeploy**.

För varje distributionsnamn är platsen oföränderlig. Du kan inte skapa en distribution på en plats när det finns en befintlig distribution med samma namn på en annan plats. Om du får `InvalidDeploymentLocation`felkoden använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mallfunktioner

För klientdistributioner finns det några viktiga överväganden när du använder mallfunktioner:

* Funktionen [resourceGroup()](template-functions-resource.md#resourcegroup) stöds **inte.**
* Funktionen [subscription()](template-functions-resource.md#subscription) stöds **inte.**
* Funktionerna [reference()](template-functions-resource.md#reference) och [list()](template-functions-resource.md#list) stöds.
* Använd funktionen [tenantResourceId()](template-functions-resource.md#tenantresourceid) för att hämta resurs-ID för resurser som distribueras på klientnivå.

  Om du till exempel vill hämta resurs-ID:t för en principdefinition använder du:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Det returnerade resurs-ID:et har följande format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Skapa en hanteringsgrupp

I [följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) skapas en hanteringsgrupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Tilldela roll

[Följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) tilldelar en roll i klientomfånget.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med hjälp av RBAC- och Azure Resource Manager-mallar](../../role-based-access-control/role-assignments-template.md).
* Du kan också distribuera mallar på [prenumerationsnivå](deploy-to-subscription.md) eller [hanteringsgruppsnivå](deploy-to-management-group.md).
