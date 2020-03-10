---
title: Stream codec Compressed Audio med tal SDK-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du direktuppspelar komprimerat ljud till tal tjänsten med talet SDK. Tillgängligt för C++, C#, och Java för Linux, java i Android och mål-C i iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943801"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Använd codec-komprimerad ljud inspelning med talet SDK

Med API: t för tal service SDK **komprimerad ljud inspelnings ström** får du ett sätt att strömma komprimerat ljud till tal tjänsten med antingen en `PullStream` eller `PushStream`.

> [!IMPORTANT]
> Strömmande komprimerade indata-ljud C#stöds C++för närvarande för Java på Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Det finns också stöd för java i Android och mål-C på iOS-plattformen.
> Tal SDK-version 1.7.0 eller senare krävs (version 1.10.0 eller senare för RHEL 8, CentOS 8).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempel kod med codec komprimerad ljud inspelning

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att känna igen tal](quickstarts/speech-to-text-from-microphone.md)
