---
title: 'Snabbstart: Synthesize speech, C++ (Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att syntetisera tal i C++ i Linux med hjälp av tal-SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: yinhew
ms.openlocfilehash: 211394186fe8fc0fd1514a5ce1dbc1e0efc10b7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020615"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i C++ i Linux med hjälp av tal-SDK

I den här artikeln skapar du en C++ konsolen program för Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9). Du använder Cognitive Services [tal SDK](speech-sdk.md) att syntetisera tal från text i realtid och spela upp tal på datorns högtalare. Programmet har skapat med [Speech SDK för Linux](https://aka.ms/csspeech/linuxbinary) och Linux-distributionens C++-kompilerare (till exempel `g++`).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Speech Services-prenumeration för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Se [testa Speech Services kostnadsfritt](get-started.md) mer information.

## <a name="install-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.5.0`.

Speech SDK för Linux kan användas för att bygga både 64-bitars och 32-bitars program. De nödvändiga biblioteken och huvudfilerna kan laddas ned som en tar-fil från https://aka.ms/csspeech/linuxbinary.

Ladda ned och installera SDK enligt följande:

1. Kontrollera att SDK-beroenden är installerade.

   * I Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * On Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Välj en katalog som Speech SDK-filerna ska extraheras till och ange miljövariabeln `SPEECHSDK_ROOT` så att den pekar på den katalogen. Den här variabeln gör det enkelt att referera till katalogen i framtida kommandon. Exempel: Om du vill använda katalogen `speechsdk` i arbetskatalogen använder du ett kommando som liknar följande:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte redan finns.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Ladda ned och extrahera arkivet `.tar.gz` som innehåller Speech SDK-binärfilerna:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i toppnivåkatalogen i det extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Kataloglistan ska innehålla meddelande från tredje part och licensfiler, samt en `include`-katalog som innehåller huvudfiler (`.h`) och en `lib`-katalog som innehåller bibliotek.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Skapa en C++-källfil med namnet `helloworld.cpp` och klistra in följande kod i den.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. I den nya filen ersätter du strängen `YourSubscriptionKey` med din prenumerationsnyckel för Speech Services.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-the-app"></a>Skapa appen

> [!NOTE]
> Se till att ange kommandon nedan som en _enda kommandorad_. Det enklaste sättet att göra det är att kopiera kommandot med hjälp av **kopieringsknappen** bredvid varje kommando och klistra in den i gränssnittsprompten.

* Kör följande kommando för att skapa programmet i ett **x64**-system (64-bitars).

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Kör följande kommando för att skapa programmet i ett **x86**-system (32-bitars).

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Kör appen

1. Konfigurera inläsarens bibliotekssökväg så att den pekar på Speech SDK-biblioteket.

   * På ett **x64**-system (64-bitars) anger du följande kommando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * På ett **x86**-system (32-bitars) anger du följande kommando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Kör appen.

   ```sh
   ./helloworld
   ```

1. I konsolfönstret visas en fråga, där du uppmanas att ange lite text. Skriv några ord eller en mening. Den text som du har angett skickas till Speech Services och syntetiskt till tal som spelar upp på högtalaren.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa rösttyper](how-to-customize-voice-font.md)
- [Post voice-exempel](record-custom-voice-samples.md)
