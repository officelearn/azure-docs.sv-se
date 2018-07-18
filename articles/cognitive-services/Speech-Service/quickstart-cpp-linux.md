---
title: 'Snabbstart: Känna igen tal i C++ på Linux med hjälp av Cognitive Services tal SDK | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i C++ på Linux med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: b5f5cdbe202b406c724a9f4f5787e566b432a66c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116154"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i C++ i Linux med hjälp av tal-SDK

I den här artikeln lär du dig att skapa ett C++-konsolprogram i Linux (Ubuntu 16.04) med hjälp av Cognitive Services tal SDK för att transkribera tal till text.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En Ubuntu 16.04 dator med en fungerande mikrofon.
* Om du vill installera kör paket som behövs för att skapa och köra det här exemplet du följande:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Hämta tal SDK

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services tal SDK är `0.5.0`.

Cognitive Services tal SDK för Linux är tillgänglig för att bygga för 64-bitars och 32-bitars program.
De nödvändiga filerna kan hämtas som tar-filen från https://aka.ms/csspeech/linuxbinary.
Hämta och installera SDK: N på följande sätt:

1. Välj en katalog (absolut sökväg) där du vill placera tal SDK-binärfilerna och rubriker.
   Välj till exempel sökvägen `speechsdk` under arbetskatalogen:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte finns:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Ladda ned och extrahera den `.tar.gz` Arkiv med tal SDK-binärfilerna:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i katalogen på den högsta nivån av extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Du bör se meddelande från tredje part och licensfiler, såväl som `include` katalog för rubriker och en `lib` katalogen för bibliotek.

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. Lägg till följande kod i en fil med namnet `helloworld.cpp`:

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="building"></a>Skapar

> [!NOTE]
> Se till att kopiera och klistra in build-kommandot nedan som en _enskild rad_.

* På en **x64** dator, kör följande kommando för att skapa programmet:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* På en **x86** dator, kör följande kommando för att skapa programmet:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Kör exemplet

1. Konfigurera konfigurera inläsarens bibliotekssökväg så att den pekar till tal SDK-biblioteket.

   * På en **x64** dator, kör:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * På en **x86** dator, kör:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Kör programmet på följande sätt:

   ```sh
   ./helloworld
   ```

1. Du bör se utdata som liknar detta:

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/cpp-linux` mapp.

## <a name="next-steps"></a>Nästa steg

* Gå till den [sidan med kodexempel](samples.md) ytterligare exempel.
