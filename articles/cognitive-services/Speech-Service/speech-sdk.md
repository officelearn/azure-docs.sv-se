---
title: Om talet för tal-SDK – tal
titleSuffix: Azure Cognitive Services
description: Talfunktionerna Software Development Kit (SDK) ger dina program inbyggd åtkomst till funktionerna i tal tjänsten, vilket gör det lättare att utveckla program. Den här artikeln innehåller mer information om SDK för Windows, Linux och Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331101"
---
# <a name="about-the-speech-sdk"></a>Om Speech SDK

Tal Software Development Kit (SDK) ger dina program åtkomst till funktioner i tjänsten tal, vilket gör det enklare att utveckla talbaserade programvara. För närvarande ger SDK: er åtkomst till tal översättning av **tal till text**, **text till tal**, **tal översättning**, **avsikts igenkänning**och **bot-ramverkets direkta rad**igenkännings kanal.

Du kan enkelt spela in ljud från en mikrofon, läsa från en ström eller komma åt ljudfiler från lagringen med talet SDK. Tal-SDK: n stöder WAV/PCM 16-bit, 16 kHz/8 kHz, ett ljud med en kanal för tal igenkänning. Ytterligare ljud format stöds med hjälp av [slut punkten tal-till-text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) eller [batch-avskrifts tjänsten](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

En allmän översikt över funktionerna och plattformarna som stöds finns på sidan med dokumentations [registrering](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Tal-SDK: n stöder Windows 10 eller senare versioner. Tidigare Windows-versioner **stöds inte**.

För Windows stöder vi följande språk:

* C# (UWP- och .NET), C++: du kan referera till och använda den senaste versionen av vår tal SDK NuGet-paketet. Paketet innehåller 32-bitars och 64-bitars klientbibliotek och hanterade (.NET)-bibliotek. SDK kan installeras i Visual Studio med hjälp av NuGet, [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Du kan referera till och använda den senaste versionen av vår tal SDK Maven-paketet som stöder endast Windows x64. I maven-projektet lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare en lagrings plats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` som ett beroende.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> För närvarande stöder vi bara Ubuntu 16,04, Ubuntu 18,04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 och CentOS 8 på följande mål arkitekturer:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för C++ utveckling
> - x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för Java
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

På RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: Du kan referera till och använder den senaste versionen av vår tal SDK NuGet-paketet. Lägg till följande paketreferens i projektet om du vill referera till SDK:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Du kan referera till och använda den senaste versionen av vår tal SDK Maven-paketet. I maven-projektet lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare en lagrings plats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` som ett beroende.

* C++: Ladda ned SDK som ett [. tar-paket](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i valfri katalog. I följande tabell visas mappstrukturen SDK:

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Header-filer för C och C++|
  |`lib/x64`|Interna x64 bibliotek för att länka med ditt program|
  |`lib/x86`|Interna x86 bibliotek för att länka med ditt program|

  Om du vill skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö. Lägga till dem som krävs i Byggprocessen.

# <a name="android"></a>[Android](#tab/android)

Java SDK för Android paketeras som ett [AAR (Android-bibliotek)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i en maven-lagringsplats på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Om du vill använda paketet från Android Studio-projekt gör du följande ändringar:

* Lägg till följande i avsnittet `repository` i filen build. gradle för projekt nivå:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* I filen build. gradle för modulnivå lägger du till följande i avsnittet `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK är också en del av [tal enhets-SDK: n](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se identifiera tal iC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
