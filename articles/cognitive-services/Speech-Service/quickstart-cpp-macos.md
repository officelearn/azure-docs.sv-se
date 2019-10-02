---
title: 'Snabbstart: Identifiera tal, C++ (MacOS) – tal service'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal C++ i på MacOS med hjälp av tal-SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 12abb7fb6d5e93e226ae539f8161566e3c30bd65
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803333"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Snabbstart: Identifiera tal i C++ på MacOS med hjälp av tal-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln skapar du ett C++ konsol program för MacOS 10,13 och senare. Du använder Cognitive Services [Speech SDK](speech-sdk.md) för att skriva tal till text i real tid från Mac-mikrofonen. Programmet har skapats med [talet SDK för MacOS](https://aka.ms/csspeech/macosbinary) och din Mac- C++ standardkompilerare (till exempel @no__t 2).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänsterna kostnads fritt](get-started.md) .

## <a name="install-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Talet SDK för macOS kan laddas ned som ett zippat Ramverks paket från https://aka.ms/csspeech/macosbinary.

Ladda ned och installera SDK enligt följande:

1. Välj en katalog som Speech SDK-filerna ska extraheras till och ange miljövariabeln `SPEECHSDK_ROOT` så att den pekar på den katalogen. Den här variabeln gör det enkelt att referera till katalogen i framtida kommandon. Exempel: Om du vill använda katalogen `speechsdk` i arbetskatalogen använder du ett kommando som liknar följande:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte redan finns.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Hämta och extrahera `.zip`-arkivet som innehåller talet SDK Framework:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i toppnivåkatalogen i det extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Katalog listan ska innehålla meddelanden från tredje part och licensfiler, samt en `MicrosoftCognitiveServicesSpeech.framework`-katalog.

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Skapa en C++-källfil med namnet `helloworld.cpp` och klistra in följande kod i den.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerations nyckel för tal tjänster i den nya filen.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-the-app"></a>Skapa appen

> [!NOTE]
> Se till att ange kommandon nedan som en _enda kommandorad_. Det enklaste sättet att göra det är att kopiera kommandot med hjälp av **kopieringsknappen** bredvid varje kommando och klistra in den i gränssnittsprompten.

* Kör följande kommando för att skapa programmet.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Kör appen

1. Konfigurera inläsarens bibliotekssökväg så att den pekar på Speech SDK-biblioteket.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Kör appen.

   ```sh
   ./helloworld
   ```

1. I konsolfönstret visas en uppmaning som ber dig säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till tal tjänsterna och skrivs till text som visas i samma fönster.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
