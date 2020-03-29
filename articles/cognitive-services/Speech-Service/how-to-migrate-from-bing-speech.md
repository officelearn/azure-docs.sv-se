---
title: Migrera från Bing-tal till taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du migrerar från en befintlig Bing Speech-prenumeration till taltjänsten från Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: nitinme
ms.openlocfilehash: b95e16f2d8257bfffcaf2524fe7f8ce6be565689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366598"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrera från Bing-tal till taltjänsten

Använd den här artikeln om du vill migrera dina program från API:et för Bing-tal till taltjänsten.

I den här artikeln beskrivs skillnaderna mellan API:erna för Bing-tal och taltjänsten och strategier för att migrera dina program föreslås. Prenumerationsnyckeln för Bing Speech API fungerar inte med taltjänsten. Behöver du en ny prenumeration på Taltjänsten.

En enda prenumerationsnyckel för Taltjänst ger åtkomst till följande funktioner. Varje funktion mäts separat, så att du bara debiteras för de funktioner du använder.

* [Tal till text](speech-to-text.md)
* [Anpassat tal till text](https://cris.ai)
* [Text-till-tal](text-to-speech.md)
* [Anpassad text till talade röster](how-to-customize-voice-font.md)
* [Talöversättning](speech-translation.md) (omfattar inte [textöversättning](../translator/translator-info-overview.md))

[Speech SDK](speech-sdk.md) är en funktionell ersättning för Bing Speech-klientbiblioteken, men använder ett annat API.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

Taltjänsten liknar till stor del Bing-tal, med följande skillnader.

| Funktion | Bing-taligenkänning | Tjänst för taligenkänning | Information |
|--|--|--|--|
| C#-SDK | :heavy_check_mark: | :heavy_check_mark: | Taltjänsten stöder Windows 10, Universal Windows Platform (UWP) och .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Taltjänsten stöder Windows och Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Taltjänsten stöder Android- och Talenheter. |
| Kontinuerlig taligenkänning | 10 minuter | Obegränsad (med SDK) | WebSockets-protokoll för både Bing-tal och taltjänst stöder upp till 10 minuter per samtal. Tal-SDK återansluter dock automatiskt på timeout eller frånkoppling. |
| Partiella eller interimistiska resultat | :heavy_check_mark: | :heavy_check_mark: | Med WebSockets-protokollet eller SDK. |
| Anpassade talmodeller | :heavy_check_mark: | :heavy_check_mark: | Bing Tal kräver en separat anpassad talprenumeration. |
| Anpassade röstteckensnitt | :heavy_check_mark: | :heavy_check_mark: | Bing Speech kräver en separat Custom Voice-prenumeration. |
| 24 kHz röster | :heavy_minus_sign: | :heavy_check_mark: |
| Erkännande av tatalvsikt | Kräver separat LUIS API-anrop | Integrerad (med SDK) | Du kan använda en LUIS-tangent med taltjänsten. |
| Enkel avsiktsigenkänning | :heavy_minus_sign: | :heavy_check_mark: |
| Batch transkription av långa ljudfiler | :heavy_minus_sign: | :heavy_check_mark: |
| Igenkänningsläge | Manuell via slutpunkt URI | Automatisk | Igenkänningsläget är inte tillgängligt i taltjänsten. |
| Slutpunktslokalitet | Global | Regional | Regionala slutpunkter förbättrar svarstiden. |
| REST API:er | :heavy_check_mark: | :heavy_check_mark: | REST-API:erna för taltjänsten är kompatibla med Bing-tal (annan slutpunkt). REST API:er stöder text-till-tal- och begränsad tal-till-text-funktionalitet. |
| WebSockets-protokoll | :heavy_check_mark: | :heavy_check_mark: | Speech-tjänsten WebSockets API är kompatibelt med Bing-tal (annan slutpunkt). Migrera till Tal-SDK om möjligt för att förenkla koden. |
| API-anrop från service till tjänst | :heavy_check_mark: | :heavy_minus_sign: | Tillhandahålls i Bing Tal via C # Service Library. |
| SDK med öppen källkod | :heavy_check_mark: | :heavy_minus_sign: |

Taltjänsten använder en tidsbaserad prismodell (i stället för en transaktionsbaserad modell). Mer information finns i Priser för [taltjänst.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har program under utveckling eller produktion som använder ett Bing Speech API bör du uppdatera dem så att de använder taltjänsten så snart som möjligt. Se [taltjänstdokumentationen](index.yml) för tillgängliga SDK:er, kodexempel och självstudier.

[REST-API:erna](rest-apis.md) för taltjänsten är kompatibla med API:erna för Bing-tal. Om du för närvarande använder API:erna för Bing Speech REST behöver du bara ändra REST-slutpunkten och växla till en prenumerationsnyckel för taltjänsten.

WebSockets-protokollen för taltjänsten är också kompatibla med dem som används av Bing Speech. Vi rekommenderar att du använder Tal-SDK i stället för WebSockets för ny utveckling. Det är en bra idé att migrera befintlig kod till SDK också. Men som med REST API: er, befintlig kod som använder Bing Tal via WebSockets kräver bara en förändring i slutpunkten och en uppdaterad nyckel.

Om du använder ett Bing Speech-klientbibliotek för ett visst programmeringsspråk kräver migrering till [Tal-SDK](speech-sdk.md) ändringar i programmet, eftersom API:et är annorlunda. Tal-SDK kan göra din kod enklare, samtidigt som du får tillgång till nya funktioner. Tal-SDK finns på en mängd olika programmeringsspråk. API:er på alla plattformar är likartade, vilket underlättar utveckling av flera plattformar.

Taltjänsten erbjuder ingen global slutpunkt. Ta reda på om ditt program fungerar effektivt när det använder en enda regional slutpunkt för all trafik. Om inte, använd geolokalisering för att bestämma den mest effektiva slutpunkten. Du behöver en separat taltjänstprenumeration i varje region du använder.

Om ditt program använder långlivade anslutningar och inte kan använda en tillgänglig SDK kan du använda en WebSockets-anslutning. Hantera 10-minuters tidsgränsen genom att återansluta vid lämpliga tidpunkter.

Så här kommer du igång med Tal-SDK:

1. Ladda ner [tal-SDK](speech-sdk.md).
1. Arbeta igenom [snabbstartsguiderna](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) och [självstudiekurserna](how-to-recognize-intents-from-speech-csharp.md)för taltjänsten . Titta också på [kodexemplen](samples.md) för att få erfarenhet av de nya API:erna.
1. Uppdatera programmet så att det använder taltjänsten.

## <a name="support"></a>Support

Bing Speech-kunder bör kontakta kundtjänst genom att öppna en [supportbiljett](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Du kan också kontakta oss om din support behöver en [teknisk supportplan.](https://azure.microsoft.com/support/plans/)

För stöd för taltjänster, SDK och API besöker du [supportsidan](support.md)för taltjänsten .

## <a name="next-steps"></a>Nästa steg

* [Prova Taltjänst gratis](get-started.md)
* [Snabbstart: Känna igen tal i en UWP-app med tal-SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Se även
* [Viktig information om taltjänsten](releasenotes.md)
* [Vad är taltjänsten](overview.md)
* [Taltjänst och Tal-SDK-dokumentation](speech-sdk.md#get-the-sdk)
