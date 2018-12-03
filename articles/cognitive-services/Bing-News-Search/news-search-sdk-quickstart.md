---
title: 'Snabbstart: SDK för nyhetssökning i Bing, C#'
titleSuffix: Azure Cognitive Services
description: Konfiguration av konsolprogrammet för SDK för nyhetssökning i Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.openlocfilehash: 447c59d45e3b1614ce8dbddc56f9dba0dd156e90
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317066"
---
# <a name="quickstart-bing-news-search-sdk-with-c"></a>Snabbstart: SDK för nyhetssökning i Bing med C#

SDK för nyhetssökning i Bing innehåller funktionerna i REST API:et för nyhetsfrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för nyhetssökning i Bing med C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch) är tillgänglig på GitHub.

## <a name="application-dependencies"></a>Programberoenden
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  

Om du vill konfigurera ett konsolprogram med hjälp av SDK:n för nyhetssökning i Bing bläddrar du till alternativet `Manage NuGet Packages` från Solution Explorer i Visual Studio.  Lägg till paketet `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

När du installerar [NuGet SDK-paketet för nyhetssökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0) installeras även beroenden, inklusive:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="news-search-client"></a>Klient för nyhetssökning
För att skapa en instans av `NewsSearchAPI`-klienten lägger du till med hjälp av direktiv:
```
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

```
Instansiera sedan klienten:
```
var client = new NewsSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Använd klienten för att söka med en frågetext:
```
var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

```
Parsa de nyheter som returneras i resultaten från den föregående frågan:
```
if (newsResults.Value.Count > 0)
{
    var firstNewsResult = newsResults.Value.First();

    Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
    Console.WriteLine($"News result count: {newsResults.Value.Count}");
    Console.WriteLine($"First news name: {firstNewsResult.Name}");
    Console.WriteLine($"First news url: {firstNewsResult.Url}");
    Console.WriteLine($"First news description: {firstNewsResult.Description}");
    Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
    Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
}

else
{
    Console.WriteLine("Couldn't find news results!");
}

```
## <a name="complete-console-application"></a>Slutföra konsolprogram

Följande konsolprogram kör den tidigare definierade frågan och söker i nyheterna för ”Quantum Computing” (kvantberäkning). Begäran innehåller parametrarna `market` och `count`. Koden verifierar antalet resultat och skriver ut `totalEstimatedMatches`, `name`, `url`, `description`, `published time` och `name` för `provider` för det första nyhetsresultatet.

```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

namespace NewsSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new NewsSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
                Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these methods to run queries explained under headings following.
            // NewsSearchWithFilters(client);
            // NewsCategory(client);
            // TrendingTopics(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}

```
## <a name="recent-news-freshness-and-sortby-parameters"></a>De senaste nyheterna, parametrarna freshness och sortBy
Följande kod söker i de senaste nyheterna om ”Artificial Intelligence” (artificiell intelligens) med parametrarna `freshness` och `sortBy`. Den verifierar antalet resultat och skriver ut `totalEstimatedMatches`, `name`, `url`, `description`, `published time` och `name` av källan för det första nyhetsresultatet.
```
        public static void NewsSearchWithFilters(NewsSearchAPI client)
        {
            try
            {
                var newsResults = client.News.SearchAsync(query: "Artificial Intelligence", market: "en-us", freshness: "Week", sortBy: "Date").Result;
                Console.WriteLine("Search most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nTotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="category-news-safe-search"></a>Kategorinyheter, säker sökning
Följande kod söker efter kategorinyheter för film och TV-underhållning med säker sökning.  Den verifierar antalet resultat och skriver ut `category`, `name`, `url`, `description`, `published time` och `name` för källan för det första nyhetsresultatet.
```
        public static void NewsCategory(NewsSearchAPI client)
        {
            try
            {
                var newsResults = client.News.CategoryAsync(category: "Entertainment_MovieAndTV", market: "en-us", safeSearch: "strict").Result;
                Console.WriteLine("Search category news for movie and TV entertainment with safe search");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nNews result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news category: {firstNewsResult.Category}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="trending-topics"></a>Trendande ämnen
Följande kod söker efter nyheter med trändande ämnen i Bing. Den verifierar antalet resultat och skriver ut `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` och `image.Url` för det första nyhetsresultatet.
```
        public static void TrendingTopics(NewsSearchAPI client)
        {
            try
            {
                var trendingTopics = client.News.TrendingAsync(market: "en-us").Result;
                Console.WriteLine("Search news trending topics in Bing");

                if (trendingTopics == null)
                {
                    Console.WriteLine("Didn't see any news trending topics..");
                }
                else
                {
                    if (trendingTopics.Value.Count > 0)
                    {
                        var firstTopic = trendingTopics.Value.First();

                        Console.WriteLine($"\r\nTrending topics count: {trendingTopics.Value.Count}");
                        Console.WriteLine($"First topic name: {firstTopic.Name}");
                        Console.WriteLine($"First topic query: {firstTopic.Query.Text}");
                        Console.WriteLine($"First topic image url: {firstTopic.Image.Url}");
                        Console.WriteLine($"First topic webSearchUrl: {firstTopic.WebSearchUrl}");
                        Console.WriteLine($"First topic newsSearchUrl: {firstTopic.NewsSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news trending topics!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
