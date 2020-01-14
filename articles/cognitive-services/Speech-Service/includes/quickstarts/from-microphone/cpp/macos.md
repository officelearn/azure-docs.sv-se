---
title: 'Snabb start: identifiera tal från en mikrofon C++ , (MacOS) – tal tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 08459238173b379280cb513ccb3242e4f15ef0bc
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75927822"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=macos)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Skapa en C++-källfil med namnet `helloworld.cpp` och klistra in följande kod i den.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i den nya filen med din prenumerationsnyckel för Speech-tjänsten.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

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

1. I konsolfönstret visas en uppmaning som ber dig säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i samma fönster.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]