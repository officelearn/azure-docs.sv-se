---
title: 'Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision SDK för Java'
titleSuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna projektet och identifiera objekt med hjälp av Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: areddish
ms.openlocfilehash: 78db95240974d1c9ca07546f8237eca2b564ecb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77616328"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision SDK för Java

Den här artikeln visar hur du kommer igång med custom vision SDK med Java för att skapa en objektidentifieringsmodell. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, Hämta projektets standard-URL för förutsägelse slut punkt och använda slut punkten för att testa en avbildning. Använd det här exemplet som en mall för att skapa ditt eget Java-program.

## <a name="prerequisites"></a>Krav

- En valfri Java IDE
- [JDK 7 eller 8](https://aka.ms/azure-jdks) installerat.
- [Maven](https://maven.apache.org/) installerat
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision SDK och exempel kod

Om du vill skriva ett Java-program som använder Custom Vision behöver du Custom Vision maven-paketen. Dessa paket ingår i exempelprojektet som du kommer att ladda ner, men du kan komma åt dem individuellt här.

Du hittar Custom Vision SDK i maven central databas:
- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klona eller ladda ned [Cognitive Services Java SDK-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navigera till mappen **Vision/CustomVision/**.

Det här Java-projektet skapar ett nytt Custom Vision-projekt för objektidentifiering som kallas för __Sample Java OD Project__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra om ett objekt är en **gaffel** eller **sax**.

[!INCLUDE [get-keys](includes/get-keys.md)]

Programmet är konfigurerat för att referera till dina nyckeldata som miljövariabler. Navigera till mappen **Vision/CustomVision** och ange följande PowerShell-kommandon för att ange miljövariabler. 

> [!NOTE]
> Om du använder ett operativsystem som inte är Windows läser du [Konfigurera miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) för instruktioner.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Förstå koden

Läs in `Vision/CustomVision`-projektet i din Java IDE och öppna filen _CustomVisionSamples.java_. Hitta **metoden runSample** och kommentera **ut** ImageClassification_Sample&mdash;metod anropet kör bildklassificeringsscenariot, som inte ingår i den här guiden. Metoden **ObjectDetection_Sample** implementerar de primära funktionerna i den här snabbstarten. Gå till dess definition och granska koden. 

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt Custom Vision Service-projekt

Gå till det kodblock som skapar en träningsklient och ett projekt för objektidentifiering. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [metoden CreateProject-metodens](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) överbelastningar om du vill ange andra alternativ när du skapar projektet (förklaras i webbportalguiden [För bygg en detektor).](get-started-build-detector.md)

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objekt identifierings projekt måste du ange regionen för varje taggat objekt med normaliserade koordinater. Gå till definitionen av `regionMap`-kartan. Den här koden associerar var och en av exempelbilderna med dess taggade region.

> [!NOTE]
> Om du inte har ett klick-och-dra-verktyg för att markera koordinaterna för regioner kan du använda webbgränssnittet på [Customvision.ai](https://www.customvision.ai/). I det här exemplet finns koordinaterna redan.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Gå sedan till det kodblock som lägger till bilderna i projektet. Bilderna läses från mappen **src/main/resurser** i projektet och laddas upp till tjänsten med lämpliga taggar och regionskoordinater.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Det föregående kodavsnittet använder sig av två hjälpfunktioner som hämtar bilderna som resursströmmar och laddar upp dem till tjänsten (du kan ladda upp upp till 64 bilder i en enda batch).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Träna projektet och publicera

Den här koden skapar den första iterationen av förutsägelsemodellen och publicerar sedan iterationen till förutsägelseslutpunkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelseslutpunkten förrän den publiceras.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Använd förutsägelse slut punkten

Förutsägelseslutpunkten, som representeras av objektet `predictor` här, är den referens som du kan använda för att skicka en bild till den aktuella modellen och få en klassificeringsförutsägelse. I det här exemplet har `predictor` definierats någon annanstans med hjälp av miljövariabeln för förutsägelsenyckeln.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Köra appen

Om du vill kompilera och köra lösningen med maven navigerar du till projektkatalogen (**Vision/CustomVision**) i en kommandotolk och kör kommandot kör:

```bash
mvn compile exec:java
```

Visa konsolens utdata för loggnings- och förutsägelseresultat. Du kan sedan kontrollera att testbilden har taggats på rätt sätt och att regionidentifieringen är rätt.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i processen för objektidentifiering kan utföras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)
