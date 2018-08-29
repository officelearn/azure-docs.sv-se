---
title: Om Cognitive Services tal SDK
description: 'En översikt över SDK: er tillgängliga för Speech-tjänsten.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: v-jerkin
ms.openlocfilehash: c26aeb1d29c3b2c8b5b43d1a1face818295e9d2f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122090"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Om Cognitive Services tal SDK

Den Cognitive Services tal Software Development Kit (SDK) ger dina program inbyggd åtkomst till funktioner i tjänsten tal, vilket gör det enklare att utveckla programvara. För närvarande SDK ger åtkomst till **tal till Text**, **Talöversättning**, och **avsikt erkännande**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

### <a name="windows"></a>Windows

Vi stöder följande språk för Windows:

* C# (UWP- och .NET), C++: du kan referera till och använda den senaste versionen av vår tal SDK NuGet-paketet.
  Paketet innehåller 32-bitars och 64-bitars klientbibliotek som hanterade (.NET)-bibliotek.
  SDK: N kan installeras i Visual Studio med hjälp av NuGet; Sök bara efter `Microsoft.CognitiveServices.Speech`.

* Java: Du kan referera till och använda den senaste versionen av vår tal SDK Maven-paketet som har stöd för Windows x64.
  I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` som ett beroende. 

### <a name="linux"></a>Linux

> [!NOTE]
> För närvarande stöder vi bara Ubuntu 16.04 på en dator (x86 eller x64 för utveckling med C++, x64 för .NET Core och Java).

Kontrollera att du har de nödvändiga kompilatorn och bibliotek som är installerade genom att köra följande kommandon för shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Du kan referera till och använder den senaste versionen av vår tal SDK NuGet-paketet.
  Lägg till följande paketreferens i projektet om du vill referera till SDK:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="0.6.0" />
  ```

* Java: Du kan referera till och använda den senaste versionen av vår tal SDK Maven-paketet.
  I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` som ett beroende. 

* C++: ladda ned SDK som en [.tar-paketet](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i en katalog som du önskar. I följande tabell visas mappstrukturen SDK.

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Header-filer för C och C++|
  |`lib/x64`|Interna x64 bibliotek för att länka med ditt program|
  |`lib/x86`|Interna x86 bibliotek för att länka med ditt program|

  Om du vill skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö och lägga till dem som krävs i utvecklingsprocessen.

### <a name="android"></a>Android

Java SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs för att använda den.
Den finns i ett Maven-centrallager på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`.
Om du vill använda paketet från Android Studio-projekt gör följande ändringar:

* I projektet servernivå `build.gradle` Lägg till följande till den `repository` avsnittet:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* I modul servernivå `build.gradle` Lägg till följande till den `dependencies` avsnittet:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.6.0'
  ```

Java SDK är också en del av den [tal Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
