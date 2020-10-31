---
title: 'Snabb start: föreslå Sök frågor med Automatiska förslag i Bing REST API och Java'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du snabbt kan börja föreslå Sök termer i real tid med API för automatiska förslag i Bing och Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 05/06/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: c8eca53f118fe0b2012ff0b687a939842ebc8576
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101875"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-java"></a>Snabb start: föreslå Sök frågor med Automatiska förslag i Bing REST API och Java

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Följ den här snabb starten om du vill lära dig att ringa till API för automatiska förslag i Bing och läsa JSON-svaret. Det här enkla Java-programmet skickar en partiell Sök fråga till API: et och returnerar förslag för sökningar. Även om det här programmet är skrivet i Java, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingAutosuggestv7.java)

## <a name="prerequisites"></a>Förutsättningar

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* [Gson-biblioteket](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import java.io.*;
    import java.net.*;
    import java.util.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Skapa variabler för din prenumerations nyckel, API-värden och sökväg, din [marknads kod](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes)och en Sök fråga. Använd den globala slut punkten nedan eller Använd den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.
    
    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/Suggestions";
    static String mkt = "en-US";
    static String query = "sail";
    ```


## <a name="format-the-response"></a>Formatera svaret

Skapa en metod med namnet `prettify()` för att formatera det svar som returneras från API:et för Bing-video. Använda Gson-bibliotekets `JsonParser` för att ta en JSON-sträng och konvertera den till ett objekt. Använd `GsonBuilder()` och `toJson()` för att skapa den formaterade strängen.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="construct-and-send-the-search-request"></a>Skapa och skicka sökbegäran

1. Skapa en ny metod med namnet  `get_suggestions()` och utför följande steg:

   1. Skapa URL: en för din begäran genom att kombinera din API-värd, sökväg och kodning av Sök frågan. Se till att URL-koda frågan innan du lägger till den. Skapa en parameter sträng för din fråga genom att lägga till marknads koden i `mkt=` parametern och din fråga till `q=` parametern.
    
      ```java
  
      public static String get_suggestions () throws Exception {
         String encoded_query = URLEncoder.encode (query, "UTF-8");
         String params = "?mkt=" + mkt + "&q=" + encoded_query;
         //...
      }
      ```
    
   2. Skapa en ny URL för begäran med API-värden, sökvägen och parametrarna som du skapade i föregående steg. 
    
       ```java
       //...
       URL url = new URL (host + path + params);
       //...
       ```
    
   3. Skapa ett `HttpsURLConnection` objekt och Använd  `openConnection()` för att skapa en anslutning. Konfigurera begäransmetoden till `GET` och lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken.

      ```java
       //...
       HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
       connection.setRequestMethod("GET");
       connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
       connection.setDoOutput(true);
       //...
      ```

   4. Lagra API-svaret i `StringBuilder` . När svaret har registrerats stänger du `InputStreamReader` strömmen och returnerar svaret.

       ```java
       //...
       StringBuilder response = new StringBuilder ();
       BufferedReader in = new BufferedReader(
       new InputStreamReader(connection.getInputStream()));
       String line;
       while ((line = in.readLine()) != null) {
         response.append(line);
       }
       in.close();
    
       return response.toString();
       ```

2. I appens huvud funktion, anropa `get_suggestions()` och skriv ut svaret med hjälp av `prettify()` .
    
    ```java
    public static void main(String[] args) {
      try {
        String response = get_suggestions ();
        System.out.println (prettify (response));
      }
      catch (Exception e) {
        System.out.println (e);
      }
    }
    ```

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorials/autosuggest.md)

- [Vad är Automatiska förslag i Bing?](../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
