---
title: 'Snabb start: kontrol lera stavningen med REST API och Java-Stavningskontroll i Bing'
titleSuffix: Azure Cognitive Services
description: Kom igång med Stavningskontroll i Bing REST API och Java för att kontrol lera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: e74954e33be1b2d24219ca61762dd43941415306
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366986"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Snabb start: kontrol lera stavningen med Stavningskontroll i Bing REST API och Java

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Det här enkla Java-programmet skickar en begäran till API:et och returnerar en lista över föreslagna korrigeringar. 

Även om det här programmet är skrivet i Java är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Käll koden för det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Förutsättningar

* Java Development Kit (JDK) 7 eller senare.

* Importera [Gson--2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) -eller den mest aktuella [Gson](https://github.com/google/gson) -versionen. För kommando rads körning lägger du till i `.jar` din Java-mapp med huvud klassen.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Skapa och initiera ett program

1. Skapa ett nytt Java-projekt i din favorit-IDE eller-redigerare med ett klass namn som du väljer och importera sedan följande paket:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Skapa variabler för API-slutpunktens värd, sökväg och prenumerationsnyckel. Skapa sedan variabler för din marknad, den text som du vill stavnings kontrol lera och en sträng för stavnings kontroll läge. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Skapa och skicka en API-begäran

1. Skapa en funktion som heter `check()` för att skapa och skicka API-begäran. I den här funktionen lägger du till koden som anges i nästa steg. Skapa en sträng för parametrarna för begäran:

   1. Tilldela din marknads kod till- `mkt` parametern med `=` operatorn. 

   1. Lägg till `mode` parametern med `&` operatorn och tilldela sedan läget för stavnings kontroll. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Skapa en URL genom att kombinera slut punktens värd, sökväg och parameter sträng. Skapa ett nytt `HttpsURLConnection` objekt.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Öppna en anslutning till URL:en. Ange metoden för begäran till `POST` och Lägg till parametrarna för begäran. Se till att lägga till din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Skapa ett nytt `DataOutputStream` objekt och skicka begäran till API: et.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formatera och Läs API-svar

1. Lägg till `prettify()` metoden i klassen, som formaterar JSON för en mer läsbar utdata.

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

I programmets huvud funktion anropar du `check()` metoden som skapats tidigare.
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

Skapa och kör ditt projekt. Om du använder kommando raden använder du följande kommandon för att skapa och köra programmet:

1. Bygg programmet:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Kör programmet:

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
- [API för stavningskontroll i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)