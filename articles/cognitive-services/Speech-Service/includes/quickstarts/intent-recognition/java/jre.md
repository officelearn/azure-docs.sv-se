---
title: 'Snabb start: identifiera tal, avsikter och entiteter, Java-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9d847bbc27710a510564363281b65ac57f57ed7e
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796194"
---
## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att:

> [!div class="checklist"]
>
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Skapa ett Language Understanding-program (LUIS) och hämta en slut punkts nyckel](../../../../quickstarts/create-luis.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Öppna projektet

Läs in projektet och öppna `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `IntentRecognizer`-objekt måste du skapa en konfiguration som använder din LUIS-slutpunkt nyckel och region. Infoga den här koden i try/catch-blocket i huvud

I det här exemplet används metoden `FromSubscription()` för att bygga `SpeechConfig`. En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klass](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

> [!NOTE]
> Det är viktigt att använda slut punkts nyckeln LUIS och inte start-eller redigerings nycklarna eftersom endast slut punkts nyckeln är giltig för tal till avsikts igenkänning. Mer information om hur du hämtar rätt nyckel finns i [skapa ett Luis-program och hämta en slut punkts nyckel](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>Initiera en IntentRecognizer

Nu ska vi skapa en `IntentRecognizer`. Infoga den här koden direkt under din tal konfiguration.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Lägg till ett LanguageUnderstandingModel och avsikter

Nu måste du associera ett `LanguageUnderstandingModel` med avsikts igenkänningen och lägga till de avsikter som du vill identifiera.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Identifiera en avsikt

Från `IntentRecognizer`-objektet kommer du att anropa metoden `recognizeOnceAsync()`. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Visa tolknings resultat (eller fel)

När igenkännings resultatet returneras av tal tjänsten vill du göra något med det. Vi ska hålla det enkelt och skriva ut resultatet till-konsolen.

Under ditt anrop till `recognizeOnceAsync()`lägger du till följande kod: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Versions resurser

Det är viktigt att publicera tal resurserna när du är klar med dem. Infoga den här koden i slutet av try/catch-blocket: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Kontrol lera koden

Nu bör din kod se ut så här:  
(Vi har lagt till några kommentarer till den här versionen) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Tryck på F11 eller välj **Kör** > **Felsök**.
Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]