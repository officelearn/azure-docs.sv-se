---
title: Exempel på anpassad färdighet med api för sökning av Bing-entitet
titleSuffix: Azure Cognitive Search
description: Demonstrerar med hjälp av tjänsten Bing Entitetssökning i en anpassad färdighet som mappas till en AI-berikad indexeringspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113812"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exempel: Skapa en anpassad färdighet med hjälp av API:et för sökning efter Bing-entitet

I det här exemplet lär du dig hur du skapar en anpassad webb-API-färdighet. Den här färdigheten accepterar platser, offentliga personer och organisationer och returnerar beskrivningar för dem. I exemplet används en [Azure-funktion](https://azure.microsoft.com/services/functions/) för att radbryt [API:et för Bing-entitetssökning](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) så att det implementerar det anpassade färdighetsgränssnittet.

## <a name="prerequisites"></a>Krav

+ Läs om anpassade [färdighetsgränssnittsartikel](cognitive-search-custom-skill-interface.md) om du inte är bekant med indata-/utdatagränssnittet som en anpassad färdighet bör implementera.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) eller senare, inklusive Azure-utvecklingsarbetsbelastningen.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Även om det här exemplet använder en Azure-funktion som värd för ett webb-API, krävs det inte.  Så länge du uppfyller [gränssnittskraven för en kognitiv färdighet](cognitive-search-custom-skill-interface.md)är det tillvägagångssätt du tar oväsentligt. Azure Functions gör det dock enkelt att skapa en anpassad färdighet.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

1. Välj **Nytt** > **projekt** på Arkiv-menyn i Visual Studio.

1. I dialogrutan Nytt projekt väljer du **Installerad**, expanderar **Visual C#** > **Cloud**, väljer **Azure Functions**, skriver ett namn för projektet och väljer **OK**. Funktionsappnamnet måste vara giltigt som ett C#-namnområde, så använd inte understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Välj **Azure-funktioner v2 (.NET Core)**. Du kan också göra det med version 1, men koden nedan är baserad på v2-mallen.

1. Välj den typ som ska vara **HTTP-utlösare**

1. För lagringskonto kan du välja **Ingen**, eftersom du inte behöver någon lagring för den här funktionen.

1. Välj **OK** om du vill skapa funktionen funktionsprojekt och HTTP-utlöst funktion.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Ändra koden för att anropa söktjänsten för Bing-entitet

Visual Studio skapar ett projekt och i det en klass som innehåller formaterad exempelkod för den valda typen. Attributet *FunctionName* i metoden anger namnet på funktionen. Attributet *HttpTrigger* anger att funktionen utlöses av en HTTP-förfrågan.

Nu, ersätta allt innehåll i filen *Function1.cs* med följande kod:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Se till att ange ditt `key` eget *nyckelvärde* i konstanten baserat på nyckeln du fick när du registrerar dig för Bing-entitetens sök-API.

Det här exemplet innehåller all nödvändig kod i en enda fil för enkelhetens skull. Du kan hitta en något mer strukturerad version av samma färdighet i [power skills-databasen](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Naturligtvis kan du byta namn `Function1.cs` på `BingEntitySearch.cs`filen från till .

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

Tryck på **F5** för att köra programmet och testa funktionsbeteenden. I det här fallet använder vi funktionen nedan för att slå upp två entiteter. Använd Postman eller Fiddler för att utfärda ett samtal som det som visas nedan:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Begärandetext
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicera funktionen i Azure

När du är nöjd med funktionsbeteendet kan du publicera det.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. Välj **Skapa ny** > **publicering**.

1. Om du inte redan har anslutit Visual Studio till ditt Azure-konto väljer du **Lägg till ett konto....**

1. Följ anvisningarna på skärmen. Du uppmanas att ange ett unikt namn för din apptjänst, Azure-prenumerationen, resursgruppen, värdplanen och det lagringskonto som du vill använda. Du kan skapa en ny resursgrupp, en ny värdplan och ett lagringskonto om du inte redan har dessa. När du är klar väljer du **Skapa**

1. När distributionen är klar lägger du märke till webbadressen. Det är adressen till din funktionsapp i Azure. 

1. I [Azure-portalen](https://portal.azure.com)navigerar du till resursgruppen och letar efter den `EntitySearch` funktion som du publicerade. Under avsnittet **Hantera** bör du se Värdnycklar. Välj ikonen **Kopiera** för *standardvärden.*  

## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har standardvärdentt för att testa funktionen på följande sätt:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Begärandetext
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Det här exemplet bör ge samma resultat som du såg tidigare när du kör funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Ansluta till pipelinen
Nu när du har en ny anpassad färdighet kan du lägga till den i din kompetens. Exemplet nedan visar hur du anropar färdigheten för att lägga till beskrivningar till organisationer i dokumentet (detta kan utökas till att även arbeta på platser och personer). Ersätt `[your-entity-search-app-name]` med namnet på appen.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Här räknar vi med att den inbyggda [entitetsigenkänningsfärdigheten](cognitive-search-skill-entity-recognition.md) ska finnas med i kompetensen och ha berikat dokumentet med organisationslistan. Som referens, här är en enhet extrahering kompetenskonfiguration som skulle vara tillräckligt för att generera de data vi behöver:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första anpassade färdighet. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. Klicka på följande länkar om du vill veta mer.

+ [Power Skills: en databas med anpassade färdigheter](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Lägga till en anpassad färdighet i en AI-anrikningspipeline](cognitive-search-custom-skill-interface.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Så här mappar du berikade fält](cognitive-search-output-field-mapping.md)
