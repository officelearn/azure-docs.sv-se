---
title: Arbeta med stöd för ändrings flöden i Azure Cosmos DB
description: Använd Azure Cosmos DB ändra feed-stöd för att spåra ändringar i dokument och utföra händelse-baserad bearbetning som utlösare och hålla cacheminnen och analys system uppdaterade.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: eef950c4e8c4a880d331022ed60477bebce65b5d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689095"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Ändra feed i Azure Cosmos DB – översikt

Ändra stöd för feed i Azure Cosmos DB fungerar genom att lyssna på en Azure Cosmos-behållare för eventuella ändringar. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Ändringarna är beständiga, kan bearbetas asynkront och inkrementellt, och utdata kan distribueras bland en eller flera konsumenter för parallell bearbetning. 

Azure Cosmos DB passar bra för IoT-, spel-, detalj-och drift loggnings program. Ett gemensamt design mönster i dessa program är att använda ändringar av data för att utlösa ytterligare åtgärder. Exempel på ytterligare åtgärder är:

* Utlösa ett meddelande eller ett anrop till ett API när ett objekt infogas eller uppdateras.
* Data ström bearbetning i real tid för IoT-eller real tids analys bearbetning av drift data.
* Ytterligare data förflyttning genom att antingen synkronisera med en cache eller en sökmotor eller ett informations lager eller att arkivera data till kall lagring.

Med ändrings flödet i Azure Cosmos DB kan du bygga effektiva och skalbara lösningar för var och en av dessa mönster, som du ser i följande bild:

