---
title: Azure Policy-definitionsstruktur
description: Beskriver hur resource principdefinitionen används av Azure Policy för att etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken effekt ska börja.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 0ff56b86243956d1fa6b51a6dfd14af9e00d8367
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212785"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resursdefinitionen princip som används av Azure Policy kan du etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken effekt ska börja. Du kan styra kostnaderna genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att bara vissa typer av virtuella datorer är tillåtna. Eller så kan du kräva att alla resurser har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip tillämpas på en resursgrupp, är det därför gäller för alla resurser i resursgruppen.

Ett schema som används av Azure Policy finns här: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Du kan använda JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

- läge
- parameters
- Visningsnamn
- beskrivning
- principregel
  - logiska utvärdering
  - effekt

Följande JSON visar exempelvis en princip som begränsar där resurser har distribuerats:

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

Alla exempel för Azure Policy finns på [princip exempel](../samples/index.md).

## <a name="mode"></a>Läge

Den **läge** avgör vilka typer av resurser kommer att utvärderas för en princip. De stödda lägena är:

- `all`: utvärdera resursgrupper och alla resurstyper
- `indexed`: endast utvärdera resurstyper som stöder taggar och plats

Vi rekommenderar att du ställer in **läge** till `all` i de flesta fall. Alla principdefinitioner som skapats via portalen användning i `all` läge. Om du använder PowerShell eller Azure CLI kan du ange den **läge** parametern manuellt. Om principdefinitionen inte innehåller en **läge** värdet det standardvärde `all` i Azure PowerShell och till `null` i Azure CLI, vilket motsvarar `indexed`, för bakåtkompatibilitet kompatibilitet.

