---
title: 'Exempel: Skapa en anpassad kunskap i kognitiva Sök pipeline (Azure Search) | Microsoft Docs'
description: Visar med API översätta Text i anpassade kunskaper som mappas till en kognitiva sökning indexering pipeline i Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 056cff192b25068fa2e895fd46d143a834b7af0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641092"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Exempel: Skapa en anpassad kunskap med API översätta Text

I det här exemplet lär du dig hur du skapar ett web API anpassade kunskaper som accepterar text på alla språk och översätts till engelska. I exemplet används en [Azure-funktion](https://azure.microsoft.com/services/functions/) att omsluta den [översätta API: et](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) så att den implementerar gränssnittet anpassade kunskaper.

## <a name="prerequisites"></a>Förutsättningar

+ Läs mer om [anpassade kunskaper gränssnittet](cognitive-search-custom-skill-interface.md) artikel om du inte är bekant med in-/ utdata-gränssnitt som en anpassad kunskap ska implementera.

+ [Registrera dig för översättare Text API](../cognitive-services/translator/translator-text-how-to-signup.md), och få en API-nyckel för att använda den.

+ Installera [Visual Studio 2017 version 15,5](https://www.visualstudio.com/vs/) eller senare, inklusive Azure-utveckling arbetsbelastningen.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Även om det här exemplet används en Azure-funktion som värd för en webb-API, är det inte krävs.  Förutsatt att du uppfyller de [gränssnitt kraven för en kognitiva kunskap](cognitive-search-custom-skill-interface.md), en metod som du har betydelse. Azure Functions dock göra det enkelt att skapa en anpassad kunskap.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

1. I Visual Studio väljer **ny** > **projekt** från menyn Arkiv.

1. Välj i dialogrutan Nytt projekt **installerad**, expandera **Visual C#** > **moln**väljer **Azure Functions**, ange en Namn för ditt projekt och välj **OK**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Välj den typ som ska vara **http-utlösare**

1. Du kan välja för Lagringskonto **ingen**, som du inte behöver all lagring för den här funktionen.

1. Välj **OK** skapa funktionen projekt och HTTP aktiveras funktionen.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Ändra koden för att anropa tjänsten översätta kognitiva

Visual Studio skapar ett projekt och i det en klass som innehåller formaterad exempelkod för den valda typen. Attributet *FunctionName* i metoden anger namnet på funktionen. Attributet *HttpTrigger* anger att funktionen utlöses av en HTTP-förfrågan.

Ersätt hela innehållet i filen nu *Function1.cs* med följande kod:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion


        /// <summary>
        /// Note that this function can translate up to 1000 characters. If you expect to need to translate more characters, use 
        /// the paginator skill before calling this custom enricher
        /// </summary>
        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, 
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string originalLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            originalLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            // Only translate records that actually need to be translated. 
            if (!originalLanguage.Contains("en"))
            {
                translatedText = TranslateText(originalText, "en-us").Result;
            }
            else
            {
                // text is already in English.
                translatedText = originalText;
            }

            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response); 
        }

        /// <summary>
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="myText">The text to translate</param>
        /// <param name="destinationLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string myText, string destinationLanguage)
        {
            string host = "https://api.microsofttranslator.com";
            string path = "/V2/Http.svc/Translate";

            // NOTE: Replace this example key with a valid subscription key.
            string key = "064d8095730d4a99b49f4bcf16ac67f8";

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            List<KeyValuePair<string, string>> list = new List<KeyValuePair<string, string>>() {
                new KeyValuePair<string, string>(myText, "en-us")
            };

            StringBuilder totalResult = new StringBuilder();

            foreach (KeyValuePair<string, string> i in list)
            {
                string uri = host + path + "?to=" + i.Value + "&text=" + System.Net.WebUtility.UrlEncode(i.Key);

                HttpResponseMessage response = await client.GetAsync(uri);

                string result = await response.Content.ReadAsStringAsync();

                // Parse the response XML
                System.Xml.XmlDocument xmlResponse = new System.Xml.XmlDocument();
                xmlResponse.LoadXml(result);
                totalResult.Append(xmlResponse.InnerText); 
            }

            return totalResult.ToString();
        }
    }
}
```

Se till att ange egna *nyckeln* värde i den *TranslateText* metod baserat på den nyckel som du fick när du registrerar dig för API: et för översätta Text.

Det här exemplet är en enkel enricher som fungerar bara på en post i taget. Detta är viktigt senare när du ställer in batchstorlek för kunskaper.

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

Tryck på **F5** att köra programmet och testa funktionen beteenden. Använd Postman eller Fiddler för att utfärda ett anrop som visas nedan:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Begärandetext
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "es"
            }
        }
   ]
}
```
### <a name="response"></a>Svar
Du bör se ett svar som liknar följande exempel:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicera funktionen till Azure

Du kan publicera när du är nöjd med funktionsbeteende.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. Välj **skapa nya** > **publicera**.

1. Om du inte har redan anslutit Visual Studio till ditt Azure-konto, Välj **Lägg till ett konto...**

1. Följer den instruktionerna på skärmen. Du uppmanas att ange det Azure-kontot, resursgruppen, värd planen och storage-konto som du vill använda. Du kan skapa en ny resursgrupp, en ny värd plan och storage-konto om du inte redan har dessa. När du är klar väljer **skapa**

1. När distributionen är klar kan du Observera webbplatsens URL. Det är adressen till appen funktionen i Azure. 

1. I den [Azure-portalen](https://portal.azure.com), navigera till resursgruppen och leta efter översätta funktionen som du har publicerat. Under den **hantera** avsnitt, bör du se Värdnycklar. Välj den **kopiera** ikonen för den *standard* värdnyckel.  


## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har värden standardnyckeln testa din funktion på följande sätt:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Begärandetext
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "es"
            }
        }
   ]
}
```

Detta bör ge en liknande resultatet med det som du såg tidigare när du kör funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Ansluta till din pipeline
Nu när du har nya anpassade kunskaper kan du lägga till den dina kunskaper. Exemplet nedan visar hur du anropar kompetensen. Eftersom kompetensen kan inte hantera batchar, lägger du till en instruktion för Maximal batchstorlek ska bara ```1``` dokument att skicka en i taget.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första anpassade enricher. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. 

+ [Lägg till en anpassad kunskap för en pipeline kognitiva sökning](cognitive-search-custom-skill-interface.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Skapa kunskaper (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Utökade mappning](cognitive-search-output-field-mapping.md)