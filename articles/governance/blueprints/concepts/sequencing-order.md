---
title: Förstå sekvensen distributionsordning
description: Läs om livscykeln för som en skissdefinitionen passerar och information om varje steg.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5552e44fcca056bd4fd5b4fd19559adfbd005444
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266196"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Förstå sekvensen för distribution i Azure skisser

Azure skisser använder en **ordningsföljd** att fastställa ordningen för resursskapande vid bearbetning av tilldelning av skissdefinitionen. Den här artikeln beskriver följande begrepp:

- Sekvensering ordning som används
- Hur du anpassar ordningen
- Hur anpassade beställning bearbetas

Det finns variabler i JSON-exempel som du måste ersätta med dina egna värden:

- `{YourMG}` – Ersätt med namnet på hanteringsgruppen

## <a name="default-sequencing-order"></a>Standard ordningsföljd

Om skissdefinitionen innehåller inga direktiv att distribuera artefakter eller direktivet är null, används följande ordning:

- Prenumerationsnivå **rolltilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Prenumerationsnivå **principtilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Prenumerationsnivå **Azure Resource Manager-mall** artefakter sorterade efter sammansättningsartefaktens namn
- **Resursgrupp** artefakter (inklusive underordnade artefakter) sorterade efter platshållarens namn

Inom varje **resursgrupp** artefakt följande sekvens ordning används för artefakter som ska skapas i resursgruppen:

- Resource group underordnade **rolltilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Resource group underordnade **principtilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Resource group underordnade **Azure Resource Manager-mall** artefakter sorterade efter sammansättningsartefaktens namn

## <a name="customizing-the-sequencing-order"></a>Anpassa ordningsföljd

När du skriver stora skissdefinitioner, kan det vara nödvändigt för resurser som ska skapas i en viss ordning. De vanligaste användningsmönstret för det här scenariot är när en skissdefinitionen innehåller flera Azure Resource Manager-mallar. Skisser hanterar det här mönstret genom att låta ordningsföljd definieras.

Sorteringen åstadkoms genom att definiera en `dependsOn` egenskap i JSON. Skissdefinitionen, stöder den här egenskapen för resursgrupper och artefakt-objekt. `dependsOn` är en strängmatris av artefaktnamn som viss artefakten måste skapas innan den har skapats.

### <a name="example---ordered-resource-group"></a>Exempel – sorterade resursgrupp

Det här exemplet skissdefinitionen har en resursgrupp som har definierat en anpassad ordningsföljd genom att deklarera ett värde för `dependsOn`, tillsammans med en standard resursgrupp. I det här fallet artefakten med namnet **assignPolicyTags** behandlas före den **sorterade rg** resursgrupp.
**standard-rg** bearbetas per sekvensering ordning.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Exempel - artefakt av anpassad ordning

Det här exemplet är en princip-artefakt som är beroende av en Azure Resource Manager-mall. Genom att standard beställa skapas en artefakt i princip innan Azure Resource Manager-mallen. Den här sorteringen kan principen artefakten att vänta på Azure Resource Manager-mall som ska skapas.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exempel – prenumerationen på mallen artefakt beroende på en resursgrupp

Det här exemplet är för en Resource Manager-mall som har distribuerats på prenumerationsnivå förlita sig på en resursgrupp. I ordning, skulle prenumeration på artefakter skapas innan alla resursgrupper och underordnade artefakter i de resursgrupperna. Resursgruppen har definierats i skissdefinitionen så här:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Prenumeration på mallen artefakten beroende på den **wait-för-mig** resursgrupp definieras så här:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>Anpassade Bearbetningsordning

Under skapandeprocessen används en topologisk sortering för att skapa beroendegrafen av skisser artefakter. Kontrollen ser till att det finns stöd för varje nivå i beroende mellan resursgrupper och artefakter.

Om beroende av ett artefakten har deklarerats som skulle ändra standardordningen, görs ingen ändring. Ett exempel är en resursgrupp som är beroende av en princip på prenumerationsnivå. Ett annat exempel är en grupp standard-rg underordnade princip resurstilldelning som är beroende av rolltilldelning i resursgruppen ”standard-rg” underordnade. I båda fallen kan de `dependsOn` inte har ändrat standard ordningsföljd och inga ändringar skulle göras.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).