`indexed` ska användas när du skapar principer som kommer att framtvinga taggar eller platser. Det är inget krav, men de resurser som inte stöder taggar och platser från dyker upp som icke-kompatibla i kompatibilitetsresultaten. Det enda undantaget är **resursgrupper**. Principer som försöker tillämpa plats eller taggar på en resursgrupp bör ange **läge** till `all` och specifikt mål den `Microsoft.Resources/subscriptions/resourceGroup` typen. Ett exempel finns i [framtvinga grupp resurstaggar](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Parametrar

Parametrar underlätta hanteringen av principer genom att minska antalet principdefinitioner. Tänk på parametrar som fält i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar desamma alltid, men deras värden ändras baserat på enskilda fyller i formuläret.
Parametrar fungerar på samma sätt som när du skapar principer. Du kan återanvända principen för olika scenarier med hjälp av olika värden genom att lägga till parametrar i en principdefinition.

> [!NOTE]
> Parametrarna-definition för en princip eller initiativdefinition kan endast konfigureras under inledande genereringen av principen eller initiativ. Definitionen för parametrar kan inte ändras senare.
> Detta förhindrar att befintliga tilldelningar för principen eller initiativ indirekt görs ogiltig.

Exempelvis kan definiera du en princip för en resursegenskap att begränsa de platser där resurser kan distribueras. I det här fallet skulle du deklarera följande parametrar när du skapar en princip:

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

Typen för en parameter kan vara antingen sträng eller matris. Metadata-egenskapen används för verktyg som Azure-portalen för att visa användarvänliga information.

Du kan använda inom metadata-egenskap **strongType** att tillhandahålla en flervalslista med alternativ i Azure-portalen. Tillåtna värden för **strongType** nu:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

I principregeln du referera till parametrar med följande `parameters` distribution värdet funktionens syntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Definitionens plats

När du skapar en definition av initiativ- eller är det viktigt att du anger definitionens plats.

Definitionens plats anger omfattningen som initiativ- eller definitionen kan tilldelas till. Platsen kan anges som en hanteringsgrupp eller en prenumeration.

> [!NOTE]
> Om du planerar att använda den här principdefinitionen till flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller de prenumerationer som du vill tilldela initiativ- eller principen till.

## <a name="display-name-and-description"></a>Namn och beskrivning

Du kan använda **displayName** och **beskrivning** identifiera principdefinitionen och ge ett sammanhang för när den används.

## <a name="policy-rule"></a>Principregel

Principregeln består av **om** och **sedan** block. I den **om** block, definierar du en eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att ange exakt scenariot för en princip.

I den **sedan** block, definierar du den effekt som händer när den **om** villkor är uppfyllda.

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

Du kan kapsla logiska operatorer. I följande exempel visas en **inte** åtgärd som är kapslade i en **allOf** igen.

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

Ett villkor utvärderas om en **fältet** uppfyller vissa villkor. Villkor som stöds är:

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

När du använder den **som** och **notLike** villkor, kan du ange ett jokertecken `*` i värdet.
Värdet får inte innehålla fler än ett jokertecken `*`.

När du använder den **matchar** och **notMatch** villkor, ger `#` som representerar en siffra, `?` för en bokstav, `.` så att den matchar alla tecken och alla andra tecken till Ange det faktiska tecknet. Exempel finns i [Tillåt flera namn mönster](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fält

Villkor bildas genom att använda fält. Ett fält representerar egenskaper i nyttolasten för begäran resurs som används för att beskriva tillståndet för resursen.

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar det fullständiga namnet på resursen. Det fullständiga namnet på en resurs är resursnamnet föregås av ett anpassningsprefix av alla överordnade resursnamn (till exempel ”minserver/myDatabase”).
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Där **\<tagName\>** är namnet på taggen för att verifiera villkoret för.
  - Exempel: `tags.CostCenter` där **CostCenter** är namnet på taggen.
- `tags[<tagName>]`
  - Den här syntaxen hakparentes stöder taggnamn som innehåller punkter.
  - Där **\<tagName\>** är namnet på taggen för att verifiera villkoret för.
  - Exempel: `tags[Acct.CostCenter]` där **Acct.CostCenter** är namnet på taggen.
- Egenskapen alias – en lista i [alias](#aliases).

### <a name="effect"></a>Verkan

Principen har stöd för följande typer av effekt:

- **Neka**: Generera en händelse i aktivitetsloggen och misslyckas begäran
- **Granska**: genererar en varning-händelse i aktivitetsloggen men misslyckas inte begäran
- **Lägg till**: lägger till en definierad uppsättning fält på begäran
- **AuditIfNotExists**: aktiverar granskning om en resurs inte finns
- **DeployIfNotExists**: distribuerar en resurs om det inte redan finns.

För **lägga till**, måste du ange följande information:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Värdet kan vara en sträng eller ett JSON-format-objekt.

Med **AuditIfNotExists** och **DeployIfNotExists** du kan utvärdera förekomsten av en relaterad resurs och tillämpa en regel och en motsvarande effekt när den här resursen inte finns. Du kan till exempel kräva att en nätverksbevakare distribueras för alla virtuella nätverk. Ett exempel på granskning när tillägg för virtuell dator inte distribueras kan se [granska om tillägg inte finns](../samples/audit-ext-not-exist.md).

Mer information om varje effekt ordningen för utvärdering, egenskaper och exempel finns i [Förstå princip effekterna](effects.md).

### <a name="policy-functions"></a>Princip fungerar

En delmängd av [Resource Manager-Mallfunktioner](../../../azure-resource-manager/resource-group-template-functions.md) är tillgängliga för användning i en regel. De funktioner som stöds för närvarande är:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [ResourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [prenumeration](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Dessutom kan den `field` funktionen är tillgänglig för hanteringsprincipregler (MPR). Den här funktionen är främst avsedd för användning med **AuditIfNotExists** och **DeployIfNotExists** till referensfälten som för resursen som utvärderas. Ett exempel på detta visas på den [DeployIfNotExists exempel](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Exempel på funktion

Den här principen regelexempel använder den `resourceGroup` resurs-funktionen för att hämta den **namn** egenskapen tillsammans med den `concat` matris och objekt-funktionen för att skapa en `like` villkor som tillämpar resursnamnet att starta med resursgruppens namn.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

Den här principen regelexempel använder den `resourceGroup` resurs-funktionen för att hämta den **taggar** matris-värdet för den **kostnadsställe** taggen på resursgruppen och lägger till dem till den **kostnadsställe**  taggen på den nya resursen.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Alias

Du kan använda egenskapen alias för att få åtkomst till specifika egenskaper för en resurstyp. Alias kan du begränsa vilka värden eller villkor tillåts för en egenskap för en resurs. Varje alias som mappas till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärdering hämtar principmodulen egenskapssökvägen för den API-versionen.

Lista över alla alias växer. Använd någon av följande metoder för att identifiera vilka alias är för närvarande stöds av Azure Policy:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  # Use Get-AzureRmPolicyAlias to list available providers
  Get-AzureRmPolicyAlias -ListAvailable

  # Use Get-AzureRmPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzureRmPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST-API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Förstå alias [*]

Flera av de alias som är tillgängliga har en version som visas som ett ”normal” namn och en annan som har **[\*]** kopplade till den. Exempel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Det första exemplet används för att utvärdera hela matrisen där den **[\*]** alias utvärderar varje element i matrisen.

Nu ska vi titta på en regel som ett exempel. Den här principen ska **neka** ett lagringskonto som har ipRules som konfigurerats och om **ingen** av ipRules har värdet ”127.0.0.1”.

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

Den **ipRules** matrisen är på följande sätt för det här exemplet:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Här är hur det här exemplet har bearbetats:

- `networkAcls.ipRules` -Kontrollera att matrisen är icke-null. Utvärderas SANT, så utvärdering fortsätter.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` -Kontrollerar _värdet_ -egenskapen i den **ipRules** matris.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Varje element kommer att behandlas som en matris.

    - ”127.0.0.1”! = ”127.0.0.1” utvärderas som false.
    - ”127.0.0.1”! = ”192.168.1.1” utvärderas som true.
    - Minst en _värdet_ -egenskapen i den **ipRules** matris som utvärderas som false, så utvärdering slutar.

Som ett villkor som utvärderas till false, den **neka** effekt utlöses inte.

## <a name="initiatives"></a>Initiativ

Initiativ kan du gruppera flera relaterade principdefinitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enskilt objekt. Du kan exempelvis gruppera alla relaterade taggning principdefinitioner i ett enda initiativ. I stället för att tilldela varje princip individuellt kan använda du initiativ.

I följande exempel illustrerar hur du skapar ett initiativ för att hantera två taggar: `costCenter` och `productName`. Två inbyggda principer används för att tillämpa taggen standardvärdet.

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

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska [förstå effekterna av princip](effects.md)
- Förstå hur du [skapa principer programmässigt](../how-to/programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](../how-to/getting-compliance-data.md)
- Upptäck hur du [åtgärda icke-kompatibla resurser](../how-to/remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)
