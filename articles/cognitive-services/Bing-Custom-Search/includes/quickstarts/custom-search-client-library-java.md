---
title: Bing Anpassad sökning Java-klientbibliotek snabbstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 32e02d3a7c1af6d15e7f381807d80f19b94da38f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587074"
---
Kom igång med klientbiblioteket för anpassad sökning på Bing för Java. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med API:et för anpassad sökning på Bing kan du skapa skräddarsydda, annonsfria sökupplevelser för ämnen som du bryr dig om. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Använd klientbiblioteket Bing Custom Search för Java för att:

* Hitta sökresultat på webben, från din anpassade Bing-sökningsinstans. 

[Referensdokumentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/).
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Verktyget Gradle-byggverktyg](https://gradle.org/install/)eller annan beroendehanterare.
* En instans av anpassad Bing-sökning. Se [Snabbstart: Skapa din första förekomst av anpassad bing-sökning](../../quick-start.md) för mer information.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

När du har fått en nyckel från utvärderingsprenumerationen `AZURE_BING_CUSTOM_SEARCH_API_KEY`eller resursen skapar du en [miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln med namnet .

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

> [!TIP]
> Om du inte använder Gradle kan du hitta information om klientbiblioteket för andra beroendehanterare i [Mavens centrala databas](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive en *build.gradle.kts-fil* som används vid körning för att konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="install-the-client-library"></a>Installera klientbiblioteket 

Hitta *build.gradle.kts* och öppna den med önskad IDE eller textredigerare. Kopiera sedan i den här build-konfigurationen. Var noga med att `dependencies`inkludera klientbiblioteket under:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Skapa en mapp för exempelappen. Kör följande kommando i arbetskatalogen:

```console
mkdir src/main/java
```

Navigera till den nya mappen och skapa en fil som heter *BingCustomSearchSample.java*. Öppna den och `import` lägg till följande satser:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Skapa en klass med namnet`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Skapa en `main` metod och en variabel för resursens nyckel i klassen. Om du har skapat miljövariabeln när du har startat programmet stänger du och öppnar redigeraren, IDE: n eller skalet som kör den för att komma åt variabeln. Du kommer att definiera metoderna senare.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektmodell

Bing Custom Search-klienten är ett [BingCustomSearchAPI-objekt](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) som skapas från [BingCustomSearchManager-objektets](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) [metoden authenticate().](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) Du kan skicka en sökbegäran med hjälp av klientens [BingCustomInstances.search()-metod.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__)

API-svaret är ett [SearchResponse-objekt](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) som innehåller information om sökfrågan och sökresultat.

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Bing Custom Search för Java:

* [Autentisera klienten](#authenticate-the-client)
* [Hämta sökresultat från din anpassade sökinstans](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autentisera klienten

Din huvudsakliga metod bör innehålla ett [BingCustomSearchManager-objekt](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) som tar din nyckel och anropar dess `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Hämta sökresultat från din anpassade sökinstans

Använd klientens [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) funktion för att skicka en sökfråga till din anpassade instans. Ange `withCustomConfig` till ditt anpassade konfigurations-ID eller standard till `1`. När du har fått ett svar från API:et kontrollerar du om några sökresultat hittades. Om så är fallet får du det `webPages().value().get()` första sökresultatet genom att anropa svarets funktion och skriva ut resultatets namn och webbadress. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Köra appen

Skapa appen med följande kommando från projektets huvudkatalog:

```console
gradle build
```

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portalen](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](../../tutorials/custom-search-web-page.md)
