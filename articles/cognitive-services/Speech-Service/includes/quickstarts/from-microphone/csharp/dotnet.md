---
title: 'Snabb start: identifiera tal från en mikrofon C# , (.net)-tal-service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 88ac0f05bf937af19a0bd6bf3cf2253fd3052f4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503488"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Läs in projektet och öppna `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt. Observera att du har skapat en asynkron metod som heter `RecognizeSpeechAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `SpeechRecognizer`-objekt måste du skapa en konfiguration som använder din prenumerations nyckel och din prenumerations region. Infoga den här koden i metoden `RecognizeSpeechAsync()`.

> [!NOTE]
> I det här exemplet används metoden `FromSubscription()` för att bygga `SpeechConfig`. En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klass](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]

## <a name="initialize-a-speechrecognizer"></a>Initiera en SpeechRecognizer

Nu ska vi skapa en `SpeechRecognizer`. Det här objektet skapas i en using-instruktion för att säkerställa en korrekt version av ohanterade resurser. Infoga den här koden i metoden `RecognizeSpeechAsync()`, direkt under din tal konfiguration.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Identifiera en fras

Från `SpeechRecognizer`-objektet kommer du att anropa metoden `RecognizeOnceAsync()`. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal.

I using-instruktionen lägger du till följande kod: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Visa tolknings resultat (eller fel)

När igenkännings resultatet returneras av tal tjänsten vill du göra något med det. Vi ska hålla det enkelt och skriva ut resultatet till-konsolen.

Lägg till följande kod i using-instruktionen nedan `RecognizeOnceAsync()`: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Kontrol lera koden

Nu bör din kod se ut så här: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår tal igenkänning med röst tjänsten.

1. **Kompilera koden** – från meny raden i Visual Studio väljer du **Bygg** > build- **lösning**.
2. **Starta din app** – från meny raden väljer du **Felsök** > **Starta fel sökning** eller tryck på **F5**.
3. **Starta igenkänning** – du uppmanas att tala en fras på engelska. Ditt tal skickas till tal tjänsten, skrivs som text och återges i-konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
