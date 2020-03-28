---
title: 'Snabbstart: Klientbibliotek för Innehållsmoderator för Java'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kan du lära dig hur du kommer igång med klientbiblioteket För Azure Cognitive Services Content Moderator för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5dd1c1bb2b321e617efc430ce48745c06a827305
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772453"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Snabbstart: Klientbibliotek för Innehållsmoderator för Java

Kom igång med klientbiblioteket Innehållsmoderator för Java. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Content Moderator är en kognitiv tjänst som kontrollerar text-, bild- och videoinnehåll för material som kan vara stötande, riskabelt eller på annat sätt oönskat. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare.

Använd klientbiblioteket innehållsmoderator för Java för att:

* Måttliga bilder för barn eller racy innehåll, text eller mänskliga ansikten.

[Exempel på referensdokumentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Verktyget Gradle-byggverktyg](https://gradle.org/install/)eller annan beroendehanterare.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-content-moderator-azure-resource"></a>Skapa en Azure-resurs för innehållsmoderator

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Content Moderator med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/).

När du har hämtat en nyckel från din utvärderingsprenumeration eller -resurs [skapar du en miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln med namnet `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. 

```console
mkdir myapp && cd myapp
```
Kör `gradle init`. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts*, som används vid körning för att skapa och konfigurera ditt program. Kör kommandot från arbetskatalogen:

```console
gradle init --type basic
```

När du uppmanas att välja ett byggskript DSL väljer du **Kotlin**.

Hitta *build.gradle.kts* och öppna den med önskad IDE eller textredigerare. Kopiera sedan i följande byggkonfiguration. Den här konfigurationen definierar projektet som ett Java-program vars startpunkt är klassen **ContentModeratorQuickstart**. Den importerar Content Moderator SDK samt Gson sdk för JSON serialisering.

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

Kör följande kommando från arbetskatalogen för att skapa en projektkällamapp.

```console
mkdir -p src/main/java
```

Skapa sedan en fil med namnet *ContentModeratorQuickstart.java* i den nya mappen. Öppna filen i önskad redigerare eller IDE och importera följande bibliotek högst upp:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objektmodell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator Java SDK.

|Namn|Beskrivning|
|---|---|
|[InnehållModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Den här klassen behövs för alla funktioner för innehållsmodererator. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Den här klassen tillhandahåller funktioner för att analysera bilder för barnförbjudet innehåll, personlig information eller mänskliga ansikten.|
|[TextModerationer](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Den här klassen tillhandahåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Den här klassen innehåller funktionerna i gransknings-API:erna, inklusive metoder för att skapa jobb, anpassade arbetsflöden och mänskliga granskningar.|


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Innehållsmoderator för Java:

* [Autentisera klienten](#authenticate-the-client)
* [Måttliga bilder](#moderate-images)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Det här steget förutsätter att du har skapat en `AZURE_CONTENTMODERATOR_KEY` [miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln Innehållsmoderator med namnet .

Skapa ett `main` [ContentModeratorClient-objekt](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) med hjälp av prenumerationsslutpunktsvärdet och miljövariabeln för prenumerationsnyckeln i programmet. 

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Måttliga bilder

### <a name="get-images"></a>Hämta bilder

Skapa en **resursmapp** **i projektets src/main/-mapp** och navigera till den. Skapa sedan en ny textfil, *ImageFiles.txt*. I den här filen lägger du&mdash;till url:erna för bilder för att analysera en URL på varje rad. Du kan använda följande exempelbilder:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definiera hjälpklass

Lägg sedan till följande klassdefinition i klassen ContentModeratorQuickstart.java i filen *ContentModeratorQuickstart.java.* **ContentModeratorQuickstart** Den här inre klassen kommer att användas senare i bildmodereringsprocessen.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterera genom bilder

Lägg sedan till följande kod längst `main` ned i metoden. Du kan också lägga till den i en `main`separat metod som anropas från . Den här koden går igenom varje rad i _filen ImageFiles.txt._

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sök efter innehåll för vuxna/racy
Den här kodraden kontrollerar bilden på den angivna webbadressen för barnförbjudet eller racy-innehåll. Se konceptguiden för bildmodering för information om dessa termer.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sök efter text
Den här kodraden kontrollerar bilden efter synlig text.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sök efter ansikten
Den här kodraden kontrollerar bilden för mänskliga ansikten.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Slutligen lagra den returnerade `EvaluationData` informationen i listan.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Skriv ut resultat

Efter `while` loopen lägger du till följande kod, som skriver ut resultaten till konsolen och till en utdatafil, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Stäng `try` satsen och `catch` lägg till en sats för att slutföra metoden.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Köra appen

Du kan skapa appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

Navigera sedan till *filen src/main/resources/ModerationOutput.json* och visa resultatet av innehållsmoderering.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Java-biblioteket för innehållsmoderator för att utföra modereringsuppgifter. Läs sedan mer om måttfullhet av bilder eller andra medier genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
>[Begrepp för bildmoderering](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Vad är Azure Content Moderator?](./overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).