---
title: Redigera principer för mat ris egenskaper för resurser
description: Lär dig att arbeta med mat ris parametrar och matris språk uttryck, utvärdera [*]-aliaset och lägga till element med Azure Policy definitions regler.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 60044d4a599c14088ea923a6a14cb46543646995
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920465"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Redigera principer för mat ris egenskaper på Azure-resurser

Azure Resource Manager egenskaper definieras vanligt vis som strängar och booleska värden. När en en-till-många-relation finns, definieras komplexa egenskaper i stället som matriser. I Azure Policy används matriser på flera olika sätt:

- Typ av [definitions parameter](../concepts/definition-structure.md#parameters)för att tillhandahålla flera alternativ
- En del av en [princip regel](../concepts/definition-structure.md#policy-rule) med hjälp av villkoren **i** eller **notIn**
- En del av en princip regel som utvärderar det [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) som ska utvärderas:
  - Scenarier som **ingen**, **alla** eller **alla**
  - Komplexa scenarier med **antal**
- I Lägg till- [effekter](../concepts/effects.md#append) för att ersätta eller lägga till i en befintlig matris

Den här artikeln beskriver varje användning av Azure Policy och innehåller flera exempel definitioner.

## <a name="parameter-arrays"></a>Parameter mat ris

### <a name="define-a-parameter-array"></a>Definiera en parameter mat ris

Genom att definiera en parameter som en matris kan principen vara flexibel när fler än ett värde krävs.
Med den här princip definitionen kan parametern **allowedLocations** och standard platsen vara _eastus2_:

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

Som **typen** var _sträng_, kan endast ett värde anges när principen tilldelas. Om den här principen är tilldelad tillåts endast resurser i omfattning inom en enda Azure-region. De flesta princip definitioner måste tillåta en lista över godkända alternativ, till exempel att tillåta _eastus2_, _öster_ och _westus2_.

Använd _mat ris_ **typen** för att skapa princip definitionen för att tillåta flera alternativ. Samma princip kan skrivas om på följande sätt:

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
> När en princip definition har sparats går det inte att ändra **typ** egenskapen för en parameter.

Den här nya parameter definitionen tar fler än ett värde under princip tilldelningen. När du har definierat mat ris egenskapen **allowedValues** är de värden som är tillgängliga under tilldelningen ytterligare begränsad till den fördefinierade listan med alternativ. Användning av **allowedValues** är valfritt.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Skicka värden till en parameter mat ris under tilldelningen

När du tilldelar principen via Azure Portal visas en parameter av **typen** _matris_ som en enda text ruta. Tipset står "use; separera värden. (t. ex. London; New York) ". Använd följande sträng för att överföra de tillåtna positions värden för _eastus2_, _öster_ och _westus2_ till parametern:

`eastus2;eastus;westus2`

Parameter värdets format skiljer sig från Azure CLI, Azure PowerShell eller REST API. Värdena skickas via en JSON-sträng som även innehåller namnet på parametern.

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

Använd följande kommandon om du vill använda den här strängen med varje SDK:

- Azure CLI: kommando [AZ princip tilldelning skapa](/cli/azure/policy/assignment#az_policy_assignment_create) med parameter **parametrar**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) med parametern **PolicyParameter**
- REST API: i åtgärden _Lägg_ till [skapa](/rest/api/resources/policyassignments/create) som en del av begär ande texten som värde för egenskapen **Properties. Parameters**

## <a name="array-conditions"></a>Mat ris villkor

Princip regel [villkoren](../concepts/definition-structure.md#conditions) som en _mat ris_ 
 **typ** parameter kan användas med är begränsad till `in` och `notIn` . Vidta följande princip definition med villkor `equals` som exempel:

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

Försöket att skapa princip definitionen via Azure Portal leder till ett fel meddelande som detta fel meddelande:

- "Det gick inte att parameterstyrda principen {GUID} på grund av verifierings fel. Kontrol lera att princip parametrarna är korrekt definierade. Det inre undantaget för utvärderings resultatet av språk uttrycket [parameters (' allowedLocations ')] är type ' matris ', förväntad typ är sträng '. "

Den förväntade **typen** av villkor `equals` är _sträng_. Eftersom **allowedLocations** har definierats som **typ** _mat ris_, utvärderar principmodulen språk uttryck och genererar felet. Med `in` `notIn` villkoret och förväntar sig princip motorn **typ** _mat ris_ i språk uttrycket. Lös det här fel meddelandet genom att ändra `equals` till antingen `in` eller `notIn` .

## <a name="referencing-array-resource-properties"></a>Referera till egenskaper för mat ris resurs

Många användnings fall kräver att du arbetar med mat ris egenskaper i den utvärderade resursen. Vissa scenarier kräver att du refererar till en hel matris (till exempel genom att kontrol lera dess längd). Andra måste tillämpa ett villkor för varje enskild mat ris medlem (till exempel se till att alla brand Väggs regler blockerar åtkomst från Internet). Att förstå de olika sätten Azure Policy kan referera till resurs egenskaper och hur dessa referenser beter sig när de refererar till mat ris egenskaper är nyckeln för att skriva villkor som beskriver dessa scenarier.

### <a name="referencing-resource-properties"></a>Refererande resurs egenskaper
Resurs egenskaper kan refereras till av Azure Policy med hjälp av [alias](../concepts/definition-structure.md#aliases) det finns två sätt att referera till värdena för en resurs egenskap i Azure policy:

- Använd [fält](../concepts/definition-structure.md#fields) villkor för att kontrol lera om **alla** valda resurs egenskaper uppfyller ett villkor. Exempel:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Använd `field()` funktionen för att få åtkomst till värdet för en egenskap. Exempel:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

Fält villkoret har ett implicit "alla"-beteende. Om aliaset representerar en samling värden kontrollerar det om alla enskilda värden uppfyller villkoret. `field()`Funktionen returnerar värdena som representeras av aliaset som de är, som sedan kan manipuleras av andra mall funktioner.

### <a name="referencing-array-fields"></a>Referera till mat ris fält

Egenskaper för mat ris resurser representeras vanligt vis av två olika typer av alias. Ett normalt alias och [mat ris Ali Aset](../concepts/definition-structure.md#understanding-the--alias) som `[*]` är kopplade till den:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Referera till matrisen

Det första aliaset representerar ett enda värde, värdet för `stringArray` egenskapen från innehållet i begäran. Eftersom värdet för egenskapen är en matris är det inte särskilt användbart i princip villkor. Exempel:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Det här villkoret jämför hela `stringArray` matrisen med ett enda sträng värde. De flesta villkor, inklusive `equals` , accepterar bara sträng värden, så det finns inte mycket användning av en matris till en sträng. Huvud scenariot där referenser till mat ris egenskapen är användbart är att kontrol lera om det finns:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Med `field()` funktionen är det returnerade värdet matrisen från innehållet i begäran, som sedan kan användas med någon av de [mall funktioner som stöds](../concepts/definition-structure.md#policy-functions) och som accepterar mat ris argument. Följande villkor kontrollerar t. ex. om längden `stringArray` är större än 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Referens till samlingen av mat ris medlemmar

Alias som använder `[*]` syntaxen representerar en **samling egenskaps värden som valts från en mat ris egenskap**, vilket skiljer sig från att välja själva mat ris egenskapen. I fallet `Microsoft.Test/resourceType/stringArray[*]` returneras en samling som har alla medlemmar i `stringArray` . Som tidigare nämnts `field` kontrollerar ett villkor att alla valda resurs egenskaper uppfyller villkoret, vilket innebär att följande villkor är sant endast om **alla** medlemmar i `stringArray` är lika med "" värde "".

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Om matrisen innehåller objekt kan ett `[*]` alias användas för att välja värdet för en speciell egenskap från varje mat ris medlem. Exempel:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Det här villkoret är sant om värdena för alla `property` Egenskaper i `objectArray` är lika med `"value"` .

När du använder `field()` funktionen för att referera till ett mat ris alias är det returnerade värdet en matris med alla valda värden. Detta innebär att det vanliga användnings fallet för `field()` funktionen, möjligheten att tillämpa mall-funktioner till resurs egenskaps värden, är mycket begränsad. De enda mal funktioner som kan användas i det här fallet är de som accepterar mat ris argument. Det är till exempel möjligt att hämta längden på matrisen med `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Det är dock bara möjligt att använda fler komplexa scenarier som att använda funktionen mall för varje mat ris medlem och jämföra det med ett önskat värde när du använder `count` uttrycket. Mer information finns i [Count-uttryck](#count-expressions).

Information om hur du sammanfattar finns i följande exempel på resurs innehåll och de valda värdena som returneras av olika alias:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

När du använder fält villkoret i resurs innehållet i exemplet är resultatet följande:

| Alias | Valda värden |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | En tom samling med värden. |
| `Microsoft.Test/resourceType/missingArray[*].property` | En tom samling med värden. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

När du använder `field()` funktionen i resurs innehållet i exemplet är resultatet följande:

| Uttryck | Returnerat värde |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>Räkna uttryck

[Count-uttryck räknar](../concepts/definition-structure.md#count) hur många mat ris medlemmar som uppfyller ett villkor och jämför antalet till ett målvärde. `Count` är mer intuitivt och flexibelt för utvärdering av matriser jämfört med `field` villkor. Syntax:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

När det används utan ett Where-villkor `count` returnerar bara längden på en matris. Med exempel resurs innehållet från föregående avsnitt `count` utvärderas följande uttryck `true` eftersom `stringArray` har tre medlemmar:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Det här beteendet fungerar också med kapslade matriser. Till exempel `count` utvärderas följande uttryck eftersom det finns `true` fyra mat ris medlemmar i `nestedArray` matriserna:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

Kraften hos `count` är i `where` villkoret. När den anges räknar Azure Policy upp mat ris medlemmarna och utvärdera var och en av dem mot villkoret och räknar hur många mat ris medlemmar som utvärderas till `true` . I varje iteration av `where` villkors utvärderingen väljer Azure policy en enskild mat ris medlem ***i** _ och utvärdera resurs innehållet mot `where` villkoret _* som om **_i_*_ är den enda medlemmen i array_ *. Att endast en mat ris medlem är tillgänglig i varje iteration är ett sätt att tillämpa komplexa villkor på varje enskild mat ris medlem.

Exempel:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
För att utvärdera `count` uttrycket utvärderar Azure policy `where` villkoret 3 gånger, en gång för varje medlem i `stringArray` och räknar hur många gånger det utvärderades `true` . När `where` villkoret refererar till `Microsoft.Test/resourceType/stringArray[*]` mat ris medlemmar, i stället för att markera alla medlemmar i `stringArray` , så markeras bara en enskild mat ris medlem varje gång:

| Iteration | Valda `Microsoft.Test/resourceType/stringArray[*]` värden | `where` Utvärderings resultat |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

Och kommer därför `count` att returnera `1` .

Här är ett mer komplext uttryck:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteration | Valda värden | `where` Utvärderings resultat |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

Och därmed `count` returneras `1` .

Det faktum att `where` uttrycket utvärderas mot **hela** begär ande innehållet (med ändringar enbart till den mat ris medlem som för närvarande räknas upp) innebär att `where` villkoret kan också referera till fält utanför matrisen:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iteration | Valda värden | `where` Utvärderings resultat |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Kapslade Count-uttryck tillåts också:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Upprepning av yttre loop | Valda värden | Inre loop-iteration | Valda värden |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>`field()`Funktionen inuti `where` villkor

Hur `field()` funktioner fungerar när de ingår i ett `where` villkor baseras på följande begrepp:
1. Mat ris Ali Aset matchas i en samling värden som valts från alla mat ris medlemmar.
1. `field()` funktioner som refererar till mat ris Ali Aset returnerar en matris med de valda värdena.
1. Referenser till det räknade mat ris Ali Aset i `where` villkoret returnerar en samling med ett enskilt värde markerat från den mat ris medlem som utvärderas i den aktuella iterationen.

Det här beteendet innebär att när du refererar till den räknade mat ris medlemmen med en `field()` funktion inuti `where` villkoret, **returnerar den en matris med en enda medlem**. Det kanske inte är intuitivt, men det är konsekvent med idén att mat ris Ali Aset alltid returnerar en samling med valda egenskaper. Här är ett exempel:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteration | Uttrycks värden | `where` Utvärderings resultat |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

När det finns ett behov av att komma åt värdet för det räknade mat ris Ali Aset med en `field()` funktion, är det därför att omsluta det med en `first()` mall funktion:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteration | Uttrycks värden | `where` Utvärderings resultat |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Användbara exempel finns i [antal exempel](../concepts/definition-structure.md#count-examples).

## <a name="modifying-arrays"></a>Ändra matriser

Ändra egenskaper för att [lägga till](../concepts/effects.md#append) och [ändra](../concepts/effects.md#modify) på en resurs vid skapande eller uppdatering. När du arbetar med mat ris egenskaper beror beteendet för dessa effekter på om åtgärden försöker ändra  **\[\*\]** aliaset eller inte:

> [!NOTE]
> Användning av `modify` effekterna med alias är för närvarande en för **hands version**.

|Alias |Effekt | Resultat |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy lägger till hela matrisen som anges i informationen om den saknas. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` med `add` åtgärd | Azure Policy lägger till hela matrisen som anges i informationen om den saknas. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` med `addOrReplace` åtgärd | Azure Policy lägger till hela matrisen som anges i informationen om saknade eller ersätter den befintliga matrisen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy lägger till den mat ris medlem som anges i resultat informationen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` med `add` åtgärd | Azure Policy lägger till den mat ris medlem som anges i resultat informationen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` med `addOrReplace` åtgärd | Azure Policy tar bort alla befintliga mat ris medlemmar och lägger till den mat ris medlem som anges i resultat informationen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy lägger till ett värde i `action` egenskapen för varje mat ris medlem. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` med `add` åtgärd | Azure Policy lägger till ett värde i `action` egenskapen för varje mat ris medlem. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` med `addOrReplace` åtgärd | Azure Policy lägger till eller ersätter den befintliga `action` egenskapen för varje mat ris medlem. |

Mer information finns i [Lägg till exempel](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
