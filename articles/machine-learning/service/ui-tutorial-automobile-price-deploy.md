---
title: 'Självstudier: Distribuera en maskininlärningsmodell med det visuella gränssnittet'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar en lösning för förutsägelseanalys i det visuella gränssnittet för Azure Machine Learning-tjänsten. Träna, poäng, och distribuera en modell för maskininlärning med dra och släppa moduler. Den här självstudien är del två i en serie med två delar om att förutsäga bilpriser med hjälp av linjär regression.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190922"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Självstudier: Distribuera en maskininlärningsmodell med det visuella gränssnittet

I den här självstudien får titta du närmare på att utveckla en förutsägande lösning i det visuella gränssnittet för Azure Machine Learning-tjänsten. Självstudien är **del två i en självstudieserie i två delar**. I [del ett av självstudiekursen](ui-tutorial-automobile-price-train-score.md), tränas, poängsättas och utvärderas en modell för att förutsäga bil priser. I den här delen av självstudiekursen ska du:

> [!div class="checklist"]
> * Förbereda en modell för distribution
> * Distribuera en webbtjänst
> * Testa en webbtjänst
> * Hantera en webbtjänst
> * Förbruka webbtjänsten

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför [del ett av självstudien](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

För att ge andra prova att använda förutsägande modell som har utvecklats i den här självstudien måste distribuera du den som en Azure-webbtjänst.

Hittills har du experimenterat med träna din modell. Nu är det dags att skapa nya förutsägelser baserat på indata från användaren.

Förberedelse för distribution är en tvåstegsprocess:  

1. Konvertera den *träningsexperiment* som du har skapat i en *förutsägbart experiment*
1. Distribuera förutsägelseexperiment som en webbtjänst

Du kanske vill göra en kopia av experimentet först genom att välja **Spara som** längst ned på arbetsytan för experimentet.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägbart experiment

För att förbereda den här modellen för distribution måste du konvertera den här träningsexperiment till ett förutsägbart experiment. Detta omfattar vanligtvis tre steg:

1. Spara modellen du har tränat och ersätter utbildningsmoduler
1. Trimma experimentet för att ta bort moduler som behövdes endast för utbildning
1. Definiera där webbtjänsten ska ta emot indata och där det genererar utdata

Du kan utföra dessa steg manuellt eller så kan du välja **konfigurera Web Service** längst ned på arbetsytan för experimentet har gjort automatiskt.

![Animerad gif som visar automatisk konvertering av ett träningsexperiment till ett förutsägbart experiment](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

När du väljer **konfigurera Web Service**, flera saker:

* Den tränade modellen konverteras till en enda **Trained Model** modulen. Den är lagrad på modulpaletten till vänster om arbetsytan för experimentet. Du hittar den under **tränade modeller**.
* Moduler som har använts för utbildning tas bort; mer specifikt:
  * Träningsmodell
  * Dela data
  * Utvärdera modell
* Den sparade tränade modellen har lagts till i experimentet
* **Web service indata** och **Web service utdata** moduler har lagts till. Dessa moduler identifiera där användarens data kommer att ange modellen och där data returneras.

Du kan se att experimentet sparas i två delar under de nya flikarna högst upp på arbetsytan för experimentet. Det ursprungliga träningsexperimentet är under fliken **träningsexperiment**, och den nyligen skapade förutsägelseexperiment är **förutsägelseexperiment**. Du ska distribuera förutsägelseexperimentet som en webbtjänst.

Experimentet bör nu se ut så här:  

![Skärmbild som visar den förväntade konfigurationen av experimentet när du har förberett för distribution](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Kör experimentet en sista gång (Välj **kör**). Välja beräkningsmål som du vill att experiment att köra i popup-dialogruta. Om du vill kontrollera att modellen fortfarande fungerar, Välj utdata från modulen poängsätta modell och välj **visa resultat**. Du kan se den ursprungliga informationen visas, tillsammans med det förväntade priset (”poängsatta etiketter”).

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten

Distribuera en ny webbtjänst som härrör från ditt experiment:

1. Välj **distribuera webbtjänsten** under arbetsytan.
1. Välj den **Compute Target** att du vill köra webbtjänsten.

    Det visuella gränssnittet stöder för närvarande bara distribution till Azure Kubernetes Service (AKS) beräkningsmål. Du kan välja bland tillgängliga beräkningsmål för AKS i din machine learning-arbetsyta eller konfigurera en ny AKS-miljö med hjälp av stegen i dialogrutan som visas.

    ![Skärmbild som visar en möjlig konfiguration för en ny beräkningsmål](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Välj **distribuera webbtjänst**. Följande meddelande visas när distributionen är klar. Distributionen kan ta några minuter.

    ![Skärmbild som visar bekräftelsemeddelandet för en lyckad distribution.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testa webbtjänsten

Indata för användaren anger din distribuerade modell via den **Web service indata** modulen. Indata beräknas sedan i den **Poängmodell** modulen. På sätt som du har konfigurerat förutsägelseexperiment modellen förväntas data i samma format som den ursprungliga datauppsättningen bil pris. Slutligen resultaten returneras till användaren via den **Web service utdata** modulen.

Du kan testa en webbtjänst i web service-flik i det visuella gränssnittet.

1. Gå till avsnittet web service. Du ser den webbtjänst som du har distribuerat med namnet **självstudie – förutsäga priset för bil [förutsägande Exp]**.

     ![Skärmbild som visar fliken tjänsten med den nyligen skapade webbtjänst markerat](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Välj webbtjänstnamn att visa ytterligare information.

     ![Skärmbild som visar ytterligare information som är tillgängliga i webbtjänsten visa](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Välj **Test**.

    ![Skärmbild som visar webbtjänsten testa sidan](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Ange testa data eller använda autofilled exempeldata och välj **Test** längst ned på sidan. Testbegäran har skickats till webbtjänsten och resultatet visas på sidan.

## <a name="manage-the-web-service"></a>Hantera webbtjänsten

När du har distribuerat din webbtjänst kan du hantera den från den **webbtjänster** flik i det visuella gränssnittet.

Du kan ta bort en webbtjänst genom att välja **ta bort** i sidan med web service.

   ![Skärmbild som visar platsen för ta bort web service-knappen längst ned i fönstret](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Förbruka webbtjänsten

I de föregående stegen i den här kursen du har distribuerat en bil förutsägelsemodell som en Azure-webbtjänst. Användare kan nu skicka data till den och ta emot resultatet via REST-API.

**Begäran/svar** -användaren skickar en eller flera rader med bildata till tjänsten med hjälp av en HTTP-protokollet. Tjänsten svarar med en eller flera uppsättningar med resultat.

Du hittar exempel REST-anrop i den **förbruka** fliken sidan information om tjänsten.

   ![Skärmbild som visar ett exempel på REST anropa användarna kan hitta på fliken förbruka](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navigera till den **API-dokument** fliken mer API-information.

  ![Skärmbild som visar ytterligare API-information som användarna kan hitta på fliken API-dokument](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Hantera modeller och distributioner i Azure Machine Learning-tjänstens arbetsyta

Modeller och webbtjänstdistributioner som du skapar i det visuella gränssnittet kan hanteras från arbetsytan Azure Machine Learning-tjänsten.

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

I den här självstudien har du lärt dig nyckeln steg i att skapa, distribuera och använda machine learning-modell i det visuella gränssnittet. Om du vill veta mer om hur du kan använda det visuella gränssnittet för att lösa andra typer av problem kan ta en titt på exempelexperiment.

> [!div class="nextstepaction"]
> [Kredit risk klassificering exemplet](ui-sample-classification-predict-credit-risk-basic.md)
