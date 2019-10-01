---
title: 'Snabbstart: Visuell sökning i Bing klient bibliotek för Java | Microsoft Docs'
description: Ladda upp en bild med hjälp av API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695864"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Snabbstart: Visuell sökning i Bing klient bibliotek för Java

Kom igång med Visuell sökning i Bing klient bibliotek för Java. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. 

Använd Visuell sökning i Bing klient bibliotek för Java för att:

* Ladda upp en bild för att skicka en begäran om visuella sökningar.
* Hämta taggar för bild insikter och visuella Sök taggar.

[Referens dokumentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [artefakt (maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-bing-visual-search-azure-resource"></a>Skapa en Visuell sökning i Bing Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Visuell sökning i Bing med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `BING_SEARCH_V7_SUBSCRIPTION_KEY`med namnet.

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

`gradle init` Kör kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i den här build-konfigurationen:

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

Skapa en mapp för din exempel App. Kör följande kommando från arbets katalogen:

```console
mkdir -p src/main/java
```

Skapa en mapp för avbildningen som du vill överföra till API: et. Placera avbildningen i mappen **resurser** .

```console
mkdir -p src/main/resources
``` 

Navigera till den nya mappen och skapa en fil med namnet *BingVisualSearchSample. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande `import` -uttryck:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Skapa sedan en ny klass.

```java
public class BingVisualSearchSample {
}
```

I programmets `main`-metod skapar du variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln. Skapa sedan en `byte[]` för avbildningen som ska laddas upp. Skapa ett `try`-block för de metoder som du definierar senare och Läs in avbildningen och konvertera den till byte med `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven-lagringsplatsen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

I projektets *build. gradle. KTS* -fil måste du ta med klient biblioteket som ett `implementation`-uttryck. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande uppgifter med Visuell sökning i Bing klient bibliotek och java:

* [Autentisera klienten](#authenticate-the-client)
* [Skicka en begäran om visuella sökningar](#send-a-visual-search-request)
* [Skriv ut bild Insight-token och visuella Sök taggar](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din visuell sökning i Bing nyckel, med namnet `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


I din huvud metod måste du se till att använda din prenumerations nyckel för att instansiera ett [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) -objekt.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Skicka en begäran om visuella sökningar

I en ny metod skickar du bildens byte mat ris (som skapades i metoden `main()`) med hjälp av klientens [bingImages (). visualSearch ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) -metod. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Skriv ut bild Insight-token och visuella Sök taggar

Kontrol lera om [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) -objektet är null. Om inte skriver du ut image Insights-token, antalet taggar, antalet åtgärder och den första åtgärds typen.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Köra programmet

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-visual-search-single-page-app.md)
