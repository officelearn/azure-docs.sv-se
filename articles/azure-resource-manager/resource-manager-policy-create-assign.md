---
title: Tilldela och hantera Azure resursprinciper | Microsoft Docs
description: "Beskriver hur du använder Azure resursprinciper prenumerationer och resursgrupper och hur du visar resursprinciper."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>Tilldela och hantera principer för företagsresurser

För att implementera en princip, måste du utföra de här stegen:

1. Kontrollera principdefinitioner (inklusive inbyggda principer som tillhandahålls av Azure) att se om det redan finns i din prenumeration som uppfyller dina krav.
2. Hämta namnet om det finns ett.
3. Om det inte finns, definiera principregeln med JSON och Lägg till den som en principdefinition i din prenumeration. Det här steget gör principen som är tillgängliga för tilldelning, men gäller inte reglerna för din prenumeration.
4. Tilldela principen till ett scope (till exempel en grupp för prenumerationen eller resursen) för båda fallen. Regler för principen tillämpas nu.

Den här artikeln fokuserar på stegen för att skapa en principdefinition och tilldela den definitionen till ett scope via REST-API, PowerShell eller Azure CLI. Om du föredrar att använda portalen för att tilldela principer, se [använder Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md). Den här artikeln inte fokusera på syntaxen för att skapa principdefinitionen. Information om principen syntax finns [resurs uppgifter](resource-manager-policy.md).

## <a name="exclusion-scopes"></a>Undantag scope

Du kan utesluta ett scope när du tilldelar en princip. Den här möjligheten förenklar principtilldelningar eftersom du kan tilldela en princip på prenumerationsnivå men där principen tillämpas inte. Du kan till exempel ha en resursgrupp som är avsedd för nätverksinfrastrukturen i din prenumeration. Programteam distribuera sina resurser till andra resursgrupper. Vill du inte dessa grupper för att skapa nätverksresurser som kan leda till säkerhetsproblem. Men i Nätverksresursgruppen vill du tillåta nätverksresurser. Du tilldelar principen på prenumerationsnivå men undanta Nätverksresursgruppen. Du kan ange flera sub omfång.

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

Om du anger en omfattning som ska undantas i din tilldelning av **2017-06-01-preview** API-versionen.

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>Skapa en principdefinition för

Du kan skapa en princip med de [REST API för Principdefinitioner](/rest/api/resources/policydefinitions). REST-API kan du skapa och ta bort principdefinitioner och få information om befintliga definitioner.

Om du vill skapa en principdefinition, kör du:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Inkludera en begärantext som liknar följande exempel:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Tilldela principen

Du kan använda principdefinition i det önskade omfånget via den [REST API för principtilldelningar](/rest/api/resources/policyassignments). REST-API kan du skapa och ta bort principtilldelningar och få information om befintliga tilldelningar.

Om du vill skapa en principtilldelning, kör du:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{-Principtilldelningen} är namnet på tilldelning av principer.

Inkludera en begärantext som liknar följande exempel:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Visa policy
En princip får använda den [hämta principdefinitionen](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get) igen.

### <a name="get-aliases"></a>Hämta alias
Du kan hämta alias via REST API:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

I följande exempel visas en definition av ett alias. Som du ser definierar ett alias sökvägar i olika API-versioner, även om det finns en namnändring för egenskapen. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

Innan du fortsätter med PowerShell-exemplen, kontrollera att du har [installerat den senaste versionen](/powershell/azure/install-azurerm-ps) av Azure PowerShell. Principparametrar har lagts till i version 3.6.0. Om du har en tidigare version kan returnera ett felmeddelande om att det inte går att hitta parametern exemplen.

### <a name="view-policy-definitions"></a>Visa principdefinitioner
Om du vill se alla principdefinitioner i din prenumeration, använder du följande kommando:

```powershell
Get-AzureRmPolicyDefinition
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Titta på de inbyggda principerna innan du fortsätter att skapa en principdefinition. Om du hittar en inbyggd princip som gäller de gränser som du behöver kan du hoppa över att skapa en principdefinition. I stället tilldela inbyggda principen till det önskade omfånget.

### <a name="create-policy-definition"></a>Skapa en principdefinition för
Du kan skapa en princip definition med hjälp av den `New-AzureRmPolicyDefinition` cmdlet.

Om du vill skapa en principdefinition från en fil, skickar du sökvägen till filen. För en extern fil Använd:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

För en lokal fil, använder du:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Använd för att skapa en principdefinition med en infogad regel:

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

Utdata lagras i en `$definition` -objekt som ska användas vid tilldelning av principer. 

I följande exempel skapas en principdefinition som innehåller parametrar:

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Tilldela principen

Du tillämpa principen på det önskade omfånget med hjälp av den `New-AzureRmPolicyAssignment` cmdlet. I följande exempel tilldelar principen till en resursgrupp.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Om du vill tilldela en princip som kräver parametrar, skapa och objekt med dessa värden. I följande exempel hämtar en inbyggd princip och skickar in värden för parametrar:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Visa tilldelning av principer

För att få en specifik principtilldelning, använder du:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Om du vill visa principregel för en principdefinition, använder du:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Ta bort tilldelning av principer 

Ta bort en principtilldelning med:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Azure CLI

### <a name="view-policy-definitions"></a>Visa principdefinitioner
Om du vill se alla principdefinitioner i din prenumeration, använder du följande kommando:

```azurecli
az policy definition list
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Titta på de inbyggda principerna innan du fortsätter att skapa en principdefinition. Om du hittar en inbyggd princip som gäller de gränser som du behöver kan du hoppa över att skapa en principdefinition. I stället tilldela inbyggda principen till det önskade omfånget.

### <a name="create-policy-definition"></a>Skapa en principdefinition för

Du kan skapa en principdefinition som använder Azure CLI med kommandot princip definition.

Använd för att skapa en principdefinition med en infogad regel:

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Tilldela principen

Du kan tillämpa principen till det önskade omfånget med hjälp av kommandot princip tilldelningen. I följande exempel tilldelas en princip till en resursgrupp.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Visa tilldelning av principer

Om du vill visa en principtilldelning innehåller principtilldelningsnamnet och räckvidd:

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Ta bort tilldelning av principer 

Ta bort en principtilldelning med:

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Nästa steg
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

