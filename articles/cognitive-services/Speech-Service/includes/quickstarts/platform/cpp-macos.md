---
title: 'Snabb start: tal SDK C++ (macOS) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för C++ på macOS med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3ad8eb9564c4d8343a0763cc2f6f5061ee602b72
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188377"
---
Den här guiden visar hur du installerar [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för C++ på MacOS 10,13 och senare.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemkrav

macOS 10,13 och senare

## <a name="install-speech-sdk"></a>Installera Speech SDK

1. Välj en katalog som Speech SDK-filerna ska extraheras till och ange miljövariabeln `SPEECHSDK_ROOT` så att den pekar på den katalogen. Den här variabeln gör det enkelt att referera till katalogen i framtida kommandon. Exempel: Om du vill använda katalogen `speechsdk` i arbetskatalogen använder du ett kommando som liknar följande:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte redan finns.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Hämta och extrahera `.zip` arkivet som innehåller talet SDK Framework:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i toppnivåkatalogen i det extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Katalog listan ska innehålla meddelanden från tredje part och licensfiler, samt en `MicrosoftCognitiveServicesSpeech.framework` katalog.

Nu kan du gå vidare till [Nästa steg](#next-steps) nedan.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
