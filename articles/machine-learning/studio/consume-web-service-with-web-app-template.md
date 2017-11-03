---
title: "Använda en Machine Learning-webbtjänst med en mall för appen | Microsoft Docs"
description: "Använd en mall för app i Azure Marketplace för att använda en förutsägbar webbtjänst i Azure Machine Learning."
keywords: "maskininlärning för webbtjänst, operationalization REST API"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.openlocfilehash: 1c182403409966923440f359cb2514af7b7df9f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Använda en Azure Machine Learning-webbtjänst med en webbappmall

När du har utvecklat din förutsägelsemodell och distribueras som en Azure-webbtjänst med hjälp av Machine Learning Studio eller med verktyg som R eller Python kan du komma åt operationalized modellen med hjälp av REST-API.

Det finns ett antal sätt att använda REST-API och få åtkomst till webbtjänsten. Du kan till exempel skriva ett program i C#, R eller Python med exempelkoden genererade för dig när du distribuerade webbtjänsten (tillgänglig i den [Machine Learning Web Services-portalen](https://services.azureml.net/quickstart) eller i web service instrumentpanelen i Machine Learning Studio). Eller så kan du använda exemplet Microsoft Excel-arbetsbok på samma gång.

Men de snabbaste och enklaste sättet att komma åt webbtjänsten är via Web App mallar som är tillgängliga i den [Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>I Azure Machine Learning mallar för App
Web app tillgängliga mallar i Azure Marketplace kan skapa ett anpassat webbprogram som känner din webbtjänsten indata och förväntat resultat. Allt du behöver göra är att ge appen webbåtkomst webbtjänsten och data och mallen gör resten.

Två mallar är tillgängliga:

* [Azure ML-svar på begäranden tjänstmall Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML-Batch Execution Service Web App mall](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Varje mall skapar ett exempel ASP.NET-program med hjälp av API-URI och nyckel för webbtjänsten, och distribuerar den som en webbplats till Azure. Svar på begäranden tjänsten (RR) skapas en webbapp som gör det möjligt att skicka en enda rad med data till webbtjänsten för att få ett enskilt resultat. Batch Execution Service BES-mallen skapar en webbapp som gör att du kan skicka många rader med data för att få flera resultat.

Ingen kodning krävs för att använda dessa mallar. Du ange bara API-nyckel och URI och mallen skapar program åt dig.

Hämta API-nyckel och Begärd URI för en webbtjänst:

1. I den [Web Services-portalen](https://services.azureml.net/quickstart), en ny webbtjänst klickar du på **Web Services** längst upp. Eller för en klassisk web service klickar du på **klassiska webbtjänster**.
2. Klicka på den webbtjänst som du vill komma åt.
3. Klicka på den slutpunkt som du vill komma åt för en klassisk webbtjänst.
4. Klicka på **förbruka** längst upp.
5. Kopiera den **primära** eller **sekundärnyckeln** och spara den.
6. Om du skapar en mall för svar på begäranden tjänsten (RR), kopiera den **begäran och svar** URI och spara den. Om du skapar en mall för Batch Execution Service BES-, kopiera den **gruppbegäranden** URI och spara den.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Hur du använder mallen svar på begäranden tjänsten (RR)
Följ dessa steg om du vill använda mallen Resursposter web app, som visas i följande diagram.

![Processen för att använda Resursposter webbmall][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Gå till den [Azure-portalen](https://portal.azure.com), **inloggning**, klickar du på **ny**, söka efter och välj **Azure ML-svar på begäranden Service Web App**, klicka på **skapa**. 
   
   * Ge ett unikt namn för ditt webbprogram. URL till webbprogrammet blir namnet följt av `.azurewebsites.net.` t.ex.`http://carprediction.azurewebsites.net.`
   * Välj Azure-prenumeration och tjänster som webbtjänsten körs under.
   * Klicka på **Skapa**.
     
     ![Skapa webbapp][image5]

4. När Azure distribution webbprogrammet har slutförts, klickar du på den **URL** på webbappsinställningarna sidan i Azure eller anger en URL i en webbläsare. Till exempel, `http://carprediction.azurewebsites.net.`
5. När webbappen körs första gången uppmanas du för den **API Post URL** och **API-nyckeln**.
   Ange de värden som du sparade tidigare (**Begärd URI** och **API-nyckel**respektive).
     
     Klicka på **skicka**.
     
     ![Ange Post URI och API-nyckel][image6]

6. Web app visar dess **Webbappkonfigurationen** sidan med de aktuella inställningarna för web service. Här kan du ändra inställningarna som används av webbappen.
   
   > [!NOTE]
   > Om du ändrar de här inställningarna endast ändras dem för det här webbprogrammet. Standardinställningarna för webbtjänsten ändras inte. Till exempel om du ändrar den **beskrivning** här ändras inte den beskrivning som visas på instrumentpanelen web service i Machine Learning Studio.
   > 
   > 
   
    När du är klar klickar du på **spara ändringar**, och klicka sedan på **gå till startsidan**.

7. Du kan ange värden ska skickas till webbtjänsten från startsidan. Klicka på **skicka** när du är klar och resultatet returneras.

Om du vill gå tillbaka till den **Configuration** går du till den `setting.aspx` sidan i webbprogrammet. Till exempel: `http://carprediction.azurewebsites.net/setting.aspx.` uppmanas du att ange API-nyckeln igen – du behöver som kan komma åt sidan och uppdatera inställningarna.

Du kan stoppa, starta om eller ta bort webbprogrammet i Azure-portalen som andra webbprogram. Så länge som den körs kan du bläddra till hem webbadressen och ange nya värden.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Hur du använder Batch Execution Service BES-mall
Du kan använda mallen BES web app på samma sätt som RR-mall, förutom att webbappen har skapats kan du skicka flera rader med data och ta emot flera resultat.

Indatavärden för en webbtjänst för batch-körningen kan komma från Azure-lagring eller en lokal fil. resultatet lagras i en Azure storage-behållare.
Så du behöver en Azure storage-behållare för att hålla resultaten som returnerades av webbprogrammet och måste du förbereda dina indata.

![Processen för att använda mallen för BES-webbtjänst][image2]

1. Följ samma procedur för att skapa webbprogram BES som mallen Resursposter utom gå till [Azure ML Batch Execution Service Web Appmallen](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) öppnas BES-mallen på Azure Marketplace och på **skapa webbprogrammet**.

2. Där du vill att resultatet lagras ange mål behållaren information på sidan web app. Ange också där webbprogrammet får indatavärden, antingen i en lokal fil eller en Azure storage-behållare.
   Klicka på **skicka**.
   
    ![Storage-informationen][image7]

Webbprogrammet visas en sida med jobbstatus.
När jobbet har slutförts får du platsen för resultaten i Azure blob storage. Du har också möjlighet att hämta resultaten till en lokal fil.

## <a name="for-more-information"></a>Mer information
Mer information om...

* Skapa ett experiment i machine learning med Machine Learning Studio finns [skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md)
* hur du distribuerar ditt machine learning-experiment som en webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md)
* andra sätt att få åtkomst till din webbtjänsten finns [använda en Azure Machine Learning-webbtjänst](consume-web-services.md)

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
