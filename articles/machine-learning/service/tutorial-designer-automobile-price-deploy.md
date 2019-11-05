---
title: 'Självstudie: Distribuera en maskin inlärnings modell med designern'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en förutsägelse analys lösning i Azure Machine Learning designer (för hands version). Träna, score och distribuera en maskin inlärnings modell med hjälp av dra och släpp moduler.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 69fba508eac4b778dcd72371fd1471625ecb8c1a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501605"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Självstudie: Distribuera en maskin inlärnings modell med designer (för hands version)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Om du vill ge andra möjlighet att använda den förutsägelse modell som utvecklats i [del ett av själv studie kursen](tutorial-designer-automobile-price-train-score.md)kan du distribuera den som en slut punkt i real tid. I del 1 har du tränat din modell. Nu är det dags att generera nya förutsägelser baserat på användarindata. I den här delen av självstudien:

> [!div class="checklist"]
> * Distribuera en slut punkt i real tid
> * Skapa ett inferencing-kluster
> * Testa en slut punkt i real tid

## <a name="prerequisites"></a>Förutsättningar

Slutför [del ett av självstudien](tutorial-designer-automobile-price-train-score.md) och lär dig hur du tränar och poängs ätter en maskin inlärnings modell i designern.

## <a name="deploy-a-real-time-endpoint"></a>Distribuera en slut punkt i real tid

För att kunna distribuera din pipeline måste du:

1. Omvandla utbildnings pipelinen till en pipeline för real tids härledning, som tar bort moduler för utbildning och lägger till indata och utdata för inferencing-begäranden.
1. Distribuera härlednings pipelinen.

### <a name="create-a-real-time-inference-pipeline"></a>Skapa en pipeline för real tids härledning

1. Överst på pipeline-arbetsytan väljer du **skapa härlednings pipeline** > **real tids härlednings pipeline**

    När du väljer **skapa en härlednings pipeline**inträffar flera saker:
    
    * Den tränade modellen lagras som en **data uppsättnings** modul i modulens palett. Du kan hitta den under **mina data uppsättningar**.
    * Moduler, som att **träna modell** och **dela data**, tas bort.
    * Den sparade utbildade modellen läggs tillbaka i pipelinen.
    * Moduler för **webb tjänst indata** och **webb tjänstens utdata** läggs till. Dessa moduler identifierar var användar data kommer att ange modellen och var data returneras.

    > [!Note]
    > **Inlärnings pipelinen** sparas under den nya fliken överst i pipeline-arbetsytan. Det kan också finnas som en publicerad pipeline i designern.
    >

    Din pipeline bör nu se ut så här:  

   ![Skärm bild som visar den förväntade konfigurationen för pipelinen när den har förberedats för distribution](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

1. Välj **Kör** och Använd samma beräknings mål och experiment som du använde i del 1.

1. Välj modulen **Poäng modell** .

1. I fönstret Egenskaper väljer du **utdata** > **visualisera** för att kontrol lera att modellen fortfarande fungerar. Du kan se att ursprungliga data visas tillsammans med det förväntade priset ("Poäng etiketter").

1. Välj **Distribuera**.

### <a name="create-an-inferencing-cluster"></a>Skapa ett inferencing-kluster

I dialog rutan som visas kan du välja från befintliga Azure Kubernetes service-kluster (AKS) i din arbets yta för att distribuera din modell. Om du inte har något AKS-kluster kan du använda följande steg för att skapa ett.

1. Välj **Compute** i dialog rutan för att navigera till **beräknings** sidan.

1. I menyfliksområdet navigering väljer du utgångs **kluster** >  **+ ny**.

    ![Skärm bild som visar hur du navigerar till fönstret nytt fönster för utlåsnings kluster](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Konfigurera en ny Kubernetes-tjänst i fönstret fönster för utlåsnings kluster.

1. Ange "AKS-Compute" för **beräknings namnet**.
    
1. Välj en tillgänglig **region**i närheten.

1. Välj **Skapa**.

    > [!Note]
    > Det tar cirka 15 minuter att skapa en ny AKS-tjänst. Du kan kontrol lera etablerings statusen på sidan för att lösa **kluster**
    >

### <a name="deploy-the-real-time-endpoint"></a>Distribuera real tids slut punkten

När din AKS-tjänst har slutfört etableringen återgår du till inferencing-pipeline i real tid för att slutföra distributionen.

1. Välj **distribuera** ovanför arbets ytan.

1. Välj **distribuera ny slut punkt i real tid**. 

1. Välj det AKS-kluster som du skapade.

1. Välj **Distribuera**.

    ![Skärm bild som visar hur du konfigurerar en ny slut punkt för Real tid](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Ett meddelande som visar att arbets ytan visas när distributionen är klar, det kan ta några minuter.

## <a name="test-the-real-time-endpoint"></a>Testa real tids slut punkten

Du kan testa din real tids slut punkt genom att gå till sidan **slut punkter** i navigerings fönstret för arbets ytan till vänster.

1. På sidan **slut punkter** väljer du den slut punkt som du har distribuerat.

    ![Skärm bild som visar fliken slut punkter i real tid med den nyligen skapade slut punkten markerad](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Välj **test**.

1. Ange test data eller Använd de autofyllde exempel data och välj **testa**.

    Test förfrågan skickas till slut punkten och resultaten visas på sidan. Även om ett pris värde genereras för indata används inte det för att generera förutsägelse värdet.

    ![Skärm bild som visar hur du testar real tids slut punkten med den visade etiketten för pris som marker ATS](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig viktiga steg för att skapa, distribuera och använda en maskin inlärnings modell i designern. Mer information om hur du kan använda designern för att lösa andra typer av problem finns i våra andra exempel pipelines.

> [!div class="nextstepaction"]
> [Exempel på kredit risk klassificering](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
