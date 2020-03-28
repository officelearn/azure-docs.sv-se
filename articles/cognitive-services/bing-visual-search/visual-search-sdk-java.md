---
title: 'Snabbstart: Bing Visual Search klientbibliotek för Java | Microsoft-dokument'
description: Ladda upp en bild med hjälp av API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379510"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Snabbstart: Bing Visual Search klientbibliotek för Java

Kom igång med Bing Visual Search-klientbiblioteket för Java. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. 

Använd klientbiblioteket Bing Visual Search för Java för att:

* Ladda upp en bild för att skicka en visuell sökbegäran.
* Hämta token för bildinsikt och visuell sökning.

[Referensdokumentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Verktyget Gradle-byggverktyg](https://gradle.org/install/)eller annan beroendehanterare

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

När du har fått en nyckel från utvärderingsprenumerationen `BING_SEARCH_V7_SUBSCRIPTION_KEY`eller resursen skapar du en [miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln med namnet .

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

Hitta *build.gradle.kts* och öppna den med önskad IDE eller textredigerare. Kopiera sedan i den här build-konfigurationen:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Skapa en mapp för exempelappen. Kör följande kommando i arbetskatalogen:

```console
mkdir -p src/main/java
```

Skapa en mapp för den bild som du vill överföra till API:et. Placera bilden i **resources** resursmappen.

```console
mkdir -p src/main/resources
``` 

Navigera till den nya mappen och skapa en fil som heter *BingVisualSearchSample.java*. Öppna den i önskad redigerare eller `import` IDE och lägg till följande satser:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Skapa sedan en ny klass.

```java
public class BingVisualSearchSample {
}
```

Skapa variabler `main` för resursens Azure-slutpunkt och -nyckel i programmets metod. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln. Skapa sedan `byte[]` en för den bild du ska ladda upp. Skapa `try` ett block för de metoder som du ska definiera senare och `toByteArray()`läs in avbildningen och konvertera det till byte med .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Installera klientbiblioteket

Den här snabbstarten använder Gradle-beroendehanteraren. Du hittar klientbiblioteket och informationen för andra beroendehanterare i [Mavens centrala databas](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

I projektets *version.gradle.kts-fil* ska du inkludera klientbiblioteket som ett `implementation` utdrag. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Bing Visual Search och Java:

* [Autentisera klienten](#authenticate-the-client)
* [Skicka en visuell sökbegäran](#send-a-visual-search-request)
* [Skriv ut token för bildinsikt och visuell sökning](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har skapat en `BING_SEARCH_V7_SUBSCRIPTION_KEY` [miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln Bing Visual Search med namnet .


I din huvudmetod, se till att använda din prenumerationsnyckel för att instansiera ett [BingVisualSearchAPI-objekt.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable)

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Skicka en visuell sökbegäran

I en ny metod skickar du matrisen för `main()` bildbyte (som skapades i metoden) med hjälp av klientens [metod bingImages().visualSearch().](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Skriv ut token för bildinsikt och visuell sökning

Kontrollera om [ImageKnowledge-objektet](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) är null. Om inte, skriv ut token för bildinsikter, antalet taggar, antalet åtgärder och den första åtgärdstypen.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Köra appen

Du kan skapa appen med:

```console
gradle build
```

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-visual-search-single-page-app.md)
