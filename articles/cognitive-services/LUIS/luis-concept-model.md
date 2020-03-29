---
title: Design med modeller - LUIS
titleSuffix: Azure Cognitive Services
description: Språkförståelse ger flera typer av modeller. Vissa modeller kan användas på mer än ett sätt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219999"
---
# <a name="design-with-intent-and-entity-models"></a>Design med avsikts- och entitetsmodeller 

Språkförståelse ger flera typer av modeller. Vissa modeller kan användas på mer än ett sätt. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Författande använder maskinundervisning

LUIS tillåter människor att enkelt lära begrepp till en maskin. Maskinen kan sedan bygga modeller (funktionella approximationer av begrepp som klassificerare och utsug) som kan användas för att driva intelligenta applikationer. Luis drivs av maskininlärning, men förståelse för maskininlärning är inte nödvändig för att använda den. Istället kommunicerar maskinlärare begrepp till LUIS genom att visa positiva och negativa exempel på konceptet och förklara hur ett koncept ska modelleras med andra relaterade begrepp. Lärare kan också förbättra LUIS:s modell interaktivt genom att identifiera och åtgärda förutsägelsemisstaget. 

## <a name="v3-authoring-model-decomposition"></a>V3 Författande modell sönderdelning

LUIS stöder _modellfördefall_ med V3-redigerings-API:erna och delar upp modellen i mindre delar. Detta gör att du kan bygga dina modeller med tillförsikt i hur de olika delarna är konstruerade och förutspådde.

Modelldepositionen har följande delar:

