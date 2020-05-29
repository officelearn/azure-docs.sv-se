---
title: Vad är API för textanalys? Trådlösa
titleSuffix: Azure Cognitive Services
description: Använd API för textanalys från Azure Cognitive Services för sentiment-analys, extrahering av nyckel fraser, språk identifiering och enhets igenkänning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 1e9928359f314ed57004df0264ac843871083eba
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141865"
---
# <a name="what-is-the-text-analytics-api"></a>Vad är API för textanalys?

API för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av naturligt språk över rå text och innehåller fyra huvud funktioner: sentiment analys, extrahering av nyckel fraser, språk identifiering och identifiering av namngivna enheter.

API:et är en del av [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), en samling maskininlärnings- och AI-algoritmer i molnet för dina utvecklingsprojekt.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Text analyser kan betyda olika saker, men i Cognitive Services tillhandahåller API för textanalys fyra typer av analys enligt beskrivningen nedan. Du kan använda dessa funktioner med [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)eller [klient biblioteket](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Attitydanalys
Använd [sentiment analys](how-tos/text-analytics-how-to-sentiment-analysis.md) för att ta reda på vad kunderna tycker om ditt varumärke eller ditt ämne genom att analysera rå text för LED trådar om positiv eller negativ sentiment. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva.<br /> Analysmodellen tränas i förväg med hjälp av en omfattande textmängd och tekniker för naturligt språk från Microsoft. För [utvalda språk](text-analytics-supported-languages.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet.

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser
[Extrahera automatiskt viktiga fraser](how-tos/text-analytics-how-to-keyword-extraction.md) för att snabbt identifiera huvud punkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”.

## <a name="language-detection"></a>Språkidentifiering
Du kan [identifiera vilket språk som indatamängden skrivs i](how-tos/text-analytics-how-to-language-detection.md) och rapportera en enda språkkod för varje dokument som skickas på begäran i en rad olika språk, varianter, dialekter och vissa regionala/kulturella språk. Språkkoden paras med poäng som anger styrkan hos poängen.

## <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet
[Identifiera och kategorisera entiteter](how-tos/text-analytics-how-to-entity-linking.md) i din text som personer, platser, organisationer, datum/tid, kvantiteter, procent, valutor och mycket annat. Välkända entiteter identifieras även länkas till mer information på webben.

## <a name="use-containers"></a>Använda containrar

[Använd textanalyscontainrarna](how-tos/text-analytics-how-to-install-containers.md) för att extrahera nyckelfraser, identifiera språk och analysera sentiment lokalt genom att installera standardiserade Docker-containrar närmare dina data.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkelt: du skickar data för analys och hanterar utdata i din kod. Analysverktyg förbrukas i befintligt skick, utan någon ytterligare konfiguration eller anpassning.

1. [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för textanalys. Sedan [hämtar du nyckeln](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som genereras för att du ska kunna autentisera dina begär Anden.

2. [Formulera en begäran](how-tos/text-analytics-how-to-call-api.md#json-schema) som innehåller dina data som rå ostrukturerad text, i JSON.

3. Publicera begäran till den slut punkt som upprättats vid registreringen, Lägg till önskad resurs: sentiment analys, extrahering av nyckel fraser, språk identifiering eller identifiering av namngiven entitet.

4. Strömma eller lagra svaret lokalt. Beroende på begäran är resultatet attitydpoäng, en samling extraherade nyckelfraser eller en språkkod.

Utdata returneras som ett enda JSON-dokument, med resultat för varje textdokument du har publicerat, baserat på ID. Du kan därefter analysera, visualisera eller kategorisera resultatet i användbara insikter.

Data lagras inte på ditt konto. Åtgärder som utförs av API för textanalys är tillståndslösa, vilket betyder att texten du anger bearbetas och resultaten returneras omedelbart.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalys för flera programmerings miljö nivåer

Du kan börja använda API för textanalys i dina processer, även om du inte har mycket erfarenhet av programmering. Använd de här självstudierna för att lära dig hur du kan använda API: t för att analysera text på olika sätt för att passa din erfarenhets nivå. 

* Minimal programmering krävs:
    * [Extrahera information i Excel med Textanalys och energi automatisering](tutorials/extract-excel-information.md)
    * [Använd API för textanalys och MS Flow för att identifiera sentiment av kommentarer i en Yammer-grupp](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrera Power BI med API för textanalys för att analysera feedback från kunder](tutorials/tutorial-power-bi-key-phrases.md)
* Programmerings upplevelse rekommenderas:
    * [Sentimentanalys på strömmade data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Bygg en kolv-app för att översätta text, analysera sentiment och syntetiskt tal](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Referera till [språk som stöds i API för textanalys](text-analytics-supported-languages.md) för det här innehållet.

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla av slutpunkterna för API för textanalys accepterar råtextdata. Den aktuella gränsen är 5 120 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre bitar.

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 120 tecken som mäts av [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) . |
| Maximal storlek på hela begäran | 1 MB |
| Maximalt antal dokument i en begäran | Upp till 1 000 dokument ([varierar för varje funktion](concepts/data-limits.md)) |

Din hastighets gräns varierar beroende på pris nivå.

| Nivå          | Förfrågningar per sekund | Begär Anden per minut |
|---------------|---------------------|---------------------|
| S/multi-service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Begär Anden mäts för varje Textanalys funktion separat. Du kan till exempel skicka maximalt antal begär Anden för din pris nivå till varje funktion på samma tillfälle.      

## <a name="unicode-encoding"></a>Unicode-kodning

API för textanalys använder Unicode-kodning för textrepresentation och beräkningar av antal tecken. Begäranden kan skickas i både UTF-8 och UTF-16 utan mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för teckenlängd och anses motsvara för databegränsningar för textanalys. Om du använder [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) för att hämta antalet bokstäver använder du samma metod som vi använder för att mäta data storleken.

## <a name="next-steps"></a>Nästa steg

+ [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för textanalys för att få en nyckel och slut punkt för dina program.

+ Använd [snabb](quickstarts/text-analytics-sdk.md) start för att börja skicka API-anrop. Lär dig hur du skickar text, väljer en analys och visar resultat med minimal kod.

+ Se [vad som är nytt i API för textanalys](whats-new.md) för information om nya versioner och funktioner.

+ Lär dig mer om den här [självstudien om sentiment analys](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) med hjälp av Azure Databricks.

+ Kolla in vår lista med blogg inlägg och fler videor om hur du använder API för textanalys med andra verktyg och tekniker på sidan för den [externa & community-innehåll](text-analytics-resource-external-community.md).
