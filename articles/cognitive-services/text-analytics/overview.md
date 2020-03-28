---
title: Vad är API för textanalys? - Kapacitet -
titleSuffix: Azure Cognitive Services
description: Använd API:et för textanalys från Azure Cognitive Services för sentimentanalys, extrahering av nyckelfraser, språkidentifiering och entitetsigenkänning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78395719"
---
# <a name="what-is-the-text-analytics-api"></a>Vad är API för textanalys?

Api:et för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av naturligt språk över rå text och innehåller fyra huvudfunktioner: sentimentanalys, extrahering av nyckelfraser, språkidentifiering och namngiven entitetsigenkänning.

API:et är en del av [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), en samling maskininlärnings- och AI-algoritmer i molnet för dina utvecklingsprojekt.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Textanalys kan betyda olika saker, men i Cognitive Services tillhandahåller API:et för textanalys fyra typer av analyser enligt beskrivningen nedan. Du kan använda dessa funktioner med [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)eller [klientbiblioteket](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Attitydanalys
Använd [sentimentanalys](how-tos/text-analytics-how-to-sentiment-analysis.md) för att ta reda på vad kunderna tycker om ditt varumärke eller ämne genom att analysera rå text för ledtrådar om positiva eller negativa känslor. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva.<br /> Analysmodellen tränas i förväg med hjälp av en omfattande textmängd och tekniker för naturligt språk från Microsoft. För [utvalda språk](text-analytics-supported-languages.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet.

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser
Extrahera automatiskt [nyckelfraser](how-tos/text-analytics-how-to-keyword-extraction.md) för att snabbt identifiera de viktigaste punkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”.

## <a name="language-detection"></a>Språkidentifiering
Du kan [identifiera vilket språk indatatexten är skriven på](how-tos/text-analytics-how-to-language-detection.md) och rapportera en enda språkkod för varje dokument som skickas in på begäran på en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk. Språkkoden paras med poäng som anger styrkan hos poängen.

## <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet
[Identifiera och kategorisera entiteter](how-tos/text-analytics-how-to-entity-linking.md) i texten som personer, platser, organisationer, datum/tid, kvantiteter, procenttal, valutor med mera. Välkända entiteter identifieras även länkas till mer information på webben.

## <a name="use-containers"></a>Använda containrar

[Använd textanalyscontainrarna](how-tos/text-analytics-how-to-install-containers.md) för att extrahera nyckelfraser, identifiera språk och analysera sentiment lokalt genom att installera standardiserade Docker-containrar närmare dina data.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkelt: du skickar data för analys och hanterar utdata i din kod. Analysverktyg förbrukas i befintligt skick, utan någon ytterligare konfiguration eller anpassning.

1. [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för Textanalys. Efteråt [får du nyckeln](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) genererad så att du kan autentisera dina begäranden.

2. [Formulera en begäran](how-tos/text-analytics-how-to-call-api.md#json-schema) som innehåller dina data som rå ostrukturerad text, i JSON.

3. Publicera begäran till slutpunkten som upprättats under registreringen, vilket lägger till önskad resurs: sentimentanalys, extrahering av nyckelfraser, språkidentifiering eller namngiven entitetsigenkänning.

4. Strömma eller lagra svaret lokalt. Beroende på begäran är resultatet attitydpoäng, en samling extraherade nyckelfraser eller en språkkod.

Utdata returneras som ett enda JSON-dokument, med resultat för varje textdokument du har publicerat, baserat på ID. Du kan därefter analysera, visualisera eller kategorisera resultatet i användbara insikter.

Data lagras inte på ditt konto. Åtgärder som utförs av API för textanalys är tillståndslösa, vilket betyder att texten du anger bearbetas och resultaten returneras omedelbart.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalys för flera programmeringsupplevelsenivåer

Du kan börja använda API:et för textanalys i dina processer, även om du inte har mycket erfarenhet av programmering. Använd dessa självstudier för att lära dig hur du kan använda API:et för att analysera text på olika sätt så att den passar din upplevelsenivå. 

* Minimal programmering krävs:
    * [Extrahera information i Excel med textanalys och Power Automate](tutorials/extract-excel-information.md)
    * [Använda API:et för textanalys och MS-flöde för att identifiera känslan för kommentarer i en Yammer-grupp](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrera Power BI med API:et för textanalys för att analysera feedback från kunder](tutorials/tutorial-power-bi-key-phrases.md)
* Programmeringsupplevelse rekommenderas:
    * [Sentimentanalys på strömmade data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Skapa en Flask-app för att översätta text, analysera sentiment och syntetisera tal](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Se [språk som stöds i TEXTAnalys-API:et](text-analytics-supported-languages.md) för det här innehållet.

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla av slutpunkterna för API för textanalys accepterar råtextdata. Den aktuella gränsen är 5 120 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre bitar.

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5,120 tecken mätt [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)med . |
| Maximal storlek på hela begäran | 1 MB |
| Maximalt antal dokument i en begäran | 1 000 dokument |

Din prisgräns varierar med din prisnivå.

| Nivå          | Förfrågningar per sekund | Förfrågningar per minut |
|---------------|---------------------|---------------------|
| S / Multi-service | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Begäranden mäts separat för varje textanalysfunktion. Du kan till exempel skicka det maximala antalet begäranden för din prisnivå till varje funktion samtidigt.      

## <a name="unicode-encoding"></a>Unicode-kodning

API för textanalys använder Unicode-kodning för textrepresentation och beräkningar av antal tecken. Begäranden kan skickas i både UTF-8 och UTF-16 utan mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för teckenlängd och anses motsvara för databegränsningar för textanalys. Om du [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) använder för att få teckenantalet använder du samma metod som vi använder för att mäta datastorlek.

## <a name="next-steps"></a>Nästa steg

+ [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för Text Analytics för att få en nyckel och slutpunkt för dina program.

+ Använd [snabbstarten](quickstarts/text-analytics-sdk.md) för att börja skicka API-anrop. Lär dig hur du skickar text, väljer en analys och visar resultat med minimal kod.

+ Se [vad som är nytt i TEXT Analytics API](whats-new.md) för information om nya versioner och funktioner.

+ Gräva in dig lite djupare med den här [sentimentanalysstudien](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) med Azure Databricks.

+ Kolla in vår lista över blogginlägg och fler videor om hur du använder API:et för textanalys med andra verktyg och tekniker på vår [sida för externt & communityinnehåll.](text-analytics-resource-external-community.md)
