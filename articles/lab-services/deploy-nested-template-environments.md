---
title: Distribuera kapslade mallmiljöer i Azure DevTest Labs
description: Lär dig hur du distribuerar kapslade Azure Resource Manager-mallar för att tillhandahålla miljöer med Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168826"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Distribuera kapslade Azure Resource Manager-mallar för testmiljöer
Med en kapslad distribution kan du köra andra Azure Resource Manager-mallar från en huvudmall för Resource Manager. Det gör att du kan bryta ned distributionen till en uppsättning riktade och ändamålsenliga mallar. Det ger fördelar när det gäller testning, återanvändning och läsbarhet. Artikeln [Använda länkade mallar när du distribuerar Azure-resurser](../azure-resource-manager/templates/linked-templates.md) ger en bra översikt över den här lösningen med flera kodexempel. Den här artikeln innehåller ett exempel som är specifikt för Azure DevTest Labs. 

## <a name="key-parameters"></a>Viktiga parametrar
Även om du kan skapa din egen Resource Manager-mall från grunden rekommenderar vi att du använder [Azure Resource Group-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) i Visual Studio, vilket gör det enkelt att utveckla och felsöka mallar. När du lägger till en kapslad distributionsresurs i azuredeploy.json lägger Visual Studio till flera objekt för att göra mallen mer flexibel. Dessa objekt inkluderar undermappen med den sekundära mall- och parameterfilen, variabelnamn i huvudmallfilen och två parametrar för lagringsplatsen för de nya filerna. De **_artifactsLocation** och **_artifactsLocationSasToken** är de viktigaste parametrarna som DevTest Labs använder. 

Om du inte är bekant med hur DevTest Labs fungerar med miljöer läser du [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). Mallarna lagras i databasen som är länkad till labbet i DevTest Labs. När du skapar en ny miljö med dessa mallar flyttas filerna till en Azure Storage-behållare i labbet. För att kunna identifiera och kopiera de kapslade filerna identifierar DevTest Labs parametrarna _artifactsLocation och _artifactsLocationSasToken och kopierar undermapparna till lagringsbehållaren. Sedan infogas automatiskt plats- och SaS-token (Shared Access Signature) i parametrar. 

## <a name="nested-deployment-example"></a>Exempel på kapslad distribution
Här är ett enkelt exempel på en kapslad distribution:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Mappen i databasen som innehåller den här `nestedtemplates` mallen har en undermapp med filerna **NestOne.json** och **NestOne.parameters.json**. I **azuredeploy.json**skapas URI för mallen med hjälp av artefaktplatsen, kapslad mallmapp, kapslad mallfilnamn. På samma sätt skapas URI för parametrarna med artefaktplatsen, den kapslade mallmappen och parameterfilen för den kapslade mallen. 

Här är bilden av samma projektstruktur i Visual Studio: 

![Projektstruktur i Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Du kan lägga till ytterligare mappar i den primära mappen men inte djupare än en enda nivå. 

## <a name="next-steps"></a>Nästa steg
Mer information om miljöer finns i följande artiklar: 

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Ansluta en miljö till labbets virtuella nätverk i Azure DevTest Labs](connect-environment-lab-virtual-network.md)