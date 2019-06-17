---
title: Distribuera kapslade Resource Manager-mall-miljöer i Azure DevTest Labs | Microsoft Docs
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
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835292"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Distribuera kapslade Azure Resource Manager-mallar för testmiljöer
En kapslad distribution kan du utföra andra Azure Resource Manager-mallar från huvudsakliga Resource Manager-mall. Det kan du dela upp din distribution till en uppsättning riktade och specifika ändamål mallar. Det ger fördelar vad gäller testning, återanvändning och läsbarhet. Artikeln [använda länkade mallar när du distribuerar Azure-resurser](../azure-resource-manager/resource-group-linked-templates.md) ger en bra översikt över den här lösningen med flera kodexempel. Den här artikeln innehåller ett exempel som är specifik för Azure DevTest Labs. 

## <a name="key-parameters"></a>Nyckelparametrar
Du kan skapa en egen Resource Manager-mall från början, rekommenderar vi att du använder den [Azure-resursgruppsprojekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) i Visual Studio, vilket gör det enkelt att utveckla och felsöka mallar. När du lägger till en resurs för kapslade distributionen azuredeploy.json, Visual Studio lägger du till flera objekt om du vill att mallen ska vara mer flexibel. Dessa objekt omfattar undermappen med filen sekundära mall och parametrar och variabelnamn i den huvudsakliga mallfilen två parametrar för lagringsplatsen för de nya filerna. Den **_artifactsLocation** och **_artifactsLocationSasToken** är de viktigaste parametrarna som använder DevTest Labs. 

Om du inte känner till hur DevTest Labs fungerar med miljöer, se [skapa miljöer för flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). Mallarna lagras i databasen som är länkad till labb i DevTest Labs. När du skapar en ny miljö med dessa mallar kan flyttas filerna till en Azure Storage-behållare i laboratoriet. För att kunna identifiera och kopierar de underordnade filerna, labb identifierar parametrarna _artifactsLocation och _artifactsLocationSasToken och kopierar undermappar upp till behållaren. Sedan infogas automatiskt plats och token för signatur för delad åtkomst (SaS) i parametrar. 

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

Mappen i databasen som innehåller den här mallen har en undermapp `nestedtemplates` med filerna **NestOne.json** och **NestOne.parameters.json**. I den **azuredeploy.json**, URI: N för mallen har skapats med artefakter platsen, kapslade tjänstmall-mapp, kapslade mallnamnet. På samma sätt skapas URI för parametrar med artefakter platsen, kapslade tjänstmall-mapp och parameterfilen för den kapslade mallen. 

Här är en avbildning av samma projektstruktur i Visual Studio: 

![Projektstruktur i Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Du kan lägga till ytterligare mappar i mappen primära men inte alla djupare än en enda nivå. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för ytterligare information om miljöer: 

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Ansluta en miljö till ditt labb-nätverk i Azure DevTest Labs](connect-environment-lab-virtual-network.md)