---
title: Avskrifts tjänst för samtals Center – tal tjänst
titleSuffix: Azure Cognitive Services
description: Ett vanligt scenario för tal till text är att skriva över stora volymer av telefoni data som kan komma från olika system, till exempel interaktivt röst svar (IVR). Ljudet kan vara stereo eller mono och RAW med lite-till-ingen-bearbetning som utförs på signalen. Med hjälp av tal tjänster och den enhetliga tal modellen kan ett företag få kvalitets avskrifter med många ljud tagnings system.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b7c7bfffb5ddf947dc9bd25e6828e2816a7325cd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559743"
---
# <a name="speech-services-for-telephony-data"></a>Tal tjänster för telefoni data

Telefoni data som genereras via landlines, mobil telefoner och radio är normalt låg kvalitet och Narrowband i intervallet 8 KHz, vilket skapar utmaningar när tal-till-text konverteras. De senaste tal igenkännings modellerna från Azure Speech Services i Excel vid inmatning av dessa telefoni data, även i fall då det är svårt för människor att förstå dem. Dessa modeller tränas med stora volymer av telefoni data och har bästa möjliga marknads igenkännings precision, även i miljöer med störningar.

Ett vanligt scenario för tal till text är att skriva över stora volymer av telefoni data som kan komma från olika system, till exempel interaktivt röst svar (IVR). Det ljud som dessa system erbjuder kan vara stereo eller mono, och RAW med lite-till-ingen-bearbetning som utförs på signalen. Med tal tjänster och Unified tal-modellen kan ett företag få kvalitets avskrifter, oavsett vilka system som används för att avbilda ljud.

Telefoni data kan användas för att bättre förstå dina kunders behov, identifiera nya marknadsförings möjligheter eller utvärdera prestanda för Call Center-agenter. När data har tilldelats kan ett företag använda utdata för förbättrad telemetri, identifiera nyckel fraser eller analysera kund sentiment.

De tekniker som beskrivs i den här sidan är av Microsoft internt för olika Support tjänster för tjänst hantering, både i real tid och batchläge.

Nu ska vi gå igenom några av de tekniker och relaterade funktionerna i Azure Speech Services-erbjudandet.

> [!IMPORTANT]
> Tal tjänster enhetlig modell tränas med olika data och erbjuder en enda modell lösning till ett antal scenarier från Diktering till telefoni analys.

## <a name="azure-technology-for-call-centers"></a>Azure-teknik för Call Center

Utöver den funktionella aspekten av tal tjänsternas primära syfte – är det bättre att förbättra kund upplevelsen. Det finns tre tydliga domäner i detta hänseende:

* Analys efter anrop som är en batchbearbetning av anrops inspelningar
* Analys i real tid av ljud signalen för att extrahera olika insikter när samtalet sker (med sentiment är ett framträdande användnings fall) och
* Virtuella assistenter (robotar), antingen genom att köra dialogen mellan kunden och roboten i ett försök att lösa kundens problem med ingen agent medverkan eller att använda AI-protokoll för att hjälpa agenten.

