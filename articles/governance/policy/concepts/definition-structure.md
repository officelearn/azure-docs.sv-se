---
title: Närmare uppgifter om den politiska definitionsstrukturen
description: Beskriver hur principdefinitioner används för att upprätta konventioner för Azure-resurser i organisationen.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 017878c4c47a5645ea8815580d2176c7a2ff5d66
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314020"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Azure Policy upprättar konventioner för resurser. Principdefinitioner beskriver [villkor](#conditions) för resursefterlevnad och effekten som ska börja om ett villkor uppfylls. Ett villkor jämför ett [field](#fields) resursegenskapsfält med ett obligatoriskt värde. Resursegenskapsfält används med hjälp av [alias](#aliases). Ett resursegenskapsfält är antingen ett fält med ett värde som värderas eller en [matris](#understanding-the--alias) med flera värden. Villkorsutvärdering är olika på matriser.
Läs mer om [villkor](#conditions).

Genom att definiera konventioner kan du kontrollera kostnader och enklare hantera dina resurser. Du kan till exempel ange att endast vissa typer av virtuella datorer är tillåtna. Du kan också kräva att alla resurser har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip tillämpas på en resursgrupp gäller den för alla resurser i resursgruppen.

Principdefinitionsschemat finns här:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Du använder JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

- mode
- parameters
- visningsnamn
- description
- principregel
  - logisk utvärdering
  - Effekt

Följande JSON visar till exempel en princip som begränsar var resurser distribueras:

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
                "defaultValue": [ "westus2" ]
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

Alla Azure-principexempel finns på [Azure Policy-exempel](../samples/index.md).

## <a name="mode"></a>Läge

**Läget** är konfigurerat beroende på om principen är inriktad på en Azure Resource Manager-egenskap eller egenskapen Resource Provider.

### <a name="resource-manager-modes"></a>Lägen för Resurshanteraren

**Läget** avgör vilka resurstyper som ska utvärderas för en princip. Lägena som stöds är:

- `all`: utvärdera resursgrupper och alla resurstyper
- `indexed`: utvärdera endast resurstyper som stöder taggar och plats

Resurs stöder `Microsoft.Network/routeTables` till exempel taggar och plats och utvärderas i båda lägena. Resursen `Microsoft.Network/routeTables/routes` kan dock inte taggas och utvärderas `Indexed` inte i läge.

Vi rekommenderar att **mode** du `all` ställer in läge till i de flesta fall. Alla principdefinitioner som skapas `all` via portalen använder läget. Om du använder PowerShell eller Azure **mode** CLI kan du ange lägesparametern manuellt. Om principdefinitionen inte innehåller ett **lägesvärde** `all` är det standard `null` i Azure PowerShell och i Azure CLI. Ett `null` läge är detsamma som att använda `indexed` för att stödja bakåtkompatibilitet.

`indexed`bör användas när du skapar principer som framtvingar taggar eller platser. Även om det inte krävs, förhindrar det resurser som inte stöder taggar och platser från att visas som icke-kompatibla i efterlevnadsresultaten. Undantaget är **resursgrupper**. Principer som tillämpar plats eller taggar i `all` en resursgrupp `Microsoft.Resources/subscriptions/resourceGroups` bör ange **läge** till och specifikt rikta typen. Ett exempel finns i [Framtvinga resursgrupptaggar](../samples/enforce-tag-rg.md). En lista över resurser som stöder taggar finns i [Taggstöd för Azure-resurser](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Resursproviderlägen (förhandsgranskning)

Följande resursproviderlägen stöds för närvarande under förhandsgranskningen:

- `Microsoft.ContainerService.Data`för hantering av regler för åtkomstkontrollant på [Azure Kubernetes Service](../../../aks/intro-kubernetes.md). Principer som använder det här resursproviderläget **måste** använda effekten [EnforceRegoPolicy.](./effects.md#enforceregopolicy)
- `Microsoft.Kubernetes.Data`för att hantera självhanterade AKS Engine Kubernetes-kluster på Azure.
  Principer som använder det här resursproviderläget **måste** använda effekten [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
- `Microsoft.KeyVault.Data`för hantering av valv och certifikat i [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> Resursproviderlägen stöder bara inbyggda principdefinitioner och stöder inte initiativ i förhandsversion.

## <a name="parameters"></a>Parametrar

Parametrar hjälper till att förenkla din principhantering genom att minska antalet principdefinitioner. Tänk på parametrar som fälten `name` `address`i `city` `state`ett formulär – , , . Dessa parametrar förblir alltid desamma, men deras värden ändras baserat på den enskilde fylla i formuläret.
Parametrar fungerar på samma sätt när du skapar principer. Genom att inkludera parametrar i en principdefinition kan du återanvända principen för olika scenarier med hjälp av olika värden.

> [!NOTE]
> Parametrar kan läggas till i en befintlig och tilldelad definition. Den nya parametern måste innehålla egenskapen **defaultValue.** Detta förhindrar att befintliga tilldelningar av principen eller initiativet indirekt blir ogiltiga.

### <a name="parameter-properties"></a>Egenskaper för parameter

En parameter har följande egenskaper som används i principdefinitionen:

- **namn**: Namnet på parametern. Används av `parameters` distributionsfunktionen i principregeln. Mer information finns [i använda ett parametervärde](#using-a-parameter-value).
- `type`: Avgör om parametern är en **sträng**, **matris**, **objekt**, **booleska,** **heltal,** **float**eller **datetime**.
- `metadata`: Definierar underegenskaper som främst används av Azure-portalen för att visa användarvänlig information:
  - `description`: Förklaringen till vad parametern används till. Kan användas för att ge exempel på godtagbara värden.
  - `displayName`: Det eget namn som visas i portalen för parametern.
  - `version`: (Valfritt) Spårar information om versionen av innehållet i en principdefinition.

    > [!NOTE]
    > Azure Policy-tjänsten `version` `preview`använder `deprecated` , och egenskaper för att förmedla förändringsnivå till en inbyggd principdefinition eller ett initiativ och tillstånd. Formatet `version` på är: `{Major}.{Minor}.{Patch}`. Specifika tillstånd, till exempel _inaktuella_ eller _förhandsgranska,_ läggs `version` till i egenskapen eller i en annan egenskap som en **boolesk**.

  - `category`: (Valfritt) Avgör under vilken kategori i Azure-portalen principdefinitionen visas.
  - `strongType`: (Valfritt) Används när principdefinitionen tilldelas via portalen. Ger en kontextmedveten lista. Mer information finns i [strongType](#strongtype).
  - `assignPermissions`: (Valfritt) Ange som _sant_ om Azure-portalen ska skapa rolltilldelningar under principtilldelning. Den här egenskapen är användbar om du vill tilldela behörigheter utanför tilldelningsomfånget. Det finns en rolltilldelning per rolldefinition i principen (eller per rolldefinition i alla principer i initiativet). Parametervärdet måste vara en giltig resurs eller ett giltigt scope.
- `defaultValue`: (Valfritt) Anger parameterns värde i en tilldelning om inget värde anges.
  Krävs vid uppdatering av en befintlig principdefinition som har tilldelats.
- `allowedValues`: (Valfritt) Tillhandahåller en matris med värden som parametern accepterar under tilldelningen.

Som ett exempel kan du definiera en principdefinition för att begränsa de platser där resurser kan distribueras. En parameter för den principdefinitionen kan **tillåtasPlatser**. Den här parametern används av varje tilldelning av principdefinitionen för att begränsa de godkända värdena. Användningen av **strongType** ger en förbättrad upplevelse när du slutför tilldelningen via portalen:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Använda ett parametervärde

I principregeln refererar du `parameters` till parametrar med följande funktionssyntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Det här exemplet refererar till parametern **allowedLocations** som demonstrerades i [parameteregenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType (olikartade)

I `metadata` egenskapen kan du använda **strongType** för att tillhandahålla en lista över alternativ med flera val i Azure-portalen. **strongType** kan vara en _resurstyp_ som stöds eller ett tillåtet värde. Om du vill ta reda på om en _resurstyp_ är giltig för **strongType**använder du [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Vissa _resurstyper som_ inte returneras av **Get-AzResourceProvider** stöds. Dessa är:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

De tillåtna värdena för _icke-resurstyp_ för **strongType** är:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definitionsplats

När du skapar ett initiativ eller en princip är det nödvändigt att ange definitionsplatsen. Definitionsplatsen måste vara en hanteringsgrupp eller en prenumeration. Den här platsen bestämmer omfattningen som initiativet eller principen kan tilldelas. Resurser måste vara direkta medlemmar av eller underordnade inom hierarkin för definitionsplatsen som ska inriktas på tilldelning.

Om definitionsplatsen är en:

- **Prenumeration** - Endast resurser inom den prenumerationen kan tilldelas principen.
- **Hanteringsgrupp** - Endast resurser inom underordnade hanteringsgrupper och underordnade prenumerationer kan tilldelas principen. Om du planerar att tillämpa principdefinitionen på flera prenumerationer måste platsen vara en hanteringsgrupp som innehåller dessa prenumerationer.

## <a name="display-name-and-description"></a>Visa namn och beskrivning

Du använder **displayName** och **beskrivning** för att identifiera principdefinitionen och ange kontext för när den används. **displayName** har en maximal längd på _128_ tecken och **beskriver** en maximal längd på _512_ tecken.

> [!NOTE]
> När en principdefinition skapas eller uppdateras definieras **id,** **typ**och **namn** av egenskaper som är externa till JSON och är inte nödvändiga i JSON-filen. Om du hämtar principdefinitionen via SDK returneras **egenskaperna id,** **typ**och **namn** som en del av JSON, men var och en är skrivskyddad information som är relaterad till principdefinitionen.

## <a name="policy-rule"></a>Principregel

Principregeln består av **Om** och **Sedan** block. I blocket **Om** definierar du ett eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer på dessa villkor för att exakt definiera scenariot för en princip.

I **sedanblocket** definierar du den effekt som inträffar när **villkoren Om** är uppfyllda.

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

Syntaxen **inte** inverterar resultatet av villkoret. **Syntaxen allOf** (liknar den logiska **och** åtgärden) kräver att alla villkor är sanna. **Syntaxen (som** liknar den logiska **eller** åtgärden) kräver att ett eller flera villkor är sanna.

Du kan kapsla logiska operatorer. I följande exempel visas en **inte-åtgärd** som är kapslad i en **allOf-åtgärd.**

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

Ett villkor utvärderar om ett **fält** eller värdeahöjet uppfyller vissa villkor. **value** Villkoren som stöds är:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

För **mindre**, **lessOrEquals**, **större**och **störreOrEquals**, om egenskapstypen inte matchar villkorstypen, genereras ett fel. Strängjämnningar `InvariantCultureIgnoreCase`görs med .

När du använder **liknande** och **inteLike** villkor, ger du ett jokertecken `*` i värdet.
Värdet bör inte ha mer än `*`ett jokertecken .

När du använder **villkoren matchning** och **notMatch** ska du ange `#` att matcha en siffra, `?` för en bokstav, `.` för att matcha alla tecken och alla andra tecken som matchar det faktiska tecknet. Matchning **match** och **notMatch** är skiftlägeskänsliga, men alla andra villkor som utvärderar en _stringValue_ är skiftlägeskänsliga. Case-okänsliga alternativ finns i **matchInsensitively** och **notMatchInsensitively**.

I ** \[ \* \] ** ett rumsmatrisfältsvärde utvärderas varje element i matrisen individuellt med logiska **och** mellan element. Mer information finns i [ \[ \* \] Utvärdera aliaset](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Fält

Villkor bildas med hjälp av fält. Ett fält matchar egenskaper i resursbegäran nyttolast och beskriver tillståndet för resursen.

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar resursens fullständiga namn. Det fullständiga namnet på en resurs är det resursnamn som förbereds av alla överordnade resursnamn (till exempel "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Använd **globalt** för resurser som är platsoberoende.
- `identity.type`
  - Returnerar den typ av [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) som är aktiverad på resursen.
- `tags`
- `tags['<tagName>']`
  - Den här konsolsyntaxen stöder taggnamn som har skiljetecken, till exempel ett bindestreck, en punkt eller ett blanksteg.
  - Där ** \<tagName\> ** är namnet på taggen som du vill validera villkoret för.
  - Exempel: `tags['Acct.CostCenter']` där **Acct.CostCenter** är namnet på taggen.
- `tags['''<tagName>''']`
  - Den här konsolsyntaxen stöder taggnamn som har apostrofer i den genom att fly med dubbla apostrofer.
  - Där **\<'\>tagName '** är namnet på taggen som du vill validera villkoret för.
  - Exempel: `tags['''My.Apostrophe.Tag''']` där **"My.Apostropof.Tag"** är namnet på taggen.
- egenskapsalias - för en lista, se [Alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`och `tags[tag.with.dots]` är fortfarande godtagbara sätt att deklarera ett taggfält. De föredragna uttrycken är dock de som anges ovan.

#### <a name="use-tags-with-parameters"></a>Använda taggar med parametrar

Ett parametervärde kan skickas till ett taggfält. Om du skickar en parameter till ett taggfält ökar flexibiliteten i principdefinitionen vid principtilldelning.

I följande exempel `concat` används för att skapa en taggarfältsökning för taggen som heter värdet för **parametern tagName.** Om taggen inte finns används **ändringseffekten** för att lägga till taggen med värdet för samma namngivna `resourcegroup()` tagguppsättning i den granskade resursgruppen för resurser med hjälp av uppslagsfunktionen.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Värde

Villkor kan också bildas med hjälp av **värde**. **värde** kontrollerar villkoren mot [parametrar,](#parameters) [mallfunktioner](#policy-functions)som stöds eller litteraler.
**värdet** paras ihop med alla [villkor](#conditions)som stöds .

> [!WARNING]
> Om resultatet av en _mallfunktion_ är ett fel misslyckas principutvärderingen. En misslyckad utvärdering är en implicit **neka**. Mer information finns i [undvika mallfel](#avoiding-template-failures). Använd [enforcementMode](./assignment-structure.md#enforcement-mode) of **DoNotEnforce** för att förhindra att en misslyckad utvärdering påverkar nya eller uppdaterade resurser när du testar och validerar en ny principdefinition.

#### <a name="value-examples"></a>Exempel på värde

I det här **value** principregelexemplet `resourceGroup()` används värdet för att jämföra resultatet `*netrg`av funktionen och egenskapen returnerat **namn** med ett **liknande** villkor i . Regeln nekar alla resurser `Microsoft.Network/*` som inte är av **typen** i någon resursgrupp vars namn slutar i `*netrg`.

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

I det här principregelexemplet används **värdet** för att kontrollera om resultatet av flera kapslade funktioner **är** `true`lika med . Regeln nekar alla resurser som inte har minst tre taggar.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Undvika mallfel

Användningen av _mallfunktioner_ i **värde** möjliggör många komplexa kapslade funktioner. Om resultatet av en _mallfunktion_ är ett fel misslyckas principutvärderingen. En misslyckad utvärdering är en implicit **neka**. Ett exempel på ett **värde** som misslyckas i vissa scenarier:

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

Exempelprincipregeln ovan använder [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) för att jämföra de tre första **tecknen i namn** med **abc**. Om **namnet** är kortare än `substring()` tre tecken resulterar funktionen i ett fel. Det här felet medför att principen blir en **nekad** effekt.

Använd i stället funktionen [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) för att kontrollera om de tre första **tecknen i namn** är lika med **abc** utan att tillåta ett **namn** som är kortare än tre tecken att orsaka ett fel:

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

Med den reviderade `if()` principregeln kontrollerar du **namnlängden** innan du försöker få ett `substring()` värde på ett värde med färre än tre tecken. Om **namnet** är för kort returneras värdet "inte börjar med abc" i stället och jämförs med **abc**. En resurs med ett kort namn som inte börjar med **abc** misslyckas fortfarande principregeln, men orsakar inte längre ett fel under utvärderingen.

### <a name="count"></a>Antal

Villkor som räknar hur många medlemmar i en matris i resursnyttolasten uppfyller ett villkorsuttryck som kan bildas med **hjälp** av count-uttryck. Vanliga scenarier är att kontrollera om "minst en av", "exakt en av", "alla" eller "ingen av" matrismedlemmarna uppfyller villkoret. **antal** utvärderar varje [ \[ \* \] ](#understanding-the--alias) aliasmatrismedlem för ett villkorsuttryck och summerar de _sanna_ resultaten, vilket sedan jämförs med uttrycksoperatorn.

Strukturen för **count-uttrycket** är:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Följande egenskaper används med **antal:**

- **count.field** (obligatoriskt): Innehåller sökvägen till matrisen och måste vara ett matrisalias. Om matrisen saknas utvärderas uttrycket till _false_ utan att hänsyn tas till villkorsuttrycket.
- **count.where** (valfritt): Villkorsuttrycket [ \[ \* \] ](#understanding-the--alias) för att individuellt utvärdera varje aliasmatrismedlem i **count.field**. Om den här egenskapen inte anges utvärderas alla matrismedlemmar med sökvägen till fältet till _true_. Alla [villkor](../concepts/definition-structure.md#conditions) kan användas i den här egenskapen.
  [Logiska operatorer](#logical-operators) kan användas i den här egenskapen för att skapa komplexa utvärderingskrav.
- villkor (obligatoriskt): Värdet jämförs med antalet objekt som uppfyllde count.where-villkorsuttrycket. **count.where** ** \<\> ** Ett numeriskt [villkor](../concepts/definition-structure.md#conditions) bör användas.

#### <a name="count-examples"></a>Räkna exempel

Exempel 1: Kontrollera om en matris är tom

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Exempel 2: Sök efter endast en matrismedlem för att uppfylla villkorsuttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Exempel 3: Sök efter minst en matrismedlem för att uppfylla villkorsuttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Exempel 4: Kontrollera att alla objektmatrismedlemmar uppfyller villkorsuttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Exempel 5: Kontrollera att alla strängmatrismedlemmar uppfyller villkorsuttrycket

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Exempel 6: Använd **fält** inuti **värde** för att kontrollera att alla matrismedlemmar uppfyller villkorsuttrycket

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Exempel 7: Kontrollera att minst en matrismedlem matchar flera egenskaper i villkorsuttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Verkan

Azure Policy stöder följande typer av effekt:

- **Lägg till**: lägger till den definierade uppsättningen fält i begäran
- **Granskning**: genererar en varningshändelse i aktivitetsloggen men misslyckas inte med begäran
- **AuditIfNotExists**: genererar en varningshändelse i aktivitetsloggen om det inte finns någon relaterad resurs
- **Neka**: genererar en händelse i aktivitetsloggen och misslyckas begäran
- **DeployIfNotExists**: distribuerar en relaterad resurs om den inte redan finns
- **Inaktiverad**: utvärderar inte resurser för efterlevnad av principregeln
- **EnforceOPAConstraint** (förhandsversion): konfigurerar åtkomstkontrollanten för öppna principagenter med Gatekeeper v3 för självhanterade Kubernetes-kluster på Azure (förhandsversion)
- **EnforceRegoPolicy** (förhandsversion): konfigurerar åtkomstkontrollanten för öppna principagenter med Gatekeeper v2 i Azure Kubernetes Service
- **Ändra**: lägger till, uppdaterar eller tar bort de definierade taggarna från en resurs

Fullständig information om varje effekt, utvärderingsordning, egenskaper och exempel finns i [Förstå Azure-principeffekter](effects.md).

### <a name="policy-functions"></a>Policyfunktioner

Alla [Resource Manager-mallfunktioner](../../../azure-resource-manager/templates/template-functions.md) är tillgängliga att använda i en principregel, förutom följande funktioner och användardefinierade funktioner:

- copyIndex()
- distribution()
- lista*
- newGuid()
- pickZones()
- leverantörer()
- referens()
- resourceId()
- variabler()

> [!NOTE]
> Dessa funktioner är fortfarande `details.deployment.properties.template` tillgängliga inom den del av malldistributionen i en **distributionOmutexisterar** principdefinition.

Följande funktion är tillgänglig att använda i en principregel, men skiljer sig från användning i en Azure Resource Manager-mall:

- `utcNow()`- Till skillnad från en Resource Manager-mall kan detta användas utanför standardvärde.
  - Returnerar en sträng som är inställd på aktuellt datum och tid i Universal ISO 8601 DateTime-formatet 'yyyy-MM-ddTHH:mm:ss.fffffffZ'

Följande funktioner är endast tillgängliga i principregler:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [Obligatorisk] sträng - Sträng i Universal ISO 8601 DateTime-formatet 'yyyy-MM-ddTHH:mm:ss.fffffffZ'
  - **numberOfDaysToAdd**: [Obligatoriskt] heltal - Antal dagar att lägga till
- `field(fieldName)`
  - **fieldName**: [Obligatorisk] sträng - Namnet på [fältet](#fields) som ska hämtas
  - Returnerar värdet för det fältet från den resurs som utvärderas av villkoret Om
  - `field`används främst med **AuditIfNotExists** och **DeployIfNotExists** för att referera till fält på resursen som utvärderas. Ett exempel på den här användningen kan ses i [exemplet DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Returnerar API-versionen av begäran som utlöste `2019-09-01`principutvärdering (exempel: ).
    Detta kommer att vara API-versionen som användes i PUT/ PATCH-begäran om utvärderingar om att skapa/uppdatera resurser. Den senaste API-versionen används alltid under utvärdering av efterlevnad på befintliga resurser.
  
#### <a name="policy-function-example"></a>Exempel på funktionen Princip

I det här `resourceGroup` principregelexemplet används resursfunktionen `concat` för att hämta `like` namnegenskapen, i kombination med matris- och objektfunktionen för att skapa ett villkor som framtvingar resursnamnet så att det börjar med resursgruppsnamnet. **name**

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

Du kan använda egenskapsalias för att komma åt specifika egenskaper för en resurstyp. Med alias kan du begränsa vilka värden eller villkor som tillåts för en egenskap på en resurs. Varje alias mappar till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärderingen får principmotorn egenskapssökvägen för den API-versionen.

Listan över alias växer alltid. Om du vill ta reda på vilka alias som för närvarande stöds av Azure Policy använder du någon av följande metoder:

- Azure Princip-tillägg för Visual Studio-kod (rekommenderas)

  Använd [Azure Policy-tillägget för Visual Studio-kod](../how-to/extension-for-vscode.md) för att visa och identifiera alias för resursegenskaper.

  ![Azure Principtillägg för Visual Studio-kod](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Använd `project` operatorn för att visa **alias** för en resurs.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Förstå [*] alias

Flera av de alias som är tillgängliga har en version som visas ** \[ \* ** som ett "normalt" namn och ett annat som har bifogat den. Ett exempel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Det "normala" aliaset representerar fältet som ett enda värde. Det här fältet är för exakta matchningsjämförelsescenarier när hela uppsättningen värden måste vara exakt som de definierade, varken mer eller mindre.

Aliaset ** \[ \* ** gör det möjligt att jämföra med värdet för varje element i matrisen och specifika egenskaper för varje element. Den här metoden gör det möjligt att jämföra elementegenskaper för "om inget av", "om något av" eller "om alla" scenarier. Om du vill ha mer komplexa scenarier använder du villkorsuttrycket [för antal.](#count) Använda **ipRules\[\***, ett exempel skulle validera att varje _åtgärd_ är _Neka_, men inte oroa sig för hur många regler som finns eller vad _IP-värdet_ är.
Den här exempelregeln söker efter matchningar av **ipRules\[\*\].value** till **10.0.4.1** och tillämpar **effektenType** endast om den inte hittar minst en matchning:

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

Mer information finns i [utvärdera [\*] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Initiativ

Med initiativ kan du gruppera flera relaterade principdefinitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enda objekt. Du kan till exempel gruppera relaterade taggningsprincipdefinitioner i ett enda initiativ. I stället för att tilldela varje princip individuellt tillämpar du initiativet.

> [!NOTE]
> När ett initiativ har tilldelats kan parametrar på initiativnivå inte ändras. På grund av detta är rekommendationen att ange ett **defaultValue** när parametern definieras.

Följande exempel illustrerar hur du skapar ett initiativ `costCenter` `productName`för hantering av två taggar: och . Den använder två inbyggda principer för att tillämpa standardtaggvärdet.

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
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Förstå policy-effekter](effects.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
