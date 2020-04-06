---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 8d3f3bfcf1f175d5dbb05e661efbcf28e9c4a974
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671324"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure Speech-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programming-language-cpp)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

## <a name="source-code"></a>Källkod

Skapa en C++-källfil med namnet *helloworld.cpp*och klistra in följande kod i den.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kod förklaring

[!INCLUDE [code explanation](../code-explanation.md)]

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

[!INCLUDE [footer](../footer.md)]
