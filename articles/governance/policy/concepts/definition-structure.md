---
title: Information om policy-definitionsstruktur
description: Beskriver hur resource principdefinitionen används av Azure Policy för att etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken effekt ska börja.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 7bb25aa1f77a49363fe2e08d1430282b9b33caae
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549362"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resursen principdefinitioner används av Azure Policy för att etablera konventioner för resurser. Varje definition beskriver resurskompatibilitet och vad i kraft för att vidta när en resurs är inkompatibla.
Du kan styra kostnaderna genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att bara vissa typer av virtuella datorer är tillåtna. Eller så kan du kräva att alla resurser har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip tillämpas på en resursgrupp, kan den användas för alla resurser i resursgruppen.

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
                },
                "defaultValue": "westus2"
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

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Läge

Den **läge** avgör vilka typer av resurser kommer att utvärderas för en princip. De stödda lägena är:

- `all`: utvärdera resursgrupper och alla resurstyper
- `indexed`: endast utvärdera resurstyper som stöder taggar och plats

Vi rekommenderar att du ställer in **läge** till `all` i de flesta fall. Alla principdefinitioner som skapats via portalen användning i `all` läge. Om du använder PowerShell eller Azure CLI kan du ange den **läge** parametern manuellt. Om principdefinitionen inte innehåller en **läge** , den standardvärdet `all` i Azure PowerShell och till `null` i Azure CLI. En `null` läge är detsamma som att använda `indexed` att stödja bakåtkompatibilitet kompatibilitet.

`indexed` ska användas när du skapar principer som tillämpar taggar eller platser. Du måste inte, förhindrar resurser som inte stöder taggar och platser från dyker upp som icke-kompatibla i kompatibilitetsresultaten. Undantaget är **resursgrupper**. Principer som framtvinga plats eller taggar på en resursgrupp bör ange **läge** till `all` och specifikt mål den `Microsoft.Resources/subscriptions/resourceGroups` typen. Ett exempel finns i [framtvinga grupp resurstaggar](../samples/enforce-tag-rg.md). En lista över resurser som stöder taggar finns i [tagga stöd för Azure-resurser](../../../azure-resource-manager/tag-support.md).

## <a name="parameters"></a>Parametrar

Parametrar underlätta hanteringen av principer genom att minska antalet principdefinitioner. Tänk på parametrar som fält i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar desamma alltid, men deras värden ändras baserat på enskilda fyller i formuläret.
Parametrar fungerar på samma sätt som när du skapar principer. Du kan återanvända principen för olika scenarier med hjälp av olika värden genom att lägga till parametrar i en principdefinition.

> [!NOTE]
> Parametrar kan läggas till en befintlig och tilldelade definition. Den nya parametern måste innehålla den **defaultValue** egenskapen. Detta förhindrar att befintliga tilldelningar för principen eller initiativ indirekt görs ogiltig.

### <a name="parameter-properties"></a>Egenskaper för rapportparameter

En parameter har följande egenskaper som används i principdefinitionen:

