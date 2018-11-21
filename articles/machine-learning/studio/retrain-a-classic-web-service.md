---
title: Omtrimma en klassisk webbtjänst | Microsoft Docs
description: Lär dig hur du tränar en modell och uppdatera webbtjänsten för att använda den nyligen tränade modellen i Azure Machine Learning programmässigt.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: bd8fb59390bc54e5819183d13f16a557d62ec7ce
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52260757"
---
# <a name="retrain-a-classic-web-service"></a>Omtrimma en klassisk webbtjänst
Förutsägande webbtjänsten som du har distribuerat är standardinställningen bedömningsslutpunkten. Standardslutpunkterna hålls synkroniserade med den ursprungliga träningen och bedömning experiment och därför den tränade modellen för standardslutpunkten kan inte ersättas. För att träna om webbtjänsten, måste du lägga till en ny slutpunkt för webbtjänsten. 

## <a name="prerequisites"></a>Förutsättningar
Du måste har ställt in ett träningsexperiment och ett förutsägelseexperiment som visas i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Förutsägbart experiment måste distribueras som en klassisk machine learning-webbtjänst. 
> 
> 

Ytterligare information om distribuera webbtjänster finns i [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Lägg till en ny slutpunkt
Förutsägande webbtjänsten som du har distribuerat innehåller en bedömningsslutpunkten som hålls synkroniserad med den ursprungliga utbildningen och bedömning experiment tränade modellen. Om du vill uppdatera webbtjänsten till med en ny tränad modell, måste du skapa en ny bedömnings-slutpunkt. 

Skapa en ny bedömnings-slutpunkt på den förebyggande webbtjänst som kan uppdateras med den tränade modellen:

> [!NOTE]
> Var noga med att du lägger till slutpunkten för förutsägande Web Service, inte webbtjänsten utbildning. Om du har distribuerat både ett utbildnings- och en förutsägbar webbtjänst korrekt, bör du se två separata webbtjänster som visas. Förutsägande webbtjänsten ska sluta med ”[förutsägande exp.]”.
> 
> 

Det finns två sätt som du kan lägga till en ny slutpunkt till en webbtjänst:

1. Programmässigt
2. Använda portalen för Microsoft Azure-webbtjänster

### <a name="programmatically-add-an-endpoint"></a>Lägga till en slutpunkt
Du kan lägga till bedömnings slutpunkter med hjälp av exempelkoden i den här [github-lagringsplatsen](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Använda Microsoft Azure Web Services-portalen för att lägga till en slutpunkt
1. Klicka på webbtjänster i Machine Learning Studio, på den vänstra kolumnen.
2. Längst ned i instrumentpanelen för webbtjänsten klickar du på **hantera slutpunkter förhandsversion**.
3. Klicka på **Lägg till**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns i [aktivera loggning för Machine Learning web services](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Uppdatera har lagts till slutpunkten tränade modellen
Om du vill slutföra omtränings, måste du uppdatera den tränade modellen för den nya slutpunkten som du har lagt till.

Om du har lagt till slutpunkten med exempelkoden som inkluderar platsen för hjälp-URL som identifieras av den *HelpLocationURL* värdet i utdata.

Att hämta sökvägen URL: en:

1. Kopiera och klistra in URL: en i webbläsaren.
2. Klicka på länken resursuppdatering.
3. Kopiera POST-URL: en för PATCH-begäran. Exempel:
   
     PATCH-URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Du kan nu använda den tränade modellen för att uppdatera bedömnings slutpunkten som du skapade tidigare.

Följande exempelkod visar hur du använder den *BaseLocation*, *RelativeLocation*, *SasBlobToken*, och KORRIGERA URL: en att uppdatera slutpunkten.

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

Den *apiKey* och *endpointUrl* för anropet kan hämtas från slutpunkt-instrumentpanelen.

Värdet för den *namn* parameter i *resurser* ska matcha resursnamnet för den sparade tränade modellen i förutsägbart experiment. Hämta resursnamnet:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn klickar du på **Maskininlärning**.
3. Under namn klickar du på din arbetsyta och klicka sedan på **webbtjänster**.
4. Under namn, klickar du på **insamlade modell [förutsägande exp.]** .
5. Klicka på den nya slutpunkten som du har lagt till.
6. På endpoint-instrumentpanelen klickar du på **resursuppdatering**.
7. På sidan Update Resource API-dokumentation för webbtjänsten som du kan hitta den **resursnamn** under **uppdateras resurser**.

Om din SAS-token upphör att gälla innan du har uppdaterat slutpunkten, måste du utföra en hämtning med jobb-Id för att hämta en ny token.

När koden har körts, ska den nya slutpunkten börja använda retrained modellen i ungefär 30 sekunder.

## <a name="summary"></a>Sammanfattning
Med API: erna Retraining kan uppdatera du den tränade modellen av en förutsägbar webbtjänst att aktivera scenarier som:

* Periodiska modell träna med nya data.
* Distribution av en modell för kunder med målet att så att de kan träna modellen med sina egna data.

## <a name="next-steps"></a>Nästa steg
[Felsöka omtrimning av en klassisk Azure Machine Learning-webbtjänst](troubleshooting-retraining-models.md)

