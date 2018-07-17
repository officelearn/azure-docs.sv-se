---
title: Om Cognitive Services tal SDK | Microsoft Docs
description: 'En översikt över SDK: er tillgängliga för Speech-tjänsten.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: bf37ffa9b5a185845c8eeafa7e00d352486fdce2
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069472"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Om Cognitive Services tal SDK

Den Cognitive Services tal Software Development Kit (SDK) ger dina program inbyggd åtkomst till funktioner i tjänsten tal, vilket gör det enklare att utveckla programvara. För närvarande SDK ger åtkomst till **tal till Text**, **Talöversättning**, och **avsikt erkännande**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](includes/license-notice.md)]

## <a name="get-the-windows-sdk"></a>Hämta Windows SDK

Windows-versionen av tal SDK innehåller 32-bitars och 64-bitars C/C++-klientbibliotek som hanterade (.NET)-bibliotek för användning med C#. SDK: N kan installeras i Visual Studio med hjälp av NuGet; Sök bara efter `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Hämta SDK för Linux

Kontrollera att du har de nödvändiga kompilatorn och bibliotek genom att köra följande shell-kommandon:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Dessa instruktioner förutsätter att du kör Ubuntu 16.04 på en dator (x86 eller x64). Anpassa stegen i din miljö på en annan Ubuntu-version eller en annan Linux-distribution.

Sedan [ladda ned SDK](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i en katalog som du önskar. Den här tabellen visar strukturen för SDK-mappen.

|Sökväg|Beskrivning|
|-|-|
|`license.md`|Licens|
|`third-party-notices.md`|Meddelanden från tredje part|
|`include`|Header-filer för C och C++|
|`lib/x64`|Interna x64 bibliotek för att länka med ditt program|
|`lib/x86`|Interna x86 bibliotek för att länka med ditt program|

Om du vill skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö och lägga till dem som krävs i utvecklingsprocessen.

## <a name="get-the-java-sdk"></a>Hämta Java SDK

Java SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs för att använda den.
Den finns i ett Maven-centrallager på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.

* Lägg till i Android Studio-projekt

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

  i den `dependencies` avsnitt i din modul-nivå `build.gradle` fil.

Java SDK är också en del av den [tal Devices SDK](speech-devices-sdk.md).

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
