---
title: Arbeta med stöd för ändringsfeed i Azure Cosmos DB
description: Använd Azure Cosmos DB ändra feedstöd för att spåra ändringar i dokument, händelsebaserad bearbetning som utlösare och hålla cacheminnen och analytiska system uppdaterade
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984869"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Ändringsflöde i Azure Cosmos DB

Stödet för ändringsflöde i Azure Cosmos DB fungerar genom att lyssna efter ändringar på en Azure Cosmos-container. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Ändringarna är beständiga, kan bearbetas asynkront och inkrementellt, och utdata kan distribueras bland en eller flera konsumenter för parallell bearbetning.

Läs mer om [att ändra mönster för flödesdesign](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>API:er och klient-SDK:er som stöds

Den här funktionen stöds för närvarande av följande Azure Cosmos DB API:er och klient-SDK:er.

| **Klientdrivrutiner** | **SQL-API** | **Azure Cosmos DB:s API för Cassandra** | **API för Azure Cosmos DB för MongoDB** | **Gremlin-API**|**Tabell-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ja | Ja | Ja | Ja | Inga |
|Java|Ja|Ja|Ja|Ja|Inga|
|Python|Ja|Ja|Ja|Ja|Inga|
|Nod/JS|Ja|Ja|Ja|Ja|Inga|

## <a name="change-feed-and-different-operations"></a>Ändra foder och olika operationer

Idag visas alla infogningar och uppdateringar i ändringsflödet. Du kan inte filtrera ändringsflödet för en viss typ av åtgärd. Ett möjligt alternativ är att lägga till en "mjuk markör" på objektet för uppdateringar och filter baserat på det när artiklar i ändringsflödet bearbetas.

För närvarande loggas inte borttagningar. I likhet med föregående exempel kan du lägga till en mjuk markör på de objekt som tas bort. Du kan till exempel lägga till ett attribut i objektet "borttaget" och ange det till "true" och ange en TTL för objektet, så att det kan tas bort automatiskt. Du kan läsa ändringsflödet för historiska objekt (den senaste ändringen som motsvarar objektet, den innehåller inte mellanliggande ändringar), till exempel objekt som lades till för fem år sedan. Du kan läsa ändringsflödet så långt tillbaka som ursprunget för din behållare, men om ett objekt tas bort tas det bort från ändringsflödet.

### <a name="sort-order-of-items-in-change-feed"></a>Sortera ordning för objekt i ändringsflödet

Ändra foderobjekt kommer i den ordning de ändras. Den här sorteringsordningen garanteras per logisk partitionsnyckel.

### <a name="consistency-level"></a>Konsekvensnivå

När du konsumerar ändringsflödet på en eventuell konsekvensnivå kan det finnas dubbletthändelser mellan efterföljande ändringsflödesläsningsåtgärder (den sista händelsen i en läsåtgärd visas som den första i nästa).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Ändra feed i Azure Cosmos-konton med flera regioner

I ett Azure Cosmos-konto med flera regioner, om en skrivregion växlar över, kommer ändringsflödet att fungera i den manuella redundansåtgärden och det kommer att vara sammanhängande.

### <a name="change-feed-and-time-to-live-ttl"></a>Ändra flöde och tid till Live (TTL)

Om en TTL-egenskap (Time to Live) anges på ett objekt till -1, kommer ändringsflödet att finnas kvar för alltid. Om data inte tas bort finns de kvar i ändringsflödet.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Ändra foder och _etag, _lsn eller _ts

Formatet _etag är internt och du bör inte vara beroende av det, eftersom det kan ändras när som helst. _ts är en ändrings- eller tidsstämpel för att skapa. Du kan använda _ts för kronologisk jämförelse. _lsn är ett batch-ID som läggs till endast för ändringsflödet. Det representerar transaktions-ID. Många objekt kan ha samma _lsn. ETag på FeedResponse skiljer sig från _etag du ser på objektet. _etag är en intern identifierare och används för samtidighetskontroll berättar om versionen av artikeln, medan ETag används för sekvensering av flödet.

## <a name="working-with-change-feed"></a>Arbeta med ändringsflöde

Du kan arbeta med ändringsflödet med följande alternativ:

* [Använda ändringsfeed med Azure Functions](change-feed-functions.md)
* [Använda ändringsflöde med ändringsmatningsprocessor](change-feed-processor.md) 

Ändringsfeed är tillgänglig för varje logisk partitionsnyckel i behållaren, och den kan distribueras över en eller flera konsumenter för parallell bearbetning som visas i bilden nedan.

![Distribuerad bearbetning av Azure Cosmos DB-ändringsflöde](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funktioner för förändring foder

* Ändringsfeed är aktiverad som standard för alla Azure Cosmos-konton.

* Du kan använda ditt etablerade dataflöde för att läsa från [ändringsflödet,](request-units.md) precis som alla andra Azure Cosmos DB-åtgärder, i någon av de regioner som är associerade med din Azure Cosmos-databas.

* Ändringsflödet innehåller skär och uppdateringsåtgärder som gjorts till artiklar i behållaren. Du kan ta bort borttagningar genom att ange en "mjuk borttagning" flagga i dina objekt (till exempel dokument) i stället för borttagningar. Du kan också ange en begränsad förfalloperiod för dina artiklar med [TTL-funktionen](time-to-live.md). Till exempel 24 timmar och använd värdet för den egenskapen för att fånga bort. Med den här lösningen måste du bearbeta ändringarna inom ett kortare tidsintervall än TTL-utgångsperioden.

* Varje ändring av ett objekt visas exakt en gång i ändringsflödet och klienterna måste hantera kontrollpunktslogiken. Om du vill undvika komplexiteten i att hantera kontrollpunkter ger ändringsmatningsbehandlaren automatisk kontrollpunkt och "minst en gång" semantik. Se [använda ändringsflödet med ändringsmatningsprocessor .](change-feed-processor.md)

* Endast den senaste ändringen för ett visst objekt inkluderas i ändringsloggen. Mellanliggande ändringar kanske inte är tillgängliga.

* Ändringsfeeden sorteras efter ändringsordningen inom varje logiskt partitionsnyckelvärde. Det finns ingen garanterad ordning över partitionsnyckelvärdena.

* Ändringar kan synkroniseras från vilken tidpunkt som helst, det vill än att det inte finns någon fast datalagringsperiod för vilken ändringar är tillgängliga.

* Ändringar är tillgängliga parallellt för alla logiska partitionsnycklar i en Azure Cosmos-behållare. Den här funktionen gör att ändringar från stora behållare kan bearbetas parallellt av flera konsumenter.

* Program kan begära flera ändringsflöden på samma behållare samtidigt. ChangeFeedOptions.StartTime kan användas för att ange en första startpunkt. Om du till exempel vill hitta fortsättningstoken som motsvarar en viss klocktid. ContinuationToken har, om det anges, företräde framför starttids- och startfrånbesningsvärdena. Precisionen i ChangeFeedOptions.StartTime är ~5 sekunder.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API:er för Cassandra och MongoDB

Ändra matningsfunktioner visas som ändringsström i MongoDB API och Fråga med predikat i Cassandra API. Mer information om implementeringsinformationen för MongoDB API finns [i Ändra strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Native Apache Cassandra ger förändring datainsamling (CDC), en mekanism för att flagga specifika tabeller för arkivering samt avvisa skrivningar till dessa tabeller när en konfigurerbar storlek-på-disk för CDC loggen nås. Ändringsfeedfunktionen i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementeringsinformationen finns [i Ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödet i följande artiklar:

* [Alternativ för att läsa ändringsflöde](read-change-feed.md)
* [Använda ändringsfeed med Azure Functions](change-feed-functions.md)
* [Använda ändringsmatningsprocessor](change-feed-processor.md)
