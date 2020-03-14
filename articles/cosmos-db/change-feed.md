---
title: Arbeta med stöd för ändringsflödet i Azure Cosmos DB
description: Använd Azure Cosmos DB ändra feed-stöd för att spåra ändringar i dokument, händelsebaserade bearbetningar som utlösare och hålla cacheminnen och analys system uppdaterade
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368136"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Ändra feed i Azure Cosmos DB – översikt

Stödet för ändringsflöde i Azure Cosmos DB fungerar genom att lyssna efter ändringar på en Azure Cosmos-container. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Ändringarna är beständiga, kan bearbetas asynkront och inkrementellt, och utdata kan distribueras bland en eller flera konsumenter för parallell bearbetning. 

Azure Cosmos DB är passar bra för IoT, spel, återförsäljnings, och operativa loggningsprogram. Ett vanliga designmönster i dessa program är att använda ändringar av data för att utlösa ytterligare åtgärder. Exempel på ytterligare åtgärder:

* Utlösa en avisering eller ett anrop till ett API när ett objekt infogas eller uppdateras.
* Strömningsbearbetning för IoT- eller bearbetning av användningsdata analys i realtid.
* Ytterligare dataförflyttning genom synkronisering med en cache eller en sökmotor eller ett informationslager eller arkivering av data till kall lagring.

Ändringsflödet i Azure Cosmos DB kan du bygga skalbara lösningar för var och en av dessa mönster som du ser i följande bild:

