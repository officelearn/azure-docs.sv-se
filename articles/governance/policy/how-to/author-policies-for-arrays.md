---
title: Författare principer för matrisegenskaper på resurser
description: Lär dig att arbeta med matrisparametrar och matrisspråkuttryck, utvärdera [*] aliaset och lägga till element med Azure Policy definition rules.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280669"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Författare principer för matrisegenskaper på Azure-resurser

Azure Resource Manager-egenskaper definieras ofta som strängar och booleans. När det finns en 1:N-relation definieras komplexa egenskaper i stället som matriser. I Azure Policy används matriser på flera olika sätt:

- Typ av [definitionsparameter](../concepts/definition-structure.md#parameters)för att ange flera alternativ
- En del av en [principregel](../concepts/definition-structure.md#policy-rule) som använder villkoren **i** eller **inteI**
- En del av en principregel som utvärderar [ \[ \* \] aliaset](../concepts/definition-structure.md#understanding-the--alias) för att utvärdera:
  - Scenarier som **Ingen,** **Alla**eller **Alla**
  - Komplexa scenarier med **antal**
- I [tilläggseffekten](../concepts/effects.md#append) för att ersätta eller lägga till i en befintlig matris

Den här artikeln täcker varje användning av Azure Policy och innehåller flera exempeldefinitioner.

## <a name="parameter-arrays"></a>Parametermatriser

### <a name="define-a-parameter-array"></a>Definiera en parametermatris

Genom att definiera en parameter som en matris kan principflexibiliteten när mer än ett värde behövs.
Med den här principdefinitionen tillåts en enskild plats för parametern **tillåtnaplatser** och standardvärden till _eastus2:_

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

Eftersom **typen** var _sträng_kan endast ett värde anges när principen tilldelas. Om den här principen har tilldelats tillåts resurser i omfattning endast inom en enda Azure-region. De flesta principer definitioner måste tillåta en lista över godkända alternativ, till exempel att tillåta _eastus2_, _eastus_och _westus2_.

Om du vill skapa principdefinitionen för att tillåta flera alternativ använder du _array_ **matristypen**. Samma policy kan skrivas om på följande sätt:

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
> När en principdefinition har sparats kan **egenskapen type** på en parameter inte ändras.

Den här nya parameterdefinitionen tar mer än ett värde under principtilldelning. Med matrisegenskapen **tillåtenVärde definierad** begränsas de värden som är tillgängliga under tilldelning ytterligare till den fördefinierade listan med alternativ. Användning av **tillåtnavärden** är valfritt.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Skicka värden till en parametermatris under tilldelning

När du tilldelar principen via Azure-portalen visas en parameter för **type** _typmatris_ som en enda textruta. Ledtråden säger "Använd; separata värden. (t.ex. New York)". Om du vill skicka de tillåtna platsvärdena _för eastus2_, _eastus_och _westus2_ till parametern använder du följande sträng:

`eastus2;eastus;westus2`

Formatet för parametervärdet är annorlunda när du använder Azure CLI, Azure PowerShell eller REST API. Värdena skickas genom en JSON-sträng som också innehåller namnet på parametern.

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

Om du vill använda den här strängen med varje SDK använder du följande kommandon:

- Azure CLI: Kommando [az principtilldelning skapa](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) med **parameterparams**
- Azure PowerShell: Cmdlet [New-AzPolicyTilldelning](/powershell/module/az.resources/New-Azpolicyassignment) med **parameterprincipParameter**
- REST API: I åtgärden _PUT_ [create](/rest/api/resources/policyassignments/create) som en del av begärandetexten som värdet för egenskapen **properties.parameters**

## <a name="policy-rules-and-arrays"></a>Principregler och matriser

### <a name="array-conditions"></a>Matrisvillkor

[Principregelvillkoren](../concepts/definition-structure.md#conditions) som en _matristyp_
**type** av parameter kan `in` användas `notIn`med är begränsade till och . Ta följande principdefinition `equals` med villkor som exempel:

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

Om du försöker skapa den här principdefinitionen via Azure-portalen visas ett fel som det här felmeddelandet:

- "Principen {GUID} kunde inte parameteriseras på grund av valideringsfel. Kontrollera om principparametrarna har definierats korrekt. Det inre undantaget "Utvärderingsresultatet av språkuttrycket "[parametrar('allowedLocations')]" är typen Array, den förväntade typen är "String".

Den **type** förväntade typen `equals` av villkor är _sträng_. Eftersom **allowedLocations** definieras som _typmatris_utvärderar principmotorn språkuttrycket och genererar felet. **type** Med `in` villkoret och `notIn` förväntar sig principmotorn _textmatrisen_ i språkuttrycket. **type** Lös det här felmeddelandet `in` genom `notIn`att ändra `equals` till antingen eller .

### <a name="evaluating-the--alias"></a>Utvärdera [*] alias

Alias som ** \[ \* ** har kopplat till sitt namn anger att **typen** är en _matris_. I stället för att utvärdera ** \[ \* ** värdet för hela matrisen gör det möjligt att utvärdera varje element i matrisen individuellt, med logiska OCH mellan dem. Det finns tre standardscenarier som utvärderingen per artikel är användbart i: _Ingen_, _Alla_eller _Alla_ element matchar. För komplexa scenarier använder [du antal](../concepts/definition-structure.md#count).

Principmotorn utlöser **effekten** **då** endast när **if-regeln** utvärderas som sann.
Detta faktum är viktigt att förstå ** \[ \* ** i samband med hur utvärderar varje enskilt element i matrisen.

Exempelprincipregeln för scenariotabellen nedan:

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

Den **ipRules** array är följande för scenariot tabellen nedan:

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

Ersätt `<field>` med `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Följande resultat är resultatet av kombinationen av villkoret och exempelprincipregeln och matrisen med befintliga värden ovan:

|Villkor |Resultat | Scenario |Förklaring |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Ingenting |Ingen matchning |Ett matriselement utvärderas som falskt (127.0.0.1 != 127.0.0.1) och ett som sant (127.0.0.1 != 192.168.1.1), så **villkoret notEquals** är _falskt_ och effekten utlöses inte. |
|`{<field>,"notEquals":"10.0.4.1"}` |Policyeffekt |Ingen matchning |Båda matriselementen utvärderas som true (10.0.4.1 != 127.0.0.1 och 10.0.4.1 != 192.168.1.1), så **villkoret notEquals** är _sant_ och effekten utlöses. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Policyeffekt |En eller flera matchning |Ett arrayelement utvärderas som falskt (127.0.0.1 != 127.0.0.1) och ett som sant (127.0.0.1 != 192.168.1.1), så **villkoret om intekvalitet är** _falskt_. Den logiska operatorn utvärderar som sant (**inte** _falskt_), så effekten utlöses. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Ingenting |En eller flera matchning |Båda matriselementen utvärderas som sanna (10.0.4.1 != 127.0.0.1 och 10.0.4.1 != 192.168.1.1), så **villkoret om ejKvaliteter** är _sant_. Den logiska operatorn utvärderas som falsk (**inte** _sant_), så effekten utlöses inte. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Policyeffekt |Inte alla matchning |Ett matriselement utvärderas som sant (127.0.0.1 == 127.0.0.1) och ett som falskt (127.0.0.1 == 192.168.1.1), så **villkoret Lika är** _falskt_. Den logiska operatorn utvärderar som sant (**inte** _falskt_), så effekten utlöses. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Policyeffekt |Inte alla matchning |Båda matriselementen utvärderas som falska (10.0.4.1 == 127.0.0.1 och 10.0.4.1 == 192.168.1.1), så **villkoret Lika är** _falskt_. Den logiska operatorn utvärderar som sant (**inte** _falskt_), så effekten utlöses. |
|`{<field>,"Equals":"127.0.0.1"}` |Ingenting |Alla match |Ett matriselement utvärderas som sant (127.0.0.1 == 127.0.0.1) och ett som falskt (127.0.0.1 == 192.168.1.1), så **villkoret Lika är** _falskt_ och effekten utlöses inte. |
|`{<field>,"Equals":"10.0.4.1"}` |Ingenting |Alla match |Båda matriselementen utvärderas som falska (10.0.4.1 == 127.0.0.1 och 10.0.4.1 == 192.168.1.1), så **villkoret Lika är** _falskt_ och effekten utlöses inte. |

## <a name="the-append-effect-and-arrays"></a>Tilläggseffekten och matriserna

Effekten [lägg till](../concepts/effects.md#append) fungerar olika beroende på om fältet **är** ett ** \[ \* ** alias eller inte.

- När du ** \[ \* ** inte är ett alias ersätter append hela matrisen med **egenskapen value**
- När ** \[ \* ** ett alias läggs till värdeegenskapen i den befintliga matrisen eller skapar den nya matrisen **value**

Mer information finns i [tilläggsexempelna](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
