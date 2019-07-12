---
title: 'Självstudier: Distribuera en maskininlärningsmodell med det visuella gränssnittet'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar en lösning för förutsägelseanalys i det visuella gränssnittet för Azure Machine Learning-tjänsten. Träna, poäng, och distribuera en modell för maskininlärning med dra och släppa moduler. Den här självstudien är del två i en serie med två delar om att förutsäga bilpriser med hjälp av linjär regression.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839311"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Självstudier: Distribuera en maskininlärningsmodell med det visuella gränssnittet

Att ge andra prova att använda förutsägelsemodell som utvecklats i [del ett av självstudiekursen](ui-tutorial-automobile-price-train-score.md), du kan distribuera den som en Azure-webbtjänst. Hittills har du experimenterat med träna din modell. Nu är det dags att skapa nya förutsägelser baserat på indata från användaren. I den här delen av självstudiekursen ska du:

> [!div class="checklist"]
> * Förbereda en modell för distribution
> * Distribuera en webbtjänst
> * Testa en webbtjänst
> * Hantera en webbtjänst
> * Förbruka webbtjänsten

## <a name="prerequisites"></a>Förutsättningar

Fullständig [del ett av självstudiekursen](ui-tutorial-automobile-price-train-score.md) och lär dig att träna och betygsätta ett machine learning-modellen i det visuella gränssnittet.

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Innan du distribuerar ditt experiment som en webbtjänst måste du först behöva konvertera din *träningsexperiment* i en *förutsägelseexperiment*.

1. Välj **skapa förutsägande experimentera*** längst ned på arbetsytan för experimentet.

    ![Animerad gif som visar automatisk konvertering av ett träningsexperiment till ett förutsägbart experiment](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    När du väljer **skapa Förutsägelseexperiment**, flera saker:
    
    * Den tränade modellen lagras som en **Trained Model** modulen på modulpaletten. Du hittar den under **tränade modeller**.
    * Moduler som har använts för utbildning tas bort; mer specifikt:
      * Träningsmodell
      * Dela data
      * Utvärdera modell
    * Den sparade tränade modellen har lagts till i experimentet.
    * **Web service indata** och **Web service utdata** moduler har lagts till. Dessa moduler identifiera där användarens data kommer att ange modellen och där data returneras.

    Den **träningsexperiment** fortfarande sparas under de nya flikarna högst upp på arbetsytan för experimentet.

1. **Kör** experimentet.

1. Välj utdata från den **Poängmodell** modul och välj **visa resultat** att verifiera modellen fortfarande fungerar. Du kan se den ursprungliga informationen visas, tillsammans med det förväntade priset (”poängsatta etiketter”).

Experimentet bör nu se ut så här:  

![Skärmbild som visar den förväntade konfigurationen av experimentet när du har förberett för distribution](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten

1. Välj **distribuera webbtjänsten** under arbetsytan.

1. Välj den **Compute Target** att du vill köra webbtjänsten.

    Det visuella gränssnittet stöder för närvarande bara distribution till Azure Kubernetes Service (AKS) beräkningsmål. Du kan välja bland tillgängliga beräkningsmål för AKS i din machine learning-arbetsyta eller konfigurera en ny AKS-miljö med hjälp av stegen i dialogrutan som visas.

    ![Skärmbild som visar en möjlig konfiguration för en ny beräkningsmål](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Välj **distribuera webbtjänst**. Följande meddelande visas när distributionen är klar. Distributionen kan ta några minuter.

    ![Skärmbild som visar bekräftelsemeddelandet för en lyckad distribution.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testa webbtjänsten

Du kan testa och hantera dina webbtjänster för visuella gränssnittet genom att navigera till den **webbtjänster** fliken.

1. Gå till avsnittet web service. Du ser den webbtjänst som du har distribuerat med namnet **självstudie – förutsäga priset för bil [förutsägande Exp]** .

     ![Skärmbild som visar fliken tjänsten med den nyligen skapade webbtjänst markerat](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Välj webbtjänstnamn att visa ytterligare information.

     ![Skärmbild som visar ytterligare information som är tillgängliga i webbtjänsten visa](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Välj **Test**.

    ![Skärmbild som visar webbtjänsten testa sidan](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Ange testa data eller använda autofilled exempeldata och välj **Test**.

    Testbegäran har skickats till webbtjänsten och resultatet visas på sidan. Även om ett prisvärde genereras för indata, används den inte att generera förutsägelse-värde.

## <a name="consume-the-web-service"></a>Förbruka webbtjänsten

Användare kan nu skicka API-begäranden till din Azure-webbtjänst och ta emot resultaten för att förutsäga priset på sin nya bilar.

**Begäran/svar** -användaren skickar en eller flera rader med bildata till tjänsten med hjälp av en HTTP-protokollet. Tjänsten svarar med en eller flera uppsättningar med resultat.

Du hittar exempel REST-anrop i den **förbruka** fliken sidan information om tjänsten.

   ![Skärmbild som visar ett exempel på REST anropa användarna kan hitta på fliken förbruka](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navigera till den **API-dokument** fliken mer API-information.

  ![Skärmbild som visar ytterligare API-information som användarna kan hitta på fliken API-dokument](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>Hantera modeller och -distributioner

Modeller och webbtjänstdistributioner som du skapar i det visuella gränssnittet kan också hanteras från arbetsytan Azure Machine Learning-tjänsten.

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  

1. På arbetsytan, Välj **modeller**. Välj sedan experimentet som du skapade.

    ![Skärmbild som visar hur du navigerar till experiment i Azure portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    På den här sidan visas ytterligare information om modellen.

    ![Skärmbild som visar översikt över experiment i Azure portal](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Välj **distributioner**, den visar en lista över alla webbtjänster som använder modellen. Välj webbtjänstnamn, den övergår till information för webbtjänstsida. I den här sidan kan få du mer detaljerad information för webbtjänsten.

    ![Skärmbild detaljerad kör rapporten](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig nyckeln steg i att skapa, distribuera och använda machine learning-modell i det visuella gränssnittet. Om du vill veta mer om hur du kan använda det visuella gränssnittet för att lösa andra typer av problem kan se ut våra andra exempelexperiment.

> [!div class="nextstepaction"]
> [Kredit risk klassificering exemplet](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
