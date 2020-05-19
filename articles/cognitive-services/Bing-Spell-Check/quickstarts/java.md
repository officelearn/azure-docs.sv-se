---
title: 'Snabb start: kontrol lera stavningen med REST API och Java-Stavningskontroll i Bing'
titleSuffix: Azure Cognitive Services
description: Kom igång med REST API för stavningskontroll i Bing för att kontrollera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 491481156f026e9887244064297d0790a965158e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735121"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Snabb start: kontrol lera stavningen med Stavningskontroll i Bing REST API och Java

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Det här enkla Java-programmet skickar en begäran till API:et och returnerar en lista över föreslagna korrigeringar. Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Käll koden för det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Förutsättningar

* Java Development Kit (JDK) 7 eller senare.

* Importera [Gson--2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) -eller den mest aktuella [Gson](https://github.com/google/gson) -versionen. För kommando rads körning lägger du till i `.jar` din Java-mapp med huvud klassen.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Skapa och initiera ett program

1. Skapa ett nytt Java-projekt i din favorit-IDE eller-redigerare med ett klass namn som du väljer och importera sedan följande paket.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Skapa variabler för API-slutpunktens värd, sökväg och prenumerationsnyckel. Skapa sedan variabler för marknad, den text som du vill stavningskontrollera och en sträng för stavningskontrolläge. Du kan använda den globala slut punkten nedan eller den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Skapa och skicka en API-begäran

1. Skapa en funktion som heter `check()` för att skapa och skicka API-begäran. Följ de här stegen i den. Skapa en sträng för begärandeparametrarna. Lägg till parametern `?mkt=` i marknadssträngen och parametern `&mode=` i stavningskontrolläge.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Skapa en URL genom att kombinera slutpunktens värd, sökväg och parametersträng. Skapa ett nytt `HttpsURLConnection` objekt.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Öppna en anslutning till URL:en. Ställ in begärandemetoden på `POST`. Lägg till begärandeparametrarna. Glöm inte att lägga till din prenumerationsnyckel i `Ocp-Apim-Subscription-Key`-huvudet.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Skapa ett nytt `DataOutputStream`-objekt och skicka begäran till API:et.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formatera och Läs API-svar

1. Lägg till den här metoden i klassen. Den formaterar JSON för en mer läsbar utdata.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Skapa en `BufferedReader` och läs svaret från API:et. Skriv ut det till konsolen.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Anropa API

I programmets huvud funktion anropar du metoden check () som skapats ovan.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>Kör programmet

Skapa och kör ditt projekt.

Om du använder kommando raden använder du följande kommandon för att skapa och köra programmet.

**Konstruktion**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Fungerar**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorials/spellcheck.md)

- [Vad är API för stavningskontroll i Bing?](../overview.md)
- [API-referens för stavningskontroll i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
