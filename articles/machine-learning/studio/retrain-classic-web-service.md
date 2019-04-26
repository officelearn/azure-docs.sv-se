---
title: Träna om och distribuera en klassisk webbtjänst
titleSuffix: Azure Machine Learning Studio
description: Lär dig hur du tränar en modell och uppdatera en klassisk webbtjänst för att använda den nyligen tränade modellen i Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterlu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: fa4448c2a44a3c56548120bd04abf53df9a85ba0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502443"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>Träna och distribuera en klassiska Studio-webbtjänst

Träna modeller i machine learning är ett sätt att se till att de förblir korrekta och baserat på de mest relevanta data som är tillgängliga. Den här artikeln visar hur du tränar en klassisk Studio-webbtjänst. En guide om hur du tränar en ny Studio-webbtjänst [visa i den här artikeln.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln förutsätter att du redan har både ett omtränings experiment och ett förutsägbart experiment. De här stegen beskrivs i [tränar om och distribuera en modell för maskininlärning.](/azure/machine-learning/studio/retrain-machine-learning-model) Men i stället för att distribuera machine learning-modell som en ny webbtjänst, distribuerar du din förutsägelseexperiment som en klassiska webbtjänst.
     
## <a name="add-a-new-endpoint"></a>Lägg till en ny slutpunkt

Förutsägbar webbtjänst som du har distribuerat innehåller en bedömningsslutpunkten som hålls synkroniserad med den ursprungliga utbildningen och bedömning experiment tränade modellen. Om du vill uppdatera webbtjänsten till med en ny tränad modell, måste du skapa en ny bedömnings-slutpunkt.

Det finns två sätt som du kan lägga till en ny slutpunkt till en webbtjänst:

* Programmässigt
* Med hjälp av Azure Web Services-portalen

> [!NOTE]
> Var noga med att du lägger till slutpunkten för förutsägande Web Service, inte webbtjänsten utbildning. Om du har distribuerat både ett utbildnings- och en förutsägbar webbtjänst korrekt, bör du se två separata webbtjänster som visas. Förutsägande webbtjänsten ska sluta med ”[förutsägande exp.]”.
>

### <a name="programmatically-add-an-endpoint"></a>Lägga till en slutpunkt

Du kan lägga till bedömnings slutpunkter med hjälp av exempelkoden i den här [GitHub-lagringsplatsen](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Använd Azure Web Services-portalen för att lägga till en slutpunkt

1. Klicka på webbtjänster i Machine Learning Studio, på den vänstra kolumnen.
1. Längst ned i instrumentpanelen för webbtjänsten klickar du på **hantera slutpunkter förhandsversion**.
1. Klicka på **Lägg till**.
1. Skriv ett namn och beskrivning för den nya slutpunkten. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns i [aktivera loggning för Machine Learning web services](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Uppdatera har lagts till slutpunkten tränade modellen

### <a name="retrieve-patch-url"></a>Hämta PATCH-URL

### <a name="option-1-programmatically"></a>Alternativ 1: Programmässigt

Följ dessa steg för att få rätt KORRIGERA URL programmässigt kan:

1. Kör den [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) exempelkoden.
1. Från utdata från AddEndpoint, hitta den *HelpLocation* värde och kopiera URL: en.

   ![HelpLocation i utdata från addEndpoint-exemplet.](./media/retrain-classic/addEndpoint-output.png)
1. Klistra in URL: en i en webbläsare och navigera till en sida som innehåller länkar för webbtjänsten.
1. Klicka på den **resursuppdatering** länk för att öppna sidan patch hjälp.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Alternativ 2: Använd Azure Machine Learning Web Services-portalen

Följ dessa steg för att få rätt KORRIGERA URL: en med hjälp av webbportalen:

1. Logga in på den [Azure Machine Learning Web Services](https://services.azureml.net/) portal.
1. Klicka på **webbtjänster** eller **klassiska webbtjänster** högst upp.
1. Klicka på bedömnings webbtjänst som du arbetar med (om du använde standardnamnet för webbtjänsten, upphör den i ”[beräkning Exp.]”).
1. Klicka på **+ ny**.
1. När slutpunkten har lagts till, klickar du på namnet på slutpunkten.
1. Under den **Patch** URL: en, klicka på **API Help** att öppna sidan uppdatering hjälp.

> [!NOTE]
> Lägg till slutpunkten till webbtjänsten utbildning i stället för förutsägande webbtjänsten och du får följande felmeddelande när du klickar på den **resursuppdatering** länk: ”Vi beklagar, men den här funktionen är inte eller är inte tillgängligt i den här kontexten. Den här webbtjänsten har inga uppdateringsbara resurser. Vi ber om ursäkt för besväret och arbetar på att förbättra det här arbetsflödet ”.
>

PATCH-hjälpsidan innehåller KORRIGERA URL: en måste du använda och visar exempelkod som du kan använda för att anropa den.

![Patch-URL.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Uppdatera slutpunkten

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
1. I den vänstra menyn klickar du på **Maskininlärning**.
1. Under namn klickar du på din arbetsyta och klicka sedan på **webbtjänster**.
1. Under namn, klickar du på **insamlade modell [förutsägande exp.]** .
1. Klicka på den nya slutpunkten som du har lagt till.
1. På endpoint-instrumentpanelen klickar du på **resursuppdatering**.
1. På sidan Update Resource API-dokumentation för webbtjänsten som du kan hitta den **resursnamn** under **uppdateras resurser**.

Om din SAS-token upphör att gälla innan du har uppdaterat slutpunkten, måste du utföra en hämtning med jobb-ID för att hämta en ny token.

När koden har körts, ska den nya slutpunkten börja använda retrained modellen i ungefär 30 sekunder.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du hanterar webbtjänster och hålla reda på flera experiment körs, finns i följande artiklar:

* [Utforska Web Services-portalen](manage-new-webservice.md)
* [Hantera iterationer av experiment](manage-experiment-iterations.md)