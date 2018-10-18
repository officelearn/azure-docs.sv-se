---
title: 'Snabbstart: API Java – Skapa kunskapsbas – QnA Maker'
titlesuffix: Azure Cognitive Services
description: I den här snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmässigt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 572895e51d579efb3b141322a87e374283af1648
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49384709"
---
# <a name="create-a-new-knowledge-base-in-java"></a>Skapa en ny kunskapsbas i Java

Den här snabbstarten går igenom skapandet av ett exempel på QnA Maker-kunskapsbas, programmässigt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

Två exempel på URL:er för vanliga frågor och svar ges nedan (i ”kb.urls” för **getKB()**) som ger innehåll. QnA Maker extraherar automatiskt frågor och svar från delvis strukturerat innehåll, som vanliga frågor och svar, enligt den utförligare beskrivningen i det här dokumentet om [datakällor](../Concepts/data-sources-supported.md). Du kan också använda egna URL:er för vanliga frågor och svar i den här snabbstarten.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [JDK 7 eller 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) för att kompilera och köra den här koden. Du kan använda en Java IDE om du har en favoritutvecklingsmiljö, men en textredigerare fungerar bra.

Du måste ha ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **QnA Maker** som vald resurs. Du behöver en betald prenumerationsnyckel från ditt nya API-konto på [Azure-instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServices). Båda nycklarna fungerar för den här snabbstarten.

![Tjänstnyckeln för Azure-instrumentpanelen](../media/sub-key.png)

## <a name="create-knowledge-base"></a>Skapa kunskapsbas

Följande kod skapar en ny kunskapsbas med hjälp av metoden [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Skapa).

1. Skapa ett nytt Java-projekt i din favoritutvecklingsmiljö.
1. Lägg till [Google GSON-biblioteket](https://github.com/google/gson) i ditt Java-projekt, genom att manuellt [skapa](https://stackoverflow.com/questions/5258159/how-to-make-an-executable-jar-file) och importera .jar-filen eller lägga till ett beroende i det projekthanteringsverktyg du vill använda, till exempel Maven.
1. Kopiera/klistra in koden som anges nedan.
1. Ersätt värdet `subscriptionKey` med en giltig prenumerationsnyckel.
1. Kör programmet.

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/**
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.5</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/** NOTE: To compile and run this code without an IDE:
 * 1. Save this file as CreateKB.java
 * 2. Run *:
 *    javac CreateKB.java -cp .;<GSON> -encoding UTF-8
 * 3. Run *:
 *    java -cp .;<GSON> CreateKB
 * *replace <GSON> with the name of the current Google GSON library .jar file,
 * for example: gson-2.8.5.jar
*/

public class CreateKB {

    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace this with a valid subscription key.
    static String subscriptionKey = "ADD YOUR SUBSCRIPTION KEY HERE";

    // Components used to create HTTP request URIs for QnA Maker operations.
    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/create";

    // Serializing classes into JSON for our request to the server.
    // For the JSON request schema, see <a href="https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff">QnA Maker V4.0</a>
    public static class KB {
        String name;
        Question[] qnaList;
        String[] urls;
        File[] files;
    }

    public static class Question {
        Integer id;
        String answer;
        String source;
        String[] questions;
        Metadata[] metadata;
    }

    public static class Metadata {
        String name;
        String value;
    }

    public static class File {
        String fileName;
        String fileUri;
    }

    // This class has the HTTP response headers and body that is returned
    // by the HTTP request.
    public static class Response {
        Map<String, List<String>> Headers;
        String Response;

        /**
         * Constructor that specifies header and body response
         * @param headers List of headers
         * @param response Response returned from your HTTP request
         */
        public Response(Map<String, List<String>> headers, String response) {
            this.Headers = headers;
            this.Response = response;
        }
    }

    /**
     * Formats and indents JSON for display.
     * @param json_text The JSON string to format and indent.
     * @return The formatted, indented JSON string.
     */
    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    /**
     * Sends an HTTP GET request.
     * @param url The URL of the HTTP request.
     * @return The object that represents the HTTP response.
     */
    public static Response Get (URL url) throws Exception{
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
            connection.setDoOutput(true);
        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();
        return new Response (connection.getHeaderFields(), response.toString());
    }

    /**
     * Builds and sends an HTTP POST request.
     * @param url The URL of the HTTP request.
     * @param content The contents of your POST.
     * @return The object that represents the HTTP response.
     */
    public static Response Post (URL url, String content) throws Exception{
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
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
        return new Response (connection.getHeaderFields(), response.toString());
    }

    /**
      * Sends the request to create the knowledge base.
      * @param kb Your knowledge base object.
      * @return Sends POST request.
      * @throws Exception If POST request fails.
      */
    public static Response CreateKB (KB kb) throws Exception {
        URL url = new URL (host + service + method);
        System.out.println ("Calling " + url.toString() + ".");
        String content = new Gson().toJson(kb);
        return Post(url, content);
    }

    /**
     * Checks the status of the request to create the knowledge base.
     * @param operation The operation ID.
     * @return The specific URL being called.
     * @throws Exception If the GET request fails.
     */
    public static Response GetStatus (String operation) throws Exception {
        URL url = new URL (host + service + operation);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

    /**
     * Sends a sample request to create a knowledge base. To understand
     * this 'kb' object, refer to the <a href="https://docs.microsoft.com/azure/cognitive-services/QnAMaker/concepts/knowledge-base">Knowledge base</a> concept page.
     * @return A new knowledge base.
     */
    public static KB GetKB () {
        KB kb = new KB ();
        kb.name = "Example Knowledge Base";

        Question q = new Question();
        q.id = 0;
        q.answer = "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa";
        q.source = "Custom Editorial";
        q.questions = new String[]{"How do I programmatically update my Knowledge Base?"};

        Metadata md = new Metadata();
        md.name = "category";
        md.value = "api";
        q.metadata = new Metadata[]{md};

        kb.qnaList = new Question[]{q};
        kb.urls = new String[]{"https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",     "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"};


        return kb;
    }

    public static void main(String[] args) {
        try {
            // Send the request to create the knowledge base.
            Response response = CreateKB (GetKB ());
            String operation = response.Headers.get("Location").get(0);
            System.out.println (PrettyPrint (response.Response));
            // Loop until the request is completed.
            Boolean done = false;
            while (!done) {
                // Check on the status of the request.
                response = GetStatus (operation);
                System.out.println (PrettyPrint (response.Response));
                Type type = new TypeToken<Map<String, String>>(){}.getType();
                Map<String, String> fields = new Gson().fromJson(response.Response, type);
                String state = fields.get ("operationState");
                // If the request is still running, the server tells us how
                // long to wait before checking the status again.
                if (state.equals("Running") || state.equals("NotStarted")) {
                    String wait = response.Headers.get ("Retry-After").get(0);
                    System.out.println ("Waiting " + wait + " seconds...");
                    Thread.sleep (Integer.parseInt(wait) * 1000);
                }
                else {
                    done = true;
                }
            }
        } catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

## <a name="understanding-what-qna-maker-returns"></a>Förstå vad QnA Maker returnerar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel. Dina resultat kan skilja sig något. Om det sista anropet returnerar statusen ”Lyckades” har kunskapsbasen skapats. Om du vill felsöka går du till [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Hämta åtgärdsinformation) i API för QnA Maker.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```
   
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)