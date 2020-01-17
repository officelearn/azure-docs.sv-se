---
title: Distribuera resurser till hanterings grupp
description: Beskriver hur du distribuerar resurser i hanterings gruppens omfattning i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4ba4f4d2e95c0b878e9f402fa84139ac5b351e3c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121921"
---
# <a name="create-resources-at-the-management-group-level"></a>Skapa resurser på hanterings grupps nivå

Normalt distribuerar du Azure-resurser till en resurs grupp i din Azure-prenumeration. Men du kan också skapa resurser på hanterings grupps nivå. Du använder distributioner på hanterings grupps nivå för att vidta åtgärder som är begripliga på den nivån, till exempel tilldela [rollbaserad åtkomst kontroll](../../role-based-access-control/overview.md) eller tillämpa [principer](../../governance/policy/overview.md).

För närvarande måste du använda REST API för att kunna distribuera mallar på hanterings grupps nivå.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurs typer på hanterings grupps nivån:

* [distributioner](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Schemat som används för distributioner av hanterings grupper skiljer sig från schemat för distributioner av resurs grupper.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

För parameter-filer använder du:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Distributions kommandon

Kommandot för distributioner av hanterings grupper skiljer sig från kommandot för distribution av resurs grupper.

För REST API använder du [distributioner – skapa i hanterings gruppens omfång](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på hanterings grupp nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall med namnet **azuredeploy. JSON** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du får felkoden `InvalidDeploymentLocation`använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mall funktioner

För distributioner av hanterings grupper finns det några viktiga saker att tänka på när du använder mall-funktioner:

* Funktionen [resourceGroup ()](template-functions-resource.md#resourcegroup) stöds **inte** .
* Funktionen [Subscription ()](template-functions-resource.md#subscription) stöds **inte** .
* Funktionen [resourceId ()](template-functions-resource.md#resourceid) stöds. Använd den för att hämta resurs-ID för resurser som används vid distributioner på hanterings grupps nivå. Du kan till exempel hämta resurs-ID för en princip definition med `resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))`. Den returnerar resurs-ID i formatet `/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`.
* Funktionerna [Reference ()](template-functions-resource.md#reference) och [List ()](template-functions-resource.md#list) stöds.

## <a name="create-policies"></a>Skapa principer

### <a name="define-policy"></a>Definiera princip

I följande exempel visas hur du [definierar](../../governance/policy/concepts/definition-structure.md) en princip på hanterings grupps nivån.

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

I följande exempel tilldelas en befintlig princip definition till hanterings gruppen. Om principen använder parametrar, anger du dem som ett objekt. Om principen inte tar parametrar använder du det tomma standard objektet.

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

## <a name="next-steps"></a>Nästa steg

* Information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på distribution av arbets ytans inställningar för Azure Security Center finns i [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Mer information om hur du skapar Azure Resource Manager-mallar finns i [Redigera mallar](template-syntax.md). 
* En lista över tillgängliga funktioner i en mall finns i [Template Functions](template-functions.md).