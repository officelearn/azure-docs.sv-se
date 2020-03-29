---
title: Call Center Transkription - Taltjänst
titleSuffix: Azure Cognitive Services
description: Ett vanligt scenario för tal-till-text är att överföra stora mängder telefonidata som kommer från olika system, till exempel Interaktiv röstsvar (IVR). Med taltjänsten och unified speech-modellen kan ett företag få högkvalitativa transkriptioner med ljudinspelningssystem.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806328"
---
# <a name="speech-service-for-telephony-data"></a>Taltjänst för telefonidata

Telefonidata som genereras via fasta telefoner, mobiltelefoner och radioapparater är vanligtvis låg kvalitet och smalband i intervallet 8 KHz, vilket skapar utmaningar vid konvertering av tal-till-text. De senaste taligenkänningsmodellerna från taltjänsten utmärker sig på att transkribera dessa telefonidata, även i de fall då data är svåra för en människa att förstå. Dessa modeller är utbildade med stora mängder telefonidata och har bästa möjliga igenkänningsnoggrannhet, även i bullriga miljöer.

Ett vanligt scenario för tal-till-text är att överföra stora mängder telefonidata som kan komma från olika system, till exempel Interactive Voice Response (IVR). Ljudet dessa system ger kan vara stereo eller mono, och rå med liten-till-ingen efterbehandling gjort på signalen. Med taltjänsten och unified speech-modellen kan ett företag få högkvalitativa transkriptioner, oavsett vilka system som används för att fånga ljud.

Telefonidata kan användas för att bättre förstå dina kunders behov, identifiera nya marknadsföringsmöjligheter eller utvärdera prestanda för callcenteragenter. När data har transkriberats kan ett företag använda utdata för ändamål som förbättrad telemetri, identifiera nyckelfraser eller analysera kundsentiment.

De tekniker som beskrivs på den här sidan är av Microsoft internt för olika supportsamtalsbehandlingstjänster, både i realtid och batchläge.

Låt oss granska en del av tekniken och relaterade funktioner som taltjänsten erbjuder.

> [!IMPORTANT]
> Taltjänsten Unified-modellen är tränad med olika data och erbjuder en enmodellslösning till ett antal scenarier från diktering till telefonianalys.

## <a name="azure-technology-for-call-centers"></a>Azure-teknik för callcenter

Utöver den funktionella aspekten av taltjänstens funktioner är deras främsta syfte – när de tillämpas på kundtjänsten – att förbättra kundupplevelsen. Det finns tre tydliga områden i detta avseende:

- Efter samtalsanalys, som i huvudsak är batchbearbetning av samtalsinspelningar efter samtalet.
- Realtidsanalys, som bearbetar ljudsignalen för att extrahera olika insikter som samtalet äger rum (med känsla är en framträdande användning fall).
- Röstassistenter (bots), antingen driver dialogen mellan kunden och bot i ett försök att lösa kundens problem utan agent deltagande, eller att vara tillämpningen av artificiell intelligens (AI) protokoll för att hjälpa agenten.

