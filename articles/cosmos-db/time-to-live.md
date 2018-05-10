---
title: Ut data i Azure Cosmos DB med time to live | Microsoft Docs
description: Med TTL tillhandahåller Microsoft Azure Cosmos DB möjligheten att låta dokument automatiskt bort från systemet efter en viss tidsperiod.
services: cosmos-db
documentationcenter: ''
keywords: Time to live-
author: SnehaGunda
manager: kfile
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 13f2caa631817a5745f39b44faccb11252a2d549
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Data i Azure Cosmos DB samlingar automatiskt med time to live-att gälla
Program kan skapa och lagra stora mängder data. Vissa av dessa data, t.ex. datorn genereras data, loggar och användaren händelsesessionen information är bara användbara för en bestämd tidsperiod. När data blir överflödiga enligt behov av programmet som det är säkert att rensa data och minska lagringsbehov för ett program.

Med ”time to live” eller TTL, tillhandahåller Microsoft Azure Cosmos DB möjligheten att låta dokument automatiskt rensas bort från databasen efter en viss tidsperiod. Standard time to live kan ställas på samlingsnivå och åsidosätts på grundval av per dokument. När TTL-värde har angetts som standard samlingen eller på en dokumentnivå bort Cosmos DB automatiskt dokument som finns efter den tid i sekunder, eftersom de senast ändrades.

Time to live i Cosmos-DB-använder en förskjutning mot när dokumentet senast ändrades. Om du vill används den `_ts` fält som finns på alla dokument. Fältet _ts är en unix-format epok tidsstämpel som representerar datumet och tiden. Den `_ts` varje gång ett dokument ändras uppdateras fältet. 

## <a name="ttl-behavior"></a>TTL-beteende
TTL-funktionen styrs av TTL-egenskaper på två nivåer - samlingsnivå och dokumentnivå. Värdena anges i sekunder och behandlas som ett dokument från den `_ts` dokumentet senast ändrad.

1. DefaultTTL för samlingen
   
   * Om de saknas (eller inställt på null) dokument tas inte bort automatiskt.
   * Om finns, och värdet är ”-” 1 = oändlig – dokument inte ut som standard
   * Om finns, och värdet är ett nummer (”n”) – dokument gälla sekunder ”n” efter senaste ändring
2. TTL-värde för dokument: 
   
   * Egenskapen gäller endast om DefaultTTL finns för den överordnade samlingen.
   * Åsidosätter DefaultTTL för den överordnade samlingen.

När dokumentet har upphört att gälla (`ttl`  +  `_ts` < = aktuella servertiden), dokumentet har markerats som ”upphört att gälla”. Ingen åtgärd ska tillåtas på dessa dokument efter den tidpunkten och de kommer att uteslutas från resultatet av alla frågor som utförs. Dokumenten bort fysiskt i systemet och tas bort i bakgrunden tillfälligt vid ett senare tillfälle. Detta inte tar upp någon [begära enheter (RUs)](request-units.md) från samlingen budget.

Ovanstående logiken kan visas i matrisen följande:

|  | DefaultTTL saknas ej på samlingen | DefaultTTL = -1 på en samling | DefaultTTL = ”n” på en samling |
| --- |:--- |:--- |:--- |
| TTL-värde saknas i dokumentet |Inget att åsidosätta på dokumentnivå eftersom både dokumentet och samling har något begrepp om TTL-värde. |Inga dokument i den här samlingen upphör att gälla. |Dokument i den här samlingen upphör att gälla efter intervall n tiden. |
| TTL = -1 i dokumentet |Inget att åsidosätta på dokumentnivå eftersom samlingen inte definierar egenskapen DefaultTTL som ett dokument kan åsidosätta. TTL-värde i ett dokument är icke tolkad av systemet. |Inga dokument i den här samlingen upphör att gälla. |Dokumentet med TTL =-1 i den här samlingen upphör aldrig att gälla. Alla dokument upphör att gälla efter ”n” intervall. |
| TTL = n i dokumentet |Inget att åsidosätta på dokumentnivå. TTL-värde i ett dokument är icke tolkad av systemet. |Dokumentet har TTL = n upphör att gälla efter intervall n, i sekunder. Andra dokument kommer att ärva intervallet-1 och aldrig går ut. |Dokumentet har TTL = n upphör att gälla efter intervall n, i sekunder. Andra dokument ärver ”n” intervall från samlingen. |

## <a name="configuring-ttl"></a>Konfigurera TTL-värde
Time to live-är inaktiverat som standard i alla Cosmos DB samlingar och på alla dokument som standard. TTL-värdet kan ställas in via programmering eller i Azure-portalen i den **inställningar** avsnittet för samlingen. 