![Använda Azure Cosmos DB ändra feed till Power real tids analys och händelse drivna scenarier](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>API: er och klient-SDK som stöds

Den här funktionen stöds för närvarande av följande Azure Cosmos DB-API: er och klient-SDK: er.

| **Klient driv rutiner** | **Azure CLI** | **SQL-API** | **Azure Cosmos DB s API för Cassandra** | **Azure Cosmos DB s API för MongoDB** | **Gremlin-API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ej tillämpligt | Ja | Ja | Ja | Ja | Nej |
|Java|Ej tillämpligt|Ja|Ja|Ja|Ja|Nej|
|Python|Ej tillämpligt|Ja|Ja|Ja|Ja|Nej|
|Node/JS|Ej tillämpligt|Ja|Ja|Ja|Ja|Nej|

## <a name="change-feed-and-different-operations"></a>Ändra feed och olika åtgärder

Idag ser du alla åtgärder i ändrings flödet. Funktionerna där du kan styra ändrings flödet, för vissa åtgärder som endast uppdateringar och inte infogningar är ännu inte tillgängligt. Du kan lägga till en "mjuk markör" på objektet för uppdateringar och filter baserat på det när du bearbetar objekt i ändrings flödet. Den aktuella ändrings flödet loggar inte borttagningar. Precis som i föregående exempel kan du lägga till en mjuk markör för de objekt som tas bort, till exempel, du kan lägga till ett attribut i objektet som kallas "borttaget" och ange det som "true" och ange ett TTL-värde för objektet, så att det kan tas bort automatiskt. Du kan läsa ändrings flödet för historiska objekt (den senaste ändringen som motsvarar objektet, det innehåller inte mellanliggande ändringar), till exempel objekt som har lagts till fem år sedan. Om objektet inte tas bort kan du läsa ändrings flödet så långt som din behållares ursprung.

### <a name="sort-order-of-items-in-change-feed"></a>Sorterings ordning för objekt i ändrings flöde

Ändra feed-objekt levereras i rätt ordning efter ändrings tiden. Den här sorterings ordningen garanteras per logisk partitionsnyckel.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Ändra feed i Azure Cosmos-konton med flera regioner

I ett Azure Cosmos-konto med flera regioner, om en skrivnings region växlar över, kommer ändrings flödet att fungera i den manuella redundansväxlingen och det kommer att vara sammanhängande.

### <a name="change-feed-and-time-to-live-ttl"></a>Ändra feed och Time to Live (TTL)

Om en egenskap för ett TTL-värde (Time to Live) har angetts för ett objekt till-1 sparas ändrings flödet för alltid. Om data inte tas bort finns de kvar i ändrings flödet.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Ändra feed och _etag, _lsn eller _ts

_Etags formatet är internt och du bör inte ta beroende på det, eftersom det kan ändras när som helst. _ts är en ändring eller en tidsstämpel för skapande. Du kan använda _ts för kronologisk jämförelse. _lsn är ett batch-ID som endast läggs till för ändrings flöde. den representerar transaktions-ID: t. Många objekt kan ha samma _lsn. ETag på FeedResponse skiljer sig från _etag som visas på objektet. _etag är en intern identifierare och används för samtidighets kontroll, vilket visar om versionen av objektet, medan ETag används för att sekvensera flödet.

## <a name="change-feed-use-cases-and-scenarios"></a>Ändra användnings fall och scenarier för feed

Ändra feed möjliggör effektiv bearbetning av stora data uppsättningar med en stor mängd skrivningar. Ändra feed erbjuder också ett alternativ till att fråga en hel data uppsättning för att identifiera vad som har ändrats.

### <a name="use-cases"></a>Användningsfall

Med ändra feed kan du till exempel utföra följande uppgifter effektivt:

* Uppdatera en cache, uppdatera ett sökindex eller uppdatera ett informations lager med data som lagras i Azure Cosmos DB.

* Implementera data skiktning och arkivering på program nivå, till exempel lagra "heta data" i Azure Cosmos DB och åldras från "kalla data" till andra lagrings system, till exempel [Azure Blob Storage](../storage/common/storage-introduction.md).

* Utför migreringen från noll till ett annat Azure Cosmos-konto eller till en annan Azure Cosmos-behållare med en annan logisk partitionsnyckel.

* Implementera [lambda-arkitektur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) med hjälp av Azure Cosmos DB, där Azure Cosmos DB stöder både real tids-, batch-och frågebaserade lager, vilket möjliggör lambda-arkitektur med låg ägande kostnad.

* Ta emot och lagra händelse data från enheter, sensorer, infrastruktur och program och bearbeta dessa händelser i real tid, till exempel med hjälp av [Spark](../hdinsight/spark/apache-spark-overview.md).  Följande bild visar hur du kan implementera lambda-arkitektur med Azure Cosmos DB via ändra feed:

![Azure Cosmos DB-baserad lambda-pipeline för inmatning och fråga](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenarier

Nedan följer några av de scenarier som du enkelt kan implementera med ändrings flöden:

* [I dina webb-](https://azure.microsoft.com/solutions/serverless/) eller mobilappar kan du spåra händelser som alla ändringar i kundens profil, inställningar eller deras plats och utlösa vissa åtgärder, till exempel skicka push-meddelanden till sina enheter med hjälp av [Azure Functions](change-feed-functions.md).

* Om du använder Azure Cosmos DB för att bygga ett spel kan du till exempel använda ändra feed för att implementera ranknings listor i real tid baserat på poängen från färdiga spel.


## <a name="working-with-change-feed"></a>Arbeta med ändrings flöde

Du kan arbeta med ändrings flöden med följande alternativ:

* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda Change feed med Change feed processor](change-feed-processor.md) 

Ändrings flödet är tillgängligt för varje logisk partitionsnyckel i behållaren, och den kan distribueras över en eller flera konsumenter för parallell bearbetning som visas i bilden nedan.

![Distribuerad bearbetning av Azure Cosmos DB ändra feed](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funktioner i ändrings flöde

* Ändra feed är aktiverat som standard för alla Azure Cosmos-konton.

* Du kan använda ditt [etablerade data flöde](request-units.md) för att läsa från ändrings flödet, precis som andra Azure Cosmos DB åtgärder, i någon av de regioner som är kopplade till din Azure Cosmos-databas.

* Ändrings flödet innehåller infogningar och uppdaterings åtgärder som gjorts för objekt i behållaren. Du kan samla in borttagningar genom att ange flaggan "mjuk borttagning" i dina objekt (till exempel dokument) i stället för borttagningar. Alternativt kan du ange en begränsad förfallo period för dina objekt med TTL- [funktionen](time-to-live.md). Till exempel 24 timmar och Använd värdet för den egenskapen för att avbilda borttagningar. Med den här lösningen måste du bearbeta ändringarna inom ett kortare tidsintervall än förfallo tiden för TTL. 

* Varje ändring av ett objekt visas exakt en gång i ändrings flödet och klienterna måste hantera kontroll punkts logiken. Om du vill undvika komplexiteten med att hantera kontroll punkter, tillhandahåller Change feed-processorn automatisk kontroll punkt och "minst en gång"-semantik. Se [använda ändra feed med Change feed processor](change-feed-processor.md).

* Endast den senaste ändringen för ett angivet objekt ingår i ändrings loggen. Mellanliggande ändringar kanske inte är tillgängliga.

* Ändrings flödet sorteras efter ändrings ordningen i varje nyckel värde för logisk partition. Det finns ingen garanterad ordning för nyckel värden i partitionen.

* Ändringar kan synkroniseras från vilken tidpunkt som helst, eftersom det inte finns några fasta data lagrings perioder för vilka ändringar är tillgängliga.

* Ändringarna är tillgängliga parallellt för alla logiska partitionsnyckel för en Azure Cosmos-behållare. Den här funktionen gör det möjligt att bearbeta ändringar från stora behållare parallellt av flera konsumenter.

* Program kan begära flera ändrings flöden på samma behållare samtidigt. ChangeFeedOptions. StartTime kan användas för att ange en start punkt. Om du till exempel vill hitta en fortsättnings-token som motsvarar en angivet klock slag. ContinuationToken, om det anges, WINS över värdena StartTime och StartFromBeginning. Precisionen för ChangeFeedOptions. StartTime är ~ 5 sekunder. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API: er för Cassandra och MongoDB

Funktionen ändra feed fungerar som en ändrings ström i MongoDB API och fråga med predikat i API för Cassandra. Mer information om implementerings informationen för MongoDB-API finns i [ändrings strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Inbyggd Apache Cassandra tillhandahåller registrering av ändrings data (CDC), en mekanism för att flagga specifika tabeller för arkivering och avvisning av skrivningar till dessa tabeller när en konfigurerbar storlek-på disk för CDC-loggen nås. Funktionen ändra feed i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementerings informationen finns i [ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om ändrings flöden i följande artiklar:

* [Alternativ för att läsa ändrings flöde](read-change-feed.md)
* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda Change feed processor](change-feed-processor.md)
