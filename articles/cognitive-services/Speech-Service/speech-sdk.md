---
title: Om talet för tal-SDK – tal
titleSuffix: Azure Cognitive Services
description: Talfunktionerna Software Development Kit (SDK) ger dina program inbyggd åtkomst till funktionerna i tal tjänsten, vilket gör det lättare att utveckla program. Den här artikeln innehåller ytterligare information om SDK för Windows, Linux och Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: f6446a2dbe3f111c9c617e78a0bdd98f6cea9153
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468774"
---
# <a name="about-the-speech-sdk"></a>Om tal-SDK

Talfunktionerna Software Development Kit (SDK) ger dina program åtkomst till funktionerna i tal tjänsterna, vilket gör det lättare att utveckla program med aktiverade tal. För närvarande ger SDK: er åtkomst till tal översättning av **tal till text**, **text till tal**, **tal översättning**, **avsikts igenkänning**och **bot-ramverkets direkta rad**igenkännings kanal. En allmän översikt över funktionerna och plattformarna som stöds finns på sidan med dokumentations [registrering](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

### <a name="windows"></a>Windows

För Windows har vi stöd för följande språk:

* C#(UWP och .NET) C++: du kan referera till och använda den senaste versionen av vårt tal SDK NuGet-paket. Paketet innehåller 32-bitars-och 64-bitars klient bibliotek och hanterade (.NET) bibliotek. SDK kan installeras i Visual Studio med hjälp av NuGet. Sök efter **Microsoft. CognitiveServices. Speech**.

* Java: du kan referera till och använda den senaste versionen av vårt tal SDK maven-paket, som endast stöder Windows x64. I maven-projektet lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare en lagrings plats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` som ett beroende.

### <a name="linux"></a>Linux

> [!NOTE]
> För närvarande stöder vi endast Ubuntu 16,04, Ubuntu 18,04 och Debian 9 på följande mål arkitekturer:
> - x86, x64 och ARM64 för C++ utveckling
> - x64 och ARM64 för Java
> - x64 för .NET Core och python

Kontrol lera att du har de nödvändiga biblioteken installerade genom att köra följande Shell-kommandon:

I Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

På Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: Du kan referera till och använda den senaste versionen av vårt tal SDK NuGet-paket. Om du vill referera till SDK lägger du till följande paket referens i projektet:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: du kan referera till och använda den senaste versionen av vårt tal SDK maven-paket. I maven-projektet lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare en lagrings plats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` som ett beroende.

* C++: Ladda ned SDK som ett [. tar-paket](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i valfri katalog. I följande tabell visas SDK-mappstrukturen:

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Huvudfiler för C ochC++|
  |`lib/x64`|Inbyggt x64-bibliotek för att länka till ditt program|
  |`lib/x86`|Inbyggt x86-bibliotek för att länka till ditt program|

  Om du vill skapa ett program kopierar eller flyttar du de binärfiler (och bibliotek) som krävs till utvecklings miljön. Ta med dem enligt vad som krävs i din build-process.

### <a name="android"></a>Android

Java SDK för Android paketeras som ett [AAR (Android-bibliotek)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i en maven-lagringsplats på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Om du vill använda paketet från Android Studio-projektet gör du följande ändringar:

* Lägg till följande i avsnittet `repository` i filen build. gradle för projekt nivå:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* I filen build. gradle för modulnivå lägger du till följande i avsnittet `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK är också en del av [tal enhets-SDK: n](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se identifiera tal iC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
