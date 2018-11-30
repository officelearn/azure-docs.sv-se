---
title: Använda en Machine Learning-webbtjänst med en webbappmall - Azure Machine Learning Studio | Microsoft Docs
description: Använd en mall för webbappar i Azure Marketplace för att använda en förutsägbar webbtjänst i Azure Machine Learning.
keywords: webbtjänsten, driftsättning, REST API för machine learning
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 2c0bba4a442b73b82ebc271c3516f65954d6d8a4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311576"
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Använda en Azure Machine Learning-webbtjänst med hjälp av en mall för webbappar

Du kan utveckla en förutsägande modell och distribuera den som en Azure-webbtjänst med hjälp av:
- Azure Machine Learning Studio.
- Verktyg som R eller Python. 

Efter det kan du komma åt produktionsslutpunkt modellen med hjälp av ett REST-API.

Det finns ett antal sätt att använda REST-API och få åtkomst till webbtjänsten. Du kan till exempel skriva ett program i C#, R eller Python med exempelkoden som genererades när du distribuerade webbtjänsten. (Exempelkoden är tillgänglig i den [Machine Learning Web Services-portalen](https://services.azureml.net/quickstart) eller i instrumentpanelen för webbtjänsten i Machine Learning Studio.) Du kan också använda Microsoft Excel-exempelarbetsboken skapas åt dig på samma gång.

Men det snabbaste och enklaste sättet att komma åt din webbtjänst är via web appmallar som är tillgängliga i den [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Azure Machine Learning web app-mallar
Web appmallar som är tillgängliga på Azure Marketplace kan skapa ett anpassat webbprogram som känner till din webbtjänst indata och önskat resultat. Allt du behöver göra är att ge web appåtkomst till din webbtjänst och dina data och mallen resten.

Två mallar är tillgängliga:

* [Azure ML-Request-Response Service Webbappmall](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML-Batch Execution Service Webbappmall](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Varje mallen skapar ett exempelprogram för ASP.NET med hjälp av API-URI och nyckel för din webbtjänst. Mallen distribuerar sedan programmet som en webbplats till Azure. 

Mallen Request-Response Service (RR) skapar en webbapp som du kan använda för att skicka en enda rad med data till webbtjänsten för att få ett enskilt resultat. BES Batch Execution Service ()-mallen skapar en webbapp som du kan använda för att skicka många rader med data för att få flera resultat.

Ingen kodning krävs för att använda dessa mallar. Du ange bara API-nyckel och URI: N och mallen skapar program åt dig.

Hämta API-nyckel och begärande-URI för en webbtjänst:

1. I den [webbtjänstportalen](https://services.azureml.net/quickstart)väljer **webbtjänster** högst upp. Eller en klassiska webbtjänst, Välj **klassiska webbtjänster**.
2. Välj den webbtjänst som du vill komma åt.
3. Välj den slutpunkt som du vill komma åt för en klassisk webbtjänst.
4. Välj **förbruka** högst upp.
5. Kopiera den primära eller sekundära nyckeln och spara den.
6. Om du skapar en RRS-mall, kopierar den **Request-Response** URI och spara den. Om du skapar en BES-mall, kopierar den **gruppbegäranden** URI och spara den.


## <a name="how-to-use-the-request-response-service-template"></a>Hur du använder mallen Request-Response Service
Följ dessa steg om du vill använda mallen RRS web app som du ser i följande diagram.

![Processen för att använda mallen för RRS-webb][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **New**, Sök efter och välj **Azure ML-Request-Response Service-Webbapp**, och välj sedan **skapa**. 
3. I den **skapa** fönstret:
   
   * Ge ett unikt namn för din webbapp. URL: en för webbappen blir det här namnet följt av **. azurewebsites.net**. Ett exempel är **http://carprediction.azurewebsites.net**.
   * Välj de Azure-prenumeration och tjänster där webbtjänsten körs.
   * Välj **Skapa**.
     
   ![Skapa webbapp][image5]

4. När Azure har slutfört distributionen webbappen, väljer du den **URL** på webbappsinställningarna sidan i Azure eller ange URL: en i en webbläsare. Ange till exempel **http://carprediction.azurewebsites.net**.
5. När webbappen körs första gången, uppmanas du för den **API post-URL** och **API-nyckel**. Ange de värden som du sparade tidigare (begärande-URI och API-nyckeln, respektive). Välj **skicka**.
     
   ![Ange post URI och API-nyckeln][image6]

6. Web app visar dess **Webbappkonfigurationen** sida med de aktuella inställningarna för web service. Här kan du ändra de inställningar som webbappen använder.
   
   > [!NOTE]
   > Om du ändrar de här inställningarna endast ändras dem för det här webbprogrammet. Standardinställningarna för din webbtjänst ändras inte. Exempel: Om du ändrar texten i **beskrivning** här är den ändrar inte den beskrivning som visas på instrumentpanelen för webbtjänsten i Machine Learning Studio.
   > 
   > 
   
    När du är klar väljer du **spara ändringar**, och välj sedan **gå till startsidan**.

7. Du kan ange värden för att skicka till din webbtjänst från startsidan. Välj **skicka** när du är klar och resultatet returneras.

Om du vill gå tillbaka till den **Configuration** går du till den **setting.aspx** sidan i webbappen. Till exempel Gå till **http://carprediction.azurewebsites.net/setting.aspx**. Du uppmanas att ange API-nyckeln igen. Du behöver att för att komma åt sidan och uppdatera inställningarna.

Du kan stoppa, starta om eller ta bort webbappen i Azure-portalen som alla andra webbappar. Så länge som den körs, kan du bläddra till hemnätverk webbadress och ange nya värden.

## <a name="how-to-use-the-batch-execution-service-template"></a>Hur du använder Batch Execution Service-mall
Du kan använda mallen BES web app på samma sätt som RRS-mall. Skillnaden är att du kan använda webbappen att skicka flera rader med data och ta emot flera resultat.

Indatavärden för en webbtjänst för batch-körningen kan komma från Azure Storage eller en lokal fil. Resultaten lagras i en Azure storage-behållare. Därför behöver du en Azure storage-behållare för resultat som webbappen returnerar. Du måste också att förbereda dina indata.

![Processen för att använda mallen för BES-webb][image2]

1. Följ samma procedur för att skapa webbappen BES för RRS-mallen. Men i det här fallet går du till [Webbappmall för Azure ML Batch Execution Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) öppna mallen för BES i Azure Marketplace. Välj **skapa Webbapp**.

2. För att ange var du vill att resultaten lagras, anger du information för mål-behållaren på webbappens startsida. Även ange där webbappen kan få indatavärdena: i en lokal fil eller i en Azure storage-behållare.
   Välj **skicka**.
   
   ![Lagringsinformation][image7]

Webbappen visar en sida med jobbstatus. När jobbet har slutförts visas platsen för resultaten i Azure Blob storage. Du har också möjlighet att hämta resultaten till en lokal fil.

## <a name="for-more-information"></a>Mer information
Läs mer om:

* Skapa en machine learning-experiment med Machine Learning Studio finns i [skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md).
* Hur du distribuerar din machine learning-experiment som en webbtjänst finns i [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).
* Andra sätt att komma åt din webbtjänst finns i [hur du använder en Azure Machine Learning-webbtjänst](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
