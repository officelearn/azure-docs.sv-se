---
title: Arbeta med stöd för ändringsflödet i Azure Cosmos DB
description: Använd feed support för Azure Cosmos DB-ändring för att spåra ändringar i dokument och utföra händelsebaserade bearbetning som utlösare och uppdatera cacheminnen- och analyssystem kontinuerligt.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e21058d47f554ca4a057ab90433895800fb17dd9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886731"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Ändringsflödet i Azure Cosmos DB - översikt

Stöd för ändringsfeed i Azure Cosmos DB fungerar genom att lyssna på en Azure Cosmos DB-behållare efter ändringar. Sedan returnerar den sorterade listan över dokument som har ändrats i den ordning som de har ändrats. Ändringarna är beständiga, kan bearbetas asynkront och inkrementellt, och utdata kan distribueras bland en eller flera konsumenter för parallell bearbetning. 

Azure Cosmos DB är passar bra för IoT, spel, återförsäljnings, och operativa loggningsprogram. Ett vanliga designmönster i dessa program är att använda ändringar av data för att utlösa ytterligare åtgärder. Exempel på ytterligare åtgärder:

* Utlösa en avisering eller ett anrop till ett API när ett objekt infogas eller uppdateras.
* Strömningsbearbetning för IoT- eller bearbetning av användningsdata analys i realtid.
* Ytterligare dataförflyttning genom synkronisering med en cache eller en sökmotor eller ett informationslager eller arkivering av data till kall lagring.

Ändringsflödet i Azure Cosmos DB kan du bygga skalbara lösningar för var och en av dessa mönster som du ser i följande bild:

