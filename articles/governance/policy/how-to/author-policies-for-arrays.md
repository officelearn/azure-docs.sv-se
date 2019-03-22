---
title: Skapa principer för matris egenskaper på Azure-resurser
description: Lär dig att skapa matrisparametrar, skapa regler för matris språk uttryck, utvärdera alias [*] och om du vill lägga till element i en befintlig matris med Azure Policy definitionsregler.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 62267a4549355212a18654ff9781b2164ba19fa9
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864076"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Skapa principer för matris egenskaper på Azure-resurser

Azure Resource Manager-egenskaper definieras vanligtvis som strängar och booleska värden. När en en-till-många-relation måste definieras i stället komplexa egenskaper som matriser. I Azure Policy används matriser på flera olika sätt:

- Vilken typ av en [definition parametern](../concepts/definition-structure.md#parameters), tillhandahåller flera alternativ
- En del av en [principregeln](../concepts/definition-structure.md#policy-rule) använda villkor **i** eller **notIn**
- En del av en regel som utvärderar den [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) att utvärdera specifika scenarier som **ingen**, **alla**, eller  **Alla**
- I den [Lägg till effekt](../concepts/effects.md#append) ersätta eller lägga till i en befintlig matris

Den här artikeln beskriver varje användning av Azure Policy och innehåller flera exempel definitioner.

## <a name="parameter-arrays"></a>Parametern-matriser

### <a name="define-a-parameter-array"></a>Definiera en parametermatris

Definiera en parameter som en matris kan principen flexibilitet när fler än ett värde krävs.
Den här principdefinitionen kan en enda plats för parametern **allowedLocations** och som standard _usaöstra2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Som **typ** har _sträng_, endast ett värde kan anges när du tilldelade principen. Om den här principen tilldelas är resurser i området bara tillåtna inom en enda Azure-region. De flesta principer definitioner måste tillåta en lista över godkända alternativ, till exempel att tillåta _usaöstra2_, _eastus_, och _westus2_.

Skapa principdefinition för att tillåta flera alternativ för att använda den _matris_ **typ**. Samma princip kan skrivas på följande sätt:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> När du har sparat en principdefinition i **typ** egenskapen på en parameter kan inte ändras.

Den här nya parameterdefinitionen tar mer än ett värde under principtilldelningen. Med egenskapen matris **allowedValues** definierats, värdena som är tillgängliga under tilldelning är ytterligare begränsat till fördefinierade listan över alternativ. Användning av **allowedValues** är valfritt.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Skicka värden till en parameter under tilldelning

När du tilldelar principen i Azure-portalen, en parameter av **typ** _matris_ visas som en enda textruta. Tipset står ”användas. att avgränsa värden. (t.ex. London; New York) ”. Ange tillåtna plats värden av _usaöstra2_, _eastus_, och _westus2_ parametern, använder du följande sträng:

`eastus2;eastus;westus2`

Formatet för värdet för parametern skiljer sig när du använder Azure CLI, Azure PowerShell eller REST API. Värden som skickas via en JSON-sträng som innehåller också namnet på parametern.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Om du vill använda den här strängen med varje SDK, använder du följande kommandon:

- Azure CLI: Kommandot [az principtilldelningens skapa](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) med parametern **parametrar**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) med parametern **PolicyParameter**
- REST-API: I den _PLACERA_ [skapa](/rest/api/resources/policyassignments/create) åtgärd i Begärandetexten som värde för den **properties.parameters** egenskap

## <a name="policy-rules-and-arrays"></a>Hanteringsprincipregler (MPR) och matriser

### <a name="array-conditions"></a>Matris villkor

Principregeln [villkor](../concepts/definition-structure.md#conditions) som en _matris_
**typ** för parametern kan användas med är begränsad till `in` och `notIn`. Ta följande principdefinitionen med villkoret `equals` som exempel:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Försök att skapa den här principdefinitionen via Azure portal leder till ett fel, till exempel det här felmeddelandet:

- ”Principen {GUID} kunde inte parametriseras på grund av verifieringsfel. Kontrollera om principparametrarna är rätt definierade. Den inre undantag ”utvärdering resultatet av språkuttrycket [parameters('allowedLocations')] är av typen matris, förväntad typ är 'String' '”.

Den förväntade **typ** av villkoret `equals` är _sträng_. Eftersom **allowedLocations** definieras som **typ** _matris_, utvärderar språkuttrycket och programtypskonfigurationsobjekt genereras följande fel. Med den `in` och `notIn` villkoret principmodulen förväntar sig den **typ** _matris_ i språkuttrycket. För att lösa det här felmeddelandet kan ändra `equals` antingen `in` eller `notIn`.

### <a name="evaluating-the--alias"></a>Utvärdera alias [*]

Alias som har **[\*]** kopplade till deras namn indikerar den **typ** är en _matris_. I stället för att utvärdera värdet för hela matrisen **[\*]** gör det möjligt att utvärdera varje element i matrisen. Det finns tre scenarier som detta per objekt utvärderingen är användbart i: Ingen, eventuella och alla.

Princip-motorn utlösare den **effekt** i **sedan** endast när den **om** regeln utvärderas som true.
Detta är viktigt att förstå i samband med sättet **[\*]** utvärderar varje enskilt element i matrisen.

Exempel principregeln för scenariot tabellen nedan:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Den **ipRules** matrisen är på följande sätt för scenariot tabellen nedan:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

För varje villkor exemplet nedan ersätter `<field>` med `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Följande resultat är resultatet av en kombination av villkoret och exempel principregeln och matrisen med befintliga värdena ovan:

|Tillstånd |Resultat |Förklaring |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Inget |Ett matriselement utvärderas som false (127.0.0.1! = 127.0.0.1) och en som true (127.0.0.1! = 192.168.1.1) och **notEquals** villkoret är _FALSKT_ och effekten utlöses inte. |
|`{<field>,"notEquals":"10.0.4.1"}` |Principens effekt |Båda matriselement utvärderas som true (10.0.4.1! = 127.0.0.1 och 10.0.4.1! = 192.168.1.1) och **notEquals** villkoret är _SANT_ och effekten blir utlöst. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Principens effekt |Ett matriselement utvärderas som true (127.0.0.1 == 127.0.0.1) och en som false (127.0.0.1 == 192.168.1.1) och **är lika med** villkoret är _FALSKT_. Den logiska operatorn utvärderas som true (**inte** _FALSKT_), så att effekten blir utlöst. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Principens effekt |Båda matriselement utvärdera som false (10.0.4.1 == 127.0.0.1 och 10.0.4.1 == 192.168.1.1) och **är lika med** villkoret är _FALSKT_. Den logiska operatorn utvärderas som true (**inte** _FALSKT_), så att effekten blir utlöst. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Principens effekt |Ett matriselement utvärderas som false (127.0.0.1! = 127.0.0.1) och en som true (127.0.0.1! = 192.168.1.1) och **notEquals** villkoret är _FALSKT_. Den logiska operatorn utvärderas som true (**inte** _FALSKT_), så att effekten blir utlöst. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Inget |Båda matriselement utvärderas som true (10.0.4.1! = 127.0.0.1 och 10.0.4.1! = 192.168.1.1) och **notEquals** villkoret är _SANT_. Den logiska operatorn utvärderas som false (**inte** _SANT_), så att effekten inte utlöses. |
|`{<field>,"Equals":"127.0.0.1"}` |Inget |Ett matriselement utvärderas som true (127.0.0.1 == 127.0.0.1) och en som false (127.0.0.1 == 192.168.1.1) och **är lika med** villkoret är _FALSKT_ och effekten utlöses inte. |
|`{<field>,"Equals":"10.0.4.1"}` |Inget |Båda matriselement utvärdera som false (10.0.4.1 == 127.0.0.1 och 10.0.4.1 == 192.168.1.1) och **är lika med** villkoret är _FALSKT_ och effekten utlöses inte. |

## <a name="the-append-effect-and-arrays"></a>Lägg till effekt och matriser

Den [Lägg till effekt](../concepts/effects.md#append) fungerar på olika sätt beroende på om den **details.field** är en **[\*]** alias eller inte.

- Om det inte en **[\*]** alias, lägga till ersätter hela matrisen med den **värdet** egenskap
- När en **[\*]** alias, lägga till lägger till den **värdet** egenskap i den befintliga matris eller skapar nya matris

Mer information finns i den [lägger du till exempel](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](../concepts/definition-structure.md)
- Granska [förstå effekterna av princip](../concepts/effects.md)
- Förstå hur du [skapa principer programmässigt](programmatically-create.md)
- Lär dig hur du [åtgärda icke-kompatibla resurser](remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)