---
title: Snabbstartsguide – C# Project URL Preview - kognitiva Microsoft-tjänster | Microsoft Docs
description: Komma igång med projektet URL förhandsgranskning i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 17d44bd0c23d0a1e67da5a0e91248700d3166c1a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354027"
---
# <a name="url-preview-query-in-c"></a>URL: en förhandsgranskning frågan i C#

Följande C#-exempel skapar en förhandsgranskning för URL: en för webbplatsen SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Visual Studio 2017](https://www.visualstudio.com/downloads/) att köra den här koden i Windows. (Ledigt Community Edition fungerar).

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [kognitiva Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Koden scenario

Följande C#-kod skapar en URL-förhandsgranskning av webbplatsen SwiftKey: https://swiftkey.com/en. 

De är implementerade i följande steg:
1. Deklarera variabler för att ange slutpunkten och fråge-URL för förhandsgranskning.  
2. Skapa begäran.
3. Lägg till den *Ocp-Apim-prenumeration-nyckeln* huvud. 
4. Kör webbegäran asynkront. 
5. Läsa svaret.
6. Skriva ut sidhuvuden och JSON-resultat till konsolen.

**Källkoden**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
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
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Köra programmet

Att köra programmet:

1. Skapa en ny konsol lösning i Visual Studio.
2. Ersätt `Program.cs` med den angivna koden.
3. Ersätt den `YOUR-ACCESS-KEY` värde med en giltig åtkomstnyckeln för din prenumeration.
4. Kör programmet.

## <a name="next-steps"></a>Nästa steg
- [Java-Snabbstart](java-quickstart.md)
- [JavaScript-Snabbstart](javascript.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)