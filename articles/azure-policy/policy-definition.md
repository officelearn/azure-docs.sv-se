---
title: Azure Policy-definitionsstruktur
description: Beskriver hur resurs principdefinitionen används av Azure principen för att etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken effekt att vidta.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: f864cf45b255ac26ccf0efac9a89683d1ae650b3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601266"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resursdefinitionen princip som används av principen i Azure kan du etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken effekt ska börja. Du kan styra kostnader genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att endast vissa typer av virtuella datorer är tillåtna. Eller, du kan kräva att alla resurser som har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip används för en resursgrupp, är det så gäller för alla resurser i resursgruppen.

Schemat som används av Azure-principen finns här: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

Du kan använda JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

- läge
- parameters
- Visningsnamn
- description
- Principregel
  - logiska utvärdering
  - effekt

Till exempel visar följande JSON en princip som begränsar där resurser har distribuerats:

```json
{
    "properties": {
        "mode": "all",
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

Alla Azure princip prover finns på [princip exempel](json-samples.md).

## <a name="mode"></a>Läge

Den **läge** bestämmer vilka typer av resurser som ska utvärderas för en princip. Läget stöds är:

- `all`: utvärderar resursgrupper och alla typer av resurser
- `indexed`: endast utvärdera resurstyper som har stöd för etiketter och plats

Vi rekommenderar att du ställer in **läge** till `all` i de flesta fall. Alla principdefinitioner som skapats via portalen användningen av `all` läge. Om du använder PowerShell eller Azure CLI, kan du ange den **läge** parametern manuellt. Om principdefinitionen inte innehåller en **läge** värdet den som standard `all` i Azure PowerShell och till `null` i Azure CLI, vilket motsvarar `indexed`, för bakåtkompatibilitet kompatibilitet.

`indexed` ska användas när du skapar principer som tillämpar taggar eller platser. Detta är inte obligatoriskt, men de resurser som inte stöder taggar och platser ska visas som icke-kompatibla i kompatibilitetsresultaten. Det enda undantaget är **resursgrupper**. Principer som försöker använda plats eller taggarna i en resursgrupp ska ange **läge** till `all` och specifikt mål för den `Microsoft.Resources/subscriptions/resourceGroup` typen. Ett exempel finns [genomdriva grupp resurstaggar](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Parametrar

Parametrarna underlätta hantering av din genom att minska antalet principdefinitioner. Se parametrar som fält i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar alltid samma, men deras värden ändras baserat på enskilda fyller i formuläret. Parametrarna fungerar på samma sätt som när du skapar principer. Du kan återanvända principen för olika scenarier med hjälp av olika värden genom att lägga till parametrar i en principdefinition.

Du kan till exempel definiera en princip för en resursegenskap att begränsa de platser där resurser kan distribueras. I det här fallet, skulle du deklarera följande parametrar när du skapar din princip:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Typ av en parameter kan vara antingen sträng eller matris. Metadataegenskapen används för verktyg som Azure-portalen för att visa användarvänliga information.

Du kan använda i metadataegenskap **strongType** att tillhandahålla en flerval lista över alternativen i Azure-portalen.  Tillåtna värden för **strongType** nu:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

I principregeln referera parametrar med följande syntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Definitionens plats

När du skapar ett initiativ eller princip definition, är det viktigt att du anger platsen för definition.

Definition av platsen avgör omfattningen som initiativ eller princip definitionen kan tilldelas till. Platsen kan anges som en hanteringsgrupp eller en prenumeration.

> [!NOTE]
> Om du planerar att använda denna principdefinition på flera prenumerationer måste platsen vara en hanteringsgrupp som innehåller prenumerationer som du vill tilldela initiativ eller principen till.

## <a name="display-name-and-description"></a>Namn och beskrivning

Du kan använda **displayName** och **beskrivning** identifiera principdefinitionen och ge kontext för när den används.

## <a name="policy-rule"></a>Principregel

Principregeln består av **om** och **sedan** block. I den **om** block du definierar en eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att ange exakt scenario för en princip.

I den **sedan** block du definiera vad som händer när de **om** villkor är uppfyllda.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Logiska operatorer

Logiska operatorer som stöds är:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Den **inte** syntax inverterar resultatet av villkoret. Den **allOf** syntax (liknar den logiska **och** åtgärden) kräver att alla villkor vara uppfyllda. Den **anyOf** syntax (liknar den logiska **eller** åtgärden) kräver en eller flera villkor vara uppfyllda.

Du kan kapsla logiska operatorer. Följande exempel visar en **inte** åtgärden som är kapslad i en **allOf** igen.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Villkor

Utvärderar ett villkor om en **fältet** uppfyller vissa villkor. Villkor som stöds är:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

När du använder den **som** och **notLike** villkor, kan du ange ett jokertecken (*) i värdet.

När du använder den **matchar** och **notMatch** villkor, ger `#` som representerar en siffra `?` för en bokstav och alla andra tecken som representerar det faktiska tecknet. Exempel finns i [tillåter flera namn mönster](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fält

