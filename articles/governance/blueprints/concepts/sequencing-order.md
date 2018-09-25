---
title: Förstå sekvensen för distribution i Azure skisser
description: Läs om livscykeln som passerar en skiss och information om varje steg.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955460"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Förstå sekvensen för distribution i Azure skisser

Azure skisser använder en **ordningsföljd** att fastställa ordningen för resursskapande vid bearbetning av tilldelningen av en skiss. Den här artikeln förklarar sekvensering ordning som används, hur du anpassar ordningen och hur anpassade beställning bearbetas.

Det finns variabler i JSON-exempel som du måste ersätta med dina egna värden:

- `{YourMG}` – Ersätt med namnet på hanteringsgruppen

## <a name="default-sequencing-order"></a>Standard ordningsföljd

Om skissen innehåller inga direktiv att distribuera artefakter eller direktivet är null, används följande ordning:

- Prenumerationsnivå **rolltilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Prenumerationsnivå **principtilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Prenumerationsnivå **Azure Resource Manager-mall** artefakter sorterade efter sammansättningsartefaktens namn
- **Resursgrupp** artefakter (inklusive underordnade artefakter) sorterade efter platshållarens namn

Inom varje **resursgrupp** artefakt som har bearbetats kan följande sekvens ordning används för artefakter som ska skapas i resursgruppen:

- Resource group underordnade **rolltilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Resource group underordnade **principtilldelning** artefakter sorterade efter sammansättningsartefaktens namn
- Resource group underordnade **Azure Resource Manager-mall** artefakter sorterade efter sammansättningsartefaktens namn

## <a name="customizing-the-sequencing-order"></a>Anpassa ordningsföljd

När du skriver stora skisser kan det vara nödvändigt för en resurs som ska skapas i en viss ordning i förhållande till en annan resurs. De vanligaste användningsmönstret för detta är när en skiss innehåller flera Azure Resource Manager-mallar. Skisser hanterar detta genom att låta ordningsföljd definieras.

Detta åstadkoms genom att definiera en `dependsOn` egenskap i JSON. Stöder den här egenskapen endast skissen (för resursgrupper) och artefakt-objekt. `dependsOn` är en strängmatris av artefaktnamn som viss artefakten måste skapas innan den har skapats.

### <a name="example---blueprint-with-ordered-resource-group"></a>Exempel – skissen med sorterad resursgruppen

Det här är ett exempel skissen med en resursgrupp som har definierat en anpassad ordningsföljd genom att deklarera ett värde för `dependsOn`, tillsammans med en standard resursgrupp. I det här fallet artefakten med namnet **assignPolicyTags** behandlas före den **sorterade rg** resursgrupp.
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

Det här är ett exempel princip artefakt som är beroende av en Azure Resource Manager-mall. Genom att standard beställa skapas en artefakt i princip innan Azure Resource Manager-mallen. På så sätt kan principen artefakten att vänta på Azure Resource Manager-mall som ska skapas.

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

Under skapandeprocessen används en topologisk sortering för att skapa beroendegrafen av skissen och dess artefakter. Detta säkerställer att flera nivåer av beroendet mellan resursgrupper och artefakter kan stödjas.

Om ett beroende har deklarerats på skissen eller en artefakt som skulle ändra standardordningen görs inga ändringar att i ordningsföljd. Exempel på detta är en resursgrupp som är beroende av en prenumeration på principen eller resurs grupp ”standard-rg” underordnade tilldelning av principer som är beroende av rolltilldelning i resursgruppen ”standard-rg” underordnade. I båda fallen kan de `dependsOn` inte har ändrat standard ordningsföljd och inga ändringar skulle göras.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [skiss livscykel](lifecycle.md)
- Förstå hur du använder [Statiska och dynamiska parametrar](parameters.md)
- Ta reda på hur du får använda [skiss resource låsning](resource-locking.md)
- Lär dig hur du [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)