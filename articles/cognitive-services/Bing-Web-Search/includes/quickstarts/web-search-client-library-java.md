---
title: Snabb start för Java-klient bibliotek Webbsökning i Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 042a3edf8efe080064a3c41fd55975a40bf47e5e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386539"
---
Med Webbsökning i Bing klient biblioteket är det enkelt att integrera Webbsökning i Bing i Java-programmet. I den här snabbstarten lär du dig att skicka en begäran, ta emot ett JSON-svar och filtrera och parsa resultaten.

Vill du se koden på en gång? Exempel för [Bing-sökning klient bibliotek för Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search) finns på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Här följer några saker som du behöver innan du kör den här snabbstarten:

* [JDK 7 eller 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache Maven](https://maven.apache.org/download.cgi) eller valfritt verktyg för versionsautomatisering
* En prenumerationsnyckel

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-set-up-your-pom-file"></a>Skapa ett projekt och konfigurera din POM-fil

Skapa ett nytt Java-projekt med hjälp av Maven eller valfritt verktyg för versionsautomatisering. Under förutsättning att du använder maven lägger du till följande rader i [Pom-filen (Project Object Model)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) . Ersätt alla förekomster av `mainClass` med ditt program.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>Deklarera beroenden

Öppna projektet i valfri IDE eller redigeringsprogram och importera dessa beroenden:

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

Om du skapade projektet med Maven bör paketet redan vara deklarerat. Annars deklarerar du paketet nu. Till exempel:

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>Deklarera klassen BingWebSearchSample

Deklarera klassen `BingWebSearchSample`. Den kommer att innehålla den största delen av vår kod, inklusive metoden `main`.  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>Skapa en begäran

Metoden `runSample`, som finns i klassen `BingWebSearchSample`, konstruerar begäran. Kopiera den här koden till programmet:

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>Hantera svaret

Nu lägger vi till kod för att parsa svaret och skriva ut resultatet. `name` och `url` för den första webbplatsen, bilden, nyhetsartikeln och videon skrivs ut när de ingår i svarsobjektet.

```java
/*
* WebPages
* If the search response has web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response has images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response has news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response has videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Deklarera main-metoden

I det här programmet innehåller main-metoden kod som instansierar klienten, verifierar `subscriptionKey` och anropar `runSample`. Se till att du anger en giltig prenumerationsnyckel för ditt Azure-konto innan du fortsätter.

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>Köra programmet

Det sista steget är att köra programmet!

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det här projektet bör du ta bort din prenumerationsnyckel från programmets kod.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exempel med Cognitive Services SDK för Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>Se även

* [Azure Java SDK-referens](/java/api/overview/azure/cognitiveservices/client/bingwebsearchapi?view=azure-java-stable)