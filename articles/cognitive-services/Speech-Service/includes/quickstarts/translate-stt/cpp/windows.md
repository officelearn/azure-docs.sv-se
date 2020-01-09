---
title: 'Snabb start: Översätt tal-till-text C++ , (Windows) – tal service'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 2b32d6632c9a9860e628dbcfb810482da44316c2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467608"
---
## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande kodfragment:

   ```C++
   #include <iostream>
   #include <vector>
   #include <speechapi_cxx.h>

   using namespace std;
   using namespace Microsoft::CognitiveServices::Speech;
   using namespace Microsoft::CognitiveServices::Speech::Translation;

   void TranslateSpeechToText()
   {
       // Creates an instance of a speech translation config with specified subscription key and service region.
       // Replace with your own subscription key and service region (e.g., "westus").
       auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

       // Sets source and target languages.
       // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
       auto fromLanguage = "en-US";
       auto toLanguage = "de";
       config->SetSpeechRecognitionLanguage(fromLanguage);
       config->AddTargetLanguage(toLanguage);

       // Creates a translation recognizer using the default microphone audio input device.
       auto recognizer = TranslationRecognizer::FromConfig(config);

       // Starts translation, and returns after a single utterance is recognized. The end of a
       // single utterance is determined by listening for silence at the end or until a maximum of 15
       // seconds of audio is processed. The task returns the recognized text as well as the translation.
       // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
       // shot recognition like command or query.
       // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
       cout << "Say something...\n";
       auto result = recognizer->RecognizeOnceAsync().get();

       // Checks result.
       if (result->Reason == ResultReason::TranslatedSpeech)
       {
           cout << "RECOGNIZED '" << fromLanguage << "': " << result->Text << std::endl;
           cout << "TRANSLATED into '" << toLanguage << "': " << result->Translations.at(toLanguage) << std::endl;
       }
       else if (result->Reason == ResultReason::RecognizedSpeech)
       {
           cout << "RECOGNIZED '" << fromLanguage << "' " << result->Text << " (text could not be translated)" << std::endl;
       }
       else if (result->Reason == ResultReason::NoMatch)
       {
           cout << "NOMATCH: Speech could not be recognized." << std::endl;
       }
       else if (result->Reason == ResultReason::Canceled)
       {
           auto cancellation = CancellationDetails::FromResult(result);
           cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

           if (cancellation->Reason == CancellationReason::Error)
           {
               cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
               cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
               cout << "CANCELED: Did you update the subscription info?" << std::endl;
           }
       }
   }

   int wmain()
   {
       TranslateSpeechToText();
       return 0;
   }
   ```

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Välj **arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**).

1. Säg en engelsk fras eller en mening. Programmet skickar ditt tal till tal tjänsten som översätter och omvandlar till text (i det här fallet till tyska). Tal tjänsten skickar sedan tillbaka texten till programmet för visning.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
