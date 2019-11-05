---
title: 'Snabb start: Speech C++ SDK (MacOS) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform C++ för MacOS med Speech Services SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502550"
---
Den här guiden visar hur du installerar [talet SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för C++ på MacOS 10,13 och senare.

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
