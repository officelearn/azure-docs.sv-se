---
title: Mallar för Azure blockchain-token
description: Mallar för Azure blockchain-token är standardiserade och återanvändbara mallar som fören klar skapandet och distributionen av Ledger-baserade tokens.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360752"
---
# <a name="azure-blockchain-tokens-templates"></a>Mallar för Azure blockchain-token

[!INCLUDE [Preview note](./includes/preview.md)]

En mall för Azure blockchain-token är en standardiserad och återanvändbar mall som fören klar skapandet och distributionen av redovisningsbaserade token. En mall består av en formel som baseras på grammatiken för [token for Framework (ttf)](overview.md#token-taxonomy-framework) . Grammatiken omfattar bastypen och uppsättningen beteenden för token.  

Till exempel beskriver **τϜ {d, m, b, r}** en utbytbara-basadress som är underordnad, mintable, brännings och har roll stöd.
  
## <a name="base-token-types"></a>Bas-token-typer

När du definierar och skapar den redovisnings-baserade token för en viss till gång är det viktigt att fundera över vilken bas-token som ska användas.

### <a name="fungible"></a>Utbytbara

Utbytbara-token (τF) har utbytbara värden med varandra så länge de är i samma klass eller serie. En token har samma värde som en annan token eller en specifik kvantitet token har samma värde som en annan lika stor kvantitet. En dollar är till exempel en utbytbara-token. Om två personer är anställda på en dollar faktura kan de byta ut dessa dollar räkningar utan att detta sker. Dollar räkningar har samma värde. 

### <a name="non-fungible"></a>Icke-utbytbara

Icke-utbytbara token (τN) är inte utbytbara med andra tokens av samma typ som vanligt vis har olika värden. En egenskaps rubrik är till exempel en icke-utbytbara token. Egenskaps titlar till två olika lägenheter i en lägenhet komplex är inte nödvändigt vis lika värde, på grund av antingen platsen för enheten eller vilket golv enheten är på. Det uppfattade värdet för de två egenskaps rubrikens tokens är inte lika med.

### <a name="hybrid"></a>Hybrid

Hybrid-token är token som har komponenter med både utbytbara-token och icke-utbytbara-token. En hybrid-token är en grundläggande tokentyp som äger en klass av den andra tokentypen.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybrid icke-utbytbara Base med utbytbara segment

En hybrid icke-utbytbara Base med utbytbara segments-token har en icke-utbytbara Base med utbytbara token-segment.
Till exempel är en konsert biljett en hybrid-token där datum och tid för konsert är den icke-utbytbara bas-token. Biljetterna i olika plats avsnitt för den aktuella konserten är segmenten med utbytbara-token. Biljetterna kan bytas ut mot sitt eget säte, men inte mellan avsnitt.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybrid utbytbara-bas med icke-utbytbara segment

En hybrid utbytbara-bas med en icke-utbytbara segment-token har en utbytbara-bas med icke-utbytbara token-segment. En amorterings säkerhets kopia är till exempel en hybrid-token där flera ägare är utbytbara-basen som delas mellan många ägare. Säkerheten är utbytbar. De enskilda amorteringarna är de icke-utbytbara segment som representerar den specifika amorterings säkerheten.

## <a name="token-behaviors"></a>Token-beteenden

Ett token-beteende definierar funktioner eller begränsningar för token. Beteendet omfattar stöd för egenskaper som ingår i definitions definitionen för token. Beteenden kan tillämpas på alla typer av token eller bara en. Beteenden kan vara interna eller externa beroende på vad beteendet påverkar. Ett internt beteende aktiverar eller begränsar egenskaperna för själva token. Ett externt beteende aktiverar eller begränsar anropet av beteendet från en extern aktör.

Mer information om Azure blockchain-token som stöds av TTF-token i token i token [i token](composability.md)

## <a name="pre-built-token-templates"></a>Förbyggda token-mallar

Azure blockchain-token innehåller fyra förskapade token-mallar som kan användas utan ändringar. Du kan anropa dessa färdiga mallar för de flesta användnings fall för att komma igång med att skapa, distribuera och hantera dina tokens snabbt.

### <a name="commodity-tokens"></a>Token för råvaru

Token för token har konsekvent värde och kan deferres. Till exempel en FAT-eller enhets energi.

**τF {~ d, t, m, b, r}** -utbytbara, helhet, överförings bara, mintable, brännings bara och har stöd för roller

Många blockchain-scenarier kräver genomskinlighet och insyn i leverans kedjan eller flera organisationer. Råvaru-token baseras på dessa vanliga användnings fall. Token är utbytbara och konsekventa. Råvaru token-mallen är flexibel och anpassningsbar med metadata.

### <a name="qualified-tokens"></a>Kvalificerade token

Kvalificerade token representerar ett intjänat och är vanligt vis kopplat till en entitet och kan inte överföras. Till exempel ett diplom eller en parkerings överträdelse.

**τN {s, ~ t}** -icke-utbytbara, singleton och kan inte överföras

Olika gransknings-och attesterings scenarier kräver att ägarskapet till token inte kan ändras. Det finns en uppsättning användnings fall som måste tillhandahålla en kvalificerad token om associationen är bra eller felaktig.

### <a name="asset-tokens"></a>Till gångs-token

Till gångs-token har unika värden beroende av objektet och är inte commoditized. Till exempel en Museum-artefakt eller en egenskaps rubrik.

**τN {s, t}** -icke-utbytbara, singleton och överförings bara

Till gångs-token kan vara förvirrande med token för råvaru. Den största skillnaden mellan de två tokens är att till gångens token är unika och att värdet är oberoende av vilken typ av token det är. Till exempel är en bild som en olje målning av en etablerad artist en till gångs-token. En art utskrift av Mona-Lisa betraktas dock som en token för en token. På samma sätt är en egenskaps rubrik en till gångs-token eftersom värdet finns i egenskapens egenskaper.

### <a name="ticket-tokens"></a>Biljett-token

Biljett-token har konsekvent värde men går normalt ut. Till exempel en plan-biljett.

**τN {m, b, r}** -icke-utbytbara, mintable, brännings bara och har stöd för roller.

Biljett-token har vanligt vis ett utgångs datum som gör dem annorlunda än en vanlig token. Till exempel har ett flyg Plans biljett, en konsert biljett eller en idrotts biljett alla alternativ för tilldelad plats med angivna användnings datum. Det går inte att enkelt byta biljetter mellan datum eller plats områden.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer flexibilitet för ditt scenario kan du läsa om hur du skapar dina egna tokens med hjälp av DataStore- [datasammansättning](composability.md).
