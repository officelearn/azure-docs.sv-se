---
title: 'Snabb start: Anpassad sökning i Bing klient bibliotek för Java | Microsoft Docs'
description: Kom igång med Anpassad sökning i Bing klient biblioteket för Java genom att begära Sök Resultat från Anpassad sökning i Bing-instansen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: f0a3008dace4e9382bbb6b6d9465ce1c9b4ddc6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976068"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Snabb start: Anpassad sökning i Bing klient bibliotek för Java

Kom igång med Anpassad sökning i Bing klient bibliotek för Java. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Med API för anpassad Bing-sökning kan du skapa skräddarsydda, annons fria Sök upplevelser för ämnen som du bryr dig om.

Använd Anpassad sökning i Bing klient bibliotek för Java för att:

* Hitta Sök resultat på webben från Anpassad sökning i Bing-instansen. 

[Referens dokumentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Library Source Code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [artefakt (maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* En instans av anpassad Bing-sökning. Se [snabb start: skapa din första anpassad sökning i Bing-instans](quick-start.md) för mer information.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-bing-custom-search-azure-resource"></a>Skapa en Anpassad sökning i Bing Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Anpassad sökning i Bing med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, med namnet `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

> [!TIP]
> Om du inte använder Gradle kan du hitta klient biblioteks informationen för andra beroende hanterare på [maven Central-lagringsplatsen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot `gradle init` från din arbets katalog. Det här kommandot skapar viktiga build-filer för Gradle, inklusive en *build. Gradle. KTS* -fil som används vid körning för att konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="install-the-client-library"></a>Installera klient biblioteket 

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i den här build-konfigurationen. Se till att inkludera klient biblioteket Under `dependencies`:

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

Skapa en mapp för din exempel App. Kör följande kommando från arbets katalogen:

```console
mkdir src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *BingCustomSearchSample. java*. Öppna den och Lägg till följande `import`-satser:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Skapa en klass med namnet `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

I-klassen skapar du en `main`-metod och en variabel för resurs nyckeln. Om du har skapat miljövariabeln när du har startat programmet stänger du och öppnar redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln. Du definierar metoderna senare.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objekt modell

Anpassad sökning i Bing-klienten är ett [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) -objekt som skapas från [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) -objektets metod för [autentisering ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) . Du kan skicka en Sök förfrågan med hjälp av klientens [BingCustomInstances. search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) -metod.

API-svaret är ett [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) -objekt som innehåller information om Sök frågan och Sök resultat.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Anpassad sökning i Bing klient bibliotek för java:

* [Autentisera klienten](#authenticate-the-client)
* [Få Sök Resultat från din anpassade Sök instans](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autentisera klienten

Huvud metoden bör innehålla ett [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) -objekt som tar din nyckel och anropar dess `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Få Sök Resultat från din anpassade Sök instans

Använd klientens [BingCustomInstances. search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) -funktion för att skicka en Sök fråga till din anpassade instans. Ange `withCustomConfig` till ditt anpassade konfigurations-ID eller Använd `1`som standard. När du har skaffat ett svar från API: et kontrollerar du om det finns några Sök resultat. I så fall får du det första Sök resultatet genom att anropa svarets `webPages().value().get()` funktion och skriva ut resultatets namn och URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Köra programmet

Bygg appen med följande kommando från projektets huvud katalog:

```console
gradle build
```

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)
