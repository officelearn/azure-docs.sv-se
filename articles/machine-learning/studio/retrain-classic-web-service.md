---
title: Omskola en klassisk webbtjänst
titleSuffix: ML Studio (classic) - Azure
description: Lär dig hur du tränar om en modell och uppdaterar en klassisk webbtjänst för att använda den nyutbildade modellen i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 8094d64eab1a4b25a76554bf9eb6848c2e4d3493
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204247"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Omskola och distribuera en klassisk Studio (klassisk) webbtjänst

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Omskolning maskininlärningsmodeller är ett sätt att se till att de förblir korrekta och baseras på de mest relevanta tillgängliga uppgifterna. Denna artikel kommer att visa dig hur du omskola en klassisk Studio (klassisk) webbtjänst. En guide om hur du omskolar en ny studiowebbtjänst (klassisk) [finns i den här artikeln.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har både ett omskolningsexperiment och ett prediktivt experiment. Dessa steg förklaras i [Omskola och distribuera en maskininlärningsmodell.](/azure/machine-learning/studio/retrain-machine-learning-model) Men i stället för att distribuera din maskininlärningsmodell som en ny webbtjänst distribuerar du ditt förutsägelseexperiment som en klassisk webbtjänst.
     
## <a name="add-a-new-endpoint"></a>Lägga till en ny slutpunkt

Den förutsägande webbtjänsten som du har distribuerat innehåller en standardslutpunkt för bedömning som hålls synkroniserad med den ursprungliga tränings- och bedömningsexperimenten som tränas. Om du vill uppdatera webbtjänsten till med en ny tränad modell måste du skapa en ny bedömningsslutpunkt.

Det finns två sätt att lägga till en ny slutpunkt i en webbtjänst:

* Programmässigt
* Använda Azure Web Services-portalen

> [!NOTE]
> Se till att du lägger till slutpunkten i den förutsägande webbtjänsten, inte utbildningswebbtjänsten. Om du har distribuerat både en utbildning och en förutsägande webbtjänst korrekt bör du se två separata webbtjänster i listan. Den prediktiva webbtjänsten ska sluta med "[predictive exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Lägg till en slutpunkt programmässigt

Du kan lägga till bedömningsslutpunkter med hjälp av exempelkoden som finns i den här [GitHub-databasen](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Använda Azure Web Services-portalen för att lägga till en slutpunkt

1. Klicka på Webbtjänster i kolumnen Machine Learning Studio (klassiskt) i den vänstra navigeringskolumnen.
1. Klicka på **Hantera förhandsgranskning**av slutpunkter längst ned på webbtjänstens instrumentpanel.
1. Klicka på **Lägg till**.
1. Skriv ett namn och en beskrivning för den nya slutpunkten. Välj loggningsnivå och om exempeldata är aktiverade. Mer information om loggning finns i [Aktivera loggning för machine learning-webbtjänster](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Uppdatera den tillagda slutpunktens tränade modell

### <a name="retrieve-patch-url"></a>Hämta PATCH-URL

Följ dessa steg för att få rätt PATCH URL med hjälp av webbportalen:

1. Logga in på Azure Machine Learning Web Services-portalen. [Azure Machine Learning Web Services](https://services.azureml.net/)
1. Klicka på **Webbtjänster** eller **Klassiska webbtjänster** högst upp.
1. Klicka på den bedömningswebbtjänst som du arbetar med (om du inte har ändrat webbtjänstens standardnamn slutar den i "[Scoring Exp.]").
1. Klicka på **+NY**.
1. När slutpunkten har lagts till klickar du på slutpunktsnamnet.
1. Under **korrigeringsadressen** klickar du på **API-hjälpen** för att öppna hjälpsidan för korrigeringsfiler.

> [!NOTE]
> Om du har lagt till slutpunkten i utbildningswebbtjänsten i stället för den prediktiva webbtjänsten visas följande felmeddelande när du klickar på länken **Uppdatera resurs:** "Tyvärr, men den här funktionen stöds inte eller är tillgänglig i den här kontexten. Den här webbtjänsten har inga uppdateringsbara resurser. Vi ber om ursäkt för besväret och arbetar med att förbättra detta arbetsflöde."
>

Sidan PATCH-hjälp innehåller den PATCH-URL som du måste använda och innehåller exempelkod som du kan använda för att anropa den.

![Url för korrigeringsfil.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Uppdatera slutpunkten

Du kan nu använda den tränade modellen för att uppdatera bedömningsslutpunkten som du skapade tidigare.

Följande exempelkod visar hur du använder *url:en för BaseLocation*, *RelativeLocation*, *SasBlobToken*och PATCH för att uppdatera slutpunkten.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
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

*ApiKey* och *endpointUrl* för anropet kan erhållas från slutpunktsinstrumentpanelen.

Värdet för parametern *Name* i *Resurser* ska matcha resursnamnet för den sparade tränade modellen i förutsägelseexperimentet. Så här hämtar du resursnamnet:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Maskininlärning**på den vänstra menyn.
1. Klicka på arbetsytan under Namn och klicka sedan på **Webbtjänster**.
1. Klicka på **Folkräkningsmodell [prediktiv exp.]** under Namn.
1. Klicka på den nya slutpunkten som du har lagt till.
1. Klicka på **Uppdatera resurs**på instrumentpanelen för slutpunkt.
1. På sidan Uppdatera resurs-API-dokumentation för webbtjänsten hittar du **resursnamnet** under **Uppdateringsbara resurser**.

Om din SAS-token upphör att gälla innan du är klar med uppdateringen av slutpunkten måste du utföra ett GET med jobb-ID:t för att få en ny token.

När koden har körts ska den nya slutpunkten börja använda den omskolade modellen om cirka 30 sekunder.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar webbtjänster eller håller reda på flera experimentkörningar finns i följande artiklar:

* [Utforska webbtjänstportalen](manage-new-webservice.md)
* [Hantera experimentupprepningar](manage-experiment-iterations.md)