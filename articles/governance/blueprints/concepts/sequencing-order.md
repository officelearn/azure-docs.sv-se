---
title: Förstå distributionssekvensordningen
description: Lär dig mer om standardordningen som skissartefakter distribueras i under en skisstilldelning och hur du anpassar distributionsordern.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677566"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Förstå distributionssekvensen i Azure Blueprints

Azure Blueprints använder en **sekvenseringsorder för** att bestämma ordningen för att skapa resurser när tilldelningen av en skissdefinition bearbetas. I den här artikeln beskrivs följande begrepp:

- Standardsekvensordningen som används
- Så här anpassar du ordern
- Hur den anpassade ordern bearbetas

Det finns variabler i JSON-exemplen som du behöver ersätta med dina egna värden:

- `{YourMG}` – Ersätt med namnet på din hanteringsgrupp

## <a name="default-sequencing-order"></a>Standardsekvensordning

Om skissdefinitionen inte innehåller något direktiv för ordern att distribuera artefakter eller om direktivet är null används följande ordning:

- Artefakter för **rolltilldelning** på prenumerationsnivå sorterade efter artefaktnamn
- Artefakter för **tilldelning av prenumerationsnivå princip** som sorterats efter artefaktnamn
- Azure **Resource Manager-mallartefakter** på prenumerationsnivå sorterade efter artefaktnamn
- **Artefakter i resursgrupp** (inklusive underordnade artefakter) sorterade efter platshållarnamn

Inom varje **resursgruppsartefakt** används följande sekvensordning för artefakter som ska skapas inom resursgruppen:

- Underordnade **rolltilldelningsartefakter** för resursgrupp sorterade efter artefaktnamn
- Underordnade **principtilldelningsartefakter** för resursgrupp sorterade efter artefaktnamn
- Resursgrupp **underordnade Azure Resource Manager-mallartefakter** sorterade efter artefaktnamn

> [!NOTE]
> Användning av [artifacts()](../reference/blueprint-functions.md#artifacts) skapar ett implicit beroende av artefakten som refereras till.

## <a name="customizing-the-sequencing-order"></a>Anpassa sekvenseringsordningen

När du skriver stora skissdefinitioner kan det vara nödvändigt att skapa resurser i en viss ordning. Det vanligaste användningsmönstret för det här scenariot är när en skissdefinition innehåller flera Azure Resource Manager-mallar. Azure Blueprints hanterar det här mönstret genom att tillåta att sekvenseringsordningen definieras.

Beställningen sker genom att definiera `dependsOn` en egenskap i JSON. Skissdefinitionen för resursgrupper och artefaktobjekt stöder den här egenskapen. `dependsOn`är en strängmatris med artefaktnamn som den specifika artefakten måste skapas innan den skapas.

> [!NOTE]
> När du skapar skissobjekt får varje artefaktresurs sitt namn från filnamnet, om du använder [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)eller URL-slutpunkten, om du använder [REST API](/rest/api/blueprints/artifacts/createorupdate).
> _resourceGroup-referenser_ i artefakter måste matcha dem som definieras i skissdefinitionen.

### <a name="example---ordered-resource-group"></a>Exempel - ordnad resursgrupp

Det här exemplet skissdefinition har en resursgrupp som har definierat en `dependsOn`anpassad sekvenseringsordning genom att deklarera ett värde för , tillsammans med en standardresursgrupp. I det här fallet bearbetas artefakten **assignPolicyTags** före resursgruppen **beställd rg.**
**standard-rg** kommer att bearbetas enligt standardsekvenseringsordningen.

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
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Exempel - artefakt med anpassad ordning

Det här exemplet är en principartefakt som är beroende av en Azure Resource Manager-mall. Som standardbeställning skapas en principartefakt innan Azure Resource Manager-mallen. Med den här ordningen kan principartefakten vänta på att Azure Resource Manager-mallen skapas.

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
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exempel - mallartefakt på prenumerationsnivå beroende på en resursgrupp

Det här exemplet är att en Resource Manager-mall som distribueras på prenumerationsnivå ska vara beroende av en resursgrupp. I standardordning skapas artefakter på prenumerationsnivå innan några resursgrupper och underordnade artefakter i dessa resursgrupper. Resursgruppen definieras i skissdefinitionen så här:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Mallartefakten för prenumerationsnivå beroende på resursgruppen **vänta-för-mig** definieras så här:

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
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Bearbeta den anpassade sekvensen

Under skapandeprocessen används en topologisk sortering för att skapa beroendediagrammet för ritningarna artefakter. Kontrollen säkerställer att varje beroendenivå mellan resursgrupper och artefakter stöds.

Om ett artefaktberoende deklareras som inte skulle ändra standardordningen görs ingen ändring. Ett exempel är en resursgrupp som är beroende av en princip på prenumerationsnivå. Ett annat exempel är en resursgrupp för "standard-rg" underordnad principtilldelning som är beroende av resursgruppens underordnade rolltilldelning för standard-rg. I båda fallen `dependsOn` skulle inte ha ändrat standardsekvensordningen och inga ändringar skulle göras.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [skisslivscykeln](lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Ta reda på hur du använder [skiss resurs låsning](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).