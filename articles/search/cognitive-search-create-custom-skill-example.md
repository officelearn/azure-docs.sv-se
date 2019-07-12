---
title: 'Exempel: Skapa en anpassad kognitiva kunskaper med den Entitetssökning i Bing – Azure Search'
description: Visar hur du använder tjänsten Entitetssökning i Bing i en anpassad kunskap som mappats till en kognitiv sökning indexering av pipeline i Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672150"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exempel: Skapa en anpassad kompetens med den Entitetssökning i Bing

Lär dig hur du skapar ett web API anpassade färdighet i det här exemplet. Kompetensen accepterar platser, offentliga figurer och organisationer, och returnera beskrivningar för dessa. I exemplet används en [Azure Function](https://azure.microsoft.com/services/functions/) du omsluter den [Entitetssökning i Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) så att den implementerar gränssnittet anpassade färdigheter.

## <a name="prerequisites"></a>Förutsättningar

+ Läs mer om [anpassade färdighet gränssnittet](cognitive-search-custom-skill-interface.md) artikel om du inte är bekant med indata/utdata-gränssnitt som en anpassad färdighet bör implementera.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) eller senare, inklusive arbetsbelastningen Azure development.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Även om det här exemplet använder en Azure-funktion som värd för ett webb-API kan det inte krävs.  Förutsatt att du uppfyller de [gränssnitt för en kognitiva kunskaper](cognitive-search-custom-skill-interface.md), den metod som du använder är utan betydelse. Azure Functions kan dock göra det enkelt att skapa en anpassad kunskap.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

1. I Visual Studio väljer **New** > **projekt** från menyn Arkiv.

1. I dialogrutan Nytt projekt väljer **installerad**, expandera **Visual C#**  > **molnet**väljer **Azure Functions**, ange ett Namn för projektet och välj **OK**. Funktionsappens namn måste vara ett giltigt en C# namnområde, använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Välj **v2 för Azure Functions (.NET Core)** . Du kan också göra det med version 1, men den kod som skrivs nedan baseras på v2-mall.

1. Välj den typ som ska vara **HTTP-utlösare**

1. För Storage-konto kan du välja **ingen**, som du inte behöver all lagring för den här funktionen.

1. Välj **OK** skapa funktionen projekt och HTTP-utlöst funktion.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Ändra koden för att anropa söktjänsten för Bing-entitet

Visual Studio skapar ett projekt och i det en klass som innehåller formaterad exempelkod för den valda typen. Attributet *FunctionName* i metoden anger namnet på funktionen. Attributet *HttpTrigger* anger att funktionen utlöses av en HTTP-förfrågan.

Nu kan byta ut all innehållet i filen *Function1.cs* med följande kod:

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
    /// cognitive search pipeline.
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

Kontrollera att du anger din egen *nyckel* värde i den `key` konstant baserat på den nyckel som du fick när du registrerar dig för entitetssökning i Bing API.

Det här exemplet innehåller alla nödvändig kod i en enda fil för att underlätta. Du hittar en något mer strukturerade version av den samma färdigheten i [power kunskaper databasen](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Naturligtvis kan du kan byta namn på filen från `Function1.cs` till `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

Tryck på **F5** att köra de program och testa funktionen beteenden. I det här fallet använder vi funktionen nedan för att leta upp två entiteter. Använda Postman eller Fiddler för att utfärda en uppmaning som liknar den nedan:

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

## <a name="publish-the-function-to-azure"></a>Publicera funktionen till Azure

När du är nöjd med funktionen beteendet kan publicera du den.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. Välj **skapa en ny** > **publicera**.

1. Om du inte redan anslutit Visual Studio till ditt Azure-konto, väljer **Lägg till ett konto...**

1. Följ den anvisningarna på skärmen. Du uppmanas att ange ett unikt namn för din app service, Azure-prenumerationen, resursgruppen, värdplanen och storage-konto som du vill använda. Du kan skapa en ny resursgrupp, en ny värdplan och ett lagringskonto om du inte redan har dessa. När du är klar väljer **skapa**

1. När distributionen är klar ser du webbplatsens URL. Det är adressen till funktionsappen i Azure. 

1. I den [Azure-portalen](https://portal.azure.com), navigera till resursgruppen igen och leta efter den `EntitySearch` funktionen som du har publicerat. Under den **hantera** avsnittet bör du se Värdnycklar. Välj den **kopia** ikonen för den *standard* värdnyckel.  

## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har standard-värdnyckeln kan du testa din funktion på följande sätt:

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

Det här exemplet ska ge samma resultat som du såg tidigare när du kör funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Ansluta till din pipeline
Nu när du har en ny anpassad kunskap kan du lägga till det din kompetens. Exemplet nedan visar hur du anropar kunskaper för att lägga till beskrivningar för organisationer i dokumentet (Detta kan utökas för att fungera även om platser och personer). Ersätt `[your-entity-search-app-name]` med namnet på din app.

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

Här kan vi räknar med att inbyggt [entitet erkännande färdighet](cognitive-search-skill-entity-recognition.md) måste finnas i gruppens kunskaper avgör och har utökat dokumentet med listan över organisationer. Här är en konfiguration för entiteten extrahering färdigheter som skulle vara tillräckligt generera information vi behöver referens:

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
Grattis! Du har skapat din första anpassade enricher. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. 

+ [Lägg till en anpassad kompetens för en pipeline för kognitiv sökning](cognitive-search-custom-skill-interface.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