Ett typiskt arkitektur diagram över implementeringen av ett batch-scenario visas i bilden nedan under ![samtals Center avskrifts arkitektur](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Teknik komponenter för tal analys

Oavsett om domänen är post-Call eller i real tid erbjuder Azure en uppsättning av de mest mogna och nya teknikerna för att förbättra kund upplevelsen.

### <a name="speech-to-text-stt"></a>Tal till text (STT)

[Tal till text](speech-to-text.md) är den mest sökta efter-funktionen i en Call Center-lösning. Eftersom många av de efterföljande analys processerna förlitar sig på överformulerad text, är ordet fel frekvens (WER) ytterst viktigt. En av de viktigaste utmaningarna i inmatnings centret är bruset som är vanligt i Call Center (till exempel andra agenter som talar i bakgrunden), de många olika språkvarianterna och dialekterna samt den låga kvaliteten på själva telefon signalen. WER är mycket korrelerat med hur väl ljud-och språk modellerna tränas för ett specifikt språk, vilket innebär att det är viktigt att anpassa modellen till dina nationella inställningar. Våra senaste enhetliga version 4. x-modeller är lösningen för både avskrifts precision och svars tid. Tränad med tusentals timmar av akustiska data och miljarder av lexikalisk information enhetliga modeller är de mest exakta modellerna på marknaden för att skriva av anrops Center data.

### <a name="sentiment"></a>Sentiment
Att mäta om kunden hade en bra upplevelse är ett av de viktigaste områdena i tal analys när de tillämpas på det utrymme som används i anrops centret. Vårt [API för batch](batch-transcription.md) -avskrift erbjuder sentiment analys per uttryck. Du kan aggregera den uppsättning värden som erhålls som en del av en samtals avskrift för att fastställa sentiment för anropet för både dina agenter och kunden.

### <a name="silence-non-talk"></a>Tystnad (icke-prata)
Det är inte ovanligt för 35 procent av ett support samtal att vara vad vi kallar för att inte tala om tid. Vissa scenarier som inte är pratar: agenter som söker efter tidigare ärende historik med en kund, agenter som använder verktyg som gör det möjligt för dem att komma åt kundens skriv bord och utföra funktioner, kunder som är på plats i väntan på överföring och så vidare. Det är mycket viktigt att kunna mäta när tystnad sker i ett samtal eftersom det finns många viktiga kund sensitivities som inträffar kring de här typerna av scenarier och var de sker i anropet.

### <a name="translation"></a>Översättning
Vissa företag experimenterar med att tillhandahålla översatta avskrifter från support samtal på främmande språk så att leverans ansvariga kan förstå den världs omfattande upplevelsen av sina kunder. Våra [översättnings](translation.md) funktioner är inte överskridna. Vi kan översätta ljud till ljud eller ljud till text från ett stort antal språk.

### <a name="text-to-speech"></a>Text till tal
[Text till tal](text-to-speech.md) är ett annat viktigt utrymme för att implementera robotar som interagerar med kunderna. Typisk väg är att kunden pratar, att deras röst skrivs till text, att texten analyseras för avsikter, ett svar är syntetiskt baserat på känd avsikt och att en till gång antingen är riktad mot kunden eller ett syntetiskt röst svar är ges. Naturligtvis är allt detta att inträffa snabbt, och därför är svars tiden en viktig komponent när systemen lyckas.

Vår svars tid från slut punkt till slut punkt är ganska låg som beaktar de olika teknikerna som till exempel [tal-till-text](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), [text till tal](text-to-speech.md).

Våra nya röster är också särskiljbar från mänskliga röster. Du kan använda-röster för att ge din robot sitt unika personlighet.

### <a name="search"></a>Search
En annan häftklammer av analys är att identifiera interaktioner där en speciell händelse eller erfarenhet har inträffat. Detta görs vanligt vis med en av två metoder, antingen en ad hoc-sökning där användaren bara skriver en fras och systemet svarar eller en mer strukturerad fråga där en analytiker kan skapa en uppsättning logiska uttryck som identifierar ett scenario i ett anrop och varje anrop kan indexeras mot dessa uppsättningar med frågor. Ett bra Sökexempel är allmänt förekommande Compliance "detta samtal skall registreras i kvalitets syfte... "– så många företag vill vara säkra på att deras agenter tillhandahåller denna fri skrivning till kunderna innan samtalet faktiskt registreras. De flesta analys system har möjlighet att kunna trenda de beteenden som upptäckts av Query/search-algoritmer – eftersom den här rapporteringen av trender i slut änden är en av de viktigaste funktionerna i ett analys system. Med hjälp av [kognitiva tjänster-katalogen](https://azure.microsoft.com/services/cognitive-services/directory/search/) kan din end to end-lösning förbättras avsevärt med indexerings-och Sök funktioner.

### <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser
Det här avsnittet är ett av de mer utmanande analys programmen och en som drar nytta av AI och ML-program. Det främsta scenariot här är att härleda kund avsikten. Varför ringer kunden upp? Vad är kund problemet? Varför har kunden en negativ upplevelse? Vår [text analys tjänst](https://azure.microsoft.com/services/cognitive-services/text-analytics/) innehåller en uppsättning analys funktioner för att snabbt uppgradera din end to end-lösning för att extrahera de viktiga nyckelorden eller fraserna.

Nu har vi en titt på batchbearbetningen och real tids pipelinen för tal igenkänning i lite mer information.

## <a name="batch-transcription-of-call-center-data"></a>Batch-avskrift av Call Center-data

För att skriva över en mängd ljud har vi utvecklat [API för batch](batch-transcription.md)-avskriftering. API: et för batch-avskrift utvecklades för att kunna skriva av stora mängder ljuddata asynkront. När det gäller att skriva av anrops Center data baseras vår lösning på dessa pelare:

* **Exakthet**: Med de fjärde generationens enhetliga modeller erbjuder vi oöverträffad avskrifts kvalitet.
* **Svars tid**: Vi förstår att när du utför Mass avskrifter behövs avskrifterna snabbt. Avskrifts jobben som initieras via batch-avskrifts- [API: n](batch-transcription.md) placeras i kö omedelbart och när jobbet börjar köras körs det snabbare än avskriften i real tid.
* **Säkerhet**: Vi förstår att anrop kan innehålla känsliga data. Rest är att garantera att säkerheten är en av våra högsta prioriteter. Vår tjänst har erhållit ISO, SOC, HIPAA, PCI-certifieringar.

Call centers genererar stora mängder ljud data per dag. Om ditt företag lagrar telefoni data på en central plats, till exempel Azure Storage, kan du använda [API: et för batch](batch-transcription.md) -avskrift för att begära och ta emot avskrifter.

En typisk lösning använder dessa tjänster:

* Azure Speech Services används för att skriva av tal till text. En standard prenumeration (SO) för tal tjänster krävs för att använda API: et för batch-avskriftering. Kostnads fria prenumerationer (F0) kommer inte att fungera.
* [Azure Storage](https://azure.microsoft.com/services/storage/) används för att lagra telefoni data och avskrifterna som returneras av batch-avskrifts-API: et. Det här lagrings kontot bör använda aviseringar, särskilt när nya filer läggs till. Dessa meddelanden används för att utlösa avskrifts processen.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) används för att skapa en URL för signaturer för delad åtkomst (SAS) för varje inspelning och utlöser HTTP POST-begäran för att starta en avskrift. Dessutom används Azure Functions för att skapa begär Anden för att hämta och ta bort avskrifter med batch-avskrifts-API: et.
* [](webhooks.md) Webhooks används för att få meddelanden när avskrifter har slutförts.

Internt använder vi tekniken ovan för att stödja Microsofts kund samtal i batch-läge.
![Batch-arkitektur](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Real tids avskrift för Call Center-data

Vissa företag krävs för att kunna skriva av konversationer i real tid. Real tids avskrifter kan användas för att identifiera nyckel ord och utlösare söker efter innehåll och resurser som är relevanta för konversationen, för att övervaka sentiment, för att förbättra tillgänglighet eller för att tillhandahålla översättningar för kunder och agenter som inte är inbyggda tala.

Vi rekommenderar att du använder [tal-SDK](speech-sdk.md)för scenarier som kräver avskrifter i real tid. För närvarande är tal-till-text tillgängligt på [fler än 20 språk](language-support.md)och SDK är tillgängligt i C++, C#Java, python, Node. js, mål-C och Java Script. Det finns exempel på varje språk på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). De senaste nyheterna och uppdateringarna finns i [viktig information](releasenotes.md).

Internt använder vi teknikerna ovan för att analysera i real tids kund samtal i real tid när de sker.

![Batch-arkitektur](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Ett ord på IVRs

Tal tjänster kan enkelt integreras i en lösning med hjälp av antingen [tal-SDK](speech-sdk.md) eller [REST API](rest-apis.md). Detta kan dock kräva ytterligare tekniker. Normalt krävs en anslutning mellan ett IVR-system och Azure. Även om vi inte erbjuder sådana komponenter skulle vi vilja beskriva vad en anslutning till en IVR-Omslut.

Flera IVR-eller telefoni tjänst produkter (till exempel Generning eller AudioCodes) erbjuder integrations funktioner som kan utnyttjas för att aktivera inkommande och utgående ljud strömning till en Azure-tjänst. I princip kan en anpassad Azure-tjänst tillhandahålla ett särskilt gränssnitt för att definiera telefonsamtal (till exempel anrops start-eller anrops slut) och tillhandahålla en WebSocket-API för att ta emot inkommande ström ljud som används med tal tjänsterna. Utgående svar, till exempel konversations avskrift eller anslutningar med bot Framework, kan syntetiseras med Microsofts text till tal-tjänst och returneras till IVR för uppspelning.

Ett annat scenario är direkt SIP-integrering. En Azure-tjänst ansluter till en SIP-server och hämtar därför en inkommande ström och en utgående ström som används för tal-till-text-och text till tal-faserna. För att ansluta till en SIP-server finns kommersiella program erbjudanden, till exempel ozeki SDK eller [team som anropar och mötes-API](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (för närvarande i beta version) som är utformade för att stödja den här typen av scenario för ljud anrop.

## <a name="customize-existing-experiences"></a>Anpassa befintliga upplevelser

Azure Speech Services fungerar bra med inbyggda modeller, men du kanske vill anpassa och justera upplevelsen för din produkt eller miljö ytterligare. Anpassnings alternativ sträcker sig från akustisk modell justering till unika röst teckensnitt för ditt varumärke. När du har skapat en anpassad modell kan du använda den med någon av Azures tal tjänster både i real tid eller i batchläge.

| Tal tjänst | Modell | Beskrivning |
|----------------|-------|-------------|
| Tal till text | [Akustisk modell](how-to-customize-acoustic-models.md) | Skapa en anpassad akustisk modell för program, verktyg eller enheter som används i vissa miljöer som i en bil eller på en fabriks våning, var och en med särskilda registrerings villkor. Exempel är accenttecken, vissa bakgrunds brus eller med en speciell mikrofon för inspelning. |
| | [Språkmodell](how-to-customize-language-model.md) | Skapa en anpassad språk modell för att förbättra avskriften av branschspecifika vokabulär och grammatik, till exempel medicinsk terminologi eller IT-jargong. |
| | [Uttalsmodell](how-to-customize-pronunciation.md) | Med en anpassad uttal-modell kan du definiera fonetisk form och visa ett ord eller en term. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil. |
| Text till tal | [Rösttyp](how-to-customize-voice-font.md) | Med anpassade röst teckensnitt kan du skapa en igenkännings bara, en-av-en-röst för ditt varumärke. Det tar bara en liten mängd data att komma igång. Den mer information som du anger, desto mer naturlig och mänsklig som ditt röst teckensnitt kommer att ljud. |

## <a name="sample-code"></a>Exempelkod

Exempel kod finns på GitHub för var och en av Azure Speech Services. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller. Använd dessa länkar om du vill visa SDK: er och REST-exempel:

* [Konverterings exempel för tal till text och tal översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST API: Tal till text](rest-speech-to-text.md)
* [REST API: Text till tal](rest-text-to-speech.md)
* [REST API: Batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
