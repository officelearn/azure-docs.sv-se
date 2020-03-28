---
title: 'Snabbstart: Känna igen tal från en mikrofon, C# (.NET) - Taltjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: c969b5e5daa4c4cfd84695fef70f0a2a5c50ce02
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924888"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Ladda projektet och `Program.cs`öppna .

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt. Observera att du har skapat en `RecognizeSpeechAsync()`asynkron metod som kallas .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Innan du kan `SpeechRecognizer` initiera ett objekt måste du skapa en konfiguration som använder din prenumerationsnyckel och prenumerationsregion (välj **regionidentifieraren** från [regionen](https://aka.ms/speech/sdkregion). Infoga den här `RecognizeSpeechAsync()` koden i metoden.

> [!NOTE]
> I det `FromSubscription()` här exemplet `SpeechConfig`används metoden för att skapa . En fullständig lista över tillgängliga metoder finns i [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

## <a name="initialize-a-speechrecognizer"></a>Initiera en SpeechRecognizer

Nu ska vi skapa `SpeechRecognizer`en . Det här objektet skapas inuti en medsats för att säkerställa korrekt frisläppande av ohanterat resurser. Infoga den `RecognizeSpeechAsync()` här koden i metoden, precis under talkonfigurationen.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Känna igen en fras

Från `SpeechRecognizer` objektet ska du anropa `RecognizeOnceAsync()` metoden. Med den här metoden kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal.

Lägg till den här koden i satsen med användningssatsen.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Visa igenkänningsresultat (eller fel)

När igenkänningsresultatet returneras av taltjänsten bör du göra något med den. Vi ska hålla det enkelt och skriva ut resultatet till konsolen.

Lägg till den `RecognizeOnceAsync()`här koden i satsen nedan.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Kontrollera din kod

Nu ska koden se ut så här.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår taligenkänning med taltjänsten.

1. **Kompilera koden** - Välj **Bygg** > **bygglösning**på menyraden i Visual Studio .
2. **Starta appen** - Välj Felsökning **Debug** > **Avsöka Startfelsökning** på menyraden eller tryck på **F5**.
3. **Starta erkännande** - Det kommer att uppmana dig att tala en fras på engelska. Ditt tal skickas till taltjänsten, transkriberas som text och återges i konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
