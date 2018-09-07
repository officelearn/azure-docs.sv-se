---
title: Ta bort data i Azure Cosmos DB med TTL-värde | Microsoft Docs
description: Med TTL tillhandahåller Microsoft Azure Cosmos DB möjligheten att låta dokument automatiskt bort från systemet efter en viss tidsperiod.
services: cosmos-db
keywords: TTL-värde
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 2cae74224a9d59939175ac7e43d4d6b183ca3933
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050752"
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Ta bort data från Azure Cosmos DB-samlingarna automatiskt med TTL-värde
Program kan skapa och lagra stora mängder data. Vissa av dessa data som genererats händelse data, loggar och användaren datorsession informationen kan bara användas under en begränsad tidsperiod. När data blir överskott i förhållande till programmet, är det säkert att rensa dessa data och minska lagringsbehov för ett program.

Med ”time to live” eller TTL, tillhandahåller Microsoft Azure Cosmos DB möjligheten att låta dokument automatiskt bort från databasen efter en viss tidsperiod. Standard time to live kan ange på samlingsnivå och åsidosättas på basis av per dokument. När TTL anges som samlingsstandard eller på en dokumentnivå, Cosmos DB automatiskt att ta bort dokument som finns efter den tid i sekunder, eftersom de senast ändrades.

TTL-värde i Azure Cosmos DB använder en förskjutning mot när dokumentet senast ändrades. Du gör detta används den `_ts` fält som finns på varje dokument. Fältet _ts är en tidsstämpel för epoch av unix-format som representerar datum och tid. Den `_ts` fält som uppdateras varje gång ett dokument har ändrats. 

## <a name="ttl-behavior"></a>TTL-beteende
TTL-funktionen styrs av TTL-egenskaper på två nivåer – samlingsnivå och dokumentnivå. Värdena anges i sekunder och behandlas som en lista från den `_ts` att dokumentet senast ändrades högst.

1. DefaultTTL för samlingen
   
   * Om det saknas (eller inställt på null) dokument tas inte bort automatiskt.
   * Om närvarande och värdet är inställt på ”-1” = oändlig – dokument inte upphör att gälla som standard
   * Dokumenten upphör att gälla om närvarande och värdet anges till något nummer (”n”) sekunder ”n” efter senaste ändring
2. TTL-värde för dokument: 
   
   * Egenskapen gäller bara om DefaultTTL finns för den överordnade samlingen.
   * Åsidosätter DefaultTTL för den överordnade samlingen.

När dokumentet har upphört att gälla (`ttl`  +  `_ts` < = aktuella servertiden), dokumentet har markerats som ”upphört att gälla”. Ingen åtgärd ska tillåtas på dessa dokument efter den tidpunkten och de kommer att uteslutas från resultaten av eventuella frågor utförs. Dokumenten fysiskt tas bort i systemet och tas bort i bakgrunden autentiseringsuuppsättningarna vid ett senare tillfälle. Detta inte använda någon [programbegäran (ru)](request-units.md) från samlingen budget.

Logiken som ovan kan visas i följande matrisen:

|  | DefaultTTL ange saknas/inte för samlingen | DefaultTTL = -1 i samling | DefaultTTL = n ”i samling |
| --- |:--- |:--- |:--- |
| TTL-värde saknas på dokumentet |Inget att åsidosätta på dokumentnivå eftersom både dokumentet och samling har inga begreppet TTL-värde. |Inga dokument i den här samlingen upphör att gälla. |Dokument i samlingen upphör att gälla när intervall n' nätverksförbindelse. |
| TTL-värde = 1 på dokumentet |Inget att åsidosätta på dokumentnivå eftersom samlingen inte definierar egenskapen DefaultTTL som ett dokument kan åsidosätta. TTL-värdet från ett dokument feltolkade av systemet. |Inga dokument i den här samlingen upphör att gälla. |Dokumentet har TTL =-1 i den här samlingen upphör aldrig att gälla. Alla andra dokument upphör att gälla efter n' intervall. |
| TTL = n på dokumentet |Inget att åsidosätta på dokumentnivå. TTL-värdet från ett dokument feltolkade av systemet. |Dokumentet har TTL = n upphör att gälla efter intervall n, i sekunder. Andra dokument som ska ärva intervallet-1 och upphör aldrig att gälla. |Dokumentet har TTL = n upphör att gälla efter intervall n, i sekunder. Andra dokument ärver n' intervall från samlingen. |

## <a name="configuring-ttl"></a>Konfigurera TTL-värde
Som standard är TTL-värde inaktiverad som standard i alla Cosmos DB-samlingar och på alla dokument. TTL-värdet kan anges via programmering eller med hjälp av Azure-portalen. Använd följande steg för att konfigurera TTL-värde från Azure-portalen:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till ditt Azure Cosmos DB-konto.  

2. Navigera till den samling som du vill ställa in TTL-värdet, öppna den **skala och inställningar** fönstret. Du kan se att Time to Live är som standard inställt på **av**. Du kan ändra det till **på (inte standard)** eller **på**.

   **inaktivera** -dokument tas inte bort automatiskt.  
   **på (inte standard)** – det här alternativet anger TTL-värdet ”1” (oändligt) vilket innebär att dokument inte upphör att gälla som standard.  
   **på** -dokumenten upphör att gälla ”n” sekunder efter senaste ändringen.  

   ![Viss TTL-värde](./media/time-to-live/set-ttl-in-portal.png)

