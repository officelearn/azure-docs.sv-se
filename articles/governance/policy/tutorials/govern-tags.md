---
title: 'Självstudiekurs: Hantera taggstyrning'
description: I den här självstudien använder du effekten Ändra av Azure-principen för att skapa och framtvinga en taggstyrningsmodell på nya och befintliga resurser.
ms.date: 04/21/2020
ms.topic: tutorial
ms.openlocfilehash: 6319bbde2fdc8f78e2743dd5f1565c8680433fea
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759059"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Självstudiekurs: Hantera taggstyrning med Azure Policy

[Taggar](../../../azure-resource-manager/management/tag-resources.md) är en viktig del av att organisera dina Azure-resurser till en taxonomi. När du följer [metodtips för tagghantering](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)kan taggar ligga till grund för att tillämpa dina affärsprinciper med Azure Policy eller [spåra kostnader med Kostnadshantering](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources).
Oavsett hur eller varför du använder taggar är det viktigt att du snabbt kan lägga till, ändra och ta bort dessa taggar på dina Azure-resurser. Information om du vill se om din Azure-resurs har stöd för taggning finns i [Taggstöd](../../../azure-resource-manager/management/tag-support.md).

Azure [Policy's Modify-effekt](../concepts/effects.md#modify) är utformad för att underlätta styrningen av taggar oavsett i vilket skede av resursstyrning du befinner dig i. **Ändra** hjälper när:

- Du är ny i molnet och har ingen taggstyrning
- Redan har tusentals resurser utan tagg styrning
- Redan har en befintlig taxonomi som du behöver ändras

I den här självstudien ska du utföra följande uppgifter:

> [!div class="checklist"]
> - Identifiera dina affärskrav
> - Mappa varje krav till en principdefinition
> - Gruppera taggprinciperna i ett initiativ

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="identify-requirements"></a>Identifiera krav

Liksom alla bra genomförande av styrning kontroller, bör kraven komma från ditt företags behov och vara väl förstådda innan du skapar tekniska kontroller. I den här självstudiekursen för scenariot är följande objekt våra affärskrav:

- Två nödvändiga taggar på alla resurser: _CostCenter_ och _Env_
- _CostCenter_ måste finnas på alla behållare och enskilda resurser
  - Resurser ärver från behållaren de befinner sig i, men kan åsidosättas individuellt
- _Env_ måste finnas på alla behållare och enskilda resurser
  - Resurser avgör miljön efter namnschema för behållare och får inte åsidosättas
  - Alla resurser i en behållare är en del av samma miljö

## <a name="configure-the-costcenter-tag"></a>Konfigurera CostCenter-taggen

När det gäller en Azure-miljö som _CostCenter_ hanteras av Azure Policy anropar CostCenter-taggkraven följande:

- Neka resursgrupper som saknar _CostCenter-taggen_
- Ändra resurser för att lägga till _CostCenter-taggen_ från den överordnade resursgruppen när den saknas

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Neka resursgrupper som saknar CostCenter-taggen

Eftersom _CostCenter_ för en resursgrupp inte kan bestämmas av namnet på resursgruppen måste taggen ha taggen definierad på begäran för att skapa resursgruppen. Följande principregel med effekten [Neka](../concepts/effects.md#deny) förhindrar att resursgrupper som inte har _CostCenter-taggen_ skapas eller uppdateras:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Eftersom den här principregeln är inriktad på en resursgrupp måste _läget_ för principdefinitionen vara "Alla" i stället för "Indexerad".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Ändra resurser för att ärva CostCenter-taggen när den saknas

Det andra _CostCenter-behovet_ är att alla resurser ska ärva taggen från den överordnade resursgruppen när den saknas. Om taggen redan har definierats för resursen, även om den skiljer sig från den överordnade resursgruppen, måste den lämnas ensam. Följande principregel använder [Ändra:](../concepts/effects.md#modify)

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Den här principregeln använder **åtgärden add** i stället för **addOrReplace** eftersom vi inte vill ändra taggvärdet om det finns när befintliga resurser [åtgärdas.](../how-to/remediate-resources.md) Den använder `[resourcegroup()]` också mallfunktionen för att hämta taggvärdet från den överordnade resursgruppen.

> [!NOTE]
> Eftersom den här principregeln inriktar sig på resurser som stöder taggar måste _läget_ för principdefinitionen vara indexerat. Den här konfigurationen säkerställer också att den här principen hoppar över resursgrupper.

## <a name="configure-the-env-tag"></a>Konfigurera taggen Env

När det gäller en Azure-miljö som _Env_ hanteras av Azure Policy anropar env-taggkraven följande:

- Ändra _env-taggen_ i resursgruppen baserat på resursgruppens namngivningsschema
- Ändra _Taggen Env_ för alla resurser i resursgruppen till samma som den överordnade resursgruppen

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Ändra resursgrupper Env-tagg baserat på namn

En [ändringsprincip](../concepts/effects.md#modify) krävs för varje miljö som finns i din Azure-miljö. Ändringsprincipen för varje ser ut ungefär som den här principdefinitionen:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Eftersom den här principregeln är inriktad på en resursgrupp måste _läget_ för principdefinitionen vara "Alla" i stället för "Indexerad".

Den här principen matchar endast resursgrupper med exempelnamnningsschemat som används för produktionsresurser i `prd-`. Mer komplexa namngivningsschema kan uppnås med flera **matchvillkor** i stället för den enda **som** i det här exemplet.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Ändra resurser för att ärva Taggen Env

Affärskravet kräver att alla resurser har den _Env-tagg_ som den överordnade resursgruppen har. Den här taggen kan inte åsidosättas, så vi använder åtgärden **addOrReplace** med effekten [Ändra.](../concepts/effects.md#modify) Exempelet Ändra princip ser ut som följande regel:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Eftersom den här principregeln inriktar sig på resurser som stöder taggar måste _läget_ för principdefinitionen vara indexerat. Den här konfigurationen säkerställer också att den här principen hoppar över resursgrupper.

Den här principregeln söker efter alla resurser som inte har värdet för överordnade resursgrupper för _taggen Env_ eller som saknar _taggen Env._ Matchande resurser har sin _Env-tagg_ inställd på värdet för de överordnade resursgrupperna, även om taggen redan fanns på resursen men med ett annat värde.

## <a name="assign-the-initiative-and-remediate-resources"></a>Tilldela initiativet och åtgärda resurser

När taggprinciperna ovan har skapats ansluter du dem till ett enda initiativ för taggstyrning och tilldelar dem till en hanteringsgrupp eller prenumeration. Initiativet och inkluderade principer utvärderar sedan efterlevnaden av befintliga resurser och ändrar begäranden om nya eller uppdaterade resurser som matchar **egenskapen if** i principregeln. Principen uppdaterar dock inte automatiskt befintliga icke-kompatibla resurser med de definierade taggändringarna.

Precis som [deployIfNotExists-principer](../concepts/effects.md#deployifnotexists) använder **principen Ändra** reparationsuppgifter för att ändra befintliga resurser som inte är kompatibla. Följ anvisningarna för [hur du åtgärdar resurser](../how-to/remediate-resources.md) för att identifiera dina icke-kompatibla **Ändra** resurser och korrigera taggarna till din definierade taxonomi.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med att arbeta med resurser i den här självstudien kan du använda följande steg för att ta bort tilldelningar eller definitioner som skapades ovan:

1. Välj **Definitioner** (eller **Tilldelningar** om du ska ta bort en tilldelning) under **Redigering** till vänster på sidan Azure Policy.

1. Sök efter den nya initiativ- eller principdefinition (eller tilldelning) som du vill ta bort.

1. Högerklicka på raden eller välj ellipserna i slutet av definitionen (eller tilldelningen) och välj **Ta bort definition** (eller **Ta bort tilldelning**).

## <a name="review"></a>Granska

I den här självstudien lärde du dig om följande uppgifter:

> [!div class="checklist"]
> - Identifierade dina affärskrav
> - Mappade varje krav till en principdefinition
> - Grupperade taggprinciperna i ett initiativ

## <a name="next-steps"></a>Nästa steg

Mer information om principdefinitionernas strukturer finns i den här artikeln:

> [!div class="nextstepaction"]
> [Azure Policy-definitionsstruktur](../concepts/definition-structure.md)