---
title: Kom igång med Microsoft för Taligenkänning med hjälp av klientbibliotek för Bing-taligenkänning | Microsoft Docs
description: Använd Microsoft Speech Service klientbibliotek i Microsoft Cognitive Services för att utveckla program som omvandla talat ljud till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: f4b6a97260c6dc176600af8844001e4de819ff7c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42357518"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Kom igång med Bing Speech Service-klientbibliotek

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

> [!NOTE] 
I maj 2018 vi också släppt den nya [Speech Service](/speech-service/overview.md) i offentlig förhandsversion. Vi rekommenderar att du [prova det kostnadsfritt](/speech-service/get-started.md). 

## <a name="additional-resources"></a>Ytterligare resurser

- Den [exempel](../samples.md) sidan finns fullständiga exempel om du vill använda tal-klientbibliotek.
- Om du behöver ett klientbibliotek som ännu inte stöds kan skapa du ditt eget SDK. Implementera de [tal WebSocket-protokoll](../API-Reference-REST/websocketprotocol.md) på plattformen och Använd valfritt språk.

## <a name="license"></a>Licens

Alla kognitiva tjänster SDK: er och exempel har en licens för MIT-licensen. Mer information finns i [licens](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
