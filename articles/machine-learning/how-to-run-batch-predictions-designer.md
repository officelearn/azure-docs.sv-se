---
title: Köra batchförutsägelser med Azure Machine Learning Designer (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar en modell och konfigurerar en pipeline för batchförutsägels med hjälp av designern. Distribuera pipelinen som en parameteriserad webbtjänst, som kan utlösas från alla HTTP-bibliotek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477229"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Köra batchförutsägelser med Azure Machine Learning Designer (förhandsversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder designern för att skapa en pipeline för batchförutsägelser. Med batchförutsägelser kan du kontinuerligt poängsätta stora datauppsättningar på begäran med hjälp av en webbtjänst som kan utlösas från alla HTTP-bibliotek.

I det här in-programmet lär du dig att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa och publicera en pipeline för inferens för batch
> * Förbruka en slutpunkt för pipeline
> * Hantera slutpunktsversioner

Mer information om hur du ställer in batchbedömningstjänster med SDK finns i den medföljande [how-to](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Krav

Detta instruktioner förutsätter att du redan har en utbildning pipeline. För en guidad introduktion till designern, fyll [i del ett av designern handledning](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-batch-inference-pipeline"></a>Skapa en pipeline för inferens för batch

Din träningspipeline måste köras minst en gång för att kunna skapa en inferencing pipeline.

1. Gå till fliken **Designer** på arbetsytan.

1. Välj den träningspipeline som tränar den modell som du vill använda för att göra förutsägelse.

1. **Skicka** pipelinen.

    ![Skicka pipelinen](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Nu när träningspipelinen har körts kan du skapa en pipeline för batch inferens.

1. Välj den nya listrutan **Skapa inferenspipeline bredvid** **Skicka.**

1. Välj **Batch-inferenspipeline**.

    ![Skapa pipeline för inferens för batch](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Resultatet är en standardbatchingledning för inferens. 

### <a name="add-a-pipeline-parameter"></a>Lägga till en pipelineparameter

Om du vill skapa förutsägelser om nya data kan du antingen manuellt ansluta en annan datauppsättning i den här utkastvyn för pipeline eller skapa en parameter för datauppsättningen. Med parametrar kan du ändra beteendet för batchom uppstår vid körning.

I det här avsnittet skapar du en datauppsättningsparameter för att ange en annan datauppsättning att göra förutsägelser på.

1. Välj datauppsättningsmodulen.

1. En ruta visas till höger om arbetsytan. Längst ned i fönstret väljer du **Ange som pipeline-parameter**.
   
    Ange ett namn för parametern eller acceptera standardvärdet.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publicera din batch inferencing pipeline

Nu är du redo att distribuera inferencing pipeline. Detta distribuerar pipelinen och gör den tillgänglig för andra att använda.

1. Välj sedan knappen **Publicera**.

1. Expandera listrutan för **PipelineEndpoint**i dialogrutan som visas och välj **Ny PipelineEndpoint**.

1. Ange ett slutpunktsnamn och valfri beskrivning.

    Längst ned i dialogrutan kan du se parametern som du har konfigurerat med ett standardvärde för det datauppsättnings-ID som användes under träningen.

1. Välj **Publicera**.

![Publicera en pipeline](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Använda en slutpunkt

Nu har du en publicerad pipeline med en datauppsättningsparameter. Pipelinen använder den tränade modellen som skapats i träningspipelinen för att få den datauppsättning som du anger som parameter.

### <a name="submit-a-pipeline-run"></a>Skicka en pipeline-körning 

I det här avsnittet ställer du in en manuell pipeline-körning och ändrar pipelineparametern för att få nya data. 

1. När distributionen är klar går du till avsnittet **Slutpunkter.**

1. Välj **Pipeline-slutpunkter**.

1. Välj namnet på den slutpunkt som du skapade.

![Länk till slutpunkt](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Välj **Publicerade pipelines**.

    På den här skärmen visas alla publicerade pipelines som publicerats under den här slutpunkten.

1. Välj den pipeline som du publicerade.

    På sidan pipelineinformation visas en detaljerad körningshistorik och anslutningsstränginformation för pipelinen. 
    
1. Välj **Skicka** om du vill skapa en manuell körning av pipelinen.

    ![Information om pipeline](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Ändra parametern om du vill använda en annan datauppsättning.
    
1. Välj **Skicka** för att köra pipelinen.

### <a name="use-the-rest-endpoint"></a>Använda REST-ändpunkten

Du kan hitta information om hur du använder pipeline-slutpunkter och publicerad pipeline i avsnittet **Slutpunkter.**

Du hittar REST-slutpunkten för en pipeline-slutpunkt på översiktspanelen för körning. Genom att anropa slutpunkten förbrukar du standardportuten pipeline.

Du kan också använda en publicerad pipeline på sidan **Publicerade pipelines.** Välj en publicerad pipeline och leta reda på REST-slutpunkten för den. 

![Information om restslutpunkt](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Om du vill ringa ett REST-samtal behöver du ett autentiseringshuvud av OAuth 2.0-typ. Mer information om hur du konfigurerar autentisering på arbetsytan finns i följande [självstudieavsnitt.](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)

## <a name="versioning-endpoints"></a>Slutpunkter för versionshantering

Designern tilldelar en version till varje efterföljande pipeline som du publicerar till en slutpunkt. Du kan ange den pipeline-version som du vill köra som en parameter i REST-anropet. Om du inte anger något versionsnummer använder designern standardpipelinen.

När du publicerar en pipeline kan du välja att göra den till den nya standardpipelinen för den slutpunkten.

![Ange standardpipeline](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Du kan också ange en ny standardpipeline på fliken **Publicerade pipelines** på slutpunkten.

![Ange standardpipeline](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Nästa steg

Följ [designerns handledning](tutorial-designer-automobile-price-train-score.md) för att träna och distribuera en regressionsmodell.
''