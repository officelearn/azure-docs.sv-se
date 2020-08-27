---
title: Stream codec Compressed Audio med tal SDK-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du direktuppspelar komprimerat ljud till tal tjänsten med talet SDK. Tillgängligt för C++, C# och Java för Linux, java i Android och mål-C i iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 4585538e552e73f8f7a4b7b105153a9d26eeb4c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934112"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Använd codec-komprimerad ljud inspelning med talet SDK

Med API: t för tal service SDK **komprimerad ljud inspelnings ström** får du ett sätt att strömma komprimerat ljud till tal tjänsten med hjälp av antingen en `PullStream` eller `PushStream` .

Strömmande komprimerade indata-ljud stöds för närvarande för C#, C++, Java och python i Windows (UWP-program stöds inte) och Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). Det finns också stöd för java i Android.
* Tal SDK-version 1.10.0 eller senare krävs för RHEL 8 och CentOS 8
* Tal SDK-version 1.11.0 eller senare krävs för för Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempel kod med codec komprimerad ljud inspelning

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att känna igen tal](quickstarts/speech-to-text-from-microphone.md)
