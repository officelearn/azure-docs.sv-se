---
title: Migrera från Taligenkänning i Bing till tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du migrerar från en befintlig Taligenkänning i Bing-prenumeration till tal tjänsten från Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 00a20ac2d7f05c42be9e955eeb8f47c302db7885
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464583"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrera från Taligenkänning i Bing till tal tjänsten

Använd den här artikeln för att migrera dina program från API för Bing-taligenkänning till tal tjänsten.

Den här artikeln beskriver skillnaderna mellan Taligenkänning i Bing-API: er och tal tjänsterna och ger förslag på strategier för att migrera dina program. Din API för Bing-taligenkänning prenumerations nyckel fungerar inte med tal tjänsten. du behöver en ny prenumeration på tal tjänster.

En prenumerations nyckel för en enda tal tjänst ger åtkomst till följande funktioner. Varje funktion mäts separat, så att du bara debiteras för de funktioner du använder.

* [Tal till text](speech-to-text.md)
* [Anpassat tal till text](https://cris.ai)
* [Text till tal](text-to-speech.md)
* [Anpassad text till talade röster](how-to-customize-voice-font.md)
* [Talöversättning](speech-translation.md) (omfattar inte [textöversättning](../translator/translator-info-overview.md))

[Talet SDK](speech-sdk.md) är en funktionell ersättning för taligenkänning i Bing klient bibliotek, men använder ett annat API.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

Tal tjänsterna liknar Taligenkänning i Bing, med följande skillnader.

Funktion | Bing-taligenkänning | Speech Services | Information
-|-|-|-
C++GRÄNSSNITTET | :heavy_minus_sign: | :heavy_check_mark: | Tal tjänster stöder Windows och Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Tal tjänster stöder Android-och tal enheter.
C#-SDK | :heavy_check_mark: | :heavy_check_mark: | Tal tjänster stöder Windows 10, Universell Windows-plattform (UWP) och .NET standard 2,0.
Kontinuerlig tal igenkänning | 10 minuter | Obegränsad (med SDK) | Både Taligenkänning i Bing-och Speech Services-WebSockets-protokoll stöder upp till 10 minuter per anrop. Tal-SDK återansluter dock automatiskt vid timeout eller koppla från.
Partiella eller interimistiska resultat | :heavy_check_mark: | :heavy_check_mark: | Med WebSockets-protokoll eller SDK.
Anpassade tal modeller | :heavy_check_mark: | :heavy_check_mark: | Taligenkänning i Bing kräver en separat Custom Speech prenumeration.
Anpassade röst teckensnitt | :heavy_check_mark: | :heavy_check_mark: | Taligenkänning i Bing kräver en separat anpassad röst prenumeration.
24-KHz-röster | :heavy_minus_sign: | :heavy_check_mark:
Igenkänning av talavsikt | Kräver separat LUIS-API-anrop | Integrerad (med SDK) |  Du kan använda en LUIS-nyckel med tal tjänsten.
Enkel avsikts igenkänning | :heavy_minus_sign: | :heavy_check_mark:
Batch-avskrift av långa ljudfiler | :heavy_minus_sign: | :heavy_check_mark:
Igenkänningsläge | Manuell via slut punkts-URI | Automatisk | Igenkännings läget är inte tillgängligt i Speech service.
Slut punkts plats | Global | Regioner | Regionala slut punkter förbättrar svars tiden.
REST API:er | :heavy_check_mark: | :heavy_check_mark: | API: erna för tal tjänster REST är kompatibla med Taligenkänning i Bing (annan slut punkt). REST-API: er stöder text-till-tal och begränsade tal-till-text-funktioner.
WebSockets-protokoll | :heavy_check_mark: | :heavy_check_mark: | Anrops-API: t för Speech Services är kompatibelt med Taligenkänning i Bing (annan slut punkt). Migrera till tal-SDK om möjligt, för att förenkla koden.
API-anrop från tjänst till tjänst | :heavy_check_mark: | :heavy_minus_sign: | Tillhandahålls i Taligenkänning i Bing via C# tjänst biblioteket.
SDK för öppen källkod | :heavy_check_mark: | :heavy_minus_sign: |

Tal tjänsterna använder en tidsbaserad pris modell (i stället för en transaktions baserad modell). Se [pris information för tal tjänster](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har program i utveckling eller produktion som använder en API för Bing-taligenkänning bör du uppdatera dem så att de använder tal tjänsterna så snart som möjligt. Se [dokumentationen för tal tjänster](index.md) för tillgängliga SDK: er, kod exempel och självstudier.

[REST-API: erna](rest-apis.md) för tal tjänster är kompatibla med taligenkänning i Bing-API: er. Om du för närvarande använder Taligenkänning i Bing REST-API: er behöver du bara ändra REST-slutpunkten och byta till en prenumerations nyckel för tal tjänster.

Speech Services-WebSockets-protokollen är också kompatibla med de som används av Taligenkänning i Bing. Vi rekommenderar att du använder tal-SDK i stället för WebSockets för ny utveckling. Det är en bra idé att även migrera befintlig kod till SDK: n. Men precis som med REST-API: erna kräver befintlig kod som använder Taligenkänning i Bing via WebSockets bara en ändring i slut punkten och en uppdaterad nyckel.

Om du använder ett Taligenkänning i Bing klient bibliotek för ett särskilt programmeringsspråk, kräver migrering till tal- [SDK](speech-sdk.md) ändringar i programmet, eftersom API: et skiljer sig. Talet SDK kan göra din kod enklare, samtidigt som du ger dig till gång till nya funktioner.

För närvarande stöder C# tal-SDK ([information här](https://aka.ms/csspeech)), Java (Android och anpassade enheter), mål C (iOS), C++ (Windows och Linux) och Java Script. API: er på alla plattformar liknar varandra, med flera plattformar.

Tal tjänsterna erbjuder inte en global slut punkt. Ta reda på om programmet fungerar effektivt när det använder en enda regional slut punkt för all trafik. Om inte, Använd geolokalisering för att fastställa den effektiva slut punkten. Du behöver en separat Speech Services-prenumeration i varje region som du använder.

Om programmet använder anslutningar med lång livs längd och det inte går att använda en tillgänglig SDK, kan du använda en WebSockets-anslutning. Hantera tids gränsen på 10 minuter genom att ansluta vid rätt tidpunkter.

Kom igång med talet SDK:

1. Hämta [tal-SDK: n](speech-sdk.md).
1. Arbeta via [snabb starts guiderna](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) för tal tjänster och [självstudier](how-to-recognize-intents-from-speech-csharp.md). Titta även på [kod exemplen](samples.md) för att få erfarenhet av de nya API: erna.
1. Uppdatera programmet så att det använder tal tjänsterna.

## <a name="support"></a>Support

Taligenkänning i Bing kunder kontaktar kund support genom att öppna ett [support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Du kan också kontakta oss om support behovet kräver en [teknisk Support plan](https://azure.microsoft.com/support/plans/).

För tal service, SDK och API-stöd går du till [support Sidan](support.md)för Speech Services.

## <a name="next-steps"></a>Nästa steg

* [Prova tal tjänster kostnads fritt](get-started.md)
* [Snabb start: identifiera tal i en UWP-app med hjälp av talet SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Se även
* [Viktig information om tal tjänster](releasenotes.md)
* [Vad är tal tjänsten](overview.md)
* [Dokumentation om Speech Services och Speech SDK](speech-sdk.md#get-the-sdk)
