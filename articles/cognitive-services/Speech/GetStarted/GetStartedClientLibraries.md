---
title: Kom igång med Bing-Taligenkänning för taligenkänning med hjälp av klientbibliotek | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd Bing-taligenkänning klientbibliotek i Microsoft Cognitive Services för att utveckla program som omvandla talat ljud till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89eb18a2b4af76f6489442dc66ab12d0840e92c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515236"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Kom igång med Bing Speech Service-klientbibliotek

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Förutom att direkt HTTP-förfrågningar via ett REST-API, ger Bing Speech Service utvecklarna tal klientbibliotek på olika språk. Klientbibliotek för tal:

- Stöd mer avancerade funktioner för taligenkänning, till exempel mellanliggande resultat i realtid, länge ljudström (upp till 10 minuter) och kontinuerlig erkännande.
- Tillhandahåller en enkel och idiomatiskt API i språket du föredrar.
- Dölj information på låg nivå kommunikation.

För närvarande finns följande klientbibliotek för Bing-taligenkänning:

- [C# skrivbord bibliotek](GetStartedCSharpDesktop.md)
- [C#-tjänstbibliotek](GetStartedCSharpServiceLibrary.md)
- [JavaScript-bibliotek](GetStartedJSWebsockets.md)
- [Java-bibliotek för Android](GetStartedJavaAndroid.md)
- [Objective-C-biblioteket för iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Ytterligare resurser

- Den [exempel](../samples.md) sidan finns fullständiga exempel om du vill använda tal-klientbibliotek.
- Om du behöver ett klientbibliotek som ännu inte stöds kan skapa du ditt eget SDK. Implementera de [tal WebSocket-protokoll](../API-Reference-REST/websocketprotocol.md) på plattformen och Använd valfritt språk.

## <a name="license"></a>Licens

Alla kognitiva tjänster SDK: er och exempel har en licens för MIT-licensen. Mer information finns i [licens](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
