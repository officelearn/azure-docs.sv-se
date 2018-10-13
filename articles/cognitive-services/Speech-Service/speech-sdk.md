---
title: Om Cognitive Services tal SDK
description: 'En översikt över SDK: er tillgängliga för Speech-tjänsten.'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: erhopf
ms.openlocfilehash: a8e23e971cc0186317a291ecbdd010fc2a3ae1e8
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167604"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Om Cognitive Services tal SDK

Den Cognitive Services tal Software Development Kit (SDK) får dina program inbyggda funktioner i tjänsten tal, vilket gör det enklare att utveckla programvara. För närvarande SDK ger åtkomst till **tal till Text**, **Talöversättning**, och **avsikt erkännande**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

### <a name="windows"></a>Windows

För Windows stöder vi följande språk:

* C# (UWP- och .NET), C++: du kan referera till och använda den senaste versionen av vår tal SDK NuGet-paketet. Paketet innehåller 32-bitars och 64-bitars klientbibliotek och hanterade (.NET)-bibliotek. SDK: N kan installeras i Visual Studio med hjälp av NuGet. Sök efter **Microsoft.CognitiveServices.Speech**.

* Java: Du kan referera till och använda den senaste versionen av vår tal SDK Maven-paketet som stöder endast Windows x64. I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` som ett beroende. 

### <a name="linux"></a>Linux

> [!NOTE]
> Vi stöder för närvarande endast Ubuntu 16.04 på en dator (x86 eller x64 för utveckling med C++ och x64 för .NET Core och Java).

Kontrollera att du har de nödvändiga kompilatorn och bibliotek som är installerade genom att köra följande kommandon för shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Du kan referera till och använder den senaste versionen av vår tal SDK NuGet-paketet. Lägg till följande paketreferens i projektet om du vill referera till SDK:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.0.0" />
  ```

* Java: Du kan referera till och använda den senaste versionen av vår tal SDK Maven-paketet. I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` som ett beroende. 

* C++: Ladda ned SDK som en [.tar-paketet](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i en katalog med ditt val. I följande tabell visas mappstrukturen SDK:

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Header-filer för C och C++|
  |`lib/x64`|Interna x64 bibliotek för att länka med ditt program|
  |`lib/x86`|Interna x86 bibliotek för att länka med ditt program|

  Om du vill skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö. Lägga till dem som krävs i Byggprocessen.

### <a name="android"></a>Android

Java SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs för att använda den. Den finns i ett Maven-centrallager på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0`.

Om du vill använda paketet från Android Studio-projekt gör du följande ändringar:

* I projektet på servernivå build.gradle-filen lägger du till följande för att den `repository` avsnittet:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* I modul på servernivå build.gradle-filen lägger du till följande för att den `dependencies` avsnittet:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.0.0'
  ```

Java SDK är också en del av den [tal Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
