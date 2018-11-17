---
title: Snabbstart – skicka en fråga till den lokala företag i Bing i C# | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att börja använda den lokala företag i Bing i C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 99a9ab2ae1ed102459d2e13f060eebd08bb0ec75
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853582"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Snabbstart: Skicka en fråga till den lokala företag i Bing iC#

Använd den här snabbstarten för att börja skicka begäranden till den lokala företag i Bing, vilket är en Azure Cognitive Service. Medan detta enkla program som är skriven i C#, API: et är en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som HTTP-förfrågningar och JSON-parsning.

Det här exempelprogrammet hämtar lokala svarsdata från API: et för sökfrågan `hotel in Bellevue`.

## <a name="prerequisites"></a>Förutsättningar

* Valfri version av [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](http://www.mono-project.com/).

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API:er för Bing-sökresultat. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Du behöver en åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion.

## <a name="create-the-request"></a>Skapa begäran 

Följande kod skapar en `WebRequest`anger rubriken åtkomst och lägger till en frågesträng för ”restaurang i Bellevue”.  Därefter skickar den en begäran och tilldelar svaret till en sträng som ska innehålla JSON-texten.

````
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "appid=" + accessKey;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
````

## <a name="run-the-complete-application"></a>Kör hela appen

Den lokala företag i Bing returnerar lokaliserade sökresultat från Bing search-motorn.
1. Skapa en ny konsollösning i Visual Studio (Community Edition går bra).
2. Ersätt Program.cs med koden nedan.
3. Ersätt värdet för accessKey med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

````
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + 
                                "&appid=" + accessKey + "&market=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

````

## <a name="next-steps"></a>Nästa steg
- [Lokala företag Search-Java-Snabbstart](local-search-java-quickstart.md)
- [Lokala företag Search-Node-quickstart](local-search-node-quickstart.md)
- [Lokala företag Search Python-Snabbstart](local-search-python-quickstart.md)
