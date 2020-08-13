---
title: Snabb start för tal syntes – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal-SDK för att konvertera text till tal. I den här snabb starten får du lära dig om objekt konstruktions-och design mönster, stöd för ljud uppspelning, tal-CLI och anpassade konfigurations alternativ för tal syntes.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: e66da95bea0b03ddad1096b2b19751f73aaacbe0
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173149"
---
# <a name="get-started-with-speech-synthesis"></a>Kom igång med tal syntes

I den här snabb starten lär du dig vanliga design mönster för att skapa text till tal-Sammanfattning med hjälp av tal-SDK. Du börjar med att utföra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad program utveckling, inklusive:

* Få svar som minnes strömmar
* Anpassa samplings frekvens och bit hastighet för utdata
* Skicka syntes begär Anden med SSML (tal syntes Markup Language)
* Använda neurala-röster

> [!TIP]
> Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) på GitHub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Custom Voice](how-to-custom-voice.md)
* [Förbättra syntesen med SSML](speech-synthesis-markup.md)
* Lär dig hur du använder [långa ljud-API: er](long-audio-api.md) för stora text exempel som böcker och nyhets artiklar
* Se [snabb starts exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) på GitHub