* [Avseenden](#intents-classify-utterances)
    * [deskriptorer](#descriptors-are-features) som tillhandahålls av funktioner
* [maskininlärda enheter](#machine-learned-entities)
    * [subcomponenter](#entity-subcomponents-help-extract-data) (även maskininlärda enheter)
        * [deskriptorer](#descriptors-are-features) som tillhandahålls av funktioner 
        * [begränsningar](#constraints-are-text-rules) som tillhandahålls av enheter som inte är maskininlärda, till exempel reguljära uttryck och listor

## <a name="v2-authoring-models"></a>V2 Författande modeller

LUIS stöder sammansatta entiteter med V2-redigerings-API:erna. Detta ger liknande modell sönderdelning men är inte samma sak som V3-modell sönderdelning. Den rekommenderade modellarkitekturen är att gå över till modellfördekomst i V3-redigerings-API:erna. 

## <a name="intents-classify-utterances"></a>Avsikter klassificera yttranden

En avsikt klassificerar exempel yttranden för att lära LUIS om avsikten. Exempel yttranden i en avsikt används som positiva exempel på yttrande. Samma yttranden används som negativa exempel i alla andra avsikter.

Tänk dig en app som måste bestämma en användares avsikt att beställa en bok och en app som behöver leveransadressen för kunden. Denna app har två `OrderBook` `ShippingLocation`avsikter: och .

Följande uttryck är ett **positivt** `OrderBook` exempel för avsikten `ShippingLocation` och `None` ett **negativt exempel** för och avsikter: 

`Buy the top-rated book on bot architecture.`

Resultatet av väl utformade avsikter, med deras exempel yttranden, är en hög avsikt förutsägelse. 

## <a name="entities-extract-data"></a>Entiteter extraherar data

En entitet representerar en dataenhet som du vill extrahera från uttrycket. 

### <a name="machine-learned-entities"></a>Maskininlärda enheter

En datorinlärd entitet är en enhet på den högsta nivån som innehåller delkomponenter, som också är maskininlärda entiteter. 

**Använd en maskininlärd entitet:**

* när delkomponenterna behövs av klientprogrammet
* för att hjälpa maskininlärningsalgoritmen att bryta ned entiteter

Varje delkomponent kan ha:

* Delkomponenter
* begränsningar (entitet eller listentiteten för reguljära uttryck)
* beskrivningar (funktioner som en fraslista) 

Ett exempel på en maskininlärd entitet är en order på en flygbiljett. Begreppsmässigt är detta en enda transaktion med många mindre dataenheter, till exempel datum, tid, antal platser, typ av säte som första klass eller coach, ursprungsplats, destinationsplats och måltidsval.


### <a name="entity-subcomponents-help-extract-data"></a>Underkomponenter för entitetskomponenter hjälper till att extrahera data

En underkomponent är en maskininlärd underordnad entitet inom en datorinlärd överordnad entitet. 

**Använd delkomponenten för att**:

* de delar av den maskininlärda entiteten (moderentitet).

Följande representerar en maskininlärd entitet med alla dessa separata data:

* TravelOrder (maskininlärd enhet)
    * DateTime (fördefinierad datetimeV2)
    * Plats (maskininlärd entitet)
        * Ursprung (roll som hittas genom sammanhang som `from`)
        * Mål (roll som hittas genom sammanhang som `to`)
    * Sittplatser (maskininlärd enhet)
        * Kvantitet (fördefinierat tal)
        * Kvalitet (maskininlärd entitet med deskriptor av fraslista)
    * Måltider (maskininlärd enhet med begränsning av listentitet som val av livsmedel)

En del av dessa data, till exempel ursprungsplatsen och målplatsen, bör läras av `from` uttryckssammanhanget, kanske med en sådan formulering som och `to`. Andra delar av data kan extraheras`Vegan`med exakta strängmatchningar ( ) eller `Seattle` `Cairo`fördefinierade enheter (geographyV2 of och ). 

Du utformar hur data matchas och extraheras med vilka modeller du väljer och hur du konfigurerar dem.

### <a name="constraints-are-text-rules"></a>Villkor är textregler

Ett villkor är en textmatchningsregel som tillhandahålls av en enhet som inte har lärt sig utan dator, till exempel entiteten för reguljära uttryck eller en listentitet. Begränsningen tillämpas vid förutsägelsetid för att begränsa förutsägelsen och tillhandahålla entitetsmatchning som behövs av klientprogrammet. Du definierar dessa regler när du skapar underkomponenten. 

**Använd ett villkor:**
* när du vet den exakta texten att extrahera.

Begränsningar inkluderar:

* [enheter för reguljära uttryck](reference-entity-regular-expression.md)
* [lista](reference-entity-list.md) entiteter 
* [fördefinierade](luis-reference-prebuilt-entities.md) enheter

Fortsätter med exemplet med flygbiljetten, kan flygplatskoderna vara i en lista enhet för exakt text matchningar. 

För en flygplatslista är listposten för Seattle `Seattle` stadens namn, och synonymerna för Seattle innehåller flygplatskoden för Seattle tillsammans med omgivande städer:

|`Seattle`Lista entitets synonymer|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Om du bara vill känna igen 3 bokstavskoder för flygplatskoder använder du ett reguljärt uttryck som begränsning. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Avsikter kontra entiteter

En avsikt är det önskade resultatet av _hela_ uttrycket medan entiteter är bitar av data som extraherats från uttrycket. Vanligtvis är avsikter kopplade till åtgärder som klientprogrammet ska vidta och entiteter är information som behövs för att utföra den här åtgärden. Ur ett programmeringsperspektiv skulle en avsikt utlösa ett metodanrop och entiteterna skulle användas som parametrar för det metodanropet.

Det här uttrycket _måste_ ha en avsikt och _kan_ ha entiteter:

`Buy an airline ticket from Seattle to Cairo`

Detta yttrande har en enda avsikt:

* Köpa en flygbiljett

Det här uttrycket _kan_ ha flera entiteter:

* Platser i Seattle (ursprung) och Kairo (destination)
* Kvantiteten på en enkel biljett

## <a name="descriptors-are-features"></a>Deskriptorer är funktioner

En deskriptor är en funktion som tillämpas på en modell vid utbildning, inklusive fraslistor och entiteter. 

**Använd en deskriptor när du vill:**

* öka betydelsen av ord och fraser som identifierats av deskriptorn
* har LUIS rekommendera ny text eller fraser att rekommendera för deskriptorn
* åtgärda ett fel på träningsdata

## <a name="next-steps"></a>Nästa steg

* Förstå [avsikter](luis-concept-intent.md) och [entiteter](luis-concept-entity-types.md). 