## <a name="enabling-ttl"></a>Aktivera TTL
Om du vill aktivera TTL-värde på en samling eller dokumenten i en samling, måste du ange egenskapen DefaultTTL i en samling som antingen 1 eller ett positivt tal som inte är noll. Ställa in DefaultTTL 1 innebär som som standard alla dokument i samlingen kommer att finnas alltid men Cosmos DB-tjänsten bör övervaka den här samlingen för dokument som har åsidosatts denna standardinställning.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Konfigurera standard-TTL på en samling
Du kan konfigurera en standardtid TTL-värde på en samling. Om du vill ställa in TTL-Perioden på en samling, måste du ange ett positivt tal som är noll som anger, i sekunder för att gå ut alla dokument i samlingen när senast ändrade tidsstämpeln för dokumentet (`_ts`). Eller du kan ange standardvärdet-1, vilket innebär att alla dokument infogas i samlingen kommer att finnas på obestämd tid som standard.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Inställningen TTL för ett dokument
Förutom att en standard-TTL på en samling kan ange du specifika TTL på dokumentnivå. Detta åsidosätter standardinställningen i samlingen.

* Om du vill ställa in TTL-värdet på ett dokument, måste du ange ett positivt tal än noll, vilket anger, i sekunder för att gå ut dokumentet när senast ändrade tidsstämpeln för dokumentet (`_ts`).
* Om ett dokument har inget TTL-fält kan tillämpas i stället för samlingen.
* Om TTL är inaktiverat på samlingsnivå ignoreras TTL-fältet på dokumentet tills TTL aktiveras igen på samlingen.

Här är ett kodfragment som visar hur du ställer in TTL upphör för ett dokument:

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
Du kan återställa TTL-värdet från ett dokument genom att göra någon skrivåtgärd i dokumentet. Detta kommer att anges den `_ts` till aktuell tid och nedräkning till dokumentet upphörande, som angetts av den `ttl`, påbörjas igen. Om du vill ändra den `ttl` av dokument, du kan uppdatera fältet som du kan göra med andra inställbar fält.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>Ta bort TTL-värde från ett dokument
Om ett TTL-värde har angetts för ett dokument och du inte längre vill dokumentet att upphöra att gälla, kan sedan du hämta dokumentet, ta bort TTL-fältet och ersätter dokumentet på servern. När TTL-fältet tas bort från dokumentet, kommer i stället för samlingen att tillämpas. Om du vill stoppa ett dokument upphör att gälla och inte ärver från samlingen måste du ställa in TTL-värdet-1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>Inaktivera TTL
Om du vill inaktivera TTL helt och hållet i en samling och stoppa bakgrundsprocess från söker efter utgångna dokument DefaultTTL-egenskapen i samlingen ska tas bort. Tar bort den här egenskapen skiljer sig från att ange den till -1. Inställningen för att 1 innebär nya dokument som läggs till i samlingen kommer att finnas alltid men du kan åsidosätta detta på specifika dokument i samlingen. Ta bort den här egenskapen helt från samlingen innebär att inga dokument går ut, även om det finns dokument som har uttryckligen åsidosätts tidigare.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>TTL-värde och index interaktion
Lägga till eller ändra TTL-inställningen på en samling ändringar av underliggande indexet. När TTL-värdet ändras från av till på, omindexerade samlingen. När du ändrar indexprincip när indexering läget är konsekvent, kommer användare inte att upptäcka att till indexet. När indexering läge är inställt på lazy, indexet fånga alltid och om TTL-värdet ändras måste indexet återskapas från grunden. När TTL-värdet ändras och indexläge är inställt på lazy, returnerar frågor under indexet återskapas inte fullständig eller korrekta resultat.

Om du behöver exakt data som returneras kan du inte ändra TTL-värdet när indexering läge är inställt på lazy. Konsekvent index bör helst väljas för att säkerställa konsekvent frågeresultat. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**Vad TTL kostar mig?**

Det kostar ingenting ytterligare att ange ett TTL-värde för ett dokument.

**Hur lång tid tar det att ta bort dokumentet när TTL-värdet är igång?**

Dokumenten ska upphöra att gälla omedelbart när TTL-värdet är igång och kommer inte att komma åt via CRUD eller fråga API: er. 

**TTL-värdet från ett dokument har någon inverkan på RU kostnader?**

Nej, det blir ingen inverkan på RU-avgifter för borttagningar av utgångna dokument via TTL i Cosmos DB.

**TTL-funktionen endast gäller för hela dokument eller kan jag gå ut egenskapsvärden för enskilda dokument?**

TTL-värde gäller för hela dokumentet. Om du vill att gälla bara en del av ett dokument, sedan rekommenderar vi att du extraherar delen från huvudsakliga dokumentet till ett separat ”länkade” dokument och sedan använda TTL-värde med det extrahera dokumentet.

**Har funktionen TTL eventuella specifika indexering krav?**

Ja. Samlingen måste ha [indexering principuppsättningen](indexing-policies.md) konsekvens eller Lazy. Försök att ange DefaultTTL på en samling med indexering inställd på Ingen resulterar i ett fel, som kommer att inaktivera indexering för en samling som har en DefaultTTL som redan angetts.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure Cosmos DB kan du referera till tjänsten [ *dokumentation* ](https://azure.microsoft.com/documentation/services/cosmos-db/) sidan.

