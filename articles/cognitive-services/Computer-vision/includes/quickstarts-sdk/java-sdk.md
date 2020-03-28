---
title: 'Snabbstart: Client Library för Computer Vision för Java'
description: I den här snabbstarten kommer du igång med klientbiblioteket Computer Vision för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: dbe986145a223f1958f1945abfa189de90952f4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272742"
---
<a name="HOLTop"></a>

[Exempel på referensdokumentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [(Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Verktyget Gradle-byggverktyg](https://gradle.org/install/)eller annan beroendehanterare.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Azure-resurs för datorseende

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Datorseende med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/).

Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och `COMPUTER_VISION_SUBSCRIPTION_KEY` tjänstslutpunktssträngen, med namnet respektive `COMPUTER_VISION_ENDPOINT`.

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts*, som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

Hitta *build.gradle.kts* och öppna den med önskad IDE eller textredigerare. Kopiera sedan i följande byggkonfiguration. Den här konfigurationen definierar projektet som ett Java-program vars startpunkt är klassen **ComputerVisionQuickstarts**. Det importerar datorseende biblioteket.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

Kör följande kommando i arbetskatalogen för att skapa en projektkällasmapp:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil som heter *ComputerVisionQuickstarts.java*. Öppna den i önskad redigerare eller `import` IDE och lägg till följande satser:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Lägg sedan till en klassdefinition för **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Den här snabbstarten använder Gradle-beroendehanteraren. Du hittar klientbiblioteket och informationen för andra beroendehanterare i [Mavens centrala databas](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

I projektets *version.gradle.kts-fil,* inkludera klientbiblioteket Computer Vision som ett beroende.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Computer Vision Java SDK.

|Namn|Beskrivning|
|---|---|
| [ComputerVisionClient (Dator)](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Den här klassen behövs för alla datorseendefunktioner. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser.|
|[ComputerVision (dator)](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Den här klassen kommer från klientobjektet och hanterar direkt alla bildåtgärder, till exempel bildanalys, textidentifiering och miniatyrgenerering.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Den här uppräkningen definierar de olika typer av bildanalys som kan göras i en standardanalysåtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Computer Vision för Java:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Läsa tryckt och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har skapat `COMPUTER_VISION_SUBSCRIPTION_KEY`en [miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln Datorseende med namnet .

Följande kod lägger till en `main` metod i klassen och skapar variabler för resursens Azure-slutpunkt och nyckel. Du måste ange din egen slutpunktssträng, som du kan hitta genom att kontrollera avsnittet **Översikt** i Azure-portalen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Lägg sedan till följande kod för att skapa ett [ComputerVisionClient-objekt](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) och skickar det till andra metoder som du definierar senare.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod definierar en `AnalyzeLocalImage`metod, som använder klientobjektet för att analysera en lokal bild och skriva ut resultaten. Metoden returnerar en textbeskrivning, kategorisering, lista med taggar, identifierade ansikten, flaggor för barnförbjudet innehåll, huvudfärger och bildtyp.

### <a name="set-up-test-image"></a>Konfigurera testavbildning

Skapa först en **resurs/mapp** i **projektets src/main/-mapp** och lägg till en bild som du vill analysera. Lägg sedan till följande metoddefinition i klassen **ComputerVisionQuickstarts.** Om det behövs ändrar `pathToLocalImage` du värdet på bildfilen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Du kan också analysera en fjärravbildning med hjälp av webbadressen. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) för scenarier med fjärravbildningar.

### <a name="specify-visual-features"></a>Ange visuella funktioner

Ange sedan vilka visuella funktioner du vill extrahera i analysen. Se [VisualFeatureTypes-uppräkningen](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) för en fullständig lista.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analysera
Den här metoden skriver ut detaljerade resultat till konsolen för varje omfattning av bildanalys. Vi rekommenderar att du omger det här metodanropet i ett Try/Catch-block. Metoden **analyzeImageInStream** returnerar ett **ImageAnalysis-objekt** som innehåller all extraherad information.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

Följande avsnitt visar hur du tolkar den här informationen i detalj.

### <a name="get-image-description"></a>Få bildbeskrivning

Följande kod hämtar listan över genererade bildtexter för bilden. Mer information finns i [Beskriv bilder](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Hämta bildkategori

Följande kod får den identifierade kategorin av bilden. Mer information finns i [Kategorisera bilder](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Hämta bildtaggar

Följande kod hämtar uppsättningen identifierade taggar i bilden. Mer information finns i [Innehållstaggar](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar de identifierade ytorna i bilden med sina rektangelkoordinater och väljer ansiktsattribut. Mer information finns i [Ansiktsigenkänning](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera innehåll för vuxna, racy eller blodiga

Följande kod skriver ut den upptäckta förekomsten av sexuellt innehåll i bilden. Mer information finns i [Vuxen, racy, blodiga innehåll](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Hämta bildfärgschema

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel de dominerande färgerna och accentfärgen. Mer information finns i [Färgscheman](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Hämta domänspecifikt innehåll

Computer Vision kan använda specialiserad modell för att göra ytterligare analys på bilder. Mer information finns i [Domänspecifikt innehåll](../../concept-detecting-domain-content.md). 

Följande kod tolkar data om identifierade kändisar i bilden.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Hämta bildtypen

Följande kod skriver ut information om&mdash;vilken typ av bild det är ClipArt eller linjeritning.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Läsa tryckt och handskriven text

Computer Vision kan läsa synlig text i en bild och konvertera den till en teckenström.

> [!NOTE]
> Du kan också läsa text i en fjärrbild med hjälp av webbadressen. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) för scenarier med fjärravbildningar.

### <a name="call-the-recognize-api"></a>Anropa API:et för identifiera

Använd först följande kod för att anropa metoden **recognizePrintedTextInStream** för den givna avbildningen. När du lägger till den här koden i `localTextImagePath` projektet måste du ersätta värdet för med sökvägen till den lokala bilden. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Skriv ut igen-resultat

Följande kodblock bearbetar den returnerade texten och tolkar den för att skriva ut det första ordet på varje rad. Du kan använda den här koden för att snabbt förstå strukturen för en **OcrResult-instans.**

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Slutligen stänger du ut try/catch-blocket och metoddefinitionen.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Köra appen

Du kan skapa appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Java-biblioteket För visuellt innehåll för att utföra grunduppgifter. Därefter kan du utforska referensdokumentationen om du vill veta mer om biblioteket.

> [!div class="nextstepaction"]
>[Referens för datorseende (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Vad är visuellt innehåll?](../../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).