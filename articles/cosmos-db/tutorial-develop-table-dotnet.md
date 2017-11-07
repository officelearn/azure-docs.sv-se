---
title: 'Azure Cosmos DB: Utveckla med tabell-API i .NET | Microsoft Docs'
description: "Lär dig att utveckla med Azure Cosmos DB tabell-API med hjälp av .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/03/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: a4145f70af429274c3c908d3dedef63c5f973bf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: Utveckla med tabell-API i .NET

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

Den här kursen ingår följande uppgifter: 

> [!div class="checklist"] 
> * Skapa ett Azure Cosmos DB-konto 
> * Aktivera funktioner i filen app.config 
> * Skapa en tabell med hjälp av [tabell API](table-introduction.md) (förhandsgranskning)
> * Lägga till en entitet i en tabell 
> * Infoga en batch med entiteter 
> * Hämta en enda entitet 
> * Fråga entiteter med hjälp av automatisk sekundärindex 
> * Ersätta en entitet 
> * Ta bort en entitet 
> * Ta bort en tabell
 
## <a name="tables-in-azure-cosmos-db"></a>Tabeller i Azure Cosmos DB 

Azure Cosmos-DB tillhandahåller den [tabell API](table-introduction.md) (förhandsversion) för program som behöver en nyckel / värde-lagra med en mindre schemat design och har hög througput krav. [Azure Table storage](../storage/common/storage-introduction.md) SDK: er och REST API: er som kan användas för att arbeta med tabeller i Azure Cosmos DB.   

Den här kursen är avsedd för utvecklare som är bekant med Azure Table storage SDK och vill använda premium-funktioner tillgängliga med Azure Cosmos DB. Den är baserad på [komma igång med Azure Table storage med hjälp av .NET](table-storage-how-to-use-dotnet.md) och visar hur du kan dra nytta av ytterligare funktioner som sekundärindex, dataflöde och multihoming. Den här självstudiekursen beskrivs hur du använder Azure-portalen för att skapa ett Azure DB som Cosmos-konto och sedan skapa och distribuera ett tabell-API-program. Vi också igenom .NET-exempel för att skapa och tar bort en tabell och infoga, uppdatera, ta bort och frågar tabelldata. 

## <a name="prerequisites"></a>Krav

