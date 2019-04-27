---
title: Vad är API för textanalys? -Funktioner –
titleSuffix: Azure Cognitive Services
description: Använd API för textanalys från Azure Cognitive Services för attitydanalys, extrahering av diskussionsämne, språkidentifiering och igenkänning av entiteter.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: ae1d8f18705b1058c10d4720c64f624ed0ef4a3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829539"
---
# <a name="what-is-text-analytics-api"></a>Vad är API för textanalys?

API för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av rå text naturligt språk och som innehåller fyra huvudfunktioner: attitydanalys, extrahering av diskussionsämne, språkidentifiering och igenkänning av entiteter.

API:et är en del av [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), en samling maskininlärnings- och AI-algoritmer i molnet för dina utvecklingsprojekt.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Textanalys kan innebära olika saker, men i Cognitive Services Text Analytics API innehåller fyra typer av analyser som beskrivs nedan.

## <a name="sentiment-analysis"></a>Attitydanalys
Använd [attitydanalys](how-tos/text-analytics-how-to-sentiment-analysis.md) att ta reda på vad kunderna tycker om ditt märke eller ämne genom att analysera rå text efter ledtrådar om positiv eller negativ attityd. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva.<br /> Analysmodellen tränas i förväg med hjälp av en omfattande textmängd och tekniker för naturligt språk från Microsoft. För [utvalda språk](text-analytics-supported-languages.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. Du kan använda den [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) API eller [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser
Automatiskt [extrahera nyckelfraser](how-tos/text-analytics-how-to-keyword-extraction.md) att snabbt identifiera huvudändamålen. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. Du kan använda den [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) API här eller [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="language-detection"></a>Språkidentifiering
Du kan [identifiera vilka språk som indatatexten är skriven i](how-tos/text-analytics-how-to-language-detection.md) och rapportera en enda språkkod för varje dokument som skickats på begäran för upp till 120 språk. Språkkoden paras med poäng som anger styrkan hos poängen. Du kan använda den [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) API eller [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet
[Identifiera och klassificera entiteter](how-tos/text-analytics-how-to-entity-linking.md) i texten som personer, platser, organisationer, datum/tid, mängd, procentsatser, valutor och mer. Välkända entiteter identifieras även länkas till mer information på webben. Du kan använda den [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) API.

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

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalys för flera programmering uppleva nivåer

Du kan börja använda API för textanalys i dina processer, även om du inte har mycket erfarenhet i programmering. Använd de här självstudierna för att lära dig hur du kan använda API: et för att analysera text på olika sätt att anpassa din upplevelse-nivå. 

* Minimal programmering krävs:
    * [Använd API för textanalys och MS Flow för att identifiera sentimentet med kommentarer i en Yammer-grupp](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrera Power BI med API för textanalys att analysera kundfeedback](tutorials/tutorial-power-bi-key-phrases.md)
* Programmering upplevelse rekommenderar:
    * [Sentimentanalys på strömmade data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Skapa en Flask-app för att översätta text, analysera sentiment och syntetisera tal](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Det här innehållet finns i [Språk som stöds i API för textanalys](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla av slutpunkterna för API för textanalys accepterar råtextdata. Den aktuella gränsen är 5 120 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre bitar. Om du fortfarande kräver en högre gräns kan du [kontakta oss](https://azure.microsoft.com/overview/sales-number/) så att vi kan diskutera dina krav.

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 120 tecken enligt [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximal storlek på hela begäran | 1 MB |
| Maximalt antal dokument i en begäran | 1 000 dokument |

Gräns för överföringshastigheten i är 100 förfrågningar per sekund och 1000 begäranden per minut. Du kan skicka ett stort antal dokument i ett enda anrop (upp till 1000 dokument).

## <a name="unicode-encoding"></a>Unicode-kodning

API för textanalys använder Unicode-kodning för textrepresentation och beräkningar av antal tecken. Begäranden kan skickas i både UTF-8 och UTF-16 utan mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för teckenlängd och anses motsvara för databegränsningar för textanalys. Om du använder [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) för att hämta antalet tecken använder du samma metod som vi använder för att mäta datastorlek.

## <a name="next-steps"></a>Nästa steg

+ [Registrera dig](how-tos/text-analytics-how-to-signup.md) för en åtkomstnyckel och gå igenom stegen för att [anropa API](how-tos/text-analytics-how-to-call-api.md).

+ [Snabbstart](quickstarts/csharp.md) är en genomgång av REST API-anropen skrivna i C#. Lär dig hur du skickar text, väljer en analys och visar resultat med minimal kod. Om du vill kan du börja med den [Python-quickstart](quickstarts/python.md) i stället.

+ Gräv lite djupare med den här [sentiment analysis självstudien](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) med Azure Databricks.

+ Kolla in vår lista med blogginlägg och fler videor om hur du använder API för textanalys med andra verktyg och tekniker i vår [externa & Community Content sidan](text-analytics-resource-external-community.md).
