---
title: 'Självstudier: Distribuera en maskin inlärnings modell med det visuella gränssnittet'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en förutsägelse analys lösning i Azure Machine Learnings visuella gränssnitt. Träna, score och distribuera en maskin inlärnings modell med hjälp av dra och släpp moduler. Den här självstudien är del två i en serie i två delar om förutsägelse av bil priser med linjär regression.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 9378c6a14c3b755a6456ef68ecd73730cb77fc79
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128971"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Självstudier: Distribuera en maskin inlärnings modell med det visuella gränssnittet

Om du vill ge andra möjlighet att använda den förutsägande modellen som utvecklats i [del ett av själv studie kursen](ui-tutorial-automobile-price-train-score.md)kan du distribuera den som en Azure-webbtjänst. Hittills har du experimenterat med att träna din modell. Nu är det dags att generera nya förutsägelser baserat på användarindata. I den här delen av självstudien:

> [!div class="checklist"]
> * Förbered en modell för distribution
> * Distribuera en webbtjänst
> * Testa en webb tjänst
> * Hantera en webb tjänst
> * Använda webb tjänsten

## <a name="prerequisites"></a>Förutsättningar

Slutför [del ett av självstudien](ui-tutorial-automobile-price-train-score.md) och lär dig hur du tränar och poängs ätter en maskin inlärnings modell i det visuella gränssnittet.

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Innan du distribuerar experimentet som en webb tjänst måste du först konvertera ditt *utbildnings experiment* till ett *förutsägelse experiment*.

1. Välj **skapa förutsägande experiment*** längst ned i experiment arbets ytan.

    ![Animerad GIF som visar den automatiska konverteringen av ett utbildnings experiment till ett förutsägelse experiment](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    När du väljer **skapa förutsägande experiment**händer flera saker:
    
    * Den tränade modellen lagras som en **tränad modell** modul i modulen modul. Du hittar den i **tränade modeller**.
    * Moduler som har använts för utbildning tas bort; mer specifikt:
      * Träningsmodell
      * Dela data
      * Utvärdera modell
    * Den sparade tränade modellen läggs tillbaka i experimentet.
    * Moduler för **webb tjänst indata** och **webb tjänstens utdata** läggs till. Dessa moduler identifierar var användar data kommer att ange modellen och var data returneras.

    **Övnings experimentet** sparas fortfarande under de nya flikarna överst i experimentets arbets yta.

1. **Kör** experimentet.

1. Välj utdata från modulen **Poäng modell** och välj **Visa resultat** för att kontrol lera att modellen fortfarande fungerar. Du kan se att ursprungliga data visas, tillsammans med det förväntade priset ("Poäng etiketter").

Experimentet bör nu se ut så här:  

![Skärm bild som visar den förväntade konfigurationen av experimentet när den har förberedats för distribution](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten

1. Välj **distribuera webb tjänst** under arbets ytan.

1. Välj det **beräknings mål** som du vill köra webb tjänsten på.

    För närvarande stöder det visuella gränssnittet bara distribution till AKS-beräknings mål (Azure Kubernetes service). Du kan välja från tillgängliga AKS Compute-mål i din Machine Learning service-arbetsyta eller konfigurera en ny AKS-miljö med hjälp av stegen i dialog rutan som visas.

    ![Skärm bild som visar en möjlig konfiguration för ett nytt beräknings mål](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Välj **distribuera webb tjänst**. Följande meddelande visas när distributionen är klar. Distributionen kan ta några minuter.

    ![Skärm bild som visar bekräftelse meddelandet för en lyckad distribution.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testa webbtjänsten

Du kan testa och hantera dina visuella gränssnitts webb tjänster genom att gå till fliken **webb tjänster** .

1. Gå till avsnittet webb tjänst. Du ser den webb tjänst som du har distribuerat med namn **vägledningen – Förutsäg bil pris [förutsägande exp]** .

     ![Skärm bild som visar fliken webb tjänst med den nyligen skapade webb tjänsten markerad](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Välj namnet på webb tjänsten om du vill visa mer information.

1. Välj **test**.

    [![Skärm bild som visar sidan för webb tjänst testning](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Ange test data eller Använd de autofyllde exempel data och välj **testa**.

    Test förfrågan skickas till webb tjänsten och resultaten visas på sidan. Även om ett pris värde genereras för indata används inte det för att generera förutsägelse värdet.

## <a name="consume-the-web-service"></a>Använda webb tjänsten

Användarna kan nu skicka API-begäranden till Azure-webbtjänsten och ta emot resultat för att förutsäga priset på sina nya bilar.

**Begäran/svar** – användaren skickar en eller flera rader med mobila data till tjänsten med hjälp av ett HTTP-protokoll. Tjänsten svarar med en eller flera resultat uppsättningar.

Du hittar exempel på REST-anrop på fliken **förbrukare** på sidan med webb tjänst information.

   ![Skärm bild som visar ett exempel på REST-anrop som användare kan hitta på fliken förbrukare](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Gå till fliken **API-dokument** för att hitta mer information om API.

## <a name="manage-models-and-deployments"></a>Hantera modeller och distributioner

De modeller och webb tjänst distributioner som du skapar i det visuella gränssnittet kan också hanteras från Azure Machine Learning-arbetsytan.

1. Öppna din arbets yta i [Azure Portal](https://portal.azure.com/).  

1. I arbets ytan väljer du **modeller**. Välj sedan experimentet som du skapade.

    ![Skärm bild som visar hur du navigerar till experiment i Azure Portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    På den här sidan ser du ytterligare information om modellen.

1. Välj **distributioner**, visar en lista över alla webb tjänster som använder modellen. Välj namnet på webb tjänsten. den kommer att gå till sidan för webb tjänst information. På den här sidan kan du få mer detaljerad information om webb tjänsten.

    [![Detaljerad rapport om skärm bilds körning](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

Du kan också hitta dessa modeller och distributioner i avsnitten **modeller** och **slut punkter** i [landnings sidan för din arbets yta (för hands version)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig viktiga steg för att skapa, distribuera och använda en maskin inlärnings modell i det visuella gränssnittet. Mer information om hur du kan använda det visuella gränssnittet för att lösa andra typer av problem finns i våra andra exempel experiment.

> [!div class="nextstepaction"]
> [Exempel på kredit risk klassificering](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
