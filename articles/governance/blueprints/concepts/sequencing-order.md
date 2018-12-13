---
title: Förstå sekvensen distributionsordning
description: Läs om livscykeln som passerar en skiss och information om varje steg.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bd12aabf0ca8f82261e6b3c677d7306ee46c4171
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308625"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Förstå sekvensen för distribution i Azure skisser

Azure skisser använder en **ordningsföljd** att fastställa ordningen för resursskapande vid bearbetning av tilldelningen av en skiss. Den här artikeln beskriver följande begrepp:

- Sekvensering ordning som används
- Hur du anpassar ordningen
- Hur anpassade beställning bearbetas

Det finns variabler i JSON-exempel som du måste ersätta med dina egna värden:

- `{YourMG}` – Ersätt med namnet på din hanteringsgrupp

## <a name="default-sequencing-order"></a>Standard ordningsföljd

Om skissen innehåller inga direktiv att distribuera artefakter eller direktivet är null, används följande ordning:

- Prenumerationsnivå **rolltilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Prenumerationsnivå **principtilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Prenumerationsnivå **Azure Resource Manager-mall** artefakter sorterade efter sammansättningsartefaktens namn
- **Resursgrupp** artefakter (inklusive underordnade artefakter) sorterade efter platshållarens namn

Inom varje **resursgrupp** artefakt följande sekvens ordning används för artefakter som ska skapas i resursgruppen:

- Resource group underordnade **rolltilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Resource group underordnade **principtilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Resource group underordnade **Azure Resource Manager-mall** artefakter sorterade efter sammansättningsartefaktens namn

## <a name="customizing-the-sequencing-order"></a>Anpassa ordningsföljd

När du skriver stora skisser kan det vara nödvändigt för resurser som ska skapas i en viss ordning. De vanligaste användningsmönstret för det här scenariot är när en skiss innehåller flera Azure Resource Manager-mallar. Skisser hanterar det här mönstret genom att låta ordningsföljd definieras.

Sorteringen åstadkoms genom att definiera en `dependsOn` egenskap i JSON. Stöder den här egenskapen endast skissen (för resursgrupper) och artefakt-objekt. `dependsOn` är en strängmatris av artefaktnamn som viss artefakten måste skapas innan den har skapats.

> [!NOTE]
> **Resursgrupp** artefakter stöder den `dependsOn` egenskap, men får inte vara målet för en `dependsOn` av någon typ av artefakt.

### <a name="example---blueprint-with-ordered-resource-group"></a>Exempel – skissen med sorterad resursgruppen

Det här exemplet skissen har en resursgrupp som har definierat en anpassad ordningsföljd genom att deklarera ett värde för `dependsOn`, tillsammans med en standard resursgrupp. I det här fallet artefakten med namnet **assignPolicyTags** behandlas före den **sorterade rg** resursgrupp. **standard-rg** bearbetas per sekvensering ordning.

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

## <a name="processing-the-customized-sequence"></a>Anpassade Bearbetningsordning

Under skapandeprocessen används en topologisk sortering för att skapa beroendegrafen av skisser artefakter. Kontrollen ser till att det finns stöd för varje nivå i beroende mellan resursgrupper och artefakter.

Om beroende av ett artefakten har deklarerats som skulle ändra standardordningen, görs ingen ändring. Ett exempel är en resursgrupp som är beroende av en princip på prenumerationsnivå. Ett annat exempel är en grupp standard-rg underordnade princip resurstilldelning som är beroende av rolltilldelning i resursgruppen ”standard-rg” underordnade. I båda fallen kan de `dependsOn` inte har ändrat standard ordningsföljd och inga ändringar skulle göras.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md)
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)