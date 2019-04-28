---
title: 'Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision SDK för Java'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna projektet och identifiera objekt med hjälp av Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 00684df614771437f33655538a808468ee778d29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995970"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision SDK för Java

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision SDK med Java för att skapa en objektidentifieringsmodell. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en bild programmatiskt. Använd det här exemplet som en mall för att skapa ditt eget Java-program.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En valfri Java IDE
- [JDK 7 eller 8](https://aka.ms/azure-jdks) installerat.
- Maven installerat

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision-SDK:n och exempelkoden

Om du vill skriva ett Java-program som använder Custom Vision behöver du Custom Vision maven-paketen. De ingår i det exempelprojekt som du laddar ned, men du kan komma åt dem enskilt här.

Du kan installera SDK för Custom Vision från maven-centrallager:
- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klona eller ladda ned [Cognitive Services Java SDK-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navigera till mappen **Vision/CustomVision/**.

Det här Java-projektet skapar ett nytt Custom Vision-projekt för objektidentifiering som kallas för __Sample Java OD Project__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra huruvida ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Programmet är konfigurerat för att lagra dina nyckeldata som miljövariabler. Ange dessa variabler genom att navigera till mappen **Vision/CustomVision** i PowerShell. Ange sedan kommandona:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Förstå koden

Läs in `Vision/CustomVision`-projektet i din Java IDE och öppna filen _CustomVisionSamples.java_. Leta upp metoden **runSample** och kommentera ut metodanropet för **ImageClassification_Sample** &mdash;detta kör scenariot med bildklassificering, vilket inte avses i den här guiden. Metoden **ObjectDetection_Sample** implementerar de primära funktionerna i den här snabbstarten. Gå till dess definition och granska koden. 

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt Custom Vision Service-projekt

Gå till det kodblock som skapar en träningsklient och ett projekt för objektidentifiering. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objektidentifieringsprojekt måste du bestämma region för varje taggat objekt med hjälp av normaliserade koordinater. Gå till definitionen av `regionMap`-kartan. Den här koden associerar var och en av exempelbilderna med dess taggade region.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Gå sedan till det kodblock som lägger till bilderna i projektet. Bilderna läses från mappen **src/main/resurser** i projektet och laddas upp till tjänsten med lämpliga taggar och regionskoordinater.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

Föregående kodavsnitt använder två hjälpfunktioner som hämtar bilderna som resursdataströmmar och laddar upp dem till tjänsten.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project-and-publish"></a>Träna projektet och publicera

Den här koden skapar den första upprepningen i projektet och sedan publicerar den iterationen till slutpunkten för förutsägelse. Namnet på den publicerade iterationen kan användas för att skicka förfrågningar för förutsägelse. En iteration är inte tillgänglig i förutsägelse slutpunkten tills den har publicerats.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Använda förutsägelseslutpunkten

Förutsägelseslutpunkten, som representeras av objektet `predictor` här, är den referens som du kan använda för att skicka en bild till den aktuella modellen och få en klassificeringsförutsägelse. I det här exemplet har `predictor` definierats någon annanstans med hjälp av miljövariabeln för förutsägelsenyckeln.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Köra programmet

För att kompilera och köra lösningen med hjälp av maven kör du följande kommando i projektkatalogen i PowerShell:

```powershell
mvn compile exec:java
```

Visa konsolens utdata för loggnings- och förutsägelseresultat. Du kan sedan kontrollera att testbilden har taggats på rätt sätt och att regionidentifieringen är rätt.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i processen för objektidentifiering kan utföras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)
