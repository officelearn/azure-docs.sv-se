---
title: 'Snabbstart: Känna igen tal från en mikrofon, C++ (Linux) - Taltjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 1ba1f2a61a293880e81dde0ce1f93460a555fa62
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78926112"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=linux)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Skapa en C++-källfil med namnet `helloworld.cpp` och klistra in följande kod i den.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i den nya filen med din prenumerationsnyckel för Speech-tjänsten.

1. Ersätt strängen `YourServiceRegion` med **regionidentifieraren** från [den region](https://aka.ms/speech/sdkregion) `westus` som är associerad med din prenumeration (till exempel för den kostnadsfria utvärderingsprenumerationen).

> [!NOTE]
> Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

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

* På ett **ARM64** -system (64-bitars) kör du följande kommando för att skapa programmet.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
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

   * Ange följande kommando i ett **ARM64-system** (64-bitars).

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Kör appen.

   ```sh
   ./helloworld
   ```

1. I konsolfönstret visas en uppmaning som ber dig säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i samma fönster.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
