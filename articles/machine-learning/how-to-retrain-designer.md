---
title: Träna om modeller med hjälp av Azure Machine Learning designer (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar om modeller med publicerade pipelines i Azure Machine Learning designer (förhandsversion).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810387"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Omträningsmodeller med Azure Machine Learning Designer (förhandsversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder Azure Machine Learning designer för att omskola en maskininlärningsmodell. Du kommer att använda publicerade pipelines för att automatisera arbetsflödet och ställa in parametrar för att träna din modell på nya data. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Träna en maskininlärningsmodell.
> * Skapa en pipeline-parameter.
> * Publicera din träningspipeline.
> * Omskola din modell med nya parametrar.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta med Enterprise SKU.
* En datauppsättning som är tillgänglig för designern. Detta kan vara något av följande:
   * En Azure Machine Learning-registrerad datauppsättning
    
     **-eller...**
   * En datafil som lagras i ett Azure Machine Learning-datalager.
   
Information om dataåtkomst med designern finns [i Så här importerar du data till designern](how-to-designer-import-data.md).

Den här artikeln förutsätter också att du har grundläggande kunskaper om att bygga rörledningar i designern. För en guidad introduktion, fyll i [handledningen](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exempel på pipeline

Pipelinen som används i den här artikeln är en ändrad version av [Exempel 3: Inkomstprediktion](samples-designer.md#classification-samples). Pipelinen använder modulen [Importera data](algorithm-module-reference/import-data.md) i stället för exempeldatauppsättningen för att visa hur du tränar modeller med dina egna data.

![Skärmbild som visar den ändrade exempelpipelinen med en ruta som markerar modulen Importera data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Skapa en pipeline-parameter

Skapa pipelineparametrar för att dynamiskt ange variabler vid körning. I det här exemplet ändrar du sökvägen till träningsdata från ett fast värde till en parameter, så att du kan träna om modellen på olika data.

1. Välj modulen **Importera data.**

    > [!NOTE]
    > I det här exemplet används modulen Importera data för att komma åt data i ett registrerat datalager. Du kan dock följa liknande steg om du använder alternativa dataåtkomstmönster.

1. Välj datakällan till höger om arbetsytan i informationsfönstret för modul.

1. Ange sökvägen till dina data. Du kan också välja **Bläddra sökväg** för att bläddra i filträdet. 

1. Muspekaren över fältet **Bana** och markera ellipserna ovanför fältet **Bana** som visas.

    ![Skärmbild som visar hur du skapar en pipelineparameter](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Välj **Lägg till i pipelineparametern**.

1. Ange ett parameternamn och ett standardvärde.

   > [!NOTE]
   > Du kan granska och redigera pipelineparametrarna genom att välja ikonen **Inställningar** bredvid titeln på pipeline-utkastet. 

1. Välj **Spara**.

1. Skicka pipelinekörningen.

## <a name="find-a-trained-model"></a>Hitta en tränad modell

Designern sparar alla pipeline-utdata, inklusive tränade modeller, till standardkontot för lagringsutrymme på arbetsytan. Du kan också komma åt utbildade modeller direkt i designern:

1. Vänta tills pipelinen är klar.
1. Välj **träningsmodellmodulen**.
1. Välj Utdata + loggar till höger om arbetsytan i **informationsfönstret**för modulinformation .
1. Du hittar din modell i **Andra utdata** tillsammans med körloggar.
1. Du kan också välja ikonen **Visa utdata.** Härifrån kan du följa instruktionerna i dialogrutan för att navigera direkt till ditt datalager. 

![Skärmbild som visar hur du hämtar den tränade modellen](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publicera en träningspipeline

Publicera en pipeline till en pipeline-slutpunkt för att enkelt återanvända dina pipelines i framtiden. En pipeline-slutpunkt skapar en REST-slutpunkt för att anropa pipeline i framtiden. I det här exemplet kan du med pipelineslutpunkten återanvända pipelinen för att omskola en modell på olika data.

1. Välj **Publicera** ovanför designerarbetsytan.
1. Markera eller skapa en pipeline-slutpunkt.

   > [!NOTE]
   > Du kan publicera flera pipelines till en enda slutpunkt. Varje pipeline i en viss slutpunkt får ett versionsnummer som du kan ange när du anropar pipelineslutpunkten.

1. Välj **Publicera**.

## <a name="retrain-your-model"></a>Omskola din modell

Nu när du har en publicerad träningspipeline kan du använda den för att omskola din modell på nya data. Du kan skicka körs från en pipeline-slutpunkt från studioarbetsytan eller programmässigt.

### <a name="submit-runs-by-using-the-designer"></a>Skicka körs med hjälp av designern

Gör så här för att skicka in en parameteriserad pipeline-slutpunkt som körs från designern:

1. Gå till sidan **Slutpunkter** på studioarbetsytan.
1. Välj fliken **Pipeline-slutpunkter.** Välj sedan pipeline-slutpunkten.
1. Välj fliken **Publicerade pipelines.** Välj sedan den pipeline-version som du vill köra.
1. Välj **Skicka**.
1. I dialogrutan inställning kan du ange parametrarnas värden för körningen. I det här exemplet uppdaterar du datasökvägen för att träna din modell med hjälp av en datauppsättning som inte är usa.

![Skärmbild som visar hur du ställer in en parameteriserad pipelinekörning i designern](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Skicka körningar med hjälp av kod

Du hittar REST-slutpunkten för en publicerad pipeline på översiktspanelen. Genom att anropa slutpunkten kan du träna om den publicerade pipelinen.

Om du vill ringa ett REST-samtal behöver du ett autentiseringshuvud av typen OAuth 2.0-typ. Information om hur du konfigurerar autentisering på arbetsytan och gör ett parameteriserat REST-anrop finns i [Skapa en Azure Machine Learning-pipeline för batchbedömning](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en parameteriserad slutpunkt för träningspipelline med hjälp av designern.

En fullständig genomgång av hur du kan distribuera en modell för att göra förutsägelser finns i [designerhandledningen](tutorial-designer-automobile-price-train-score.md) för att träna och distribuera en regressionsmodell.
