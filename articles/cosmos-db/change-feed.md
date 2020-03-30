---
title: Arbeta med stöd för ändringsfeed i Azure Cosmos DB
description: Använd Azure Cosmos DB ändra feedstöd för att spåra ändringar i dokument, händelsebaserad bearbetning som utlösare och hålla cacheminnen och analytiska system uppdaterade
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368136"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Ändringsflödet i Azure Cosmos DB – översikt

Stödet för ändringsflöde i Azure Cosmos DB fungerar genom att lyssna efter ändringar på en Azure Cosmos-container. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Ändringarna är beständiga, kan bearbetas asynkront och inkrementellt, och utdata kan distribueras bland en eller flera konsumenter för parallell bearbetning. 

Azure Cosmos DB är väl lämpad för IoT-, spel-, butiks- och driftloggningsprogram. Ett vanligt designmönster i dessa program är att använda ändringar i data för att utlösa ytterligare åtgärder. Exempel på ytterligare åtgärder är:

* Utlöser ett meddelande eller ett anrop till ett API när ett objekt infogas eller uppdateras.
* Direktuppspelningsbearbetning i realtid för IoT- eller realtidsanalysbearbetning på driftdata.
* Ytterligare dataförflyttning genom att antingen synkronisera med en cache eller en sökmotor eller ett informationslager eller arkivera data till kyllagring.

Ändringsflödet i Azure Cosmos DB gör att du kan skapa effektiva och skalbara lösningar för vart och ett av dessa mönster, vilket visas i följande avbildning:

![Använda Azure Cosmos DB ändra foder för att driva analyser i realtid och händelsedrivna datorscenarier](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>API:er och klient-SDK:er som stöds

Den här funktionen stöds för närvarande av följande Azure Cosmos DB API:er och klient-SDK:er.

| **Klientdrivrutiner** | **Azure CLI** | **SQL-API** | **Azure Cosmos DB:s API för Cassandra** | **API för Azure Cosmos DB för MongoDB** | **Gremlin API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ej tillämpligt | Ja | Ja | Ja | Ja | Inga |
|Java|Ej tillämpligt|Ja|Ja|Ja|Ja|Inga|
|Python|Ej tillämpligt|Ja|Ja|Ja|Ja|Inga|
|Nod/JS|Ej tillämpligt|Ja|Ja|Ja|Ja|Inga|

## <a name="change-feed-and-different-operations"></a>Ändra foder och olika operationer

Idag ser du alla åtgärder i ändringsflödet. Funktionerna där du kan styra ändringsflödet, för specifika åtgärder som endast uppdateringar och inte infogningar är ännu inte tillgängliga. Du kan lägga till en "mjuk markör" på objektet för uppdateringar och filter baserat på det när du bearbetar objekt i ändringsflödet. För närvarande loggas inte borttagningar. I likhet med föregående exempel kan du lägga till en mjuk markör på de objekt som tas bort, till exempel kan du lägga till ett attribut i objektet som kallas "borttaget" och ställa in det på "true" och ange en TTL på objektet, så att det kan tas bort automatiskt. Du kan läsa ändringsflödet för historiska objekt (den senaste ändringen som motsvarar objektet, den innehåller inte mellanliggande ändringar), till exempel objekt som lades till för fem år sedan. Om objektet inte tas bort kan du läsa ändringsflödet när det gäller behållarens ursprung.

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

## <a name="change-feed-use-cases-and-scenarios"></a>Ändra användningsfall och scenarier för feed

Ändringsflödet möjliggör effektiv bearbetning av stora datauppsättningar med en hög mängd skrivningar. Ändringsfeeden erbjuder också ett alternativ till att fråga en hel datauppsättning för att identifiera vad som har ändrats.

### <a name="use-cases"></a>Användningsfall

Med ändringsflödet kan du till exempel utföra följande uppgifter effektivt:

* Uppdatera en cache, uppdatera ett sökindex eller uppdatera ett informationslager med data som lagras i Azure Cosmos DB.

* Implementera en datanivå och arkivering på programnivå, till exempel lagra "heta data" i Azure Cosmos DB och åldras "kalla data" till andra lagringssystem, till exempel [Azure Blob Storage](../storage/common/storage-introduction.md).

* Utför noll nedtidsmigreringar till ett annat Azure Cosmos-konto eller en annan Azure Cosmos-behållare med en annan logisk partitionsnyckel.

* Implementera [lambda-arkitektur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) med Azure Cosmos DB, där Azure Cosmos DB stöder både lager för realtids-, batch- och frågebetjänare, vilket möjliggör lambda-arkitektur med låg TCO.

* Ta emot och lagra händelsedata från enheter, sensorer, infrastruktur och program och bearbeta dessa händelser i realtid, till exempel med [Spark](../hdinsight/spark/apache-spark-overview.md).  Följande avbildning visar hur du kan implementera lambda-arkitektur med Azure Cosmos DB via ändringsflödet:

![Azure Cosmos DB-baserad lambda-pipeline för inmatning och fråga](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenarier

Följande är några av de scenarier som du enkelt kan implementera med ändringsflöde:

* I din [serverlösa](https://azure.microsoft.com/solutions/serverless/) webb eller mobilappar kan du spåra händelser som alla ändringar i kundens profil, inställningar eller deras plats och utlösa vissa åtgärder, till exempel skicka push-meddelanden till sina enheter med Hjälp av [Azure Functions](change-feed-functions.md).

* Om du använder Azure Cosmos DB för att skapa ett spel kan du till exempel använda ändringsflödet för att implementera rankningslistor i realtid baserat på poäng från slutförda spel.


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

* Program kan begära flera ändringsflöden på samma behållare samtidigt. ChangeFeedOptions.StartTime kan användas för att ange en första startpunkt. Om du till exempel vill hitta fortsättningstoken som motsvarar en viss klocktid. Fortsättningstoken, om det anges, vinner över starttids- och startfrånstartsvärdena. Precisionen i ChangeFeedOptions.StartTime är ~5 sekunder. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API:er för Cassandra och MongoDB

Ändra matningsfunktioner visas som ändringsström i MongoDB API och Fråga med predikat i Cassandra API. Mer information om implementeringsinformationen för MongoDB API finns [i Ändra strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Native Apache Cassandra ger förändring datainsamling (CDC), en mekanism för att flagga specifika tabeller för arkivering samt avvisa skrivningar till dessa tabeller när en konfigurerbar storlek-på-disk för CDC loggen nås. Ändringsfeedfunktionen i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementeringsinformationen finns [i Ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödet i följande artiklar:

* [Alternativ för att läsa ändringsflöde](read-change-feed.md)
* [Använda ändringsfeed med Azure Functions](change-feed-functions.md)
* [Använda ändringsmatningsprocessor](change-feed-processor.md)
