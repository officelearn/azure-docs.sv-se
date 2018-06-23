---
title: Översikt över text Analytics API - kognitiva Azure-tjänster | Microsoft Docs
description: Text Analytics API i Azure kognitiva tjänster för sentiment analys, viktiga frasen extrahering och identifiera språk.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: db5ea943f270aa512afb508668aec90cc4c90df4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355191"
---
# <a name="what-is-text-analytics-api-version-20"></a>Vad är Text Analytics API Version 2.0?

Text Analytics API är en molnbaserad tjänst som ger avancerade naturligt språk bearbetning, över rådata text och omfattar fyra huvudsakliga funktionerna: sentiment analys, viktiga frasen extrahering, identifiera språk och länka entiteten.

API: et backas upp av resurser i [Microsoft kognitiva Services](https://docs.microsoft.com/azure/cognitive-services/), en samling av machine learning och AI algoritmer i molnet, lätt att använda i din utvecklingsprojekt.

## <a name="capabilities-in-text-analytics"></a>Funktioner i textanalys

Analys av text kan innebära olika saker, men i kognitiva tjänster, Text Analytics API innehåller fyra typer av analys enligt beskrivningen i följande tabell.

| Åtgärder| Beskrivning | API:er |
|-----------|-------------|------|
|[**Sentiment analys**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Ta reda på vad kunder tycker varumärken eller avsnittet genom att analysera rådata text för ledtrådar om positiva eller negativa sentiment. Detta API returnerar en sentiment poäng mellan 0 och 1 för varje dokument, där 1 är den mest positivt.<br /> Analys-modeller är pretrained med hjälp av en omfattande uppsättning text och naturligt språk teknik från Microsoft. För [valt språk](text-analytics-supported-languages.md), API: et kan analysera och betygsätta rådata text som du anger måste returnera resultat direkt till det anropande programmet. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Viktiga frasen extrahering**](how-tos/text-analytics-how-to-keyword-extraction.md) | Extrahera automatiskt viktiga fraser för att snabbt identifiera de huvudsakliga delarna. Till exempel för indatatexten ”mat har delikat och det fanns fantastiska personal” API: N returnerar de viktigaste aspekterna prata: ”Mat” och ”fantastiska personal”.  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Identifiera språk**](how-tos/text-analytics-how-to-language-detection.md) | Identifiera vilka språk som indatatexten är skriven i för upp till 120 språk och rapportera en enda språkkod för varje dokument som skickades i begäran. Språkkoden paras ihop med en poäng som anger styrkan hos poängen. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Entitet som länkar (förhandsgranskning)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifiera kända enheter i din text och en länk till mer information på webben. Entiteten länka känner igen och disambiguates när en term som används som en separat kan särskiljas entiteter, verb och andra word-formulär. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkel: du kan skicka data för analys och referensen utdata i koden. Analyzers förbrukas som-är utan ytterligare konfiguration eller anpassning.

1. [Registrera dig](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för en [åtkomstnyckeln](how-tos/text-analytics-how-to-access-key.md). Nyckeln måste skickas för varje begäran.

2. [Formulera en begäran](how-tos/text-analytics-how-to-call-api.md#json-schema) som innehåller data som rådata Ostrukturerade text i JSON.

3. Efter begäran till slutpunkten som fastställts under registreringen, lägga till en resurs: sentiment analys, viktiga frasen extrahering, identifiera språk eller enhetens identifiering.

4. Strömma eller lagra svaret lokalt. Beroende på begäran är resultatet en sentiment poäng, en samling av extraherade viktiga fraser eller en språkkod.

Resultatet returneras som ett enskilt JSON-dokument med resultat för varje text-dokumentet du skickat, baserat på-ID. Därefter kan du analysera, visualisera eller kategorisera resultaten i tillämplig insikter.

Data lagras inte i ditt konto. Åtgärder som utförs av Text Analytics API är tillståndslös, vilket innebär att den text som du anger ska bearbetas och resultat returneras omedelbart.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Referera till [språk som stöds i Text Analytics API](text-analytics-supported-languages.md) för det här innehållet.

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla Text Analytics API-slutpunkter acceptera rådata textdata. Den aktuella gränsen är 5 000 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre delar. Om du fortfarande behöver en högre gräns [kontaktar du oss](https://azure.microsoft.com/overview/sales-number/) så att vi kan diskutera dina krav.

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek för ett enskilt dokument | 5 000 tecken med `String.Length`. |
| Maximal storlek på hela begäran | 1 MB |
| Maximalt antal dokument i en begäran | 1 000 dokument |

Gräns för överföringshastigheten är 100 anrop per minut. Observera att du kan skicka ett stort antal dokument i ett enda anrop (upp till 1 000 dokument).

## <a name="unicode-encoding"></a>Unicode-kodning

API: et för Text Analytics använder Unicode-kodning för textrepresentationen och tecknet antal beräkningar. Du kan skicka begäranden i både UTF-8 och UTF-16 med några mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för tecken och betraktas som motsvarande i syfte att databegränsningar för text analytics. Om du använder `String.Length` för att få teckenantalet du använder samma metod som vi använder för att mäta datastorleken.

## <a name="next-steps"></a>Nästa steg

Först och försök den [interaktiva demo](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Du kan klistra in en textinmatning (högst 5 000 tecken) för att identifiera språk (upp till 120), beräkna en sentiment poäng eller extrahera nyckeln fraser. Ingen registrering krävs.

När du är redo att anropa API: et direkt:

+ [Registrera dig](how-tos/text-analytics-how-to-signup.md) för en nyckel och gå igenom stegen för [anropar API: et](how-tos/text-analytics-how-to-call-api.md).

+ [Snabbstart](quickstarts/csharp.md) är en genomgång av REST API-anrop skrivna i C#. Lär dig mer om att skicka text, Välj en analys och visa resultat med minimal kod.

+ [API-referensdokumentation](//go.microsoft.com/fwlink/?LinkID=759346) ger den tekniska dokumentationen för API: erna. I dokumentationen stöder inbäddade anrop så att du kan anropa API: et från varje dokumentationssida.

+ [Externa & Community-innehåll](text-analytics-resource-external-community.md) innehåller en lista över blogginlägg och videor som visar hur du använder textanalys med andra verktyg och tekniker.

## <a name="see-also"></a>Se också

 [Kognitiva dokumentationssidan för tjänster](https://docs.microsoft.com/azure/cognitive-services/)
