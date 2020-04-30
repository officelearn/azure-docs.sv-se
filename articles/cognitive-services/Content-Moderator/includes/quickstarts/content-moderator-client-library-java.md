---
title: Snabb start för Java-klient bibliotek Content Moderator
titleSuffix: Azure Cognitive Services
description: I den här snabb starten lär du dig hur du kommer igång med Azure Cognitive Services Content Moderator-klient biblioteket för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: ffbb0e4bd2eec3299b4d5b2b38477edf611db1a2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187345"
---
Kom igång med Content Moderator klient bibliotek för Java. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Content Moderator är en kognitiv tjänst som kontrollerar text-, bild-och video innehåll för material som kan vara stötande, riskfyllda eller på annat sätt olämpligt. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare.

Använd Content Moderator klient bibliotek för Java för att:

* Måttliga bilder för vuxen eller vågat innehåll, text eller mänskliga ansikten.

[Exempel på referens dokumentations](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [artefakt (maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-content-moderator-azure-resource"></a>Skapa en Content Moderator Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Content Moderator med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln `AZURE_CONTENTMODERATOR_KEY`med namnet.

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```
Kör `gradle init`. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS*, som används vid körning för att skapa och konfigurera ditt program. Kör kommandot från arbetskatalogen:

```console
gradle init --type basic
```

När du uppmanas att välja en Bygg skript-DSL väljer du **Kotlin**.

Hitta *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i följande build-konfiguration. Den här konfigurationen definierar projektet som ett Java-program vars start punkt är klassen **ContentModeratorQuickstart**. Den importerar Content Moderator klient biblioteket samt Gson SDK för JSON-serialisering.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

Kör följande kommando från din arbets katalog för att skapa en Project Source-mapp.

```console
mkdir -p src/main/java
```

Skapa sedan en fil med namnet *ContentModeratorQuickstart. java* i den nya mappen. Öppna filen i önskat redigerings program eller IDE och importera följande bibliotek högst upp:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator Java-klient biblioteket.

|Name|Beskrivning|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Den här klassen krävs för alla Content Moderator-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Den här klassen innehåller funktioner för att analysera bilder för innehåll som är olämpligt för barn, personlig information eller mänskliga ansikten.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Den här klassen innehåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Den här klassen innehåller funktionerna i gransknings-API: erna, inklusive metoder för att skapa jobb, anpassade arbets flöden och mänsklig granskning.|


## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Content Moderator klient bibliotek för java:

* [Autentisera klienten](#authenticate-the-client)
* [Måttliga bilder](#moderate-images)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Det här steget förutsätter att du har [skapat en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din Content moderator `AZURE_CONTENTMODERATOR_KEY`-nyckel med namnet.

I programmets `main` Metod skapar du ett [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) -objekt med hjälp av prenumerationens slut punkts värde och prenumerations nyckelns miljö variabel. 

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Måttliga bilder

### <a name="get-images"></a>Hämta bilder

I ditt projekts **src/main/-** mapp skapar du en mapp för **resurser** och navigerar till den. Skapa sedan en ny textfil, *ImageFiles. txt*. I den här filen lägger du till URL: er för avbildningar för att analysera&mdash;en URL på varje rad. Du kan använda följande exempel bilder:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definiera hjälp klass

Lägg sedan till följande klass definition i **ContentModeratorQuickstart** -klassen i *ContentModeratorQuickstart. java* -filen. Den här inre klassen kommer att användas senare i avbildnings redigerings processen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterera genom bilder

Lägg sedan till följande kod längst ned i- `main` metoden. Eller så kan du lägga till den i en separat metod som anropas `main`från. Den här koden steg för steg för varje rad i filen _ImageFiles. txt_ .

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sök efter vuxen/vågat innehåll
Den här kodraden kontrollerar avbildningen på den angivna URL: en för vuxen eller vågat innehåll. Mer information om de här villkoren finns i översikts hand boken för bild moderator.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sök efter text
Den här kodraden kontrollerar bilden för synlig text.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sök efter ansikten
Den här kodraden kontrollerar bilden för mänskliga ansikten.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Slutligen lagrar du den information som returnerades `EvaluationData` i listan.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Utskrifts resultat

Efter `while` loopen lägger du till följande kod, som skriver ut resultatet till-konsolen och till en utdatafil, *src/main/Resources/ModerationOutput. JSON*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Avsluta `try` instruktionen och Lägg till en `catch` instruktion för att slutföra metoden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Köra appen

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

Navigera sedan till filen *src/main/Resources/ModerationOutput. JSON* och visa resultatet av din innehålls redaktör.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Content Moderator Java-biblioteket för att utföra moderator uppgifter. Läs sedan mer om hur du använder avbildningen eller andra media genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
>[Bild moderator koncept](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Vad är Azure Content Moderator?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).