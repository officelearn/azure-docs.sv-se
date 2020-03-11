---
title: Distribuera resurser till klient organisationen
description: Beskriver hur du distribuerar resurser i klient omfånget i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 64090f1a0bac4b2b5f18d8dec14be0c3b051ac17
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968880"
---
# <a name="create-resources-at-the-tenant-level"></a>Skapa resurser på klient nivå

När din organisation är vuxen kan du behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [rollbaserade åtkomst kontroller](../../role-based-access-control/overview.md) i Azure AD-klienten. Med mallar för klient organisations nivå kan du tillämpa principer och tilldela roller på global nivå.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurs typer på klient nivå:

* [distributioner](/azure/templates/microsoft.resources/deployments) – för kapslade mallar som distribueras till hanterings grupper eller prenumerationer.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Schemat som används för klient distributioner skiljer sig från schemat för resurs grupps distributioner.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Schemat för en parameter fil är detsamma för alla distributions omfång. För parameter-filer använder du:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Nödvändig åtkomst

Huvud kontot som distribuerar mallen måste ha behörighet att skapa resurser i klient omfånget. Huvud kontot måste ha behörighet att köra distributions åtgärder (`Microsoft.Resources/deployments/*`) och för att skapa de resurser som definierats i mallen. Om du till exempel vill skapa en hanterings grupp måste huvudobjektet ha deltagar behörighet för klient omfånget. Huvud kontot måste ha ägar behörighet för att skapa roll tilldelningar.

Den globala administratören för Azure Active Directory har inte automatiskt behörighet att tilldela roller. Om du vill aktivera mall distributioner i klient omfånget måste den globala administratören utföra följande steg:

1. Öka konto åtkomsten så att den globala administratören kan tilldela roller. Mer information finns i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md).

1. Tilldela ägare eller deltagare till det huvud konto som måste distribuera mallarna.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Huvud kontot har nu de behörigheter som krävs för att distribuera mallen.

## <a name="deployment-commands"></a>Distributions kommandon

Kommandona för klient distributioner skiljer sig från kommandona för resurs grupp distributioner.

För Azure PowerShell använder du [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

För REST API använder du [distributioner – skapa eller uppdatera i klient omfånget](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på klient nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall med namnet **azuredeploy. JSON** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du får felkoden `InvalidDeploymentLocation`använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mall funktioner

För klient distributioner finns det några viktiga saker att tänka på när du använder mall-funktioner:

* Funktionen [resourceGroup ()](template-functions-resource.md#resourcegroup) stöds **inte** .
* Funktionen [Subscription ()](template-functions-resource.md#subscription) stöds **inte** .
* Funktionerna [Reference ()](template-functions-resource.md#reference) och [List ()](template-functions-resource.md#list) stöds.
* Använd funktionen [tenantResourceId ()](template-functions-resource.md#tenantresourceid) för att hämta resurs-ID för resurser som distribueras på klient nivå.

  Om du till exempel vill hämta resurs-ID för en princip definition använder du:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Det returnerade resurs-ID: t har följande format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Skapa en hanteringsgrupp

[Följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) skapar en hanterings grupp.

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

[Följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) tilldelar en roll i klient omfånget.

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

* Information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Du kan också distribuera mallar på [prenumerations nivå](deploy-to-subscription.md) eller på [hanterings grupps nivå](deploy-to-management-group.md).