![Med hjälp av Azure Cosmos DB ändringen feed power realtidsanalys och händelsedrivna scenarier för högprestandaberäkning](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Stöds API: er och klient-SDK: er

Den här funktionen stöds för närvarande av följande Azure Cosmos DB API: er och klient-SDK: er.

| **Klientdrivrutiner** | **Azure CLI** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ej tillämpligt | Ja | Nej | Nej | Ja | Nej |
|Java|Ej tillämpligt|Ja|Nej|Nej|Ja|Nej|
|Python|Ej tillämpligt|Ja|Nej|Nej|Ja|Nej|
|Noden/JS|Ej tillämpligt|Ja|Nej|Nej|Ja|Nej|

## <a name="change-feed-and-different-operations"></a>Ändringsfeed och olika åtgärder

Idag kan du se alla åtgärder i den ändringsflödet. Funktionen där du kan kontrollera ändringsflödet, för specifika åtgärder som endast uppdateringar och inte infogningar inte är tillgänglig ännu. Du kan lägga till en ”mjuk markör” på för uppdateringar och filtrera utifrån som vid bearbetning av objekten i den ändringsflödet. För närvarande logga inte ändringsfeed borttagningar. På liknande sätt som i föregående exempel, du kan lägga till en mjuk markör på de objekt som tas bort, t.ex, du kan lägga till ett attribut i objektet kallas ”borttagen” och ge den värdet ”true” och ange TTL på objektet, så att den kan tas bort automatiskt. Du kan läsa ändringen-flöden för historisk objekt, till exempel objekt som har lagts till fem år sedan. Om objektet inte tas bort. Du kan läsa ändringen feed ur ursprunget behållarens.

### <a name="sort-order-of-items-in-change-feed"></a>Sortera ordningen på objekten i ändringsfeed

Ändra flödesobjekt kommer i den ordning som deras ändringstid. Den här sorteringsordningen garanteras per logisk partitionsnyckel.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Ändringsflödet i Azure Cosmos-konton för flera regioner

I ett Azure Cosmos-konto för flera regioner, om en skrivregion redundansväxlar ändringsfeed fungerar i åtgärden manuell växling vid fel och det är sammanhängande.

### <a name="change-feed-and-time-to-live-ttl"></a>Ändringsfeed och Time to Live (TTL)

Om en egenskap för TTL (Time to Live) har angetts för ett objekt till-1, behålls ändringsfeed alltid. Om data inte tas bort, förblir den i den ändringsflödet.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Ändringsfeed och _etag, _lsn eller _ts

Formatet _etag är intern och du bör inte koppla beroende på det, eftersom den kan ändras när som helst. _ts är en ändring av eller en tidsstämpel vid skapande. Du kan använda _ts kronologisk jämförelse. _lsn är ett batch-id som har lagts till för ändringsflödet. transaktions-id representerar. Många objekt som kan ha samma _lsn. ETag på FeedResponse skiljer sig från _etag som du ser på objektet. _etag är en intern identifierare och används för samtidighet kontroll meddelar om versionen av objektet medan ETag används för ordningsföljd feeden.

## <a name="change-feed-use-cases-and-scenarios"></a>Ändringsfeed användningsområden och scenarier

Ändringsfeed möjliggör effektiv bearbetning av stora datauppsättningar med ett stort antal skrivningar. Ändringsfeed erbjuder också ett alternativ till att fråga en hel datauppsättning för att identifiera vad som har ändrats.

### <a name="use-cases"></a>Användningsfall

Till exempel med ändringsflödet utföra du följande uppgifter effektivt:

* Uppdatera ett cacheminne, uppdatera ett sökindex eller uppdatera ett informationslager med data som lagras i Azure Cosmos DB.

* Implementera en programnivå data lagringsnivåer och arkivering, till exempel lagra ”frekventa data” i Azure Cosmos DB och föråldrade ”kalla data” till andra lagringssystem, till exempel [Azure Blob Storage](../storage/common/storage-introduction.md).

* Utföra noll driftstopp migreringar till en annan Azure-Cosmos-konto eller en annan Azure Cosmos-behållare med en annan logisk partitionsnyckel.

* Implementera [lambda-arkitekturen](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) med Azure Cosmos DB, Azure Cosmos DB stöder där både i realtid, batch och frågan som betjänar lager, vilket gör att lambda-arkitektur med låg total Ägandekostnad.

* Tar emot och lagring av händelsedata från enheter, sensorer, infrastruktur och program och bearbetar dessa händelser i realtid, till exempel med hjälp av [Spark](../hdinsight/spark/apache-spark-overview.md).  Följande bild visar hur du kan implementera lambda-arkitektur med Azure Cosmos DB via ändringsfeed:

![Azure Cosmos DB-baserade lambda-pipeline för skrivbelastning och frågeprestanda](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenarier

Här följer några scenarier som du enkelt kan implementera med ändringsfeed:

* I din [serverlös](https://azure.microsoft.com/en-us/solutions/serverless/) webb- eller mobilappar, kan du spåra händelser, t.ex alla ändringar till din kunds profil, inställningar eller deras plats och aktivera vissa åtgärder, till exempel skicka push-meddelanden till sina enheter med hjälp av [Azure Functions](change-feed-functions.md).

* Om du använder Azure Cosmos DB för att skapa ett spel, kan du, till exempel använda ändringsflödet att implementera i realtid rankningslistor baserat på poäng från färdiga spel.


## <a name="working-with-change-feed"></a>Arbeta med ändringsfeed

Du kan arbeta med ändringsflödet med hjälp av följande alternativ:

* [Med hjälp av ändringen feed med Azure Functions](change-feed-functions.md)
* [Med hjälp av ändringen feed med biblioteket change feed processor](change-feed-processor.md) 

Ändringsfeed är tillgängliga för varje logisk partitionsnyckel i behållaren och den kan distribueras på en eller flera konsumenter för parallell bearbetning, enligt bilden nedan.

![Distribuerad bearbetning av Azure Cosmos DB-ändringsflödet](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funktioner i ändringsfeed

* Ändringsfeed aktiveras som standard för alla Azure Cosmos-konton.

* Du kan använda din [etablerat dataflöde](request-units.md) för att läsa från den ändringsflödet, precis som andra Azure Cosmos DB åtgärder, i någon av de regioner som associeras med din Azure Cosmos-databas.

* Ändringsflöde innehåller INSERT och update-åtgärder som utförs för objekt i behållaren. Du kan avbilda borttagningar genom att ange en ”mjuk borttagning”-flagga i dina objekt (till exempel dokument) i stället för borttagningar. Du kan också ange en begränsad utgångstiden för dina objekt med den [TTL funktionen](time-to-live.md). Exempel: 24 timmar och Använd värdet för egenskapen att samla in tas bort. Med den här lösningen har att bearbeta ändringarna inom ett kortare tidsintervall än TTL giltighetsperiod. 

* Varje ändring till ett objekt visas exakt en gång i den ändringsflödet och klienterna måste hantera kontrollpunkter logiken. Om du vill undvika komplexiteten med att hantera kontrollpunkter tillhandahåller biblioteket för change feed processor automatiska kontrollpunkter och ”minst en gång” semantik. Se [med ändringen feed med ändringsflödet i biblioteket processor](change-feed-processor.md).

* Den senaste ändringen för ett visst objekt ingår i ändringsloggen. Mellanliggande ändringar är kanske inte tillgänglig.

* Ändringsflöde sorteras av ordningen ändras inom varje logisk partitionsnyckelvärdet. Det finns ingen garanterad ordning över partitionsnyckelvärdena.

* Ändringar kan synkroniseras från alla point-in-time, som är att det finns ingen fast Datalagringsperiod ändringar är tillgängliga.

* Ändringar är tillgängliga samtidigt för alla logisk partitionsnycklarna för en Azure Cosmos-behållare. Den här funktionen kan ändringar från stora behållare som ska bearbetas parallellt av flera användare.

* Program kan begära flera ändringen flöden på samma behållare samtidigt. ChangeFeedOptions.StartTime kan användas för att ge en första startpunkt. Till exempel vill hitta fortsättningstoken som motsvarar en viss clock-tid. ContinuationToken, vinner om anges över värdena StartTime och StartFromBeginning. Precisionen för ChangeFeedOptions.StartTime är ~ 5 sekunder. 

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Alternativ för att läsa ändringsflödet](read-change-feed.md)
* [Med hjälp av ändringen feed med Azure Functions](change-feed-functions.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
