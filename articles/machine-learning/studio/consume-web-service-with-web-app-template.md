---
title: Använda en Machine Learning-webbtjänst med hjälp av en mall för appen | Microsoft Docs
description: Använd en mall för app i Azure Marketplace för att använda en förutsägbar webbtjänst i Azure Machine Learning.
keywords: maskininlärning för webbtjänst, operationalization REST API
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 81b89a1f8a053fd3b1d0d6a0a1bcc5c67d2ba728
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Använda en Azure Machine Learning-webbtjänst med hjälp av en mall för app

Du kan utveckla en förutsägelsemodell och distribuera det som en Azure-webbtjänst med hjälp av:
- Azure Machine Learning Studio.
- Verktyg som R eller Python. 

Sedan kan du komma åt operationalized modellen med hjälp av REST-API.

Det finns ett antal sätt att använda REST-API och få åtkomst till webbtjänsten. Du kan till exempel skriva ett program i C#, R eller Python med hjälp av exempelkoden genererade för dig när du distribuerade webbtjänsten. (Exempelkoden är tillgänglig i den [Machine Learning-webbtjänster portal](https://services.azureml.net/quickstart) eller i web service instrumentpanelen i Machine Learning Studio.) Eller så kan du använda exemplet Microsoft Excel-arbetsbok på samma gång.

Men de snabbaste och enklaste sättet att komma åt webbtjänsten är via web app mallar som är tillgängliga i den [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Azure Machine Learning app webbmallar
Web app tillgängliga mallar i Azure Marketplace kan skapa ett anpassat webbprogram som känner din webbtjänsten indata och förväntat resultat. Allt du behöver göra är att ge appen webbåtkomst webbtjänsten och data och mallen gör resten.

Två mallar är tillgängliga:

* [Azure ML-svar på begäranden tjänstmall Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML-Batch Execution Service Web App mall](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Varje mall skapar ett exempelprogram för ASP.NET med hjälp av API-URI och nyckel för webbtjänsten. Mallen kan du sedan distribuerar programmet som en webbplats till Azure. 

Svar på begäranden tjänsten (RR) skapas en webbapp som du kan använda för att skicka en enda rad med data till webbtjänsten för att få ett enskilt resultat. Batch Execution Service BES-mallen skapar ett webbprogram som du kan använda för att skicka många rader med data för att få flera resultat.

Ingen kodning krävs för att använda dessa mallar. Du ange bara API-nyckel och URI och mallen skapar program åt dig.

Hämta API-nyckeln och begärande-URI för en webbtjänst:

1. I den [Web Services-portalen](https://services.azureml.net/quickstart)väljer **Web Services** längst upp. Eller en klassiska webbtjänst, Välj **klassiska webbtjänster**.
2. Välj den webbtjänst som du vill komma åt.
3. Välj den slutpunkt som du vill komma åt för en klassiska webbtjänst.
4. Välj **förbruka** längst upp.
5. Kopiera den primära eller sekundära nyckeln och spara den.
6. Om du skapar en mall för RRS, kopiera den **begäran och svar** URI och spara den. Om du skapar en mall för BES, kopiera den **gruppbegäranden** URI och spara den.


## <a name="how-to-use-the-request-response-service-template"></a>Hur du använder mallen svar på begäranden tjänst
Följ dessa steg om du vill använda mallen Resursposter web app, som visas i följande diagram.

![Processen för att använda Resursposter webbmall][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **ny**, söka efter och välj **Azure ML-svar på begäranden Service Web App**, och välj sedan **skapa**. 
3. I den **skapa** fönstret:
   
   * Ge ett unikt namn för ditt webbprogram. URL till webbprogrammet blir namnet följt av **. azurewebsites.net**. Ett exempel är **http://carprediction.azurewebsites.net**.
   * Välj Azure-prenumeration och tjänster som webbtjänsten körs under.
   * Välj **Skapa**.
     
   ![Skapa webbapp][image5]

4. När Azure distribution webbprogrammet har slutförts, markerar du den **URL** på webbappsinställningarna sidan i Azure eller anger en URL i en webbläsare. Ange till exempel **http://carprediction.azurewebsites.net**.
5. När webbappen körs första gången du tillfrågas för den **API Post URL** och **API-nyckeln**. Ange de värden som du sparade tidigare (begärande URI och API-nyckeln respektive). Välj **skicka**.
     
   ![Ange post URI och API-nyckeln][image6]

6. Web app visar dess **Webbappkonfigurationen** sidan med de aktuella inställningarna för web service. Här kan du ändra de inställningar som används i webbappen.
   
   > [!NOTE]
   > Om du ändrar de här inställningarna endast ändras dem för det här webbprogrammet. Standardinställningarna för webbtjänsten ändras inte. Till exempel om du ändrar texten i **beskrivning** här, ändras inte den beskrivning som visas på instrumentpanelen web service i Machine Learning Studio.
   > 
   > 
   
    När du är klar väljer du **spara ändringar**, och välj sedan **gå till startsidan**.

7. Du kan ange värden ska skickas till webbtjänsten från startsidan. Välj **skicka** när du är klar och resultatet returneras.

Om du vill gå tillbaka till den **Configuration** går du till den **setting.aspx** sidan i webbprogrammet. Till exempel Gå till **http://carprediction.azurewebsites.net/setting.aspx**. Du uppmanas att ange API-nyckeln igen. Du måste som kan komma åt sidan och uppdatera inställningarna.

Du kan stoppa, starta om eller ta bort webbprogrammet i Azure-portalen som andra webbprogram. Så länge som den körs, kan du bläddra till hem webbadressen och ange nya värden.

## <a name="how-to-use-the-batch-execution-service-template"></a>Hur du använder mallen Batch Execution Service
Du kan använda mallen BES web app på samma sätt som en RR-mall. Skillnaden är att du kan använda webbappen att skicka flera rader med data och ta emot flera resultat.

Indatavärden för en webbtjänst för batch-körningen kan komma från Azure Storage eller en lokal fil. Resultatet lagras i en Azure storage-behållare. Du behöver en Azure storage-behållare för de resultat som webbappen returnerar. Du måste också att förbereda dina indata.

![Processen för att använda mallen för BES-webbtjänst][image2]

1. Följ samma procedur för att skapa BES-webbprogram som RR-mallen. Men i det här fallet går du till [Azure ML Batch Execution Service Web Appmallen](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) öppna BES-mallen i Azure Marketplace. Välj **skapa webbprogram**.

2. Där du vill att resultatet lagras ange målinformation behållare på startsidan för webbapp. Även ange där webbprogrammet får indatavärdena: i en lokal fil eller i en Azure storage-behållare.
   Välj **skicka**.
   
   ![Storage-informationen][image7]

Webbprogrammet visas en sida med jobbstatus. När jobbet har slutförts kan du hämta platsen för resultatet i Azure Blob storage. Du har också möjlighet att hämta resultaten till en lokal fil.

## <a name="for-more-information"></a>Mer information
Mer information om:

* Skapa ett experiment i machine learning med Machine Learning Studio finns [skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md).
* Hur du distribuerar ditt machine learning-experiment som en webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).
* Andra sätt att få åtkomst till din webbtjänsten finns [använda en Azure Machine Learning-webbtjänst](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
