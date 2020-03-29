---
title: Om tal-SDK - Taltjänst
titleSuffix: Azure Cognitive Services
description: Speech Software Development Kit (SDK) ger dina program inbyggd åtkomst till taltjänstens funktioner, vilket gör det enklare att utveckla programvara. Den här artikeln innehåller ytterligare information om SDK för Windows, Linux och Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331101"
---
# <a name="about-the-speech-sdk"></a>Om Speech SDK

Speech Software Development Kit (SDK) ger dina program tillgång till taltjänstens funktioner, vilket gör det enklare att utveckla talaktiverad programvara. För närvarande ger SDK:erna tillgång till **tal-till-text,** **text-till-tal,** **talöversättning,** **avsiktsigenkänning**och **Bot Frameworks direktlinjetalskanal**.

Du kan enkelt spela in ljud från en mikrofon, läsa från en ström eller komma åt ljudfiler från lagring med Tal SDK. Tal SDK stöder WAV/PCM 16-bitars, 16 kHz/8 kHz, enkanaligt ljud för taligenkänning. Ytterligare ljudformat stöds med hjälp av [tids-till-text REST-slutpunkten](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) eller [batchutskriftstjänsten](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

En allmän översikt över funktionerna och plattformar som stöds finns på dokumentationens [startsida](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Speech SDK stöder Windows 10 eller senare versioner. Tidigare Windows-versioner **stöds inte**.

För Windows stöder vi följande språk:

* C# (UWP och .NET), C++: Du kan referera till och använda den senaste versionen av vårt Speech SDK NuGet-paket. Paketet innehåller 32- och 64-bitars klientbibliotek och hanterade (.NET) bibliotek. SDK kan installeras i Visual Studio med NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Du kan referera till och använda den senaste versionen av vårt Speech SDK Maven-paket, som endast stöder Windows x64. Lägg till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare en databas och `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` referens som ett beroende i Maven-projektet.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> För närvarande stöder vi endast Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 och CentOS 8 på följande målarkitekturer:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för C++-utveckling
> - x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för Java
> - x64 för .NET Core och Python

Kontrollera att du har de bibliotek som krävs installerade genom att köra följande skalkommandon:

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
> På RHEL/CentOS 8 följer du instruktionerna för [hur du konfigurerar OpenSSL för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: Du kan referera till och använda den senaste versionen av vårt Speech SDK NuGet-paket. Om du vill referera till SDK lägger du till följande paketreferens till projektet:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Du kan referera till och använda den senaste versionen av vårt Speech SDK Maven-paket. Lägg till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare en databas och `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` referens som ett beroende i Maven-projektet.

* C++: Ladda ner SDK som ett [.tar-paket](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i en valfri katalog. I följande tabell visas SDK-mappstrukturen:

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Header-filer för C och C++|
  |`lib/x64`|Inbyggt x64-bibliotek för länkning med ditt program|
  |`lib/x86`|Inbyggt x86-bibliotek för länkning med ditt program|

  Om du vill skapa ett program kopierar eller flyttar du de binärfiler som krävs (och biblioteken) till utvecklingsmiljön. Inkludera dem som krävs i din byggprocess.

# <a name="android"></a>[Android](#tab/android)

Java SDK för Android är paketerad som ett [AAR (Android-bibliotek),](https://developer.android.com/studio/projects/android-library)som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i ett Maven-arkiv `https://csspeechstorage.blob.core.windows.net/maven/` `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`på som paket .

Så här använder du paketet från ditt Android Studio-projekt:

* Lägg till följande i avsnittet `repository` build.gradle på projektnivåsnivå:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Lägg till följande i avsnittet `dependencies` build.gradle på modulnivå:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK är också en del av [Speech Devices SDK](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du känner igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
