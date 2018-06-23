---
title: Kom igång med Microsoft Speech Recognition API med hjälp av klientbibliotek | Microsoft Docs
description: Använd Microsoft tal Service klientbibliotek i Microsoft kognitiva Services för att utveckla program som tal konverteras till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 5abe5bc48c2bd73d0facf33e41a8076df2972153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352179"
---
# <a name="get-started-with-speech-service-client-libraries"></a>Kom igång med tal Service-klientbibliotek

Förutom att göra direkt HTTP-förfrågningar via ett REST-API, ger tal tjänsten utvecklare med tal klientbibliotek på olika språk. Klientbibliotek tal:

- Stöd mer avancerade funktioner i taligenkänning, till exempel mellanresultat i realtid, länge ljudström (upp till 10 minuter) och kontinuerlig igenkänning.
- Tillhandahåller en enkel och idiomatiska API på önskat språk.
- Dölj information på låg nivå kommunikation.

Följande tal-klientbibliotek för närvarande tillgängliga:

- [C# skrivbord bibliotek](GetStartedCSharpDesktop.md)
- [C#-tjänstbibliotek](GetStartedCSharpServiceLibrary.md)
- [JavaScript-bibliotek](GetStartedJSWebsockets.md)
- [Java-bibliotek för Android](GetStartedJavaAndroid.md)
- [Objective-C-biblioteket för iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Ytterligare resurser

- Den [exempel](../samples.md) innehåller komplett exempel om du vill använda klientbibliotek för tal.
- Du kan skapa egna SDK om du behöver ett klientbiblioteket som ännu inte stöds. Implementera den [tal WebSocket-protokollet](../API-Reference-REST/websocketprotocol.md) på plattformen och Använd önskat språk.

## <a name="license"></a>Licens

Alla kognitiva Services SDK: er och prover licensieras med MIT-licensen. Mer information finns i [licens](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
