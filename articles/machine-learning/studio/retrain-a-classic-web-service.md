---
title: Träna om en klassisk webbtjänst | Microsoft Docs
description: Lär dig mer om att träna om en modell och uppdatera webbtjänsten för att använda den nya tränade modellen i Azure Machine Learning programmässigt.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: d0c40d952008f58f9b99c1c44d06e971a1e60c3b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-a-classic-web-service"></a>Omtrimma en klassisk webbtjänst
Förutsägande webbtjänsten som du har distribuerat är standard bedömningsslutpunkten. Slutpunkter hålls synkroniserade med den ursprungliga utbildningen och bedömningen experiment och därför den tränade modellen för standardslutpunkten kan inte ersättas. För att träna om webbtjänsten måste du lägga till en ny slutpunkt till webbtjänsten. 

## <a name="prerequisites"></a>Förutsättningar
Du måste ha konfigurerat en träningsexperiment och prediktivt experiment som visas i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Prediktivt experiment måste distribueras som klassisk machine learning-webbtjänst. 
> 
> 

Ytterligare information om distribuera webbtjänster finns [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Lägg till en ny slutpunkt
Den förutsägande webbtjänst som du har distribuerat innehåller en bedömningsslutpunkten som hålls synkroniserade med den ursprungliga utbildningen och bedömningen experiment tränade modellen. Om du vill uppdatera webbtjänsten till med en ny tränad modell, måste du skapa en ny bedömningsprofil slutpunkt. 

Skapa en ny bedömningsprofil slutpunkt på den förutsägande webbtjänst som kan uppdateras med den tränade modellen:

> [!NOTE]
> Var noga med att du lägger till slutpunkten förutsägande webbtjänsten inte utbildning-webbtjänsten. Om du har distribuerat en utbildnings- och en förutsägbar webbtjänst korrekt, bör du se två separata webbtjänster som anges. Förutsägande webbtjänsten ska avslutas med ”[förutsägande exp.]”.
> 
> 

Det finns två sätt som du kan lägga till en ny slutpunkt till en webbtjänst:

1. Programmässigt
2. Webbtjänster för Microsoft Azure-portalen

### <a name="programmatically-add-an-endpoint"></a>Lägga till en slutpunkt
Du kan lägga till bedömningsprofil slutpunkter som använder exempelkoden i den här [github-lagringsplatsen](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Använda webbtjänster för Microsoft Azure-portalen för att lägga till en slutpunkt
1. Klicka på webbtjänster på kolumnen navigeringen till vänster i Machine Learning Studio.
2. Längst ned i web service instrumentpanelen klickar du på **hantera slutpunkter preview**.
3. Klicka på **Lägg till**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns [aktivera loggning för Machine Learning-webbtjänster](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Uppdatera tillagda slutpunkten tränade modellen
Om du vill slutföra omtränings, måste du uppdatera den tränade modellen för den nya slutpunkt som du har lagt till.

Om du har lagt till slutpunkten med exempelkoden inkluderar plats för hjälp-URL som identifieras av den *HelpLocationURL* värdet i utdata.

Att hämta sökvägen-URL:

1. Kopiera och klistra in Webbadressen i webbläsaren.
2. Klicka på länken Update resurs.
3. Kopiera URL-Adressen för POST till PATCH-begäran. Exempel:
   
     PATCH-URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Du kan nu använda den tränade modellen för att uppdatera bedömningsprofil slutpunkten som du skapade tidigare.

Följande exempelkod visar hur du använder den *BaseLocation*, *RelativeLocation*, *SasBlobToken*, och KORRIGERA URL för att uppdatera slutpunkten.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

Den *apiKey* och *endpointUrl* för anropet kan hämtas från slutpunkten instrumentpanelen.

Värdet för den *namn* parameter i *resurser* ska matcha namnet på den sparade tränade modellen i prediktivt experiment resurs. Hämta resursnamnet:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn klickar du på **Maskininlärning**.
3. Klicka på arbetsytan under namn och klicka sedan på **Web Services**.
4. Under namn, klickar du på **inventering modellen [förutsägande exp].** .
5. Klicka på ny slutpunkt som du har lagt till.
6. Klicka på infopanelen endpoint **uppdatering resurs**.
7. På sidan uppdatera resurs API-dokumentationen för webbtjänsten hittar du den **resursnamnet** under **uppdateras resurser**.

Om din SAS-token upphör att gälla innan du har uppdaterat slutpunkten, måste du utföra GET med jobb-Id för att få en ny token.

När koden har körts, ska ny slutpunkt börja använda retrained modellen cirka 30 sekunder.

## <a name="summary"></a>Sammanfattning
Du kan använda Omtränings-API för att uppdatera den tränade modellen av en förutsägbar webbtjänsten för att aktivera scenarier som:

* Periodiska modell via programmering med nya data.
* Distribution av en modell för kunder med målet att låta dem träna om modellen med sina egna data.

## <a name="next-steps"></a>Nästa steg
[Felsökning av omtränings av en klassisk Azure Machine Learning-webbtjänst](troubleshooting-retraining-models.md)

