---
title: Strömma codec-komprimerat ljud med tjänsten Tal SDK - Tal
titleSuffix: Azure Cognitive Services
description: Lär dig hur du streamar komprimerat ljud till taltjänsten med Tal-SDK. Tillgänglig för C++, C#och Java för Linux, Java i Android och Objective-C i iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637277"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Använda codec-komprimerad ljudingång med Tal SDK

Speech-tjänsten SDK **Compressed Audio Input Stream** API är ett sätt att `PullStream` strömma `PushStream`komprimerat ljud till taltjänsten med hjälp av antingen a eller .

Strömmande komprimerat indataljud stöds för närvarande för C#, C++, Java på Windows (UWP-program stöds inte) och Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Det stöds också för Java i Android och Objective-C i iOS-plattformen.
* Tal SDK version 1.10.0 eller senare krävs för RHEL 8 och CentOS 8
* Tal SDK version 1.11.0 eller senare krävs för Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Krav

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempelkod med codec-komprimerad ljudingång

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att känna igen tal](quickstarts/speech-to-text-from-microphone.md)
