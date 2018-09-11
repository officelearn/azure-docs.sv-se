---
title: 'Snabbstart: Använder C# för att anropa API för textanalys | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API för textanalys i Microsoft Cognitive Services på Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: b4d945b7495897caf1f4edd1e909581614798a23
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303029"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Använder C# för att anropa tjänsten Text Analytics Cognitive
<a name="HOLTop"></a>

Den här artikeln visar hur du identifiera språk, utför sentimentanalyser och extrahera nyckelfraser med hjälp av den [Text Analytics API: er](//go.microsoft.com/fwlink/?LinkID=759711) med C#. Koden har skrivits för att arbeta med en .NET Core-App med minimal referenser till externa bibliotek, så du kan också köra den på Linux eller MacOS.

Referera till den [API-definitioner](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API: erna.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API för textanalys. Du kan använda den *kostnadsfri nivå för 5 000 transaktioner per månad* att slutföra den här snabbstarten.

Du måste också ha den [slutpunkt och åtkomstnyckel](../How-tos/text-analytics-how-to-access-key.md) som har skapats för dig under registreringen. 


## <a name="install-the-nuget-sdk-package"></a>Installera NuGet SDK-paketet
1. Skapa en ny konsol-lösning i Visual Studio.
1. Högerklicka på lösningen och välj **hantera NuGet-paket för lösningen**.
1. Välj den **inkludera förhandsversion** markerar du kryssrutan.
1. Välj den **Bläddra** fliken och Sök efter **Microsoft.Azure.CognitiveServices.Language**.
1. Välj den **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** NuGet paketera och installera den.

> [!Tip]
> Även om du kan anropa den [HTTP-slutpunkter](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) direkt från C#, Microsoft.Azure.CognitiveServices.Language SDK gör det enklare att anropa tjänsten utan att behöva bekymra dig om serialisering och avserialisering av JSON.
>
> Här är användbara länkar:
> - [SDK NuGet-sidan](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-koden](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>Anropa API för textanalys med hjälp av SDK
1. Ersätt Program.cs med följande kod. Det här programmet visar funktionerna i API för textanalys i tre avsnitt (språk extrahering, nyckel-diskussionsämne och attitydanalys).
1. Ersätt den `Ocp-Apim-Subscription-Key` huvudets värde med en åtkomstnyckel som är giltig för prenumerationen.
1. Ersätt plats i `Endpoint` som du har registrerat sig för slutpunkten. Du hittar slutpunkten på Azure portal-resursen. Slutpunkten vanligtvis börjar med ”https://[region].api.cognitive.microsoft.com”. Inkludera endast namnet protokoll och värden.
1. Kör programmet.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;
```

## <a name="detect-language"></a>Identifiera språk

Language identifiering API identifierar språket för dokument, med hjälp av den [identifiera språk metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

```csharp
            // Extracting language.
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }
```

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser

Key frasen extrahering API: et extraherar nyckelfraser från en text dokument, med hjälp av den [Nyckelfraser metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6).

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>Analysera sentiment

API för Attitydstextanalys Analysis identifierar känslan i texten postuppsättningar med hjälp av den [Sentiment metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9).

```csharp
            // Analyzing sentiment.
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>Identifiera länkade entiteter

API för Entity Linking identifierar välkända entiteter i en text dokument, med hjälp av den [Entitetslänkning metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634).

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
