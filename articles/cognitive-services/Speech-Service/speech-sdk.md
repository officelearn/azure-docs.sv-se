---
title: Om kognitiva Services tal SDK | Microsoft Docs
description: 'En översikt över SDK: er för tjänsten tal.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355647"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Om kognitiva Services tal SDK

I kognitiva Services tal Software Development Kit (SDK) ger dina program inbyggd åtkomst till funktionerna i tjänsten tal, vilket gör det enklare att utveckla programvara. För närvarande SDK ger åtkomst till **tal till Text**, **tal översättning**, och **avsikt Recognition**.

Tabellen innehåller programmeringsspråk som stöds för närvarande och operativsystem.

|Operativsystem som stöds|Programmeringsspråk|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Enheter|Java\*|

\* *Java SDK är en del av den [tal enheter SDK](speech-devices-sdk.md).*

## <a name="get-the-windows-sdk"></a>Hämta Windows SDK

Windows-versionen av tal SDK innehåller 32-bitars och 64-bitars C/C++-klientbibliotek samt hanterad (.NET)-bibliotek för användning med C#. SDK kan installeras i Visual Studio med hjälp av NuGet; Sök bara efter `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Hämta Linux-SDK

Kontrollera att du har nödvändiga kompilator och bibliotek genom att köra följande shell-kommandon:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Dessa instruktioner förutsätter att du kör Ubuntu 16.04 på en dator (x86 eller x64). På en annan Ubuntu-version och en annan distributionsplats för Linux och anpassa stegen för din miljö.

Sedan [ladda ned SDK](https://aka.ms/csspeech/linuxbinary) och packa upp filer i i en katalog som du väljer. Den här tabellen visar mappstrukturen SDK.

|Sökväg|Beskrivning|
|-|-|
|`license.md`|Licens|
|`third-party-notices.md`|Meddelanden från tredje part|
|`include`|Rubrikfiler för C och C++|
|`lib/x64`|Intern x64 bibliotek för att länka med ditt program|
|`lib/x86`|Intern x86 bibliotek för att länka med ditt program|

För att skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö och lägga till dem som krävs i build-processen.

## <a name="get-the-java-sdk"></a>Hämta Java SDK

Java SDK är en del av den [tal enheter SDK](speech-devices-sdk.md).

## <a name="next-steps"></a>Nästa steg

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du identifierar tal i C#](quickstart-csharp-windows.md)
