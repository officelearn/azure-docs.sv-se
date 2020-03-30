---
title: Information om principtilldelningsstrukturen
description: Beskriver principtilldelningsdefinitionen som används av Azure Policy för att relatera principdefinitioner och parametrar till resurser för utvärdering.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265303"
---
# <a name="azure-policy-assignment-structure"></a>Tilldelningsstruktur i Azure Policy

Principtilldelningar används av Azure Policy för att definiera vilka resurser som tilldelas vilka principer eller initiativ. Principtilldelningen kan bestämma värdena för parametrar för den resursgruppen vid tilldelningstiden, vilket gör det möjligt att återanvända principdefinitioner som behandlar samma resursegenskaper med olika behov för efterlevnad.

Du använder JSON för att skapa en principtilldelning. Principtilldelningen innehåller element för:

- visningsnamn
- description
- metadata
- verkställighetsläge
- principdefinition
- parameters

Följande JSON visar till exempel en principtilldelning i _DoNotEnforce-läge_ med dynamiska parametrar:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Alla Azure-principexempel finns på [Azure Policy-exempel](../samples/index.md).

## <a name="display-name-and-description"></a>Visa namn och beskrivning

Du använder **displayName** och **beskrivning** för att identifiera principtilldelningen och tillhandahålla kontext för dess användning med den specifika uppsättningen resurser. **displayName** har en maximal längd på _128_ tecken och **beskriver** en maximal längd på _512_ tecken.

## <a name="enforcement-mode"></a>Verkställighetsläge

**Egenskapen enforcementMode** ger kunderna möjlighet att testa resultatet av en princip på befintliga resurser utan att initiera principeffekten eller utlösa poster i [Azure Activity-loggen](../../../azure-monitor/platform/platform-logs-overview.md). Det här scenariot kallas ofta "Vad händer om" och anpassar sig till säker distributionspraxis. **enforcementMode** skiljer sig från effekten [Inaktiverad,](./effects.md#disabled) eftersom den effekten förhindrar att resursutvärdering överst.

Den här egenskapen har följande värden:

|Läge |JSON-värde |Typ |Åtgärda manuellt |Aktivitetsloggpost |Beskrivning |
|-|-|-|-|-|-|
|Enabled |Default |sträng |Ja |Ja |Principeffekten tillämpas när resurser skapas eller uppdateras. |
|Disabled |DoNotEnforce (DoNotEnforce) |sträng |Ja |Inga | Principeffekten tillämpas inte när resurser skapas eller uppdateras. |

Om **enforcementMode** inte anges i en princip- eller initiativdefinition används värdet _Standard._ [Reparationsuppgifter](../how-to/remediate-resources.md) kan startas för [distributionOmutexistera](./effects.md#deployifnotexists) principer, även när **enforcementMode** är inställt på _DoNotEnforce_.

## <a name="policy-definition-id"></a>Principdefinitions-ID

Det här fältet måste vara det fullständiga sökvägsnamnet för antingen en principdefinition eller en initiativdefinition.
`policyDefinitionId`är en sträng och inte en matris. Vi rekommenderar att du använder ett [initiativ](./definition-structure.md#initiatives) om flera principer ofta tilldelas tillsammans.

## <a name="parameters"></a>Parametrar

Det här segmentet i principtilldelningen innehåller värdena för de parametrar som definieras i [principdefinitionen eller initiativdefinitionen](./definition-structure.md#parameters).
Den här designen gör det möjligt att återanvända en princip- eller initiativdefinition med olika resurser, men kontrollera om det finns olika affärsvärden eller resultat.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

I det här exemplet är `prefix` de parametrar `suffix`som tidigare definierats i principdefinitionen och . Den här `prefix` principtilldelningen anger `suffix` **depta** och **-LC**. Samma principdefinition kan återanvändas med en annan uppsättning parametrar för en annan avdelning, vilket minskar dubbelarbete och komplexitet i principdefinitioner samtidigt som flexibiliteten ges.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [principdefinitionsstrukturen](./definition-structure.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).