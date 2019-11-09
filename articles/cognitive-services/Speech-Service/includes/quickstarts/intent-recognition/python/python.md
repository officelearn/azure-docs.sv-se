---
title: 'Snabb start: identifiera tal, avsikter och entiteter, python-tal-tjänst'
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9a226c17dd72d0dcd6403277054cf264f0094e65
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850510"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Skapa ett LUIS-program och hämta en slut punkts nyckel](../../../../quickstarts/create-luis.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Öppna projektet

Öppna Quickstart.py i python-redigeraren.

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `IntentRecognizer`-objekt måste du skapa en konfiguration som använder din LUIS Endpoing-nyckel och region. Infoga den här koden härnäst.

Det här exemplet skapar `SpeechConfig`-objektet med hjälp av nyckel och region i LUIS. En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klass](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> Det är viktigt att använda slut punkts nyckeln LUIS och inte start-eller redigerings nycklarna eftersom endast slut punkts nyckeln är giltig för tal till avsikts igenkänning. Mer information om hur du hämtar rätt nyckel finns i [skapa ett Luis-program och hämta en slut punkts nyckel](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-a-intentrecognizer"></a>Initiera en IntentRecognizer

Nu ska vi skapa en `IntentRecognizer`. Infoga den här koden direkt under din tal konfiguration.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Lägg till ett LanguageUnderstandingModel och avsikter

Nu måste du associera ett `LanguageUnderstandingModel` med avsikts igenkänningen och lägga till de avsikter som du vill identifiera.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Identifiera en avsikt

Från `IntentRecognizer`-objektet kommer du att anropa metoden `recognize_once()`. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att stoppa reconizing tal.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Visa tolknings resultat (eller fel)

När igenkännings resultatet returneras av tal tjänsten vill du göra något med det. Vi ska hålla det enkelt och skriva ut resultatet till-konsolen.

I using-instruktionen, under ditt anrop till `recognize_once()`, lägger du till följande kod: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kontrol lera koden

I det här läget bör din kod se ut så här: (vi har lagt till några kommentarer till den här versionen) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Kör exemplet från-konsolen eller i IDE:

    ````
    python quickstart.py
    ````

Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
