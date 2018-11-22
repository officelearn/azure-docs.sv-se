---
title: 'Azure Cosmos DB: Utveckla med tabell-API i .NET | Microsoft Docs'
description: Lär dig att utveckla med Azure Cosmos DB:s tabell-API med .NET
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e198a85d1c04cbc437ad23dadfa3005a72eaa2ce
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161641"
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: Utveckla med tabell-API i .NET

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

Den här självstudien omfattar följande uppgifter: 

> [!div class="checklist"] 
> * Skapa ett Azure Cosmos DB-konto 
> * Aktivera funktioner i filen app.config 
> * Skapa en tabell med [tabell-API:n](table-introduction.md)
> * Lägga till en entitet i en tabell 
> * Infoga en batch med entiteter 
> * Hämta en enda entitet 
> * Fråga entiteter med hjälp av automatiskt sekundärt index 
> * Ersätta en entitet 
> * Ta bort en entitet 
> * Ta bort en tabell
 
## <a name="tables-in-azure-cosmos-db"></a>Tabeller i Azure Cosmos DB 

Azure Cosmos DB tillhandahåller [tabell-API](table-introduction.md) för program som behöver en nyckelvärdelagring en design utan schema. Både tabell-API:n i Azure Cosmos DB Table API och [Azure Table Storage](../storage/common/storage-introduction.md) stöder nu samma SDK:er och REST API:er. Du kan använda Azure Cosmos DB för att skapa tabeller med höga krav på dataflöde.

Den här självstudien är för utvecklare som är bekanta med SDK:er för Azure Table Storage och vill använda de förstklassiga funktionerna som finns i Cosmos DB. Den grundar sig på [Komma igång med Azure Table Storage med hjälp av .NET](table-storage-how-to-use-dotnet.md) och visar hur du använder ytterligare funktioner som sekundära index, etablerat dataflöde och flera värdar. I den här självstudien beskrivs hur du använder Azure-portalen för att skapa ett Azure Cosmos DB-konto och sedan skapa och distribuera ett tabell-API-program. Vi går också igenom .NET-exempel och visar hur du skapar och tar bort en tabell och hur du infogar, uppdaterar, tar bort och hämtar data från tabeller. 

Om du använder Azure Table Storage får du följande fördelar med Azure Cosmos DB:s tabell-API:

- Nyckelfärdig [global distribution](distribute-data-globally.md) med flera värdar och [automatisk och manuell redundans](high-availability.md)
- Stöd för automatisk schemaoberoende indexering mot alla egenskaper (”sekundära index”) och snabba frågor 
- Stöd för [oberoende skalning av lagring och dataflöde](partition-data.md) i alla regioner
- Stöd för [dedikerat dataflöde per tabell](request-units.md) som kan skalas från hundratals till miljontals förfrågningar per sekund
- Stöd för [fem finjusterbara konsekvensnivåer](consistency-levels.md) för att balansera tillgänglighet, svarstid och konsekvens baserat på dina programbehov
- 99,99% tillgänglighet inom en enskild region och möjligheten att lägga till fler regioner för högre tillgänglighet och [branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) med allmän tillgänglighet
- Arbeta med befintliga Azure Storage .NET-SDK:er och gör inga kodändringar i ditt program