![Med hjälp av Azure Cosmos DB ändringen feed power realtidsanalys och händelsedrivna scenarier för högprestandaberäkning](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Stöds API: er och klient-SDK: er

Den här funktionen stöds för närvarande av följande Azure Cosmos DB API: er och klient-SDK: er.

| **Klient driv rutiner** | **Azure CLI** | **SQL-API** | **Azure Cosmos DB s API för Cassandra** | **Azure Cosmos DB s API för MongoDB** | **Gremlin-API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ej tillämpligt | Ja | Ja | Ja | Ja | Nej |
|Java|Ej tillämpligt|Ja|Ja|Ja|Ja|Nej|
|Python|Ej tillämpligt|Ja|Ja|Ja|Ja|Nej|
|Noden/JS|Ej tillämpligt|Ja|Ja|Ja|Ja|Nej|

## <a name="change-feed-and-different-operations"></a>Ändringsfeed och olika åtgärder

Idag kan du se alla åtgärder i den ändringsflödet. Funktionen där du kan kontrollera ändringsflödet, för specifika åtgärder som endast uppdateringar och inte infogningar inte är tillgänglig ännu. Du kan lägga till en "mjuk markör" på objektet för uppdateringar och filter baserat på det när du bearbetar objekt i ändrings flödet. Den aktuella ändrings flödet loggar inte borttagningar. På liknande sätt som i föregående exempel, du kan lägga till en mjuk markör på de objekt som tas bort, t.ex, du kan lägga till ett attribut i objektet kallas ”borttagen” och ge den värdet ”true” och ange TTL på objektet, så att den kan tas bort automatiskt. Du kan läsa ändrings flödet för historiska objekt (den senaste ändringen som motsvarar objektet, det innehåller inte mellanliggande ändringar), till exempel objekt som har lagts till fem år sedan. Om objektet inte tas bort. Du kan läsa ändringen feed ur ursprunget behållarens.

### <a name="sort-order-of-items-in-change-feed"></a>Sortera ordningen på objekten i ändringsfeed

Ändra flödesobjekt kommer i den ordning som deras ändringstid. Den här sorterings ordningen garanteras per logisk partitionsnyckel.

### <a name="consistency-level"></a>Konsekvens nivå

När du förbrukar ändrings flödet i en eventuell konsekvens nivå, kan det finnas dubbla händelser i-mellan efterföljande Läs åtgärder för ändrings flöden (den sista händelsen i en Läs åtgärd visas som första av nästa).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Ändringsflödet i Azure Cosmos-konton för flera regioner

I ett Azure Cosmos-konto för flera regioner, om en skrivregion redundansväxlar ändringsfeed fungerar i åtgärden manuell växling vid fel och det är sammanhängande.

### <a name="change-feed-and-time-to-live-ttl"></a>Ändringsfeed och Time to Live (TTL)

Om en egenskap för TTL (Time to Live) har angetts för ett objekt till-1, behålls ändringsfeed alltid. Om data inte tas bort, förblir den i den ändringsflödet.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Ändringsfeed och _etag, _lsn eller _ts

Formatet _etag är intern och du bör inte koppla beroende på det, eftersom den kan ändras när som helst. _ts är en ändring av eller en tidsstämpel vid skapande. Du kan använda _ts kronologisk jämförelse. _lsn är ett batch-ID som endast läggs till för ändrings flöde. den representerar transaktions-ID: t. Många objekt som kan ha samma _lsn. ETag på FeedResponse skiljer sig från _etag som du ser på objektet. _etag är en intern identifierare och används för samtidighet kontroll meddelar om versionen av objektet medan ETag används för ordningsföljd feeden.

## <a name="change-feed-use-cases-and-scenarios"></a>Ändringsfeed användningsområden och scenarier

Ändringsfeed möjliggör effektiv bearbetning av stora datauppsättningar med ett stort antal skrivningar. Ändringsfeed erbjuder också ett alternativ till att fråga en hel datauppsättning för att identifiera vad som har ändrats.

### <a name="use-cases"></a>Användningsfall

Till exempel med ändringsflödet utföra du följande uppgifter effektivt:

* Uppdatera ett cacheminne, uppdatera ett sökindex eller uppdatera ett informationslager med data som lagras i Azure Cosmos DB.

* Implementera data skiktning och arkivering på program nivå, till exempel lagra "heta data" i Azure Cosmos DB och åldras från "kalla data" till andra lagrings system, till exempel [Azure Blob Storage](../storage/common/storage-introduction.md).

* Utföra noll driftstopp migreringar till en annan Azure-Cosmos-konto eller en annan Azure Cosmos-behållare med en annan logisk partitionsnyckel.

* Implementera [lambda-arkitektur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) med hjälp av Azure Cosmos DB, där Azure Cosmos DB stöder både real tids-, batch-och frågebaserade lager, vilket möjliggör lambda-arkitektur med låg ägande kostnad.

* Ta emot och lagra händelse data från enheter, sensorer, infrastruktur och program och bearbeta dessa händelser i real tid, till exempel med hjälp av [Spark](../hdinsight/spark/apache-spark-overview.md).  Följande bild visar hur du kan implementera lambda-arkitektur med Azure Cosmos DB via ändringsfeed:

![Azure Cosmos DB-baserade lambda-pipeline för skrivbelastning och frågeprestanda](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenarier

Här följer några scenarier som du enkelt kan implementera med ändringsfeed:

* [I dina webb-](https://azure.microsoft.com/solutions/serverless/) eller mobilappar kan du spåra händelser som alla ändringar i kundens profil, inställningar eller deras plats och utlösa vissa åtgärder, till exempel skicka push-meddelanden till sina enheter med hjälp av [Azure Functions](change-feed-functions.md).

* Om du använder Azure Cosmos DB för att skapa ett spel, kan du, till exempel använda ändringsflödet att implementera i realtid rankningslistor baserat på poäng från färdiga spel.


## <a name="working-with-change-feed"></a>Arbeta med ändringsfeed

Du kan arbeta med ändringsflödet med hjälp av följande alternativ:

* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda Change feed med Change feed processor](change-feed-processor.md) 

Ändringsfeed är tillgängliga för varje logisk partitionsnyckel i behållaren och den kan distribueras på en eller flera konsumenter för parallell bearbetning, enligt bilden nedan.

![Distribuerad bearbetning av Azure Cosmos DB-ändringsflödet](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funktioner i ändringsfeed

* Ändringsfeed aktiveras som standard för alla Azure Cosmos-konton.

* Du kan använda ditt [etablerade data flöde](request-units.md) för att läsa från ändrings flödet, precis som andra Azure Cosmos DB åtgärder, i någon av de regioner som är kopplade till din Azure Cosmos-databas.

* Ändringsflöde innehåller INSERT och update-åtgärder som utförs för objekt i behållaren. Du kan avbilda borttagningar genom att ange en ”mjuk borttagning”-flagga i dina objekt (till exempel dokument) i stället för borttagningar. Alternativt kan du ange en begränsad förfallo period för dina objekt med TTL- [funktionen](time-to-live.md). Exempel: 24 timmar och Använd värdet för egenskapen att samla in tas bort. Med den här lösningen har att bearbeta ändringarna inom ett kortare tidsintervall än TTL giltighetsperiod. 

* Varje ändring till ett objekt visas exakt en gång i den ändringsflödet och klienterna måste hantera kontrollpunkter logiken. Om du vill undvika komplexiteten med att hantera kontroll punkter, tillhandahåller Change feed-processorn automatisk kontroll punkt och "minst en gång"-semantik. Se [använda ändra feed med Change feed processor](change-feed-processor.md).

* Den senaste ändringen för ett visst objekt ingår i ändringsloggen. Mellanliggande ändringar är kanske inte tillgänglig.

* Ändringsflöde sorteras av ordningen ändras inom varje logisk partitionsnyckelvärdet. Det finns ingen garanterad ordning över partitionsnyckelvärdena.

* Ändringar kan synkroniseras från alla point-in-time, som är att det finns ingen fast Datalagringsperiod ändringar är tillgängliga.

* Ändringar är tillgängliga samtidigt för alla logisk partitionsnycklarna för en Azure Cosmos-behållare. Den här funktionen kan ändringar från stora behållare som ska bearbetas parallellt av flera användare.

* Program kan begära flera ändrings flöden på samma behållare samtidigt. ChangeFeedOptions.StartTime kan användas för att ge en första startpunkt. Till exempel vill hitta fortsättningstoken som motsvarar en viss clock-tid. ContinuationToken, vinner om anges över värdena StartTime och StartFromBeginning. Precisionen för ChangeFeedOptions.StartTime är ~ 5 sekunder. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API: er för Cassandra och MongoDB

Funktionen ändra feed fungerar som en ändrings ström i MongoDB API och fråga med predikat i API för Cassandra. Mer information om implementerings informationen för MongoDB-API finns i [ändrings strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Inbyggd Apache Cassandra tillhandahåller registrering av ändrings data (CDC), en mekanism för att flagga specifika tabeller för arkivering och avvisning av skrivningar till dessa tabeller när en konfigurerbar storlek-på disk för CDC-loggen nås. Funktionen ändra feed i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementerings informationen finns i [ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Alternativ för att läsa ändrings flöde](read-change-feed.md)
* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda Change feed processor](change-feed-processor.md)
