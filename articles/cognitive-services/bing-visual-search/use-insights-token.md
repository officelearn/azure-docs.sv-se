---
title: Använda insights token - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Visar hur du använder en bilds insiktstoken med Bing Visual Search API för att få insikter om en bild.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.openlocfilehash: 251197c456ece4fe2dbbe264219d52f3502b7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67341730"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Använd en insights-token för att få insikter för en bild

API för visuell sökning i Bing returnerar information om en bild som du anger. Du kan ange bilden med hjälp av dess URL, en insiktstoken eller genom att ladda upp en bild. Information om dessa alternativ finns i [Vad är Bing Visual Search API?](overview.md). Den här artikeln visas med hjälp av en insights-token. Exempel som visar hur du laddar upp en bild för att få insikter finns i snabbstarterna ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)).

Om du skickar en bildtoken eller URL till Bing Visual Search visas följande formulärdata som du måste inkludera i brödtexten i POST. Formulärdata måste innehålla `Content-Disposition` huvudet och du `name` måste ange parametern till "knowledgeRequest". Mer information `imageInfo` om objektet finns i begäran:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Exemplen i den här artikeln visar hur du använder insights-token. Du får insights-token `Image` från ett objekt i ett /images/search API-svar. Information om hur du hämtar insights-token finns i [Vad är API:et för bing-bildsökning?](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

Exempel som använder insights-token finns i [C#](#use-with-c) | [Java](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python).

## <a name="use-with-c"></a>Använd med C #

### <a name="c-prerequisites"></a>C# förutsättningar

- Alla versioner av [Visual Studio 2019](https://www.visualstudio.com/downloads/) för att få den här koden att köras på Windows.
- En Azure-prenumeration. För den här snabbstarten kan du använda en kostnadsfri utvärderingsprenumerationsnyckel eller en betald prenumerationsnyckel. [free trial](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)

## <a name="run-the-application"></a>Köra appen

Följ dessa steg om du vill köra programmet:

1. Skapa en konsollösning i Visual Studio.
2. Ersätt innehållet i Program.cs med koden som visas i den här snabbstarten.
3. Ersätt värdet `accessKey` med din prenumerationsnyckel.
4. Ersätt `insightsToken` värdet med en insights-token från ett /images/search-svar.
5. Kör programmet.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
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

## <a name="use-with-java"></a>Använd med Java

### <a name="java-prerequisites"></a>Java-förutsättningar

- Du måste använda [JDK 7 eller 8](https://aka.ms/azure-jdks) för att kompilera och köra den här koden. Du kan använda en Java IDE om du har en favorit, men en textredigerare räcker.
- För den här snabbstarten kan du använda en kostnadsfri utvärderingsprenumerationsnyckel eller en betald prenumerationsnyckel. [free trial](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)

## <a name="run-the-java-application"></a>Kör Java-programmet

Följ dessa steg om du vill köra programmet:

1. Ladda ner eller installera [Gson Java-biblioteket](https://github.com/google/gson). Du kan också få Gson via Maven.
2. Skapa ett nytt Java-projekt i dem IDE eller det redigeringsprogram som du föredrar.
3. Lägg till den angivna koden i en fil med namnet `VisualSearch.java`.
4. Ersätt värdet `subscriptionKey` med din prenumerationsnyckel.
5. Kör programmet.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Använd med nod.js

### <a name="nodejs-prerequisites"></a>Förutsättningar för Node.js

- Du måste ha [Node.js 6](https://nodejs.org/en/download/) för att kunna köra den här koden.
- För den här snabbstarten kan du använda en kostnadsfri utvärderingsprenumerationsnyckel eller en betald prenumerationsnyckel. [free trial](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)

## <a name="run-the-javascript-application"></a>Köra JavaScript-programmet

Följ dessa steg om du vill köra programmet:

1. Skapa en mapp för ditt projekt (eller använd din favorit-IDE eller redigerare).
2. Gå till mappen som du nyss skapade från en kommandotolk eller terminal.
3. Installera de begärda modulerna:
  
   ```
   npm install request  
   ```
1. Installera modulerna för formulärdata:  
   ```
   npm install form-data  
   ```
1. Skapa en fil med namnet GetVisualInsights.js och lägg till nedanstående kod i den.
1. Ersätt värdet `subscriptionKey` med din prenumerationsnyckel.
1. Kör programmet.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Använd med Python

### <a name="python-prerequisites"></a>Python-förutsättningar

- Du måste ha [Python 3](https://www.python.org/) för att kunna köra den här koden.
- I snabbstarten kan du använda en prenumerationsnyckel för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) eller en betald prenumerationsnyckel.

## <a name="run-the-python-application"></a>Kör Python-programmet

Följ dessa steg om du vill köra programmet:

1. Skapa ett nytt Python-projekt i valfri IDE eller redigeringsprogram.
2. Skapa en fil med namnet visualsearch.py och lägg till den kod som visas i den här snabbstarten.
3. Ersätt värdet `SUBSCRIPTION_KEY` med din prenumerationsnyckel.
4. Kör programmet.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


def main():

    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))


# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Nästa steg

[Skapa en ensidig visuell sökwebbapp](tutorial-bing-visual-search-single-page-app.md)  
[Vad är API:et för visuell sökning i Bing?](overview.md)  
[Testa Cognitive Services](https://aka.ms/bingvisualsearchtryforfree)  
[Skaffa en åtkomstnyckel för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bilder - Visuell sökning](https://aka.ms/bingvisualsearchreferencedoc)
