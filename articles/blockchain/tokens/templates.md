---
title: Azure Blockchain-tokens mallar
description: Azure Blockchain Tokens-mallar är standardiserade och återanvändbara mallar som förenklar skapandet och distributionen av redovisningsbaserade token.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252212"
---
# <a name="azure-blockchain-tokens-templates"></a>Azure Blockchain-tokens mallar

[!INCLUDE [Preview note](./includes/preview.md)]

En Azure Blockchain Tokens-mall är en standardiserad och återanvändbar mall som förenklar skapandet och distributionen av redovisningsbaserade token. En mall består av en formel som baseras på [TTF-grammatiken (Token Taxonomy Framework).](overview.md#token-taxonomy-framework) Grammatiken omfattar bastokentypen och uppsättningen beteenden för token.  

**Tokenmallen τ{d,m,b,r}** beskriver till exempel en fungiibel bastoken som är delavdelningsbar, mintable, burnable och har rollstöd.
  
## <a name="base-token-types"></a>Bastokentyper

När du definierar och skapar den redovisningsbaserade token för just din tillgång är det viktigt att tänka på vilken bastoken som ska användas.

### <a name="fungible"></a>Utbytbara

Fungible tokens (τF) har utbytbart värde med varandra så länge de är i samma klass eller serie. En token har samma värde som en annan token eller en viss mängd token har samma värde som en annan lika kvantitet. En dollar är till exempel en fungibel token. Om två personer är var och en innehar en dollarsedel, kan de byta dessa dollarsedlar utan konsekvens. Dollarsedlarna har samma värde. 

### <a name="non-fungible"></a>Icke-fungibel

Icke-fungible tokens (τN) är inte utbytbara med andra token av samma typ som de vanligtvis har olika värden. En egenskapstitel är till exempel en token som inte är fungibel. Fastighetstitlar till två olika lägenheter i ett lägenhetskomplex är inte nödvändigtvis av samma värde, antingen på grund av placeringen av enheten eller vilken våning enheten är på. Det upplevda värdet för de två egenskapstiteltoken är inte lika.

### <a name="hybrid"></a>Hybrid

Hybridtoken är token som har komponenter i både fungible tokens och icke-fungible tokens. En hybridtoken är en bastokentyp som äger en klass av den andra tokentypen.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybrid icke-fungibel bas med fungible segment

En hybrid icke-fungibel bas med fungible segment token har en icke-fungible bas med fungibel token segment.
En konsertbiljett är till exempel en hybridtoken där datum och tid för konserten är den icke fungbara bastoken. Biljetterna i olika sittplatser för den givna konserten är segmenten med fungibla tokens. Biljetterna kan bytas ut i deras individuella sittsektioner, men inte över sektioner.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybrid fungibel bas med icke-fungible segment

En hybrid fungibel bas med en icke-fungibel segment token har en fungibel bas med icke-fungible token segment. Till exempel är en inteckning säkerhetsberättad säkerhet en hybrid token där flera ägare är fungible bas som är uppdelad över många ägare. Säkerheten är utbytbar. De enskilda inteckningar är de icke-fungible segment som representerar den specifika inteckning backas säkerhet.

## <a name="token-behaviors"></a>Token-beteenden

Ett tokenbeteende definierar tokens funktioner eller begränsningar. Beteendet omfattar stödegenskaper som är en del av tokendefinitionen. Beteenden kan tillämpas över alla tokentyper eller bara en. Beteenden kan vara interna eller externa beroende på vad beteendeeffekterna. Ett internt beteende aktiverar eller begränsar egenskaper på själva token. Ett externt beteende möjliggör eller begränsar anrop av beteendet från en extern aktör.

Mer information om Azure Blockchain-tokens stöds token taxonomi Framework (TTF) token beteenden, se [token composability](composability.md).

## <a name="pre-built-token-templates"></a>Fördefinierade tokenmallar

Azure Blockchain-tokens innehåller fyra färdiga tokenmallar som kan användas utan ändringar. Du kan anropa dessa färdiga mallar för de flesta användningsfall för att komma igång med att skapa, distribuera och hantera dina token snabbt.

### <a name="commodity-tokens"></a>Råvarutoken

Råvarutoken har konsekvent värde och kan överlåtas. Till exempel ett fat olja eller en energienhet.

**τF{~d,t,m,b,r}** - fungibel, hel, överförbar, mintable, brännbar, och har rollstöd

Många blockkedjescenarier kräver transparens och synlighet i hela leveranskedjan eller flera organisationer. Råvarutoken baseras på dessa vanliga användningsfall. Tokens är utbytbara och konsekventa. Artikeltokenmallen är flexibel och anpassningsbar med metadata.

### <a name="qualified-tokens"></a>Kvalificerade tokens

Kvalificerade tokens representerar något intjänat och associeras vanligtvis med en entitet och kan inte överföras. Till exempel ett diplom eller en parkering kränkning.

**τN{s,~t}** - icke-fungibel, singleton och ej överförbar

Olika gransknings- och attestation-scenarier kräver att ägarskapet för token inte kan ändras. Det finns en uppsättning användningsfall som har ett behov av att ange en kvalificerad token om associationen är bra eller dålig.

### <a name="asset-tokens"></a>Tillgångstoken

Tillgångstoken har unikt värde som är beroende av artikeln och är inte commoditized. Till exempel en museiartefakt eller en egenskapstitel.

**τN{s,t}** - icke-fungibel, singleton och överförbar

Tillgångstoken kan förväxlas med råvarutoken. Den största skillnaden mellan de två token är att tillgångstoken är unika och värdet är oberoende av vilken typ av token det är. Till exempel är ett konstverk som en oljemålning av en etablerad konstnär en tillgång token. Ett konsttryck av Mona Lisa anses dock vara en handelsvara. På samma sätt är en egenskapstitel en tillgångstoken eftersom värdet finns i egenskapens subjektiva egenskaper.

### <a name="ticket-tokens"></a>Biljetttoken

Biljetttoken har konsekvent värde men upphör vanligtvis att gälla. Till exempel en flygbiljett.

**τN{m,b,r}** - icke-fungibel, mintable, burnable, och har rollstöd.

Biljetttoken har vanligtvis ett utgångsdatum som skiljer dem från en vanlig varutoken. Till exempel har en flygbiljett, konsertbiljett eller sportbiljett alla alternativ för tilldelade sittplatser med specifika användningsdatum. Du kan inte enkelt byta biljetter mellan datum eller sittgrupp.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer flexibilitet för ditt scenario kan du läsa om hur du skapar egna tokenmallar med hjälp av [tokenkomposterbarhet](composability.md).