Den här självstudien tar upp tabell-API för Azure Cosmos DB med .NET SDK. Du kan ladda ned [tabell-API för Azure Cosmos DB:s .NET SDK](https://aka.ms/tableapinuget) från NuGet.

Mer information om komplexa lagringsuppgifter för Azure-tabeller finns i:

* [Introduktion till Tabell-API för Azure Cosmos DB](table-introduction.md)
* Fullständig information om tillgängliga API:er finns i referensdokumentationen för tabelltjänsten, [Azure Cosmos DB Table API .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>Om den här självstudiekursen
Den här självstudien är för utvecklare som är bekanta med SDK:er för Azure Table Storage och vill använda de förstklassiga funktionerna med Cosmos DB. Den grundar sig på [Komma igång med Azure Table Storage med hjälp av .NET](table-storage-how-to-use-dotnet.md) och visar hur du använder ytterligare funktioner som sekundära index, etablerat dataflöde och flera värdar. Vi beskriver hur du använder Azure-portalen för att skapa ett Azure Cosmos DB-konto och sedan skapa och distribuera ett tabellprogram. Vi går också igenom .NET-exempel och visar hur du skapar och tar bort en tabell och hur du infogar, uppdaterar, tar bort och hämtar data från tabeller. 

Om du inte har Visual Studio 2017 installerat kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Vi ska börja med att skapa ett Azure Cosmos DB-konto i Azure-portalen.  
 
> [!IMPORTANT]  
> Du måste skapa ett nytt tabell-API-konto för att arbeta med de allmänt tillgängliga tabell-API SDK:erna. Tabell-API-konton som skapas i förhandsversionen stöds inte av de allmänt tillgängliga SDK:erna. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till en mapp där du vill installera exempelappen. 

    ```bash
    cd "C:\git-samples"
    ```

2. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. I [Azure-portalen](http://portal.azure.com/) klickar du på **Anslutningssträng**. 

    Använd knapparna på höger sida av skärmen för att kopiera PRIMÄR ANSLUTNINGSSTRÄNG.

    ![Visa och kopiera ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng](./media/create-table-dotnet/connection-string.png)

2. Öppna filen app.config i Visual Studio. 

3. Ta bort kommentarerna för StorageConnectionString på rad 8 och StorageConnectionString på rad 7 eftersom den här självstudien inte använder Storage-emulatorn. Rad 7 och 8 bör nu se ut så här:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Klistra in PRIMÄR ANSLUTNINGSSTRÄNG från portalen i värdet StorageConnectionString på rad 8. Klistra in strängen innanför citattecknen.
   
    > [!IMPORTANT]
    > Om slutpunkten använder documents.azure.com innebär det att du har ett förhandsversionskonto, och du måste skapa ett [nytt tabell-API-konto](#create-a-database-account) för att kunna arbeta med den allmänt tillgängliga SDK:n för tabell-API. 
    >

    Rad 8 bör nu se ut ungefär som:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Spara filen app.config.

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

## <a name="azure-cosmos-db-capabilities"></a>Azure DB Cosmos-funktioner
Azure Cosmos DB stöder ett antal funktioner som inte är tillgängliga i Azure Table Storage-API:er. 

Vissa funktioner nås via nya överlagringar till CreateCloudTableClient som gör det möjligt att ange anslutningsprincip och konsekvensnivå.

| Anslutningsinställningar för tabellen | Beskrivning |
| --- | --- |
| Anslutningsläge  | Med Azure Cosmos DB kan två anslutningslägen användas. I `Gateway`-läget görs alltid begäran till Azure Cosmos DB-gatewayen, som vidarebefordrar den till motsvarande datapartitioner. I `Direct`-anslutningsläget hämtar klienten tabellmappningen till partitioner, och begäran görs direkt mot datapartitioner. Vi rekommenderar standardläget `Direct`.  |
| Anslutningsprotokoll | Azure Cosmos DB stöder två anslutningslägen – `Https` och `Tcp`. `Tcp` är standardläget och rekommenderas eftersom det är enklare. |
| Önskade platser | Kommaavgränsad lista över önskade (flera värdar) platser för läsningar. Varje Azure Cosmos DB-konto kan associeras med 1–30+ regioner. Varje klientinstans kan ange en delmängd av dessa regioner i önskad ordning för läsningar med låg latens. Regionerna måste namnges med sina [visningsnamn](https://msdn.microsoft.com/library/azure/gg441293.aspx), till exempel `West US`. Läs också informationen om [API:er med flera värdar](tutorial-global-distribution-table.md). |
| Konsekvensnivå | Du kan balansera mellan svarstid, konsekvens och tillgänglighet genom att välja mellan fem väldefinierade konsekvensnivåer: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` och `Eventual`. Standardvärdet är `Session`. Valet av konsekvensnivå gör betydande skillnad i konfigurationer för flera regioner. Mer information finns i [Konsekvensnivåer](consistency-levels.md). |

Andra funktioner går att aktivera via följande `appSettings`-konfigurationsvärden.

| Nyckel | Beskrivning |
| --- | --- |
| TableQueryMaxItemCount | Konfigurera det högsta antalet objekt som ska returneras per tabellfråga i en enda kommunikation tur och retur. Standardvärdet är `-1`, som gör att Azure Cosmos DB dynamiskt kan bestämma värdet vid körning. |
| TableQueryEnableScan | Om frågan inte kan använda indexet för något filter kör du den ändå via en genomsökning. Standardvärdet är `false`.|
| TableQueryMaxDegreeOfParallelism | Graden av parallelism för körning av en fråga på flera olika partitioner. `0` är seriell utan förhämtning, `1` är seriell med förhämtning och högre värden ökar parallellitetens hastighet. Standardvärdet är `-1`, som gör att Azure Cosmos DB dynamiskt kan bestämma värdet vid körning. |

Om du vill ändra standardvärdet öppnar du filen `app.config` från Solution Explorer i Visual Studio. Lägg till innehållet i `<appSettings>`-elementet enligt nedan. Ersätt `account-name` med namnet på ditt lagringskonto och `account-key` med din åtkomstnyckel. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Vi gör en snabb genomgång av vad som händer i appen. Öppna filen `Program.cs` så ser du att de här kodraderna skapar tabellresurserna. 

## <a name="create-the-table-client"></a>Skapa tabellklienten
Du initierar en `CloudTableClient` för att ansluta till tabellens konto.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Den här klienten initieras med konfigurationsvärdena `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` och `TablePreferredLocations` om de anges i appinställningarna.

## <a name="create-a-table"></a>Skapa en tabell
Sedan kan du skapa en tabell med hjälp av `CloudTable`. Tabeller i Azure Cosmos DB kan skalas oberoende vad gäller lagring och dataflöde, och partitionering hanteras automatiskt av tjänsten. Azure Cosmos DB stöder både tabeller med fast storlek och obegränsade tabeller. Läs mer i informationen om [partitionering i Azure Cosmos DB](partition-data.md). 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Det finns en viktig skillnad i hur du skapar tabeller. Azure Cosmos DB reserverar dataflöde, till skillnad från Azure Storages förbrukningsbaserade modell för transaktioner. Ditt dataflöde är dedikerat/reserverat, så att du aldrig begränsas om din begärandehastighet ligger på eller under ditt etablerade dataflöde.

Du kan konfigurera standarddataflödet genom att inkludera det som en parameter i CreateIfNotExists.

En läsning av en entitet på 1 KB normaliseras som 1 RU, och andra åtgärder normaliseras till ett fast RU-värde baserat på processor, minne och IOPS-förbrukning. Läs mer om [begäransenheter i Azure Cosmos DB](request-units.md) och specifikt för [nyckelvärdelager](key-value-store-cost.md).

Sedan går vi igenom de enkla åtgärderna för läsning och skrivning (CRUD) med SDK:er för Azure Table Storage. I den här självstudien visas förutsägbara latensvärden på enstaka millisekunder och snabba frågor som tillhandhålls av Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Entiteter i Azure Table Storage sträcker sig från `TableEntity`-klassen och måste ha egenskaperna `PartitionKey` och `RowKey`. Här är en exempeldefinition för en kundentitet.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Följande kodfragment visar hur du infogar en entitet med Azure Storage SDK. Azure Cosmos DB är utformat för garanterat låg svarstid oavsett skala i hela världen.

Slutför skrivningar på <15 ms vid p99 och ~6 ms vid p50 för program som körs i samma region som Azure Cosmos DB-kontot. Och den här varaktigheten förklarar faktumet att skrivningar bekräftas tillbaka till klienten när de har replikerats synkront, hållbarheten har säkerställts och allt innehåll har indexerats.


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Azure Table Storage stöder en API för batchåtgärd som gör att du kan kombinera uppdateringar, borttagningar och infogningar i samma batchåtgärd.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet
Slutförda hämtningar i Azure Cosmos DB <10 ms för p99 och ~1 ms för p50 i samma Azure-region. Du kan lägga till så många regioner du vill till ditt konto för låglatensläsningar och distribuera program för att läsa från deras lokala region (med flera värdar) med inställningen `TablePreferredLocations`. 

Du kan hämta en enda entitet med följande kodfragment:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Läs om API:er med flera värdar i informationen om att [utveckla med flera regioner](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Fråga entiteter med hjälp av automatiskt sekundärt index
Tabeller kan frågas med klassen `TableQuery`. Azure Cosmos DB har en skrivoptimerad databasmotor som automatiskt indexerar alla kolumner i din tabell. Indexering i Azure Cosmos DB är schemaneutral. Även om schemat skiljer sig mellan rader eller om schemat utvecklas över tid indexeras det automatiskt. Eftersom Azure Cosmos DB stöder automatiska sekundära index kan frågor mot alla egenskaper använda indexet och hanteras effektivt.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Azure Cosmos DB stöder samma frågefunktion som Azure Table Storage för tabell-API:n. Azure Cosmos DB stöder också sortering, samlingar, geospatiala frågor, hierarki och en mängd inbyggda funktioner. Läs mer om [Azure Cosmos DB-frågor](how-to-sql-query.md) om du vill få en översikt över funktionerna. 

## <a name="replace-an-entity"></a>Ersätta en entitet
Om du vill uppdatera en entitet hämtar du den från tabelltjänsten, ändrar entitetsobjektet och sparar sedan ändringarna till tabelltjänsten igen. Följande kod ändrar en befintlig kunds telefonnummer. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
På liknande sätt kan du utföra åtgärderna `InsertOrMerge` eller `Merge`.  

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet. Följande kod hämtar och tar bort en kundentitet.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Ta bort en tabell
I det sista kodexemplet tas en tabell bort från ett lagringskonto. Du kan ta bort och återskapa en tabell omedelbart med Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har vi tagit upp hur du kommer igång med Azure Cosmos DB med tabell-API, och du har gjort följande: 

> [!div class="checklist"] 
> * Skapat ett Azure Cosmos DB-konto 
> * Aktiverat funktioner i filen app.config 
> * Skapat en tabell 
> * Lagt till en entitet i en tabell 
> * Infogat en batch med entiteter 
> * Hämtat en enda entitet 
> * Frågat entiteter med hjälp av automatiskt sekundärt index 
> * Ersatt en entitet 
> * Tagit bort en entitet 
> * Tagit bort en tabell  

Du kan nu fortsätta till nästa självstudie och lära dig mer om att fråga tabelldata. 

> [!div class="nextstepaction"]
> [Fråga med tabell-API](tutorial-query-table.md)
