---
title: Vad är textanalys?
titleSuffix: Azure Cognitive Services
description: Textanalys i Azure Cognitive Services för attitydanalys, extrahering av diskussionsämne, språkidentifiering och entitetslänkning.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: overview
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 0fe4a9f05e0f6d1abed7b906cc5cd89854885ae5
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53992863"
---
# <a name="what-is-text-analytics"></a>Vad är textanalys?

API för textanalys är en molnbaserad tjänst som ger avancerad och naturlig språkbearbetning för texter och som innehåller fyra huvudfunktioner: attitydanalys, extrahering av diskussionsämne, språkidentifiering och entitetslänkning.

API:et backas upp av resurser i [Microsoft Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), en samling maskininlärnings- och AI-algoritmer i molnet, färdiga att förbruka i distributionsprojekt.

## <a name="capabilities-in-text-analytics"></a>Funktioner i textanalys

Textanalys kan betyda olika saker men i Cognitive Services innehåller API för textanalys fyra typer av analys, enligt följande tabell.

| Åtgärder| Beskrivning | API:er |
|-----------|-------------|------|
|[**Attitydanalys**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Få reda på vad kunder tycker om ditt varumärke eller ämne genom att analysera råtext för att få ledtrådar om positiva eller negativa attityder. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva.<br /> Analysmodellen tränas i förväg med hjälp av en omfattande textmängd och tekniker för naturligt språk från Microsoft. För [utvalda språk](text-analytics-supported-languages.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Extrahering av diskussionsämne**](how-tos/text-analytics-how-to-keyword-extraction.md) | Extrahera automatiskt nyckelfraser för att snabbt identifiera huvudpoängerna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”.  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Språkidentifiering**](how-tos/text-analytics-how-to-language-detection.md) | För upp till 120 språk kan du identifiera vilka språk den inmatade texten är skriven på och rapportera en enda språkkod för varje dokument som skickats på begäran. Språkkoden paras med poäng som anger styrkan hos poängen. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Entitetsigenkänning (förhandsversion)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifiera och kategorisera entiteter som personer, platser, organisationer, datum/tid, kvantiteter, procentsatser, valutor med mera. Välkända entiteter identifieras även länkas till mer information på webben. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) |

## <a name="use-containers"></a>Använda containrar

[Använd textanalyscontainrarna](how-tos/text-analytics-how-to-install-containers.md) för att extrahera nyckelfraser, identifiera språk och analysera sentiment lokalt genom att installera standardiserade Docker-containrar närmare dina data.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkelt: du skickar data för analys och hanterar utdata i din kod. Analysverktyg förbrukas i befintligt skick, utan någon ytterligare konfiguration eller anpassning.

1. [Registrera dig ](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) och få en [åtkomstnyckel](how-tos/text-analytics-how-to-access-key.md). Nyckeln måste skickas för varje begäran.

2. [Formulera en begäran](how-tos/text-analytics-how-to-call-api.md#json-schema) som innehåller dina data som rå ostrukturerad text, i JSON.

3. Publicera begäran till slutpunkten som etablerades vid registreringen och lägg till önskad resurs: attitydanalys, extrahering av diskussionsämne, språkidentifiering och entitetsidentifiering.

4. Strömma eller lagra svaret lokalt. Beroende på begäran är resultatet attitydpoäng, en samling extraherade nyckelfraser eller en språkkod.

Utdata returneras som ett enda JSON-dokument, med resultat för varje textdokument du har publicerat, baserat på ID. Du kan därefter analysera, visualisera eller kategorisera resultatet i användbara insikter.

Data lagras inte på ditt konto. Åtgärder som utförs av API för textanalys är tillståndslösa, vilket betyder att texten du anger bearbetas och resultaten returneras omedelbart.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Det här innehållet finns i [Språk som stöds i API för textanalys](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla av slutpunkterna för API för textanalys accepterar råtextdata. Den aktuella gränsen är 5 000 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre bitar. Om du fortfarande kräver en högre gräns kan du [kontakta oss](https://azure.microsoft.com/overview/sales-number/) så att vi kan diskutera dina krav.

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 000 tecken enligt `String.Length`. |
| Maximal storlek på hela begäran | 1 MB |
| Maximalt antal dokument i en begäran | 1 000 dokument |

Gränsen är 100 anrop per minut. Observera att du kan skicka en stor mängd dokument i ett enda anrop (upp till 1 000 dokument).

## <a name="unicode-encoding"></a>Unicode-kodning

API för textanalys använder Unicode-kodning för textrepresentation och beräkningar av antal tecken. Begäranden kan skickas i både UTF-8 och UTF-16 utan mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för teckenlängd och anses motsvara för databegränsningar för textanalys. Om du använder [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) för att hämta antalet tecken använder du samma metod som vi använder för att mäta datastorlek.

## <a name="next-steps"></a>Nästa steg

Prova först den [interaktiva demon](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Du kan klistra in en textinmatning (max. 5 000 tecken) för att identifiera språket (upp till 120), beräkna attitydpoäng eller extrahera nyckelfraser. Ingen registrering krävs.

När du är redo att anropa API:et direkt:

+ [Registrera dig](how-tos/text-analytics-how-to-signup.md) för en åtkomstnyckel och gå igenom stegen för att [anropa API](how-tos/text-analytics-how-to-call-api.md).

+ [Snabbstart](quickstarts/csharp.md) är en genomgång av REST API-anropen skrivna i C#. Lär dig hur du skickar text, väljer en analys och visar resultat med minimal kod.

+ [API-referensdokumentationen](//go.microsoft.com/fwlink/?LinkID=759346) innehåller den tekniska dokumentationen för API:erna. Dokumentationen stöder inbäddade anrop så att du kan anropa API:et från varje dokumentationssida.

+ [Externt innehåll och community-innehåll](text-analytics-resource-external-community.md) innehåller en lista över blogginlägg och videofilmer som demonstrerar hur du använder textanalys med andra verktyg och tekniker.

## <a name="see-also"></a>Se även

 [Dokumentationssida om Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)
