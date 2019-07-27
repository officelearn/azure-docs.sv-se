---
title: Om talet för tal-SDK – tal
titleSuffix: Azure Cognitive Services
description: Talfunktionerna Software Development Kit (SDK) ger dina program inbyggd åtkomst till funktionerna i tal tjänsten, vilket gör det lättare att utveckla program. Den här artikeln innehåller mer information om SDK för Windows, Linux och Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: c5e12812a4305493be2cdc234946796b21dd26d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558939"
---
# <a name="about-the-speech-sdk"></a>Om tal-SDK

Talfunktionerna Software Development Kit (SDK) ger dina program åtkomst till funktionerna i tal tjänsterna, vilket gör det lättare att utveckla program med aktiverade tal. För närvarande ger SDK: er åtkomst till tal översättning av **tal till text**, **text till tal**, **tal översättning**, **avsikts igenkänning**och **bot-ramverkets direkta rad**igenkännings kanal. En allmän översikt över funktionerna och plattformarna som stöds finns på sidan med dokumentations [registrering](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

### <a name="windows"></a>Windows

För Windows stöder vi följande språk:

* C#(UWP och .NET) C++: Du kan referera till och använda den senaste versionen av vårt tal SDK NuGet-paket. Paketet innehåller 32-bitars och 64-bitars klientbibliotek och hanterade (.NET)-bibliotek. SDK: N kan installeras i Visual Studio med hjälp av NuGet. Sök efter **Microsoft.CognitiveServices.Speech**.

* Java: Du kan referera till och använda den senaste versionen av vårt tal SDK maven-paket, som endast stöder Windows x64. I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` som ett beroende.

### <a name="linux"></a>Linux

> [!NOTE]
> För närvarande stöder vi endast Ubuntu 16,04, Ubuntu 18,04 och Debian 9 på en dator (x86 eller x64 för C++ utveckling och x64 för .net Core, Java och python).

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

* C#: Du kan referera till och använda den senaste versionen av vårt tal SDK NuGet-paket. Lägg till följande paketreferens i projektet om du vill referera till SDK:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.6.0" />
  ```

* Java: Du kan referera till och använda den senaste versionen av vårt tal SDK maven-paket. I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` som ett beroende.

* C++: Ladda ned SDK som ett [. tar-paket](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i valfri katalog. I följande tabell visas mappstrukturen SDK:

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Header-filer för C och C++|
  |`lib/x64`|Interna x64 bibliotek för att länka med ditt program|
  |`lib/x86`|Interna x86 bibliotek för att länka med ditt program|

  Om du vill skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö. Lägga till dem som krävs i Byggprocessen.

### <a name="android"></a>Android

Java SDK för Android paketeras som ett [AAR (Android-bibliotek)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i ett Maven-centrallager på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`.

Om du vill använda paketet från Android Studio-projekt gör du följande ändringar:

* I projektet på servernivå build.gradle-filen lägger du till följande för att den `repository` avsnittet:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* I modul på servernivå build.gradle-filen lägger du till följande för att den `dependencies` avsnittet:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.6.0'
  ```

Java SDK är också en del av den [tal Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