- **name**: Namnet på parametern. Används av den `parameters` distribution funktion i principregeln. Mer information finns i [med hjälp av ett parametervärde](#using-a-parameter-value).
- `type`: Anger om parametern är en **sträng** eller en **matris**.
- `metadata`: Definierar subegenskaper som främst används av Azure-portalen för att visa användarvänliga information:
  - `description`: Förklaring av vad parametern används för. Kan användas för att ge exempel på de godkända värdena.
  - `displayName`: Det egna namnet som visas i portalen för parametern.
  - `strongType`: (Valfritt) Används när du tilldelar principdefinitionen via portalen. Innehåller en kontext medveten lista. Mer information finns i [strongType](#strongtype).
- `defaultValue`: (Valfritt) Anger värdet för parametern i en tilldelning om inget värde anges. Krävs när du uppdaterar en befintlig principdefinition som är tilldelad.
- `allowedValues`: (Valfritt) Innehåller en matris med värden som parametern accepterar under tilldelning.

Exempelvis kan definiera du en principdefinition för att begränsa de platser där resurser kan distribueras. En parameter för den principdefinitionen kan vara **allowedLocations**. Den här parametern används av varje tilldelning av principdefinitionen för att begränsa de godkända värdena. Användning av **strongType** ger en förbättrad upplevelse när du har slutfört tilldelningen via portalen:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "westus2",
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Med hjälp av ett parametervärde

I principregeln du referera till parametrar med följande `parameters` distribution värdet funktionens syntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Det här exemplet refererar till den **allowedLocations** parameter som visades i [parameteregenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType

I den `metadata` egenskapen, som du kan använda **strongType** att tillhandahålla en flervalslista med alternativ i Azure-portalen. Tillåtna värden för **strongType** nu:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Definitionens plats

När du skapar ett initiativ- eller är det nödvändigt att ange platsen för definition. Definitionens plats måste vara en hanteringsgrupp eller en prenumeration. Den här platsen anger omfånget som den initiativ- eller kan tilldelas. Resurser måste vara direkta medlemmar i eller underordnade objekt inom hierarkin för definitionens plats för att rikta för tilldelning.

Om den definition lagras a:

- **Prenumeration** – endast resurser inom den prenumerationen kan tilldelas principen.
- **Hanteringsgruppen** – endast resurser i underordnade hanteringsgrupper och underordnade prenumerationer kan du tilldela principen. Om du planerar att använda principdefinitionen till flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller dessa prenumerationer.

## <a name="display-name-and-description"></a>Namn och beskrivning

Du använder **displayName** och **beskrivning** identifiera principdefinitionen och ge ett sammanhang för när den används. **displayName** har en maximal längd på _128_ tecken och **beskrivning** en maximal längd på _512_ tecken.

## <a name="policy-rule"></a>Principregel

Principregeln består av **om** och **sedan** block. I den **om** block, definierar du en eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att ange exakt scenariot för en princip.

I den **sedan** block, definierar du den effekt som händer när den **om** villkor är uppfyllda.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
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

Ett villkor utvärderas om en **fältet** eller **värdet** accessor uppfyller vissa villkor. Villkor som stöds är:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

När du använder den **som** och **notLike** villkor du anger ett jokertecken `*` i värdet.
Värdet får inte innehålla fler än ett jokertecken `*`.

När du använder den **matchar** och **notMatch** villkor, ger `#` så att de matchar en siffra, `?` för en bokstav, `.` så att den matchar alla tecken och alla andra tecken som ska matchas Det faktiska tecknet.
**matcha** och **notMatch** är skiftlägeskänsliga. Skiftlägeskänsliga alternativ är tillgängliga i **matchInsensitively** och **notMatchInsensitively**. Exempel finns i [Tillåt flera namn mönster](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fält

Villkor bildas genom att använda fält. Ett fält matchar egenskaper i nyttolasten i begäran av resursen och beskriver tillståndet för resursen.

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar det fullständiga namnet på resursen. Det fullständiga namnet på en resurs är resursnamnet föregås av ett anpassningsprefix av alla överordnade resursnamn (till exempel ”minserver/myDatabase”).
- `kind`
- `type`
- `location`
  - Använd **globala** för resurser som är oberoende av platsen. Ett exempel finns i [-exempel – tillåtna platser](../samples/allowed-locations.md).
- `identity.type`
  - Returnerar typen för [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) aktiverad på resursen.
- `tags`
- `tags['<tagName>']`
  - Den här syntaxen hakparentes stöder taggnamn som har skiljetecken, till exempel ett bindestreck, punkt eller blanksteg.
  - Där **\<tagName\>** är namnet på taggen för att verifiera villkoret för.
  - Exempel: `tags['Acct.CostCenter']` där **Acct.CostCenter** är namnet på taggen.
- `tags['''<tagName>''']`
  - Den här syntaxen hakparentes stöder taggnamn som har apostrofer i den genom att undantagstecken med dubbla apostrofer.
  - Där **'\<tagName\>'** är namnet på taggen för att verifiera villkoret för.
  - Exempel: `tags['''My.Apostrophe.Tag''']` där **'\<tagName\>'** är namnet på taggen.
- Egenskapen alias – en lista i [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, och `tags[tag.with.dots]` godtas sätt deklarerar en tagg-fälten.
> Prioriterade uttrycken är de som anges ovan.

#### <a name="use-tags-with-parameters"></a>Använda taggar med parametrar

Ett parametervärde kan skickas till ett fält med taggen. Skicka en parameter till ett fält i taggen ökar flexibiliteten för principdefinitionen under principtilldelningen.

I följande exempel `concat` används för att skapa en fält-sökning för taggar för taggen med namnet värdet för den **tagName** parametern. Om taggen i fråga inte finns i **lägga till** effekt används för att lägga till taggen med hjälp av värdet för samma namngivna tagg in på granskade resurserna överordnade resursgruppen med hjälp av den `resourcegroup()` lookup-funktion.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Värde

Villkor kan även skapas med hjälp av **värdet**. **värdet** kontrollerar villkor mot [parametrar](#parameters), [stöds Mallfunktioner](#policy-functions), eller litteraler.
**värdet** paras ihop med alla stöds [villkor](#conditions).

> [!WARNING]
> Om resultatet av en _mallfunktionen_ inträffar ett fel misslyckas för utvärdering av principen. En misslyckad utvärdering är en implicit **neka**. Mer information finns i [undvika mall fel](#avoiding-template-failures).

#### <a name="value-examples"></a>Värdet exempel

Den här principen regelexempel använder **värdet** att jämföra resultatet av den `resourceGroup()` funktionen och den returnerade **namn** egenskap till en **som** villkor för `*netrg`. Regeln nekar en resurs inte av den `Microsoft.Network/*` **typ** i valfri resursgrupp vars namn slutar med `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Den här principen regelexempel använder **värdet** att kontrollera om resultatet av flera kapslade funktioner **är lika med** `true`. Regeln nekar alla resurser som inte har minst tre taggar.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Undvik mall-fel

Användning av _Mallfunktioner_ i **värdet** möjliggör många komplexa kapslade funktioner. Om resultatet av en _mallfunktionen_ inträffar ett fel misslyckas för utvärdering av principen. En misslyckad utvärdering är en implicit **neka**. Ett exempel på en **värdet** som misslyckas i vissa scenarion:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Principregeln exemplet ovan använder [substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) att jämföra de första tre tecknen i **namn** till **abc**. Om **namn** är kortare än tre tecken i `substring()` funktionen resulterar i ett fel. Det här felet gör att principen ska bli en **neka** effekt.

Använd i stället de [if()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) funktionen för att kontrollera om de första tre tecknen i **namn** lika **abc** utan att tillåta en **namn** kortare än tre tecken kan orsaka ett fel:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Med den ändrade principregeln `if()` kontrollerar längden på **namn** innan du försöker hämta en `substring()` på ett värde med färre än tre tecken. Om **namn** är för kort, värdet ”inte börjar med abc” returneras i stället och jämfört med **abc**. En resurs med ett kort namn som inte börjar med **abc** fortfarande misslyckas principregeln, men inte längre orsakar ett fel under utvärderingen.

### <a name="effect"></a>Verkan

Principen har stöd för följande typer av effekt:

- **Neka**: Generera en händelse i aktivitetsloggen och misslyckas begäran
- **Granska**: genererar en varning-händelse i aktivitetsloggen men inte misslyckas begäran
- **Lägg till**: lägger till en definierad uppsättning fält på begäran
- **AuditIfNotExists**: aktiverar granskning om en resurs inte finns
- **DeployIfNotExists**: distribuerar en resurs om den inte redan finns
- **Inaktiverad**: inte utvärdera resurser för principregeln

För **lägga till**, måste du ange följande information:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Värdet kan vara en sträng eller ett JSON-format-objekt.

**AuditIfNotExists** och **DeployIfNotExists** utvärdera förekomsten av en relaterad resurs och tillämpa en regel. Om resursen inte matchar regeln, implementeras effekten. Du kan till exempel kräva att en nätverksbevakare distribueras för alla virtuella nätverk. Mer information finns i den [granska om tillägg inte finns](../samples/audit-ext-not-exist.md) exempel.

Den **DeployIfNotExists** effekt kräver den **roleDefinitionId** -egenskapen i den **information** delen av principregeln. Mer information finns i [reparation – konfigurera principdefinitionen](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Mer information om varje effekt ordningen för utvärdering, egenskaper och exempel finns i [Förstå princip effekterna](effects.md).

### <a name="policy-functions"></a>Princip fungerar

Alla [Resource Manager-Mallfunktioner](../../../azure-resource-manager/resource-group-template-functions.md) är tillgängliga för användning i en regel, förutom följande funktioner:

- copyIndex()
- deployment()
- list*
- providers()
- reference()
- resourceId()
- variables()

Dessutom kan den `field` funktionen är tillgänglig för hanteringsprincipregler (MPR). `field` används främst med **AuditIfNotExists** och **DeployIfNotExists** till referensfält på resursen som utvärderas. Ett exempel på den här användningen visas på den [DeployIfNotExists exempel](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Princip för funktionen exempel

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

## <a name="aliases"></a>Alias

Du kan använda egenskapen alias för att få åtkomst till specifika egenskaper för en resurstyp. Alias kan du begränsa vilka värden eller villkor är tillåtna för en egenskap för en resurs. Varje alias som mappas till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärdering hämtar principmodulen egenskapssökvägen för den API-versionen.

Lista över alla alias växer. Använd någon av följande metoder för att hitta vilka alias är för närvarande stöds av Azure Policy:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
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

'Normal' alias representerar fältet som ett enda värde. Det här fältet är exakt matchning jämförelse scenarier när samtliga värden måste vara exakt som de definierats några och inte mindre.

Den **[\*]** alias gör det möjligt att jämföra med värdet för varje element i matrisen och specifika egenskaper för varje element. Den här metoden gör det möjligt att jämföra element egenskaperna för ”om inget av”, ”om någon av”, eller ”om alla av ' scenarier. Med hjälp av **ipRules [\*]**, ett exempel skulle verifierar som varje _åtgärd_ är _neka_, men inte oroa dig över hur många regler finns, eller vilka IP-Adressen _värdet_ är. Regeln exempel söker efter alla matchningar av **ipRules [\*] .value** till **10.0.4.1** och tillämpar den **effectType** endast om det inte finns minst en matchning:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Mer information finns i [utvärderar den [\*] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Initiativ

Initiativ kan du gruppera flera relaterade principdefinitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enskilt objekt. Du kan exempelvis gruppera relaterade taggning principdefinitioner i ett enda initiativ. I stället för att tilldela varje princip individuellt kan använda du initiativ.

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
- Lär dig hur du [åtgärda icke-kompatibla resurser](../how-to/remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)