Villkor bildas genom att använda fält. Ett fält representerar egenskaper i nyttolasten för begäran resurs som används för att beskriva tillståndet för resursen.  

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar det fullständiga namnet på resursen. Det fullständiga namnet på en resurs är resursnamnet inledd av alla överordnade resursnamn (till exempel ”minserver/mindatabas”).
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - Den här syntaxen för hakparentes stöder taggnamn som innehåller punkter
- Egenskapen alias - lista, se [alias](#aliases).

### <a name="alternative-accessors"></a>Alternativa accessorer

**Fältet** är primär accessorn används i regler. Den kontrollerar direkt till resursen som utvärderas. Dock stöder en andra accessor **källa**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Källan** stöder bara ett värde, **åtgärd**. Åtgärd returnerar tillstånd-åtgärd för den begäran som utvärderas. Auktorisering åtgärder visas i avsnittet auktorisering i den [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

När principen utvärderas befintliga resurser i bakgrunden, anger **åtgärd** till en `/write` tillstånd-åtgärd för den resurstypen.

### <a name="effect"></a>Verkan

Stöder följande typer av gälla:

- **Neka**: genererar en händelse i händelseloggen och misslyckas begäran
- **Granska**: genererar en varning-händelse i granskningsloggen men misslyckas inte begäran
- **Lägg till**: lägger till en definierad uppsättning fält i begäran
- **AuditIfNotExists**: aktiverar granskning om en resurs inte finns
- **DeployIfNotExists**: distribuerar en resurs om den inte redan finns. Detta stöds för närvarande endast via inbyggda principer.

För **bifoga**, måste du ange följande information:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Värdet kan vara en sträng eller ett JSON-format-objekt.

Med **AuditIfNotExists** och **DeployIfNotExists** du kan utvärdera förekomsten av en relaterad resurs och tillämpa en regel och en motsvarande effekt när resursen inte finns. Du kan till exempel kräva att en nätverksbevakaren distribueras för alla virtuella nätverk.
Ett exempel på granskning när ett tillägg för virtuell dator inte har distribuerats, se [granska om tillägg inte finns](scripts/audit-ext-not-exist.md).

Information om varje effekt ordningen för utvärdering, egenskaper och exempel finns [Förstå princip effekter](policy-effects.md).

## <a name="aliases"></a>Alias

Du kan använda egenskapen alias för att få åtkomst till specifika egenskaper för en resurstyp. Alias kan du begränsa vilka värden eller villkor tillåts för en egenskap för en resurs. Varje alias mappar till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärdering av hämtar principmodulen egenskapssökvägen för den API-versionen.

Lista över alla alias växer alltid. Använd någon av följande metoder för att identifiera vilka alias som stöds av Azure-principen:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>Initiativ

Initiativ aktivera du gruppera flera relaterade principdefinitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enskilt objekt. Du kan till exempel gruppera alla relaterade märkning principdefinitioner i ett enda initiativ. I stället för att tilldela varje princip individuellt kan använda du initiativ.

Följande exempel illustrerar hur du skapar ett initiativ för att hantera två taggar: `costCenter` och `productName`. Två inbyggda principer används för att tillämpa taggen standardvärdet.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Nästa steg

- Granska fler exempel på [Azure princip exempel](json-samples.md).