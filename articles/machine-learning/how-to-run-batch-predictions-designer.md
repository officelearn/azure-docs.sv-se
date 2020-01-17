---
title: Köra batch-förutsägelser med Azure Machine Learning designer (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar en modell och konfigurerar en pipeline för batch förutsägelse med hjälp av designern. Distribuera pipelinen som en parametriserad webb tjänst, som kan utlösas från alla HTTP-bibliotek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: 1e346d2542193ec1880ad0a56bd6afa1b0a46890
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122635"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Köra batch-förutsägelser med Azure Machine Learning designer
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här instruktionen får du lära dig hur du använder designern för att träna en modell och konfigurera en pipeline för batch förutsägelse och webb tjänst. Med batch-förutsägelse kan du utföra kontinuerliga resultat på begäran av tränade modeller på stora data mängder, om du vill, konfigureras som en webb tjänst som kan utlösas från alla HTTP-bibliotek. 

Information om hur du ställer in batch-Bedömningstjänster med SDK finns i den medföljande [instruktionen](how-to-use-parallel-run-step.md).

I den här instruktionen får du lära dig följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Basic ML-experiment i en pipeline
> * Skapa en pipeline för batch-härledning för batch
> * Hantera och köra pipelines manuellt eller från en REST-slutpunkt

## <a name="prerequisites"></a>Krav

1. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

1. Skapa en [arbets yta](tutorial-1st-experiment-sdk-setup.md).

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).

Den här instruktionen förutsätter grundläggande kunskaper om att skapa en enkel pipeline i designern. För en guidad introduktion till designern, slutför du [själv studie kursen](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Skapa en pipeline

Om du vill skapa en pipeline för batch-härledning måste du först köra ett Machine Learning-experiment. Om du vill skapa en, navigerar du till fliken **Designer** i arbets ytan och skapar en ny pipeline genom att välja alternativet **lättanvända fördefinierade moduler** .

![Designer-start sida](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

Följande är en enkel maskin inlärnings modell i demonstrations syfte. Data är en registrerad data uppsättning som skapats från Azure Open data uppsättningar diabetes-data. Se [avsnittet instruktion för att](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) registrera data uppsättningar från Azure Open DataSet. Informationen är uppdelad i utbildning och validerings uppsättningar och ett utökat besluts träd har tränats och betyget. Pipelinen måste köras minst en gång för att det ska gå att skapa en inferencing-pipeline. Klicka på knappen **Kör** för att köra pipelinen.

![Skapa ett enkelt experiment](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Skapa en pipeline för batch-härledning

Nu när pipelinen har körts finns det ett nytt alternativ som är tillgängligt bredvid **Kör** och **publicera** som kallas **skapa härlednings pipeline**. Klicka på list rutan och välj **pipeline för batch-härledning**.

![Skapa en pipeline för batch-härledning](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

Resultatet är en standard pipeline för batch-härledning. Detta inkluderar en nod för den ursprungliga installationen av installations programmet för pipeline, en nod för rå data för poängsättning och en nod för att skapa rå data mot den ursprungliga pipelinen.

![Standard pipeline för batch-härledning](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

Du kan lägga till andra noder för att ändra beteendet för batch inferencing-processen. I det här exemplet lägger du till en nod för slumpmässigt sampling från indata innan du påsöker. Skapa en **partition och en exempel** nod och placera den mellan noderna rå data och poäng. Klicka sedan på noden **partition och exempel** för att få åtkomst till inställningarna och parametrarna.

![Ny nod](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

*Frekvensen för samplings* parametern styr hur stor andel av den ursprungliga data uppsättningen som ska ta ett slumpmässigt exempel från. Det här är en parameter som kan vara användbar för att justera ofta, så att du aktiverar den som en pipeline-parameter. Pipeline-parametrar kan ändras vid körning och kan anges i ett nytto Last objekt när pipelinen körs på en REST-slutpunkt. 

Om du vill aktivera det här fältet som en pipeline-parameter klickar du på ellipsen ovanför fältet och klickar sedan på **Lägg till i pipeline-parameter**. 

![Exempel inställningar](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Ange sedan parameterns namn och standardvärde. Namnet används för att identifiera parametern och anger den i ett REST-anrop.

![Pipeline-parameter](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Distribuera batch inferencing pipeline

Nu är du redo att distribuera pipelinen. Klicka på knappen **distribuera** , så öppnas gränssnittet för att skapa en slut punkt. Klicka på list rutan och välj **ny PipelineEndpoint**.

![Pipeline-distribution](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Ge slut punkten ett namn och en valfri beskrivning. Längst ned visas `sample-rate` parametern som du konfigurerade med standardvärdet 0,8. När du är klar klickar du på **distribuera**.

![Installations slut punkt](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Hantera slutpunkter 

När distributionen är klar går du till fliken **slut punkter** och klickar på namnet på den slut punkt som du nyss skapade.

![Slut punkts länk](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

Den här skärmen visar alla publicerade pipeliner under den angivna slut punkten. Klicka på din inferencing-pipeline.

![Härlednings pipeline](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

På sidan pipeline-information visas detaljerad information om körnings historik och anslutnings sträng för din pipeline. Klicka på knappen **Kör** för att skapa en manuell körning av pipelinen.

![Pipeline-information](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

I installations programmet för kör kan du ange en beskrivning av körningen och ändra värdet för eventuella pipeline-parametrar. Den här gången kör du inferencing-pipeline igen med en samplings frekvens på 0,9. Klicka på **Kör** för att köra pipelinen.

![Pipeline-körning](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

Fliken **förbrukare** innehåller REST-slutpunkten för att köra pipelinen igen. Om du vill göra ett rest-anrop behöver du ett OAuth 2,0-värde för Authentication-typ. I följande [själv studie kurs](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) finns mer information om hur du konfigurerar autentisering till din arbets yta och gör ett parameter rest-anrop.

## <a name="next-steps"></a>Nästa steg

Följ [själv studie kursen](tutorial-designer-automobile-price-train-score.md) om du vill träna och distribuera en Regressions modell.
