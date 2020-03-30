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
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368170"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Omträningsmodeller med Azure Machine Learning Designer (förhandsversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder Azure Machine Learning designer för att omskola en maskininlärningsmodell. Ta reda på hur du använder publicerade pipelines för att automatisera maskininlärningsarbetsflöden för omskolning.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Träna en maskininlärningsmodell.
> * Skapa en pipeline-parameter.
> * Publicera din träningspipeline.
> * Omskola din modell.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://aka.ms/AMLFree).
* En Azure Machine Learning-arbetsyta med Enterprise SKU.

Den här artikeln förutsätter att du har grundläggande kunskaper om att bygga rörledningar i designern. För en guidad introduktion till designern, fyll i [handledningen](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exempel på pipeline

Pipelinen som används i den här artikeln är en ändrad version av den som finns i [Exempel 3: Inkomstprediktion](how-to-designer-sample-classification-predict-income.md). Den använder modulen [Importera data](algorithm-module-reference/import-data.md) i stället för exempeldatauppsättningen för att visa hur du tränar en modell med hjälp av dina egna data.

![Skärmbild som visar den ändrade exempelpipelinen med en ruta som markerar modulen Importera data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Träna en maskininlärningsmodell

För att träna om en modell behöver du en första modell. I det här avsnittet får du lära dig hur du tränar en modell och kommer åt den sparade modellen med hjälp av designern.

1. Välj modulen **Importera data.**
1. Ange en datakälla i egenskapsfönstret.

   ![Skärmbild som visar en exempelkonfiguration för modulen Importera data](./media/how-to-retrain-designer/import-data-settings.png)

   I det här exemplet lagras data i ett [Azure-datacenter](how-to-access-data.md). Om du inte redan har ett datalager kan du skapa ett nu genom att välja **Nytt datalager**.

1. Ange sökvägen till dina data. Du kan också välja **Bläddra sökväg** för att bläddra till ditt datalager. 
1. Välj **Kör** högst upp på arbetsytan.
    
   > [!NOTE]
   > Om du redan har angett en standardberäkning för det här pipeline-utkastet körs pipelinen automatiskt. Annars kan du följa anvisningarna i inställningsfönstret för att ställa in en nu.

### <a name="find-your-trained-model"></a>Hitta din tränade modell

Designern sparar alla pipelineutdata, inklusive tränade modeller, till standardlagringskontot. Du kan också komma åt den tränade modellen direkt i designern:

1. Vänta tills pipelinen är klar.
1. Välj **träningsmodellmodulen**.
1. Välj **Utdata+loggar i inställningsfönstret**.
1. Välj ikonen **Visa utdata** och följ instruktionerna i popup-fönstret för att hitta den tränade modellen.

![Skärmbild som visar hur du hämtar den tränade modellen](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Skapa en pipeline-parameter

Lägg till pipelineparametrar för att dynamiskt ange variabler vid körning. För den här pipelinen lägger du till en parameter för utbildningsdatasökvägen så att du kan träna om modellen på en ny datauppsättning.

1. Välj modulen **Importera data.**
1. Markera ellipserna ovanför fältet **Bana** i inställningsfönstret.
1. Välj **Lägg till i pipelineparametern**.
1. Ange ett parameternamn och ett standardvärde.

   > [!NOTE]
   > Du kan granska och redigera pipelineparametrarna genom att välja ikonen **Inställningar** bredvid titeln på pipeline-utkastet. 

![Skärmbild som visar hur du skapar en pipelineparameter](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publicera en träningspipeline

När du publicerar en pipeline skapas en pipeline-slutpunkt. Med pipelineslutpunkter kan du återanvända och hantera dina pipelines för repeterbarhet och automatisering. I det här exemplet har du ställt in pipelinen för omskolning.

1. Välj **Publicera** ovanför designerarbetsytan.
1. Markera eller skapa en pipeline-slutpunkt.

   > [!NOTE]
   > Du kan publicera flera pipelines till en enda slutpunkt. Varje pipeline i slutpunkten får ett versionsnummer som du kan ange när du anropar pipelineslutpunkten.

1. Välj **Publicera**.

## <a name="retrain-your-model"></a>Omskola din modell

Nu när du har en publicerad träningspipeline kan du använda den för att omskola din modell med hjälp av nya data. Du kan skicka körs från en pipeline-slutpunkt från Azure-portalen eller skicka dem programmässigt.

### <a name="submit-runs-by-using-the-designer"></a>Skicka körs med hjälp av designern

Gör så här för att skicka in en pipeline-slutpunkt som körs från designern:

1. Gå till sidan **Slutpunkter.**
1. Välj fliken **Pipeline-slutpunkter.**
1. Välj pipelineslutpunkten.
1. Välj fliken **Publicerade pipelines.**
1. Markera den pipeline som du vill köra.
1. Välj **Skicka**.
1. I dialogrutan Inställning kan du ange ett nytt värde för indatasökvägsvärdet. Det här värdet pekar på den nya datauppsättningen.

![Skärmbild som visar hur du ställer in en parameteriserad pipelinekörning i designern](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Skicka körningar med hjälp av kod

Du hittar REST-slutpunkten för en publicerad pipeline på översiktspanelen. Genom att anropa slutpunkten kan du träna om den publicerade pipelinen.

Om du vill ringa ett REST-samtal behöver du ett autentiseringshuvud av typen OAuth 2.0-typ. Information om hur du konfigurerar autentisering på arbetsytan och gör ett parameteriserat REST-anrop finns i [Skapa en Azure Machine Learning-pipeline för batchbedömning](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Nästa steg

Följ [designerns handledning](tutorial-designer-automobile-price-train-score.md) för att träna och distribuera en regressionsmodell.
