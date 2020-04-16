---
title: Grunderna i talsyntes - Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Tal-SDK för att konvertera text till tal. I den här artikeln får du lära dig mer om objektkonstruktion, ljudutdataformat som stöds och anpassade konfigurationsalternativ för talsyntes.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-sixteen
ms.openlocfilehash: 74f762dd0004bf72b703d0f611a16762b1ff028d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399575"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Lär dig grunderna i talsyntes

I den här artikeln får du lära dig vanliga designmönster för text-till-tal-syntes med tal-SDK. Du börjar med att göra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad programutveckling, inklusive:

* Få svar som strömmar i minnet
* Anpassa utdataexempelhastighet och bithastighet
* Skicka syntesbegäranden med SSML (talsyntesmarkeringsspråk)
* Använda neurala röster

> [!TIP]
> Om du inte har haft en chans att slutföra en av våra snabbstarter, uppmuntrar vi dig att sparka däcken och prova taligenkänning ut själv.
> * [Identifiera tal från en mikrofon](quickstarts/text-to-speech.md)

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

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Custom Voice](how-to-custom-voice.md)
* [Förbättra syntesen med SSML](speech-synthesis-markup.md)