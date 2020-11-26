---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188473"
---
## <a name="install-speech-sdk"></a>Installera Speech SDK

Speech SDK för Linux kan användas för att bygga både 64-bitars och 32-bitars program. De nödvändiga biblioteken och huvudfilerna kan laddas ned som en tar-fil från https://aka.ms/csspeech/linuxbinary.

Ladda ned och installera SDK enligt följande:

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

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]