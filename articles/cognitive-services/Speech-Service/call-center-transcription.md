---
title: Anropa Center avskrift – Speech Services
titleSuffix: Azure Cognitive Services
description: Ett vanligt scenario för tal till text skriva av stora datavolymer telefoni som kan komma från olika system, till exempel interaktiva röst svar (IVR). Ljudet kan vara stereo eller mono och raw med lite till Nej efterbearbetning utförs på signalen. Med Speech Services och enhetliga talmodell kan får ett företag hög kvalitet avskrifter, med många ljud capture system.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7d844f4d2ad77f5b7cc53275a24167e5f2e71b78
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027035"
---
# <a name="speech-services-for-telephony-data"></a>Taltjänster för telefoni data

Telefoni data som genererats via landlines, mobiltelefoner och trådlösa anslutningar är vanligtvis låg kvalitet och referensgräns inom intervallet för 8 KHz, vilket skapar utmaningar vid konvertering av ett tal till text. De senaste erkännande för talmodeller från Azure Speech Services excel på skriva av den här telefoni data, även i fall när data är svårt för en människa att förstå. Dessa modeller tränas med stora datavolymer telefoni och har bäst i marknaden taligenkänning, även i bort störande miljöer.

Ett vanligt scenario för tal till text skriva av stora datavolymer telefoni som kan komma från olika system, till exempel interaktiva röst svar (IVR). Ljudet ger dessa system kan vara stereo eller mono och raw med liten att ingen post som bearbetas på signalen. Med Speech Services och enhetliga talmodell kan kan ett företag få hög kvalitet avskrifter, oavsett de system som används för att spela in ljud.

Telefoni data kan användas för att bättre förstå dina kunders behov, hitta nya möjligheter att marknadsföring eller utvärdera prestanda anrop center-agenter. När data är transkriberas, kan ett företag använda utdata för förbättrad telemetri, identifiera nyckelfraser och analysera kundsentiment.

Tekniker som beskrivs i den här sidan är av Microsoft internt för olika anrop bearbetningstjänster, både i realtid och batch-läge.

Nu ska vi se några av tekniken och relaterade funktioner Azure Speech Services erbjudandet.

> [!IMPORTANT]
> Speech Services Unified modellen tränas med varierad och erbjuder en enda modell-lösning till ett antal scenario från diktering till telefoni analytics.

## <a name="azure-technology-for-call-centers"></a>Azure-teknik för Call Center

Därutöver funktionella aspekt av Speech Services det primära syftet – vid tillämpning callcentret - är att förbättra kundupplevelsen. Tre tydliga domäner finns i detta avseende fungerar 

* Efter anrop analytics, det vill säga batch-bearbetning av anropet inspelningar 
* Analys i realtid bearbetningen av ljudsignalen att extrahera olika insikter eftersom anropet sker (med sentiment som ett framträdande användningsfall) och
* Virtuella assistenter (robotar) styr dialogen mellan kunden och roboten i ett försök att lösa problem med ingen agent-medverkan eller som tillämpningen av AI protokoll för att hjälpa agenten.

