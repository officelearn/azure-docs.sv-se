---
title: Vad är Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services är API:er, SDK:er och tjänster som du kan använda med Microsoft Azure för att skapa intelligenta program.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 12/19/2019
ms.author: nitinme
ms.openlocfilehash: 332f33bb4046a9ca9d6abf9bec75f60bb4ca9e32
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169100"
---
# <a name="what-are-azure-cognitive-services"></a>Vad är Azure Cognitive Services?

Azure Cognitive Services är API:er, SDK:er och tjänster som är tillgängliga för att hjälpa utvecklare att skapa intelligenta program utan att ha direkta AI- eller datavetenskapliga kunskaper eller kunskaper. Azure Cognitive Services gör det möjligt för utvecklare att enkelt lägga till kognitiva funktioner i sina program. Målet med Azure Cognitive Services är att hjälpa utvecklare att skapa program som kan se, höra, tala, förstå och till och med börja resonera. Katalogen över tjänster inom Azure Cognitive Services kan kategoriseras i fem huvudpelare - Vision, Tal, Språk, Webbsökning och Beslut.

## <a name="vision-apis"></a>Api:er för vision

|Tjänstnamn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Datorseende](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Visuellt innehåll")|Tjänsten Computer Vision ger dig tillgång till avancerade algoritmer för bearbetning av bilder och returinformation.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|Med Custom Vision-tjänsten kan du skapa anpassade bildklassificerare.|
|[Ansikte](https://docs.microsoft.com/azure/cognitive-services/face/ "Ansikte")| Face-tjänsten ger tillgång till avancerade ansiktsalgoritmer, vilket möjliggör identifiering och igenkänning av ansiktsattribut.|
|[Formulärakänningsverktyget](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Formigenkänning") (förhandsgranskning)|Formulärreformifierare identifierar och extraherar nyckelvärdespar och tabelldata från formulärdokument. sedan utdata strukturerade data inklusive relationer i den ursprungliga filen.|
|[Bläck recognizer](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Handskriftsigenkänning") (förhandsgranska)|Med Pennanteckningsrecenkänningsfunktionen kan du känna igen och analysera data för radvisa penndrag, former och handskrivet innehåll och mata ut en dokumentstruktur med alla erkända entiteter.|
|[Videindexering](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Videindexering")|Med Video Indexer kan du extrahera insikter från din video.|

## <a name="speech-apis"></a>Api:er för tal

|Tjänstnamn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Tjänst för taligenkänning](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Tjänst för taligenkänning")|Taltjänsten lägger till talaktiverade funktioner i program.|
|[API för högtalareigenkänning](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Talarigenkännings-API") (förhandsgranskning)|Api:et för högtalareigenkänning tillhandahåller algoritmer för högtalaridentifiering och verifiering.|
|[Bing-tal](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing-taligenkänning") (pensionering)|Bing Speech API ger dig ett enkelt sätt att skapa talaktiverade funktioner i dina program.|
|[Översättare Tal](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Talöversättning") (pensionering)|Translator Speech är en maskinöversättningstjänst.|

> [!NOTE]
> Letar du efter [Azure Cognitive Search?](https://docs.microsoft.com/azure/search/) Även om cognitive services används för vissa uppgifter är det en annan sökteknik som stöder andra scenarier.


## <a name="language-apis"></a>Språk-API:er

|Tjänstnamn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Språk understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Med tjänsten Språk understanding (LUIS) kan ditt program förstå vad en person vill med egna ord.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|Med QnA Maker kan du skapa en fråge- och svarstjänst från ditt halvstrukturerade innehåll.|
|[Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Textanalys")|TextAnalys ger naturligt språk bearbetning över rå text för sentimentanalys, nyckelfrasextrahering och språkidentifiering.|
|[Översättare Text](https://docs.microsoft.com/azure/cognitive-services/translator/ "Translator Text")|Översättartext ger maskinbaserad textöversättning i nära realtid.|


## <a name="search-apis"></a>Sök API:er

|Tjänstnamn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Bing Nyheter Sök](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Nyhetssökning i Bing")|Bing News Search returnerar en lista över nyhetsartiklar som bedöms vara relevanta för användarens fråga.|
|[Bing-videosökning](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Videosökning i Bing")|Bing Video Search returnerar en lista över videor som bedöms vara relevanta för användarens fråga.|
|[Webbsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Webbsökning i Bing")|Bing Web Search returnerar en lista över sökresultat som bedöms vara relevanta för användarens fråga.|
|[Automatiska förslag i Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Automatiska förslag i Bing")|Med Automatiska förslag på Bing kan du skicka en partiell sökfrågeterm till Bing och få tillbaka en lista med föreslagna frågor.|
|[Anpassad Bing-sökning](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Anpassad Bing-sökning")|Med Anpassad sökning i Bing kan du skapa skräddarsydda sökupplevelser för ämnen som du bryr dig om.|
|[Bing entitetssökning](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Entitetssökning i Bing")|Bing Entity Search returnerar information om entiteter som Bing bestämmer är relevanta för en användares fråga.|
|[Bing-bildsökning](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bildsökning i Bing")|Bing Image Search returnerar en visning av bilder som bedöms vara relevanta för användarens fråga.|
|[Bing visuell sökning](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Visuell sökning i Bing")|Bing Visual Search ger returnerar insikter om en bild, till exempel visuellt liknande bilder, shoppingkällor för produkter som finns i bilden och relaterade sökningar.|
|[Bing-sökning efter lokala företag](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing-sökning efter lokala företag")| Med API:et för lokal företagssökning kan dina program hitta kontakt- och platsinformation om lokala företag baserat på sökfrågor.|
|[Stavningskontroll av Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Stavningskontroll i Bing")|Med Stavningskontroll i Bing kan du utföra kontextuell grammatik och stavningskontroll.|

## <a name="decision-apis"></a>Api:er för beslut

|Tjänstnamn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Avvikelsedetektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Avvikelseidentifiering") (förhandsgranskning)|Avvikelsedetektor kan du övervaka och upptäcka avvikelser i din tid serie data.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Innehållsmoderator övervakar eventuellt stötande, oönskat och riskabelt innehåll.|
|[Personanpassning](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personanpassning")|Personalizer kan du välja den bästa upplevelsen att visa för dina användare, lära av deras realtid beteende.|

## <a name="use-free-trials"></a>Använd kostnadsfria utvärderingsversioner

[Registrera dig för gratis prövningar](https://azure.microsoft.com/try/cognitive-services/ "Hjälp om registrering") tar bara ett mail och några enkla steg. Du behöver ett Microsoft-konto om du inte redan har ett. Du får ett unikt par nycklar för varje API som begärs. Den andra är bara en reserv. Dela inte de hemliga nycklarna med någon. Utvärderingsversioner har både en kursgräns, transaktioner per sekund eller minut och ett månatligt användningstak. En transaktion är helt enkelt ett API-anrop. Du kan uppgradera till betalda nivåer för att låsa upp begränsningarna.

## <a name="subscription-management"></a>Prenumerationshantering

När du har loggat in med ditt Microsoft-konto kan du komma åt [Mina prenumerationer](https://www.microsoft.com/cognitive-services/subscriptions "Mina prenumerationer") för att visa de produkter du använder, återstående kvot och möjligheten att lägga till ytterligare produkter i din prenumeration.

## <a name="upgrade-to-unlock-limits"></a>Uppgradera för att låsa upp gränser

Alla API:er har en kostnadsfri utvärderingsplan som har begränsningar för användning och dataflöde.  Du kan öka dessa gränser genom att använda ett betalt erbjudande och välja lämpligt prisnivåalternativ när du distribuerar tjänsten i Azure-portalen. [Läs mer om erbjudanden och priser](https://azure.microsoft.com/pricing/details/cognitive-services/ "erbjudanden och prissättning"). Du måste konfigurera ett Azure-prenumerationskonto med ett kreditkort och ett telefonnummer. Om du har ett särskilt krav eller helt enkelt vill prata med försäljning, klicka på "Kontakta oss"-knappen högst upp på prissidan.

## <a name="regional-availability"></a>Regional tillgänglighet

API:erna i Cognitive Services finns i ett växande nätverk av Microsoft-hanterade datacenter. Du hittar den regionala tillgängligheten för varje API i [Azure-regionlistan](https://azure.microsoft.com/regions).

Letar du efter en region som vi inte stöder än? Låt oss veta genom att lämna in en funktionsförfrågan på vårt [UserVoice-forum.](https://cognitive.uservoice.com/)

## <a name="supported-cultural-languages"></a>Stödda kulturspråk

 Cognitive Services stöder ett brett spektrum av kulturella språk på servicenivå. Du hittar språktillgängligheten för varje API i [listan språk som stöds](language-support.md).

## <a name="securing-resources"></a>Säkra resurser

Azure Cognitive Services tillhandahåller en säkerhetsmodell i flera lager, inklusive [autentisering](authentication.md) via Azure Active Directory-autentiseringsuppgifter, en giltig resursnyckel och [Virtuella Azure-nätverk](cognitive-services-virtual-networks.md).

## <a name="container-support"></a>Stöd för containrar

 Cognitive Services tillhandahåller behållare för distribution i Azure-molnet eller lokalt. Läs mer om [Cognitive Services Containers](cognitive-services-container-support.md).

## <a name="certifications-and-compliance"></a>Certifieringar och efterlevnad

Cognitive Services har tilldelats certifieringar som CSA STAR-certifiering, FedRAMP Måttlig och HIPAA BAA.

Du kan [ladda ner](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) certifieringar för dina egna granskningar och säkerhetsgranskningar.

Om du vill förstå sekretess och datahantering går du till [Säkerhetscenter](https://servicetrust.microsoft.com/).

## <a name="support"></a>Support

Cognitive Services tillhandahåller flera [supportalternativ](cognitive-services-support-options.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa ett konto för Cognitive Services](cognitive-services-apis-create-account.md)