Ett typiskt arkitekturdiagram över implementeringen av ett batchscenario visas i bilden nedan ![Bildcenters transkriptionsarkitektur](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Teknikkomponenter för talanalys

Oavsett om domänen är efter-samtal eller i realtid, erbjuder Azure en uppsättning mogna och nya tekniker för att förbättra kundupplevelsen.

### <a name="speech-to-text-stt"></a>Tal till text (STT)

[Tal-till-text](speech-to-text.md) är den mest eftertraktade funktionen i en callcenter-lösning. Eftersom många av de nedströms analysprocesserna är beroende av transkriberad text är ordet felfrekvens _(WER_) av yttersta vikt. En av de viktigaste utmaningarna i call center transkription är det buller som är förhärskande i callcenter (till exempel andra agenter som talar i bakgrunden), den rika variationen av språk platser och dialekter samt den låga kvaliteten på den faktiska telefonsignalen. WER är starkt korrelerad med hur väl de akustiska och språkmodellerna är utbildade för ett visst språk, vilket innebär att möjligheten att anpassa modellen till ditt språk är viktigt. Våra senaste Unified version 4.x-modeller är lösningen på både transkriptionsnoggrannhet och latens. Unified-modeller är de mest exakta modellerna på marknaden för att transkribera callcenterdata med tiotusentals timmar med akustisk data och miljarder lexikal information.

### <a name="sentiment"></a>Sentiment

Att bedöma om kunden hade en bra upplevelse är ett av de viktigaste områdena för talanalys när den tillämpas på callcenterutrymmet. Vårt [batch-transkriptions-API](batch-transcription.md) erbjuder sentimentanalys per yttrande. Du kan aggregera uppsättningen värden som erhållits som en del av en samtalsavskrift för att avgöra känslan i samtalet för både dina agenter och kunden.

### <a name="silence-non-talk"></a>Tystnad (icke-tal)

Det är inte ovanligt att 35 procent av ett supportsamtal är vad vi kallar icke-samtalstid. Vissa scenarier som icke-talk inträffar för är: agenter som letar upp tidigare ärendehistorik med en kund, agenter som använder verktyg som gör det möjligt för dem att komma åt kundens skrivbord och utföra funktioner, kunder som sitter i vänteläge och väntar på en överföring och så vidare. Det är oerhört viktigt att mäta när tystnad sker i ett samtal eftersom det finns många viktiga kundkänsligheter som uppstår kring dessa typer av scenarier och var de förekommer i samtalet.

### <a name="translation"></a>Översättning

Vissa företag experimenterar med att tillhandahålla översatta utskrifter från stödsamtal på främmande språk så att leveranschefer kan förstå sina kunders globala upplevelse. Vår [översättningskapacitet](translation.md) är oöverträffad. Vi kan översätta ljud-till-ljud eller ljud-till-text för ett stort antal språk.

### <a name="text-to-speech"></a>Text till tal

[Text-till-tal](text-to-speech.md) är ett annat viktigt område i genomförandet av robotar som interagerar med kunderna. Den typiska vägen är att kunden talar, deras röst transkriberas till text, texten analyseras för avsikter, ett svar syntetiseras baserat på den erkända avsikten, och sedan en tillgång antingen visas för kunden eller en syntetiserad röst svar är Genereras. Naturligtvis allt detta måste ske snabbt - så låg latens är en viktig komponent i framgången för dessa system.

Vår end-to-end latens är betydligt låg för de olika tekniker som berörs såsom [tal-till-text,](speech-to-text.md) [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [Text-till-tal](text-to-speech.md).

Våra nya röster är också omöjliga att skilja från mänskliga röster. Du kan använda våra röster för att ge din bot dess unika personlighet.

### <a name="search"></a>Search

En annan stapelvara i analytics är att identifiera interaktioner där en viss händelse eller upplevelse har inträffat. Detta görs vanligtvis med en av två tillvägagångssätt; Antingen en ad hoc-sökning där användaren helt enkelt skriver en fras och systemet svarar, eller en mer strukturerad fråga där en analytiker kan skapa en uppsättning logiska satser som identifierar ett scenario i ett anrop, och sedan kan varje anrop indexeras mot den uppsättningen frågor. Ett bra sökexempel är den allestädes närvarande efterlevnad uttalande "denna uppmaning skall registreras för kvalitetsändamål ... ". Många företag vill se till att deras agenter tillhandahåller denna ansvarsfriskrivning till kunder innan samtalet faktiskt registreras. De flesta analyssystem har förmågan att trenda de beteenden som hittas av fråge-/sökalgoritmer, och den här rapporteringen av trender är i slutändan en av de viktigaste funktionerna i ett analyssystem. Genom [Cognitive Services katalog](https://azure.microsoft.com/services/cognitive-services/directory/search/) din end to end-lösning kan förbättras avsevärt med indexering och sökfunktioner.

### <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Det här området är ett av de mer utmanande analysprogrammen och ett som drar nytta av tillämpningen av AI och maskininlärning. Det primära scenariot i det här fallet är att dra slutsatsen kundavsikt. Varför ringer kunden? Vad är kundproblemet? Varför hade kunden en negativ upplevelse? Vår [textanalystjänst](https://azure.microsoft.com/services/cognitive-services/text-analytics/) tillhandahåller en uppsättning analyser som inte finns i kartongen för att snabbt uppgradera din heltäckande lösning för att extrahera dessa viktiga sökord eller fraser.

Låt oss nu ta en titt på batchbearbetning och pipelines i realtid för taligenkänning i lite mer detalj.

## <a name="batch-transcription-of-call-center-data"></a>Batch transkription av callcenter data

För transkribera bulk ljud vi utvecklat [Batch Transkription API](batch-transcription.md). Batch Transkription API har utvecklats för att transkribera stora mängder ljuddata asynkront. När det gäller transkribera callcenter data, är vår lösning baserad på dessa pelare:

- **Noggrannhet** - Med fjärde generationens Unified-modeller erbjuder vi oöverträffad transkriptionskvalitet.
- **Latens** - Vi förstår att när du gör bulk transkriptioner, transkriptioner behövs snabbt. Transkriptionsjobben som initieras via [batchskription API](batch-transcription.md) kommer att köas omedelbart, och när jobbet börjar köras utförs det snabbare än transkription i realtid.
- **Säkerhet** - Vi förstår att samtal kan innehålla känsliga data. Ni kan vara säkra på att säkerhet är en av våra högsta prioriteringar. Vår tjänst har fått ISO, SOC, HIPAA, PCI-certifieringar.

Callcenter genererar stora volymer ljuddata dagligen. Om ditt företag lagrar telefonidata på en central plats, till exempel Azure Storage, kan du använda [BATCH-transkription API](batch-transcription.md) för att asynkront begära och ta emot transkriptioner.

En typisk lösning använder dessa tjänster:

- Taltjänsten används för att transkribera tal-till-text. En standardprenumeration (S0) för taltjänsten krävs för att använda api:et för batchavskrifter. Kostnadsfria prenumerationer (F0) fungerar inte.
- [Azure Storage](https://azure.microsoft.com/services/storage/) används för att lagra telefonidata och de avskrifter som returneras av API:et för batchavskrifter. Det här lagringskontot bör använda meddelanden, särskilt för när nya filer läggs till. Dessa meddelanden används för att utlösa transkriptionsprocessen.
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) används för att skapa SAS-uri (Shared Access Signatures) för varje inspelning och utlösa HTTP POST-begäran för att starta en transkription. Dessutom används Azure Functions för att skapa begäranden om att hämta och ta bort transkriptioner med hjälp av batch-transkriptions-API:et.

Internt använder vi ovanstående teknik för att stödja Microsofts kundsamtal i batch-läge.
![Batch-arkitektur](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transkription i realtid för callcenterdata

Vissa företag är skyldiga att transkribera samtal i realtid. Transkription i realtid kan användas för att identifiera nyckelord och utlösa sökningar efter innehåll och resurser som är relevanta för konversationen, för att övervaka sentiment, för att förbättra tillgängligheten eller för att tillhandahålla översättningar för kunder och agenter som inte är inbyggda Högtalare.

För scenarier som kräver transkription i realtid rekommenderar vi att du använder [Tal-SDK](speech-sdk.md). För närvarande är tal-till-text tillgängligt på [mer än 20 språk](language-support.md)och SDK är tillgängligt i C++, C#, Java, Python, Node.js, Objective-C och JavaScript. Prover finns på varje språk på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). De senaste nyheterna och uppdateringarna finns i [Viktig information](releasenotes.md).

Internt använder vi ovanstående teknik för att analysera i realtid Microsoft kundsamtal när de inträffar, vilket illustreras i följande diagram.

![Batch-arkitektur](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Några ord om IVRs

Taltjänsten kan enkelt integreras i vilken lösning som helst med hjälp av [tal-SDK](speech-sdk.md) eller [REST API](rest-apis.md). Call center-transkription kan dock kräva ytterligare teknik. Vanligtvis krävs en anslutning mellan ett IVR-system och Azure. Även om vi inte erbjuder sådana komponenter, här är en beskrivning vad en koppling till en IVR innebär.

Flera IVR- eller telefonitjänstprodukter (till exempel Genesys eller AudioCodes) erbjuder integrationsfunktioner som kan utnyttjas för att aktivera inkommande och utgående ljudpass-through till en Azure-tjänst. I grund och botten kan en anpassad Azure-tjänst tillhandahålla ett specifikt gränssnitt för att definiera telefonsamtalssessioner (till exempel Start av samtal eller Samtalsslut) och exponera ett WebSocket-API för att ta emot inkommande strömljud som används med taltjänsten. Utgående svar, till exempel konversationsavskription eller anslutningar till Bot Framework, kan syntetiseras med Microsofts text-till-tal-tjänst och returneras till IVR för uppspelning.

Ett annat scenario är direkt integrering med SESSION Initiation Protocol (SIP). En Azure-tjänst ansluter till en SIP-server och får på så sätt en inkommande ström och en utgående ström, som används för tal-till-text- och text-till-tal-faserna. För att ansluta till en SIP-server finns kommersiella programvaruerbjudanden, till exempel Ozeki SDK, eller [Teams calling and meetings API](/graph/api/resources/communications-api-overview) (för närvarande i beta), som är utformade för att stödja den här typen av scenario för ljudsamtal.

## <a name="customize-existing-experiences"></a>Anpassa befintliga upplevelser

 Taltjänsten fungerar bra med inbyggda modeller. Du kanske vill anpassa och justera upplevelsen för din produkt eller miljö ytterligare. Anpassningsalternativen sträcker sig från akustisk modelljustering till unika röstteckensnitt för ditt varumärke. När du har skapat en anpassad modell kan du använda den med någon av taltjänstens funktioner i realtids- eller batchläge.

| Tjänst för taligenkänning | Modell | Beskrivning |
| -------------- | ----- | ----------- |
| Tal till text | [Akustisk modell](how-to-customize-acoustic-models.md) | Skapa en anpassad akustisk modell för program, verktyg eller enheter som används i vissa miljöer som i en bil eller på ett fabriksgolv, var och en med specifika inspelningsvillkor. Exempel på detta är accenttal, specifika bakgrundsljud eller användning av en specifik mikrofon för inspelning. |
|                | [Språkmodell](how-to-customize-language-model.md) | Skapa en anpassad språkmodell för att förbättra transkriptionen av branschspecifik vokabulär och grammatik, till exempel medicinsk terminologi eller IT-jargong. |
|                | [Uttalsmodell](how-to-customize-pronunciation.md) | Med en anpassad uttalsmodell kan du definiera det fonetiska formuläret och visa för ett ord eller en term. Det är användbart för att hantera anpassade termer, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är ett uttal fil, som är en enkel `.txt` fil. |
| Text till tal | [Rösttyp](how-to-customize-voice-font.md) | Med anpassade röstteckensnitt kan du skapa en igenkännlig, unik röst för ditt varumärke. Det tar bara en liten mängd data för att komma igång. Ju mer data du tillhandahåller, desto mer naturligt och människoliknande ljuder röstteckensnittet. |

## <a name="sample-code"></a>Exempelkod

Exempelkod är tillgänglig på GitHub för var och en av taltjänstfunktionerna. Dessa exempel täcker vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel-shot erkännande, och arbeta med anpassade modeller. Använd dessa länkar för att visa SDK- och REST-exempel:

- [Exempel på tal-till-text och talöversättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Prov för batchutskrift (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Text-till-tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referensdokument

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Tal-till-text](rest-speech-to-text.md)
- [REST API: Text-till-tal](rest-text-to-speech.md)
- [REST API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