## <a name="enabling-ttl"></a>Aktivera TTL-värde
Om du vill aktivera TTL-värde på en samling eller dokument i en samling som du behöver ange egenskapen DefaultTTL i en samling till -1 eller ett positivt tal som inte är noll. Inställningen av DefaultTTL-1 innebär som som standard alla dokument i samlingen ska alltid live men Cosmos-DB-tjänsten bör du övervaka den här samlingen för dokument som har åsidosatt den här standardinställningen.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Konfigurera standard TTL-värde på en samling
Du kan konfigurera en standardtid för live på en samlingsnivå. Om du vill ange TTL-värdet för en samling, måste du ange ett positivt tal som är noll som anger tiden, i sekunder att gälla alla dokument i samlingen när senast ändrade tidsstämpeln för dokumentet (`_ts`). Alternativt kan du ange standardvärdet-1, vilket innebär att alla dokument som infogas i samlingen kommer live på obestämd tid som standard.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Inställningen TTL-värde på ett dokument
Du kan ange specifika TTL-värde på dokumentnivå utöver att ställa in en standard-TTL för en samling. Detta åsidosätter standardvärdet i mängden.

* Om du vill ange TTL-värdet för ett dokument, måste du ange ett positivt tal som är noll som anger tiden, i sekunder att gå ut dokumentet när senast ändrade tidsstämpeln för dokumentet (`_ts`).
* Om ett dokument har inget fält med TTL-värde, gäller standard i mängden.
* Om TTL-värdet är inaktiverat på samlingsnivå ignoreras TTL-fältet på dokumentet förrän TTL aktiveras igen på samlingen.

Här är ett kodfragment som visar hur du ställer in TTL upphör att gälla på ett dokument:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Utöka TTL-värde på ett befintligt dokument
Du kan återställa TTL för ett dokument genom att göra någon skrivåtgärd i dokumentet. Om du gör detta anger den `_ts` till aktuell tid och nedräkningen till dokumentet upphör att gälla, som angetts av den `ttl`, påbörjas igen. Om du vill ändra den `ttl` i ett dokument, du kan uppdatera fältet som du kan göra med något annat går fält.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>Ta bort TTL-värde från ett dokument
Om ett TTL-värde har angetts för ett dokument och du inte längre vill att dokumentet ska upphöra att gälla, kan sedan du hämta dokumentet, ta bort fältet TTL-värde och ersätter dokumentet på servern. När TTL-fältet tas bort från dokumentet, används standardvärdet i mängden. Om du vill stoppa ett dokument upphör att gälla och inte ärver från en samling måste du ange TTL-värdet-1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>Inaktivera TTL-värde
Om du vill inaktivera TTL helt på en samling och stoppa bakgrunden från söker efter utgångna dokument egenskapen DefaultTTL i samlingen ska tas bort. Ta bort den här egenskapen skiljer sig från att ange-1. Inställningen för att 1 innebär nya dokument läggas till i samlingen ska alltid live men du kan åsidosätta detta på specifika dokument i samlingen. Ta bort den här egenskapen helt från samlingen innebär att inga dokument upphör att gälla, även om det finns dokument som tidigare har uttryckligen åsidosätts.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>TTL-värde och ett index interaktion
Lägga till eller ändra inställningen TTL-värde på en samling ändrar underliggande indexet. När TTL-värdet ändras från av till på omindexerade samlingen. När du ändrar indexprincip när indexerings-läget är konsekvent, ser inte användare en ändring i indexet. När indexering läge är anges till lazy indexet alltid fånga och om TTL-värdet har ändrats indexet återskapas från början. När TTL-värdet ändras och index-läget är inställt på lazy returnerar frågor under index återskapas inte fullständig eller korrekta resultat.

Om du behöver exakt data som returneras, ändras inte TTL-värdet när indexering läget är inställt på lazy. Helst bör du valt konsekvent index för att säkerställa konsekvent frågeresultat. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**Vad TTL kostar mig?**

Det finns utan extra kostnad för att ange ett TTL-värde i ett dokument.

**Hur lång tid tar det att ta bort dokumentet när TTL-värdet är igång?**

Dokument som har upphört att gälla omedelbart när TTL-värdet är igång och kan inte nås via CRUD eller fråga API: er. 

**TTL-värde i ett dokument har någon effekt på RU avgifter?**

Nej, det är ingen inverkan på RU kostnader för borttagningar av utgångna dokument via TTL-värde i Cosmos-databasen.

**Funktionen TTL endast gäller för hela dokument eller kan jag upphöra att gälla egenskapsvärden för enskilda dokument?**

TTL-värde gäller för hela dokumentet. Om du vill att gälla bara en del av ett dokument, sedan rekommenderas det att du extrahera delen från det huvudsakliga dokumentet till ett separat ”länkade” dokument och sedan använda TTL-värde med det extraherade dokumentet.

**Har funktionen TTL eventuella särskilda krav för fulltextindexering?**

Ja. Samlingen måste ha [indexering principen](indexing-policies.md) konsekvent eller Lazy. Försök att ange DefaultTTL på en samling med indexering inställd på None resulterar i ett fel som kommer försök att inaktivera indexering på en samling som har en DefaultTTL som redan angetts.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure Cosmos DB kan referera till tjänsten [ *dokumentationen* ](https://azure.microsoft.com/documentation/services/cosmos-db/) sidan.

