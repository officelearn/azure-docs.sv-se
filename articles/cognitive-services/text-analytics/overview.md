---
title: Översikt över text Analytics - Azure Cognitive Services | Microsoft Docs
description: Textanalys i Azure Cognitive Services för attitydanalys, extrahering av diskussionsämne, språkidentifiering och entitetslänkning.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: a514618713afe2306b6fb99b2f8c038aeac56009
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092211"
---
# <a name="what-is-text-analytics"></a>Vad är textanalys?

Text Analytics-tjänsten tillhandahåller avancerad bearbetning av för raw ostrukturerad text naturligt språk. Den innehåller fyra huvudfunktioner: attitydanalys, extrahering av diskussionsämne, språkidentifiering och entitetslänkning.

## <a name="analyze-sentiment"></a>Analysera sentiment

[Ta reda på](how-tos/text-analytics-how-to-sentiment-analysis.md) vad kunderna tycker om ditt märke eller ämne genom att analysera rå text efter ledtrådar om positiv eller negativ attityd. Den här API: T returnerar ett sentimentpoäng mellan 0 och 1 för varje dokument, där 1 är den mest positivt.<br />
Analysis-modeller är tränats med hjälp av en omfattande mängd text och naturligt språk tekniker från Microsoft. För [valt språk](text-analytics-supported-languages.md), API: et kan analysera och bedöma alla rå text som du anger.

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser

Automatiskt [extrahera nyckelfraser](how-tos/text-analytics-how-to-keyword-extraction.md) att snabbt identifiera huvudändamålen. Till exempel få indatatexten ”livsmedelskedjan var härligt och det fanns underbar personal”, Text Analytics-tjänsten returnerar de huvudsakliga samtalsämnen: ”Mat” och ”underbar personal”.

## <a name="detect-language"></a>Identifiera språk

För upp till 120 språk [identifiera](how-tos/text-analytics-how-to-language-detection.md) vilka språk som indatatexten är skriven i och rapportera en enda språkkod för varje dokument som skickats på begäran. Språkkoden är kopplad till ett värde som anger styrkan hos poängen.

## <a name="identify-linked-entities-preview"></a>Identifiera länkade entiteter (förhandsversion)

[Identifiera](how-tos/text-analytics-how-to-entity-linking.md) välkända entiteter i din text och en länk till mer information på webben. Entitetslänkning känner igen och disambiguates när en term som används som en av olika entiteter, verb och andra ordformer.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkel: du skickar data för analys och referensen utdata i din kod. Analysverktyg förbrukas som – är utan ytterligare konfiguration eller anpassning.

1. [Registrera dig](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för en [åtkomstnyckel](how-tos/text-analytics-how-to-access-key.md). Nyckeln måste skickas för varje begäran.

2. [Skapa en begäran om](how-tos/text-analytics-how-to-call-api.md#json-schema) i JSON som innehåller dina data som raw ostrukturerad text.

3. Skicka begäran till slutpunkten som fastställts under registrering, lägga till API: et som du vill anropa: attitydanalys, extrahering av diskussionsämne, språkidentifiering eller enhetens identifiering.

4. Stream eller lagra svaren lokalt. Beroende på begäran är resultatet ett sentimentpoäng, extrahera nyckelfraser en samling eller en språkkod.

Utdata returneras som ett enda JSON-dokument med resultat för varje textdokument som du har lagt upp baserat på-ID. Du kan sedan analysera, visualisera eller kategorisera resultaten till användbara insikter.

Åtgärder som utförs av Text Analytics-tjänsten är tillståndslösa. Data lagras inte i ditt konto.

<a name="data-limits"></a>

## <a name="specifications"></a>Specifikationer

### <a name="supported-languages"></a>Språk som stöds

Se [språk som stöds i textanalys](text-analytics-supported-languages.md).

### <a name="data-limits"></a>Databegränsningar

Alla tjänstslutpunkter textanalys accepterar rå textdata. Den aktuella gränsen är 5 000 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre segment. Om du fortfarande behöver en högre gräns [Kontakta oss](https://azure.microsoft.com/overview/sales-number/) så att vi kan diskutera dina krav.

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 000 tecken enligt `String.Length`. |
| Maximal storlek på hela begäran | 1 MB |
| Högsta antalet dokument i en begäran | 1 000 dokument |

Gräns för överföringshastigheten i är 100 anrop per minut. Observera att du kan skicka ett stort antal dokument i ett enda anrop (upp till 1 000 dokument).

### <a name="unicode-encoding"></a>Unicode-kodning

Text Analytics-tjänsten använder Unicode-kodning för textrepresentationen och tecknet antal beräkningar. Du kan skicka begäranden i UTF-8- eller UTF-16, med inga mätbara skillnader i antal tecken. Om du använder `String.Length` teckenantalet får du använder samma metod som vi använder för att mäta datastorlek.

## <a name="next-steps"></a>Nästa steg

Försök först den [Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Du kan klistra in textinmatning (5 000 tecken maximalt) för att identifiera språk (upp till 120), beräkna ett sentimentpoäng, extrahera nyckelfraser eller identifiera länkade entiteter. Utan att behöva registrera är nödvändigt.

När du är redo att anropa tjänsten Text Analytics direkt:

+ [Registrera dig](how-tos/text-analytics-how-to-signup.md) för en nyckel och gå igenom stegen för [anropa API](how-tos/text-analytics-how-to-call-api.md).

+ [Snabbstart](quickstarts/csharp.md) är en genomgång av REST API-anrop skrivet i C#. Lär dig mer om att skicka text, Välj en analys och visa resultat med minimal kod.

+ [API-referensdokumentation](//go.microsoft.com/fwlink/?LinkID=759346) innehåller den tekniska dokumentationen för REST-API: er. Dokumentationen har stöd för inbäddade anrop, så du kan anropa API: et från varje dokumentationssidan.

+ [Externa & Community Content](text-analytics-resource-external-community.md) innehåller en lista över blogginlägg och videor som visar hur du använder Text Analytics med andra verktyg och tekniker.

## <a name="see-also"></a>Se också

 [Dokumentationssidan för cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)