Om du inte redan har Visual Studio 2017 installerat, du kan hämta och använda den **ledigt** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Börja med att skapa ett Azure DB som Cosmos-konto i Azure-portalen.  

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från github, ange anslutningssträngen och köra appen.

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. Öppna därefter lösningsfilen i Visual Studio.

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](http://portal.azure.com/) går du till ditt Azure Cosmos DB-konto. Klicka på **Nycklar** i det västra navigeringsfönstret och sedan på **läs- och skrivnycklar**. Du måste använda knapparna Kopiera på höger sida av skärmen för att kopiera anslutningssträngen i filen app.config i nästa steg.

2. Öppna filen app.config i Visual Studio. 

3. Kopiera URI-värdet från portalen (med kopieringsknappen) och kontrollera värdet för nyckeln konto i app.config. Använd kontonamnet som du skapade tidigare för kontonamn i app.config.
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> Om du vill använda den här appen med Azure Table storage, du behöver ändra anslutningssträngen i `app.config file`. Använda kontonamnet som tabellen kontonamnet och nyckeln som primärnyckel för Azure-lagring. <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen
1. I Visual Studio högerklickar du på projektet i **Solution Explorer** och därefter på **Hantera NuGet-paket**. 

2. I rutan NuGet **Bläddra** skriver du ***WindowsAzure.Storage PremiumTable***. Kontrollera **är förhandsversioner**.

3. Från resultatet installerar den **WindowsAzure.Storage PremiumTable** och välj förhandsversionen `0.0.1-preview`. Den här åtgärden installerar Azure Table storage paketet och alla beroenden.

4. Tryck på Ctrl + F5 för att köra programmet. 

Du kan nu gå tillbaka till Data Explorer Se fråga, ändra och arbeta med tabelldata för den här. 

> [!NOTE]
> Om du vill använda den här appen med en Azure Cosmos DB-Emulator, behöver du bara ändra anslutningssträngen i `app.config file`. Använd den mindre än värdet för emulatorn. <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Azure DB Cosmos-funktioner
Azure Cosmos DB tabell API: et stöder ett antal funktioner som inte är tillgängliga i Azure Table storage. De nya funktionerna kan aktiveras via följande `appSettings` konfigurationsvärden. Inga nya signaturer eller överlagringar har inte lagts till tabellen API: N som inte i den Azure Storage SDK: N. På så sätt kan du ansluta till tabeller i både Azure Table storage och Azure Cosmos DB och arbeta med andra Azure Storage-tjänster som Blobbar och köer. 


| Nyckel | Beskrivning |
| --- | --- |
| TableConnectionMode  | Azure Cosmos-DB stöder två lägen för anslutningen. I `Gateway` -läge-begäranden görs alltid till Azure DB som Cosmos-gateway som vidarebefordrar det till motsvarande datapartitioner. I `Direct` anslutningsläget, klienten hämtar mappningen av tabeller till partitioner och begäranden som görs direkt mot datapartitioner. Vi rekommenderar `Direct`, standard.  |
| TableConnectionProtocol | Azure Cosmos-DB stöder två anslutningsprotokoll - `Https` och `Tcp`. `Tcp`är standard och rekommenderas eftersom det är mer lightweight. |
| TablePreferredLocations | Kommaavgränsad lista över önskade (flera homing) platser för läsning. Varje Azure DB som Cosmos-kontot kan vara associerat med 1 – 30 + regioner. Varje klientinstans kan ange en delmängd av dessa regioner i önskad ordning för låg latens läsningar. Regionerna måste namnges med deras [visningsnamn](https://msdn.microsoft.com/library/azure/gg441293.aspx), till exempel `West US`. Se även [multihoming API: er](tutorial-global-distribution-table.md).
| TableConsistencyLevel | Du kan handel av mellan svarstid, konsekvens och tillgänglighet genom att välja mellan fem väldefinierade konsekvensnivåer: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix`, och `Eventual`. Standardvärdet är `Session`. Valet av konsekvensnivå gör betydande prestanda skillnader i flera regioner inställningar. Se [konsekvensnivåer](consistency-levels.md) mer information. |
| TableThroughput | Reserverat dataflöde för tabellen uttryckt i frågeenheter (RU) per sekund. Enskild tabeller har stöd för 100-tal miljontals RU/s. Se [programbegäran](request-units.md). Standardvärdet är`400` |
| TableIndexingPolicy | Konsekvent och automatisk sekundära indexering av alla kolumner i tabeller | JSON-sträng som överensstämmer med indexering principspecifikationen. Se [indexering princip](indexing-policies.md) att se hur du kan ändra indexprincip om du vill inkludera/exkludera specifika kolumner. | Automatisk indexering av alla egenskaper (hash för strängar) och intervallet för tal |
| TableQueryMaxItemCount | Konfigurera det maximala antalet objekt som returneras per fråga i en enda onödig kommunikation. Standardvärdet är `-1`, vilket gör att Azure Cosmos DB dynamiskt bestämma värdet vid körning. |
| TableQueryEnableScan | Om frågan inte kan använda indexet för filter kan sedan köra det ändå via en genomsökning. Standardvärdet är `false`.|
| TableQueryMaxDegreeOfParallelism | Grad av parallellitet för körning av en fråga för cross-partition. `0`är seriell med ingen före hämtning, `1` är seriell med före hämtning och högre värden ökar mängden parallellitet. Standardvärdet är `-1`, vilket gör att Azure Cosmos DB dynamiskt bestämma värdet vid körning. |

Om du vill ändra standardvärdet, öppna den `app.config` filen i Solutions Explorer i Visual Studio. Lägg till innehållet i `<appSettings>`-elementet enligt nedan. Ersätt `account-name` med namnet på ditt lagringskonto och `account-key` med din åtkomstnyckel. 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Vi gör en snabb genomgång av vad som händer i appen. Öppna den `Program.cs` fil och du hittar att dessa rader med kod skapar tabellen resurser. 

## <a name="create-the-table-client"></a>Skapa tabell-klient
Du har initierat en `CloudTableClient` att ansluta till kontot för tabellen.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Den här klienten har initierats med hjälp av den `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel`, och `TablePreferredLocations` konfiguration värden om anges i inställningarna för appen.
    
## <a name="create-a-table"></a>Skapa en tabell
Sedan kan du skapa en tabell med hjälp av `CloudTable`. Tabeller i Azure Cosmos DB kan skalas oberoende vad gäller lagring och genomflöde och partitionering hanteras automatiskt av tjänsten. Azure Cosmos-DB stöder både med fast storlek och obegränsat antal tabeller. Se [partitionering i Azure Cosmos DB](partition-data.md) mer information. 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

Det finns en viktig skillnad i hur du skapar tabeller. Azure Cosmos-DB reserverar dataflöde, till skillnad från Azure storage förbrukningsbaserad modell för transaktioner. Reservation-modellen har två viktiga fördelar:

* Din genomströmning reserveras dedikerad /, så du får aldrig begränsas om din frågehastigheten är vid eller under din dataflöde
* Reservation modellen är mer [kostnadseffektivt för genomströmning tunga arbetsbelastningar](key-value-store-cost.md)

Du kan konfigurera standard-genomströmning genom att konfigurera inställningen för `TableThroughput` vad gäller RU (frågeenheter) per sekund. 

En läsning av en 1 KB entitet är normaliserat som 1 RU och andra åtgärder är normaliserade till ett fast RU värde baserat på deras förbrukning av CPU, minne och IOPS. Lär dig mer om [programbegäran i Azure Cosmos DB](request-units.md).

> [!NOTE]
> Medan tabellagring SDK inte stöder för närvarande ändra dataflöde, kan du ändra genomflödet omedelbart när som helst med hjälp av Azure-portalen eller Azure CLI.

Sedan vi gå igenom enkla läsa och skriva CRUD-åtgärder med hjälp av Azure Table storage SDK. Den här kursen visar förutsägbar låg en siffra millisekunders latens och snabb frågor som tillhandahålls av Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Entiteter i Azure Table storage sträcker sig från den `TableEntity` klassen och måste ha `PartitionKey` och `RowKey` egenskaper. Här är ett exempel definitionen för en kundentitet.

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

Följande utdrag visar hur du infogar en entitet med Azure storage SDK. Azure Cosmos-DB har utformats för låg latens i alla skalor garanteras över hela världen.

Slutför skrivningar < 15 ms på p99 och ~ 6 ms på p 50 för program som körs i samma region som Azure DB som Cosmos-konto. Och varaktigheten konton för det faktum att skrivningar bekräftas tillbaka till klienten när de synkront replikeras, varaktigt allokerat och allt innehåll som är indexerad.

Tabell-API: et för Azure Cosmos DB finns i förhandsgranskningen. Vid allmän tillgänglighet backas p99 svarstid garantier upp av SLA: er som andra Azure Cosmos DB-API: er. 

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
Azure Table storage stöder en batchåtgärd API, där du kan kombinera uppdateringar, borttagningar och infogningar i samma batchåtgärd. Azure Cosmos-DB saknar vissa begränsningar på batch-API som Azure Table storage. Exempelvis kan du utföra flera läsningar inom en grupp som du kan utföra flera skrivningar till samma entitet i en batch och det finns ingen gräns på 100-åtgärder per batch. 

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
Hämtar (hämtar) i Azure Cosmos DB fullständig < 10 ms på p99 och ~ 1 ms på p 50 i samma Azure-region. Du kan lägga till så många områden i ditt konto för låg latens läsningar och distribuerar program för att läsa från sin lokala region (”multi-homed”) genom att ange `TablePreferredLocations`. 

Du kan hämta en enda entitet med följande kodavsnitt:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Lär dig mer om flera API: er på [utveckling med flera regioner](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Fråga entiteter med hjälp av automatisk sekundärindex
Tabeller kan efterfrågas med den `TableQuery` klass. Azure Cosmos-DB har en Skriv-optimerad databasmotor som indexerar automatiskt alla kolumner i tabellen. Indexering i Azure Cosmos DB är oberoende schema. Även om schemat skiljer sig mellan rader, eller om schemat utvecklas över tid, indexeras det därför automatiskt. Eftersom Azure Cosmos DB har stöd för automatisk sekundärindex, frågor mot en egenskap kan använda indexet och hanteras effektivt.

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

I preview stöder Azure Cosmos DB samma frågefunktioner som Azure Table storage för tabell-API. Azure Cosmos-DB också stöd för sortering, aggregeringar, geospatiala frågan, hierarki och en mängd olika inbyggda funktioner. Ytterligare funktioner ska anges i tabellen API i en framtida tjänstuppdatering. Se [Azure Cosmos DB-fråga](documentdb-sql-query.md) en översikt över dessa funktioner. 

## <a name="replace-an-entity"></a>Ersätta en entitet
Om du vill uppdatera en entitet hämtar du den från tabelltjänsten, ändrar entitetsobjektet och sparar sedan ändringarna till tabelltjänsten igen. Följande kod ändrar en befintlig kunds telefonnummer. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
På liknande sätt kan du utföra `InsertOrMerge` eller `Merge` åtgärder.  

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

Om du inte planerar att använda den här appen mer följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure-portalen.   

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade.  
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer vi beskrivs hur du kommer igång med Azure Cosmos DB med tabell-API och du har gjort följande: 

> [!div class="checklist"] 
> * Skapa ett Azure DB som Cosmos-konto 
> * Funktioner som aktiveras i filen app.config 
> * Skapa en tabell 
> * Lägga till en entitet i en tabell 
> * Infoga en batch med entiteter 
> * Hämta en enda entitet 
> * Efterfrågade enheter med hjälp av automatisk sekundärindex 
> * Ersätta en entitet 
> * Ta bort en entitet 
> * Ta bort en tabell  

Du kan nu gå vidare till nästa kurs och lär dig mer om frågar tabelldata. 

> [!div class="nextstepaction"]
> [Frågan med tabell-API](tutorial-query-table.md)
