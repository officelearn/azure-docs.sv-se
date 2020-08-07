---
title: Distribuera resurser till klient organisationen
description: Beskriver hur du distribuerar resurser i klient omfånget i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 3fbf869e371f385a335e4a154bc811a6fc8cab97
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922531"
---
# <a name="create-resources-at-the-tenant-level"></a>Skapa resurser på klient nivå

När din organisation är vuxen kan du behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [rollbaserade åtkomst kontroller](../../role-based-access-control/overview.md) i Azure AD-klienten. Med mallar för klient organisations nivå kan du tillämpa principer och tilldela roller på global nivå.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurs typer kan inte distribueras till klient nivån. I det här avsnittet listas vilka resurs typer som stöds.

Använd följande för Azure-principer:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Använd följande för rollbaserad åtkomst kontroll:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

För kapslade mallar som distribuerar till hanterings grupper, prenumerationer eller resurs grupper, använder du:

* [distributioner](/azure/templates/microsoft.resources/deployments)

Använd följande för att skapa hanterings grupper:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

För att hantera kostnader använder du:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [problemlösning](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

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

Huvud kontot som distribuerar mallen måste ha behörighet att skapa resurser i klient omfånget. Huvud kontot måste ha behörighet att köra distributions åtgärder ( `Microsoft.Resources/deployments/*` ) och för att skapa de resurser som definierats i mallen. Om du till exempel vill skapa en hanterings grupp måste huvudobjektet ha deltagar behörighet för klient omfånget. Huvud kontot måste ha ägar behörighet för att skapa roll tilldelningar.

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

För Azure CLI använder du [AZ Deployment Tenant Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

För Azure PowerShell använder du [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

För REST API använder du [distributioner – skapa eller uppdatera i klient omfånget](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på klient nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall som heter **azuredeploy.jspå** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du får fel koden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en klient kan du rikta in klient-eller hanterings grupper, prenumerationer och resurs grupper i klient organisationen. Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

De resurser som definieras i avsnittet resurser i mallen tillämpas på klienten.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        tenant-level-resources
    ],
    "outputs": {}
}
```

Om du vill rikta en hanterings grupp inom klienten lägger du till en kapslad distribution och anger `scope` egenskapen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedMG",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

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

[Följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) skapar en hanterings grupp.

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

## <a name="assign-role"></a>Tilldela rollen

[Följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) tilldelar en roll i klient omfånget.

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