En typisk Arkitekturdiagram över implementeringen av ett batch-scenario illustreras i bilden nedan ![anrop center avskrift arkitektur](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Teknik för taligenkänning Analyskomponenter

Om domänen är efter anrop eller i realtid, erbjuder Azure en uppsättning mogen och framväxande uppsättning tekniker för att förbättra kundupplevelsen. 

### <a name="speech-to-text-stt"></a>Tal till text (STT) 

[Tal till text](speech-to-text.md) är det mest eftersträvade efter funktion i alla anrop center-lösningar. Eftersom många av de underordnade analytics processerna förlitar sig på transkriberade texten är word Felfrekvens (WER) av stor betydelse. En av de största utmaningarna i anropet center avskrift dagens är bruset som är vanlig i Callcenter (till exempel – andra agenter som talar i bakgrunden), rad olika språk och dialekter samt faktiska telefon signalen låg kvalitet. WER korreleras med hög med hur väl- och språkdata modeller tränas för ett visst språk, så att du kan anpassa modellen lokalt är viktigt. Våra senaste enhetliga version 4.x modeller är lösningen med både avskrift noggrannhet och svarstid. Tränas med tiotals av tusentals timmars akustisk data och miljarder lexikal information enhetliga modeller är de mest korrekta modellerna på marknaden och effektiv samtalsdata center.

### <a name="sentiment"></a>Sentiment
Gauging om kunden har en bra upplevelse är en av de viktigaste områdena i talanalys vid tillämpning anrop center utrymme. Vår [Batch avskrift API](batch-transcription.md) erbjuder attitydanalys per uttryck. Du kan aggregera uppsättning värden som erhålls som en del av en avskrift för anropet att fastställa känslan i anropet för dina agenter och kunden.

### <a name="silence-non-talk"></a>Åsidosatt inaktivitet (icke-talk)
Det är inte ovanligt att 35 procent supportsamtal ska vara det vi kallar icke samtalstid. Vissa scenarier som inte är föredraget inträffar är: agenter Leta upp tidigare fallet historia med en kund, agenter med hjälp av verktyg som gör det möjligt för dem att komma åt kundens desktop och utföra funktioner, kunder som sitter håller väntar en överföring och så vidare. Det är mycket viktigt att du kan mäta när tystnad sker i ett anrop som det finns antal viktig kund sensitivities som runt dessa typer av scenarier och inträffar där i anropet.

### <a name="translation"></a>Översättning
Vissa företag experimenterar med tillhandahåller översatta avskrifter från främmande språk supportsamtal så att chefer leverans kan förstå upplevelsen av sina kunder över hela världen. Vår [translation](translation.md) funktioner är oöverträffade. Vi kan översätta ljud till ljud eller ljud till text från ett stort antal språk.

### <a name="text-to-speech"></a>Text till tal
[Text till tal](text-to-speech.md) är ett annat viktigt område i implementerar robotar som interagerar med kunderna. Vanliga dina olika val är att kunden talar, rösten transkriberas till text, texten analyseras för avsikter, ett svar är syntetiskt utifrån identifierade avsikt, och sedan en tillgång antingen visas för kunden eller ett syntetiskt voice-svar är genereras. Naturligtvis har allt detta ske snabbt – därför svarstiden är en viktig komponent i framgången för de här systemen. 

Vår svarstiden för slutpunkt till slutpunkt är ganska låg funderar på att de olika tekniker som ingår som [tal till text](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [ Text till tal](text-to-speech.md). 

Vår nya röster är också särskilja från mänskligt tal. Du kan använda ut röster för att ge din robot dess personlighet.

### <a name="search"></a>Search
En annan häfta analysens är att identifiera interaktioner där en specifik händelse eller erfarenhet har uppstått. Detta görs normalt med något av två sätt, antingen en ad hoc-sökning där du skriver en fras och systemet svarar eller en mer strukturerad fråga, där en analytiker kan skapa en uppsättning logiskt uttryck som identifierar ett scenario i ett anrop , och sedan varje anrop kan indexeras mot dessa uppsättning frågor. Ett bra search-exempel är allt vanligare efterlevnad instruktionen ”det här anropet registreras i kvalitet syfte... ”– Eftersom många företag vill kontrollera att agenterna tillhandahåller den här friskrivning kunder innan anropet faktiskt har registrerats. De flesta analytics system har möjlighet att trend beteenden som upptäckts av frågan /search algoritmer – eftersom den här rapportering av trender slutligen är en av de viktigaste funktionerna i ett system för analys. Via [Cognitive services-katalog](https://azure.microsoft.com/services/cognitive-services/directory/search/) din lösning från slutpunkt till slutpunkt kan förbättras avsevärt med funktioner för indexering och sökning.

### <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser
Det här området är en av de mer utmanande analysprogram och en som drar nytta av från programmet med AI och ML. Det primära scenariot är att härleda kundens avsikt. Varför anropar kunden? Vad är problemet kund? Varför har kunden en negativ upplevelse? Vår [Text analytics-tjänsten](https://azure.microsoft.com/services/cognitive-services/text-analytics/) tillhandahåller en uppsättning analytics direkt för att snabbt uppgradera din heltäckande lösning för att extrahera dessa viktiga nyckelord och fraser.

Nu har vi en titt på batch-bearbetning och i realtid pipelines för taligenkänning lite mer detaljerat.

## <a name="batch-transcription-of-call-center-data"></a>Batch-avskrift med samtalsdata center

För att skriva av stora mängd ljud vi utvecklat den [Batch avskrift API](batch-transcription.md). API: et för Batch avskrift har utarbetats för att transkribera stora mängder ljuddata asynkront. För att transkribera center samtalsdata baseras vår lösning på dessa pelare:

* **Precision**: Vi erbjuder oöverträffade avskrift kvalitet med fjärde generations enhetliga modeller.
* **Svarstid**: Vi förstår att när du gör bulk avskrifter avskrifter behövs snabbt. Transkription jobb initierats via den [Batch avskrift API](batch-transcription.md) placeras i kö omedelbart, och när jobbet körs det utförs snabbare än i realtid avskrift.
* **Säkerhet**: Vi förstår att anrop kan innehålla känsliga data. Känn dig trygg att säkerhet är en av vår högsta prioritet. Vår tjänst har fått ISO, SOC, HIPAA, PCI-certifieringar.

Call Center generera stora mängder ljuddata per dag. Om din verksamhet lagrar telefoni data på en central plats, till exempel Azure Storage, kan du använda den [Batch avskrift API]((batch-transcription.md) för att begära och ta emot avskrifter asynkront.

En typisk lösning använder dessa tjänster:

* Azure Speech Services används för att transkribera tal till text. En standard-prenumerationen (så) för Speech Services krävs för att använda API: et för Batch avskrift. Kostnadsfria prenumerationer (F0) fungerar inte.
* [Azure Storage](https://azure.microsoft.com/services/storage/) används för att lagra data för telefoni och betyg som returneras av API: et för Batch avskrift. Det här lagringskontot ska använda meddelanden, specifikt för när du lägger till nya filer. Dessa meddelanden används för att utlösa avskrift process.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) används för att skapa signaturer för delad åtkomst (SAS) URI för varje inspelning och utlösa HTTP POST-begäran för att starta en avskrift. Dessutom används Azure Functions för att skapa förfrågningar om att hämta och ta bort avskrifter med hjälp av Batch avskrift API.
* [WebHooks](webhooks.md) används för att få meddelanden när avskrifter har slutförts.

Internt använder vi ovan tekniker för att stödja Microsofts customer anropar i batchläge.
![Batch-arkitektur](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>I realtid avskrift för anrop center-data

Vissa företag krävs för att transkribera konversationer i realtid. I realtid avskrift kan användas för att identifiera nyckelord och utlösa söker efter innehåll och resurser som är relevanta för en konversation, för att övervaka sentiment, förbättra tillgängligheten och ange översättningar för kunder och agenter som inte är inbyggda talare.

För scenarier som kräver i realtid avskrift, bör du använda den [tal SDK](speech-sdk.md). Tal till text är för närvarande tillgängliga i [fler än 20 språk](language-support.md), och SDK är tillgänglig i C++, C#, Java, Python, Node.js och Javascript. Exemplen finns tillgängliga på varje språk på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). De senaste nyheterna och uppdateringarna finns i [viktig](releasenotes.md).

Internt använder vi ovan tekniker för analys i realtid Microsoft kundsamtal när de visar.

![Batch-arkitektur](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Ett ord på IVRs

Taltjänster kan lätt integreras i en lösning genom att använda antingen den [tal SDK](speech-sdk.md) eller [REST API](rest-apis.md). Anropet center avskrift kan dock kräva ytterligare tekniker. Vanligtvis krävs en anslutning mellan en IVR-system och Azure. Även om vi inte erbjuder dessa komponenter, vill vi att beskriva en anslutning till en IVR innebär.

Flera IVR eller telefoni serviceprodukter (till exempel Genesys eller AudioCodes) erbjuder integrationsmöjligheter som lätt kan användas för att aktivera inkommande och utgående ljud genomströmning till en Azure-tjänst. En anpassad Azure-tjänst kan i praktiken, ange ett visst gränssnitt för att definiera telefonsamtal sessioner (till exempel anropa Start eller anropa End) och exponera ett WebSocket-API för att ta emot inkommande stream ljud som används med Speech Services. Utgående svar, till exempel konversationen avskrift eller anslutningar med Bot Framework, kan syntetiskt med Microsofts text till tal-tjänst och tillbaka till IVR för uppspelning.

Ett annat scenario är direkt SIP-integrering. En Azure-tjänst som ansluter till en SIP-Server, därför få en inkommande dataström och en utgående-dataström som används för tal till text och text till tal-faser. För att ansluta till är en SIP-Server det kommersiella programvaruerbjudanden, till exempel Ozieki SDK eller [The team anropa och möten API](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (för närvarande i beta), som är utformade för att stödja den här typen av scenario för ljud-anrop.

## <a name="customize-existing-experiences"></a>Anpassa nuvarande upplevelsen

Azure Speech Services fungerar bra med inbyggda modeller, men du kanske vill att ytterligare anpassa och justera upplevelsen för din produkt eller miljö. Anpassning av alternativ för allt från akustisk modell justering till unika rösttyper för ditt varumärke. När du har skapat en anpassad modell kan använda du den med någon av Azure Speech Services både i realtid eller i batchläge.

| Med taltjänsten | Modell | Beskrivning |
|----------------|-------|-------------|
| Tal till text | [Akustisk modell](how-to-customize-acoustic-models.md) | Skapa en anpassad akustisk modell för program, verktyg, eller enheter som är särskilt används miljöer som i en bil eller på fabriksgolvet, var och en med specifika inspelning villkor. Exempel är accenttecken tal, specifika bakgrundsljud eller med en specifik mikrofon för inspelning. |
| | [Språkmodell](how-to-customize-language-model.md) | Skapa en anpassad språkmodell för att förbättra transkription av branschspecifika ordförråd och grammatik, till exempel medicinsk terminologi eller IT-jargong. |
| | [Uttalsmodell](how-to-customize-pronunciation.md) | Du kan definiera fonetiska, formulär och visning av ett ord eller en term med en anpassad uttal-modell. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil. |
| Text till tal | [Rösttyp](how-to-customize-voice-font.md) | Anpassade rösttyper kan du skapa en identifierbara, en av en typ ton för ditt varumärke. Det tar bara en liten mängd data för att komma igång. Ju mer data som du ger, mer naturliga och människoliknande din rösttyp kommer ljud. |

## <a name="sample-code"></a>Exempelkod

Exempelkoden finns på GitHub för varje Azure Speech Services. De här exemplen omfattar vanliga scenarier som läsa ljud från en fil eller dataström, kontinuerliga och inleveransen erkännande och arbeta med anpassade modeller. Använd dessa länkar om du vill visa SDK och REST-exempel:

* [Tal till text och tal translation exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch avskrift exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST-API: Speech-to-text](rest-speech-to-text.md)
* [REST-API: Text till tal](rest-text-to-speech.md)
* [REST-API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
