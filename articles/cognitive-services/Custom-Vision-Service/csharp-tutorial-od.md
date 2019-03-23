---
title: 'Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision SDK för C#'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och identifiera objekt med .NET SDK med C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 16484531e27bcbdfbd5b70303715ddd41bef7f07
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351362"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision. NET SDK

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision-SDK med C# för att skapa en objektidentifierarmodell. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en bild programmatiskt. Använd det här exemplet som en mall för att skapa dit eget .NET-program. 

## <a name="prerequisites"></a>Förutsättningar

- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision-SDK:n och exempelkoden

Om du vill skriva ett .NET-program som använder Custom Vision behöver du Custom Vision NuGet-paket. De ingår i det exempelprojekt som du laddar ned, men du kan komma åt dem enskilt här.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klona eller ladda ned [Cognitive Services .NET-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Gå till mappen **CustomVision/ObjectDetection** och öppna _ObjectDetection.csproj_ i Visual Studio.

Det här Visual Studio-projektet skapar ett nytt Custom Vision-projekt med namnet __My New Project__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en objektidentifierarmodell. I det här projektet tränas modellen i att identifiera gafflar och saxar i bilder.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Förstå koden

Öppna filen _Program.cs_ och granska koden. Infoga dina prenumerationsnycklar i lämpliga definitioner i metoden **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

Slutpunktsparametern bör peka på den region där den Azure-resursgrupp som innehåller Custom Vision-resurserna skapades. I det här exemplet antar vi att regionen är USA, södra centrala och använder:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt Custom Vision Service-projekt

Nästa bit kod skapar ett projekt för identifiering av objekt. Projektet som du har skapat visas på [Custom Vision-webbplatsen](https://customvision.ai/) som du tidigare besökt. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Lägga till taggar till projektet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objektidentifieringsprojekt måste du bestämma region för varje taggat objekt med hjälp av normaliserade koordinater. Följande kod associerar var och en av exempelbilderna med dess taggade region.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Den här mappningen av associationer används sedan för att ladda upp varje exempelbild med dess regionkoordinater.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

Nu har alla exempelbilder laddats upp, och var och en har en tagg (**gaffel** eller **sax**) och en tillhörande bildpunktsrektangel för taggen.

### <a name="train-the-project"></a>Utbilda projektet

Koden skapar den första träningsiterationen i projektet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="set-the-current-iteration-as-default"></a>Ange den aktuella iterationen som standard

Den här koden markerar den aktuella iterationen som standarditeration. Standardupprepningen speglar den version av modellen som svarar på förutsägelsebegäranden. Du bör uppdatera detta varje gång du tränar modellen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=119-124)]

### <a name="create-a-prediction-endpoint"></a>Skapa en förutsägelseslutpunkt

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=126-131)]

### <a name="use-the-prediction-endpoint"></a>Använda en förutsägelseslutpunkt

Den här delen av skriptet läser in testbilden, frågar modellslutpunkten och genererar förutsägelsedata till konsolen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=133-145)]

## <a name="run-the-application"></a>Köra programmet

När programmet körs ska det öppna ett konsolfönster och skriva följande utdata:

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Du kan sedan kontrollera att testbilden (finns i **Images/Test/**) har taggats på rätt sätt och att regionidentifieringen är rätt. Nu kan du avsluta programmet genom att trycka på valfri tangent.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i processen för objektidentifiering kan utföras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)
