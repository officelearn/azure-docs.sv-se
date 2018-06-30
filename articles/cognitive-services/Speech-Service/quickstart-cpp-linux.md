---
title: Snabbstart för tal-SDK för C++- och Linux | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Hämta information och exempel på kod för att snabbt komma igång med tal-SDK med Linux- och C++ i kognitiva Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: e5ea74f92eb91ff89f013a4ee9ef7cbe0f001db0
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111158"
---
# <a name="quickstart-for-c-and-linux"></a>Snabbstart för C++- och Linux

Den aktuella versionen av kognitiva Services tal SDK är `0.4.0`.

Kognitiva Services tal SDK för Linux är tillgängligt för skapande av 64-bitars och 32-bitars program. Filerna som krävs kan hämtas som tar-filen från https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Om du letar efter en Snabbstart för C++ och Windows, gå [här](quickstart-cpp-windows.md).
> Om du letar efter en Snabbstart för C# och Windows, gå [här](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Dessa instruktioner förutsätter att du kör på Ubuntu 16.04 på en dator (x86 eller x64).
> På en annan Ubuntu-version, eller en annan Linux-distributionsplats behöver du anpassa de nödvändiga stegen.

## <a name="prerequisites"></a>Förutsättningar

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Hämta binära paketet

[!include[License Notice](includes/license-notice.md)]

1. Välj en katalog (absolut sökväg) där du vill placera tal SDK binärfiler och rubriker.
   Välj till exempel sökvägen `speechsdk` under arbetskatalogen:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte finns:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Ladda ned och extrahera den `.tar.gz` Arkiv med binärfilerna för tal-SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i den översta katalogen i extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Den ska visa ett meddelande från tredje part och licensfiler, samt en `include` katalogen för rubriker och en `lib` katalogen för bibliotek.

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Exempelkod

Följande kod identifierar engelska tal från din mikrofon.
Placera den i en fil med namnet `quickstart-linux.cpp`:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

> [!IMPORTANT]
> Ersätt nyckeln prenumerationen med det som du har köpt. <br>
> Ersätt den [region](regions.md) med den som är associerade med prenumerationen, till exempel ersätta med `westus` för den kostnadsfria utvärderingsversionen.

## <a name="building"></a>Skapar

> [!NOTE]
> Se till att kopiera och klistra in build-kommandona nedan som en _enskild rad_.

* Kör följande kommando för att skapa programmet på en x64 datorn:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Kör följande kommando för att skapa programmet på en x86 datorn:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Körs

Om du vill köra programmet, behöver du konfigurera den inläsaren bibliotekssökväg peka tal SDK-biblioteket.

* På en x64 dator som kör:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* På en x86 dator som kör:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Kör programmet på följande sätt:

```sh
./quickstart-linux
```

Om allt går bra bör du se utdata som liknar detta:

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Hämta exemplet

Senaste uppsättning exempel, finns det [kognitiva Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

* Besök den [-exempel](samples.md) ytterligare exempel.
