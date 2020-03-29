---
title: Text-till-tal - Taltjänst
titleSuffix: Azure Cognitive Services
description: Med text-till-tal-funktionen i taltjänsten kan dina program, verktyg eller enheter konvertera text till naturligt människoliknande syntetiserat tal. Välj förinställda röster eller skapa din egen anpassade röst.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7c7574ee4e9f75b9b650ff63a10666c5e379fca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131565"
---
# <a name="what-is-text-to-speech"></a>Vad är text-till-tal?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Text-till-tal från taltjänsten gör det möjligt för dina program, verktyg eller enheter att konvertera text till människoliknande syntetiserat tal. Välj mellan standard- och neurala röster, eller skapa en anpassad röst som är unik för din produkt eller ditt varumärke. 75+ standardröster finns på mer än 45 språk och språk, och 5 neurala röster finns på ett urval av språk och språk. En fullständig lista över röster, språk och språk som stöds finns i [språk som stöds](language-support.md#text-to-speech).

> [!NOTE]
> Bing Speech avvecklades den 15 oktober 2019. Om dina program, verktyg eller produkter använder API:erna för Bing-tal eller anpassat tal har vi skapat guider som hjälper dig att migrera till taltjänsten.
> - [Migrera från Bing-tal till taltjänsten](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Kärnfunktioner

* Talsyntes - Använd [Tal-SDK-](quickstarts/text-to-speech-audio-file.md) eller [REST API:et](rest-text-to-speech.md) för att konvertera text-till-tal med hjälp av standardröster, neurala eller anpassade röster.

* Asynkron syntes av långt ljud - Använd [Long Audio API](long-audio-api.md) för att asynkront syntetisera text-till-tal-filer längre än 10 minuter (till exempel ljudböcker eller föreläsningar). Till skillnad från syntes som utförs med hjälp av REST API för tal-SDK eller tal till text returneras inte svaren i realtid. Förhoppningen är att förfrågningar skickas asynkront, svaren är tillfrågade och att det syntetiserade ljudet hämtas när det görs tillgängligt från tjänsten. Endast anpassade neurala röster stöds.

* Standardröster - Skapad med hjälp av statistiska parametrisk syntes och/eller konateringssyntestekniker. Dessa röster är mycket begripliga och låter naturliga. Du kan enkelt aktivera dina program för att tala på mer än 45 språk, med ett brett utbud av röstalternativ. Dessa röster ger hög uttalsnoggrannhet, inklusive stöd för förkortningar, akronymer, datum/ tidstolkningar, polyfoner med mera. En fullständig lista över standardröster finns i [språk som stöds](language-support.md#text-to-speech).

* Neurala röster - Djupa neurala nätverk används för att övervinna gränserna för traditionell talsyntes när det gäller stress och intonation i talat språk. Prosodi förutsägelse och röstsyntes utförs samtidigt, vilket resulterar i mer flytande och naturligt klingande utgångar. Neurala röster kan användas för att göra interaktioner med chatbots och röstassistenter mer naturliga och engagerande, konvertera digitala texter som e-böcker till ljudböcker, och förbättra i bilen navigationssystem. Med den människoliknande naturliga prosodien och tydliga ordformuleringen minskar neurala röster märkbart lyssningströttheten när du interagerar med AI-system. En fullständig lista över neurala röster finns [i språk som stöds](language-support.md#text-to-speech).

* SSML (Speech Synthesis Markup Language) – Ett XML-baserat markeringsspråk som används för att anpassa tal-till-text-utdata. Med SSML kan du justera tonhöjd, lägga till pauser, förbättra uttalet, öka eller sänka talhastigheten, öka eller minska volymen och tillskriva flera röster till ett enda dokument. Se [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Komma igång

Text-till-tal-tjänsten är tillgänglig via [Tal-SDK](speech-sdk.md). Det finns flera vanliga scenarier tillgängliga som snabbstarter, på olika språk och plattformar:

* [Syntetisera tal till en ljudfil](quickstarts/text-to-speech-audio-file.md)
* [Syntetisera tal till en högtalare](quickstarts/text-to-speech.md)
* [Asynkront syntetisera ljud i långform](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Om du vill är text-till-tal-tjänsten tillgänglig via [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Exempelkod

Exempelkod för text-till-tal finns på GitHub. Dessa exempel omfattar text-till-tal-konvertering på de flesta populära programmeringsspråk.

- [Exempel på text till tal (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Text-till-tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Anpassning

Förutom standard- och neurala röster kan du skapa och finjustera anpassade röster som är unika för din produkt eller ditt varumärke. Allt som krävs för att komma igång är en handfull ljudfiler och tillhörande transkriptioner. Mer information finns i [Komma igång med Anpassad röst](how-to-custom-voice.md)

## <a name="pricing-note"></a>Prisfaktura

När du använder text-till-tal-tjänsten faktureras du för varje tecken som konverteras till tal, inklusive interpunktion. SSML-dokumentet i sig inte kan faktureras, räknas valfria element som används för att justera hur texten konverteras till tal, till exempel fonem och tonhöjd, som fakturerbara tecken. Här är en lista över vad som är fakturerbart:

- Text som skickas till text-till-tal-tjänsten i SSML-brödtexten för begäran
- Alla markeringar i textfältet i begärandetexten i SSML-format, förutom `<speak>` och `<voice>` taggar
- Bokstäver, skiljetecken, blanksteg, flikar, markeringar och alla blankstegstecken
- Varje kodpunkt som definieras i Unicode

Detaljerad information finns i [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Varje kinesiskt, japanskt och koreanskt språktecken räknas som två tecken för fakturering.

## <a name="reference-docs"></a>Referensdokument

- [Speech SDK](speech-sdk.md)
- [REST API: Text-till-tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

- [Skaffa en prenumeration på tjänsten För yttrandefrihet](get-started.md)
- [Hämta tal-SDK](speech-sdk.md)
