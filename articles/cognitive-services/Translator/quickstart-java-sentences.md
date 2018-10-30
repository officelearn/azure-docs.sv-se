---
title: 'Snabbstart: Hämta meningslängder, Java– Translator Text-API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du hämta längden på meningar i text med hjälp av Translator Text-API:et med Java i Cognitive Services.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: 3ea4c3a6463b831fe960bf6aec9e4c1151f8424d
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644886"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-java"></a>Snabbstart: Hämta meningslängder med Translator Text REST API (Java)

I den här snabbstarten ska du hämta längden på meningar i text med hjälp av Translator Text-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [JDK 7 eller 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) för att kompilera och köra den här koden. Du kan använda en Java IDE om du har en favoritutvecklingsmiljö, men en vanlig textredigerare fungerar också.

För att använda Translator Text-API:et behöver du även en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="breaksentence-request"></a>BreakSentence-begäran

Följande kod delar in källtexten i meningar med hjälp av metoden [BreakSentence](./reference/v3-0-break-sentence.md).

1. Skapa ett nytt Java-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as BreakSentences.java.
2. Run:
    javac BreakSentences.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar BreakSentences
*/

public class BreakSentences {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/breaksentence?api-version=3.0";

    static String text = "How are you? I am fine. What did you do today?";

    public static class RequestBody {
        String Text;

        public RequestBody(String text) {
            this.Text = text;
        }
    }

    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setRequestProperty("X-ClientTraceId", java.util.UUID.randomUUID().toString());
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String BreakSentences () throws Exception {
        URL url = new URL (host + path);

        List<RequestBody> objList = new ArrayList<RequestBody>();
        objList.add(new RequestBody(text));
        String content = new Gson().toJson(objList);

        return Post(url, content);
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = BreakSentences ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="breaksentence-response"></a>BreakSentence-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och transkribering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)
