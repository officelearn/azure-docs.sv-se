---
title: 'Självstudiekurs: Distribuera en maskininlärningsmodell med designern'
titleSuffix: Azure Machine Learning
description: Den här självstudien visar hur du skapar en förutsägelseanalyslösning i Azure Machine Learning designer (förhandsversion). Träna, poängsätta och distribuera en maskininlärningsmodell med hjälp av dra-och-släpp-moduler.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389400"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Självstudiekurs: Distribuera en maskininlärningsmodell med designern (förhandsgranskning)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Du kan distribuera den prediktiva modellen som utvecklats i [del ett av självstudien](tutorial-designer-automobile-price-train-score.md) för att ge andra en chans att använda den. I del ett tränade du din modell. Nu är det dags att generera nya förutsägelser baserat på användarindata. I den här delen av självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en inferenpipeline i realtid.
> * Skapa ett inferencing-kluster.
> * Distribuera slutpunkten i realtid.
> * Testa slutpunkten i realtid.

## <a name="prerequisites"></a>Krav

Fyll [i del ett av handledningen](tutorial-designer-automobile-price-train-score.md) för att lära sig att träna och poäng en maskininlärningsmodell i designern.

## <a name="create-a-real-time-inference-pipeline"></a>Skapa en inferenspipeline i realtid

Om du vill distribuera pipelinen måste du först konvertera träningspipelinen till en inferenpipeline i realtid. Den här processen tar bort utbildningsmoduler och lägger till webbtjänstindata och utdata för att hantera begäranden.

### <a name="create-a-real-time-inference-pipeline"></a>Skapa en inferenspipeline i realtid

1. Ovanför pipeline-arbetsytan väljer du **Skapa inferenspipeline** > **Inferenspipeline**.

    ![Skärmbild som visar var du hittar knappen Skapa pipeline](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    Din pipeline bör nu se ut så här: 

   ![Skärmbild som visar den förväntade konfigurationen av pipelinen efter att ha förberett den för distribution](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    När du väljer **Skapa inferenspipeline**händer flera saker:
    
    * Den tränade modellen lagras som en **Dataset-modul** i modulpaletten. Du hittar den under **Mina datauppsättningar**.
    * Utbildningsmoduler som **Train Model** och **Split Data** tas bort.
    * Den sparade tränade modellen läggs tillbaka till pipelinen.
    * **Webbtjänstinmatnings-** och **webbtjänstutdatamoduler** läggs till. Dessa moduler visar var användardata kommer in i pipelinen och var data returneras.

    > [!NOTE]
    > Som standard förväntar sig **webbtjänstindata** samma dataschema som de träningsdata som används för att skapa den prediktiva pipelinen. I det här fallet ingår priset i schemat. Priset används dock inte som en faktor under förutsägelse.
    >

1. Välj **Skicka**och använd samma beräkningsmål och experiment som du använde i del ett.

    Om är den första körningen kan det ta upp till 20 minuter innan pipelinen körs. Standardberäkningsinställningarna har en minsta nodstorlek på 0, vilket innebär att designern måste allokera resurser efter inaktiv. Upprepade pipeline-körningar tar mindre tid eftersom beräkningsresurserna redan har allokerats. Dessutom använder designern cachelagrade resultat för varje modul för att ytterligare förbättra effektiviteten.

1. Välj **Distribuera**.

## <a name="create-an-inferencing-cluster"></a>Skapa ett inferencing-kluster

I dialogrutan som visas kan du välja bland alla befintliga AKS-kluster (Azure Kubernetes Service) som du vill distribuera din modell till. Om du inte har ett AKS-kluster kan du använda följande steg för att skapa ett.

1. Välj **Beräkna** i dialogrutan som visas för att gå till **sidan Beräkna.**

1. Välj **Inferenskluster** > **+ Nya**i navigeringsfliksområdet .

    ![Skärmbild som visar hur du kommer till det nya inferensklusterfönstret](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. Konfigurera en ny Kubernetes-tjänst i inferensklusterfönstret.

1. Ange *aks-beräkning* för **beräkningsnamnet**.
    
1. Välj en närliggande region som är tillgänglig för **regionen**.

1. Välj **Skapa**.

    > [!NOTE]
    > Det tar cirka 15 minuter att skapa en ny AKS-tjänst. Du kan kontrollera etableringstillståndet på sidan **Inferenskluster.**
    >

## <a name="deploy-the-real-time-endpoint"></a>Distribuera slutpunkten i realtid

När din AKS-tjänst har etablerats är du tillbaka i realtids pipeline för att slutföra distributionen.

1. Välj **Distribuera** ovanför arbetsytan.

1. Välj **Distribuera ny slutpunkt i realtid**. 

1. Välj det AKS-kluster som du skapade.

1. Välj **Distribuera**.

    ![Skärmbild som visar hur du konfigurerar en ny slutpunkt i realtid](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Ett meddelande om lyckade program ovanför arbetsytan visas när distributionen är klar. Det kan ta några minuter.

## <a name="test-the-real-time-endpoint"></a>Testa slutpunkten i realtid

När distributionen är klar kan du testa slutpunkten i realtid genom att gå till sidan **Slutpunkter.**

1. På sidan **Slutpunkter** väljer du den slutpunkt som du har distribuerat.

    ![Skärmbild som visar fliken slutpunkter i realtid med den nyligen skapade slutpunkten markerad](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Välj **Testa regel**.

1. Du kan mata in testdata manuellt eller använda de autofyllda exempeldata och välja **Testa**.

    Portalen skickar en testbegäran till slutpunkten och visar resultaten. Även om ett prisvärde genereras för indata används det inte för att generera förutsägelsevärdet.

    ![Skärmbild som visar hur du testar slutpunkten i realtid med den poängsatta etiketten för pris markerad](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig de viktigaste stegen i hur du skapar, distribuerar och använder en maskininlärningsmodell i designern. Mer information om hur du kan använda designern för att lösa andra typer av problem finns i våra andra exempelpipelor.

> [!div class="nextstepaction"]
> [Designer prover](samples-designer.md)
