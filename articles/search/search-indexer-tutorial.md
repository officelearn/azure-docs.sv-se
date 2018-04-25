---
title: Självstudiekurs i indexering av Azure SQL-databaser i Azure Search | Microsoft Docs
description: Crawla Azure SQL-databasen för att extrahera sökbara data och fylla ett Azure Search-index.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: abf121ec369d84dd307416d2c08971d9096de4a8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Crawla en Azure SQL-databas med Azure Search-indexerare

Den här kursen visar hur du konfigurerar en indexerare för att extrahera sökbara data från en Azure SQL-exempeldatabas. [Indexerare](search-indexer-overview.md) är en komponent i Azure Search som crawlar externa datakällor och fyller ett [sökindex](search-what-is-an-index.md) med innehåll. Indexeraren för Azure SQL-databasen är den indexerare som används mest. 

Kunskaper i indexerarkonfiguration är användbara eftersom det gör att du inte behöver skriva och underhålla lika mycket kod. I stället för att förbereda och push-överföra en schemakompatibel JSON-datauppsättning kan du bifoga en indexerare till en datakälla, låta indexeraren extrahera data och infoga dem i ett index och, om du vill, köra indexeraren enligt ett återkommande schema för att hämta ändringar i den underliggande källan.

I den här kursen får du utföra följande uppgifter med hjälp av [Azure Search .NET-klientbiblioteken](https://aka.ms/search-sdk) och ett .NET Core-konsolprogram:

> [!div class="checklist"]
> * Hämta och konfigurera lösningen
> * Lägga till söktjänstinformation i programinställningar
> * Förbereda en extern datauppsättning i Azure SQL Database 
> * Granska index- och indexerardefinitionerna i exempelkod
> * Köra indexerarkod för att importera data
> * Söka i indexet
> * Visa indexerarkonfiguration i portalen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/). 

* En Azure Search-tjänst. Information om hur du skapar en tjänst finns i [Skapa en söktjänst](search-create-service-portal.md).

* En Azure SQL-databas som tillhandahåller den externa datakällan som används av en indexerare. I exempellösningen finns en SQL-datafil för att skapa tabellen.

* Visual Studio 2017. Du kan använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), som är kostnadsfri. 

> [!Note]
> Om du använder den kostnadsfria Azure Search-tjänsten är du begränsad till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Se till att det finns utrymme på din tjänst för de nya resurserna.

## <a name="download-the-solution"></a>Ladda ned lösningen

Indexerarlösningen i den här kursen kommer från en samling Azure Search-exempel som tillhandahålls i en nedladdning (master). Den lösning som används i den här kursen är *DotNetHowToIndexers*.

1. Gå till [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) på GitHub-lagringsplatsen för Azure-exempel.

2. Klicka på **Clone or download (Klona eller ladda ned)** > **Download ZIP (Ladda ned ZIP)**. Som standard hämtas filen till mappen Hämtade filer.

3. I **Utforskaren** > **Hämtade filer** högerklickar du på filen och väljer **Extrahera alla**.

4. Inaktivera skrivskyddet. Högerklicka på mappnamnet > **Egenskaper** > **Allmänt** och avmarkera attributet **Skrivskydd** för den aktuella mappen, undermapparna och filerna.

5. Öppna *DotNetHowToIndexers.sln* i **Visual Studio 2017**.

6. I **Solution Explorer** högerklickar du på den översta noden Solution > **Restore Nuget Packages**.

## <a name="set-up-connections"></a>Konfigurera anslutningar
Anslutningsinformationen för nödvändiga tjänster anges i filen **appsettings.json** i lösningen. 

Öppna **appsettings.json** i Solution Explorer så att du kan fylla i varje inställning med instruktionerna i den här självstudiekursen.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Hämta söktjänstnamnet och admin api-nyckeln

Du hittar slutpunkten och nyckeln för söktjänsten i portalen. En nyckel ger åtkomst till tjänståtgärder. Admin-nycklar ger skrivåtkomst så att du kan skapa och ta bort objekt, till exempel index och indexerare, i din tjänst.

1. Logga in på [Azure Portal](https://portal.azure.com/) och leta reda på [söktjänster för din prenumeration](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Öppna tjänstsidan.

3. Leta reda på tjänstnamnet längst upp på huvudsidan. På följande skärmbild är det *azs-tutorial*.

   ![Tjänstnamn](./media/search-indexer-tutorial/service-name.png)

4. Kopiera och klistra in det som första post i **appsettings.json** i Visual Studio.

  > [!Note]
  > Ett tjänstnamn är en del av slutpunkten som innehåller search.windows.net. Om du är nyfiken kan du se hela URL:en i **Essentials** på översiktssidan. URL:erna ser ut som i det här exemplet: https://your-service-name.search.windows.net

5. I **Settings (Inställningar)** > **Keys (Nycklar)** kopierar du en av admin-nycklarna och klistrar in den som andra post i **appsettings.json**. Nycklarna är alfanumeriska strängar som genereras för din tjänst vid etableringen och krävs för att få åtkomst till tjänståtgärder. 

  När du har lagt till båda inställningarna bör din fil se ut ungefär som i det här exemplet:

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>Förbereda en extern datakälla

I det här steget skapar du en extern datakälla som indexeraren kan crawla. Datafilen för den här kursen är *hotels.sql* och finns i lösningsmappen \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Azure SQL Database

Du kan använda Azure Portal och filen *hotels.sql* från exemplet för att skapa datauppsättningen i Azure SQL Database. Azure Search använder utjämnade raduppsättningar, till exempel en som genereras från en vy eller en fråga. SQL-filen i exempellösningen skapar och fyller i en enskild tabell.

Följande övning utgår ifrån att det inte finns någon server eller databas, och du instrueras att skapa dessa i steg 2. Om du har en befintlig resurs kan du lägga till hotels-tabellen i den, med början i steg 4.

1. Logga in på [Azure-portalen](https://portal.azure.com/). 

2. Klicka på **Skapa en resurs** > **SQL Database** för att skapa en databas, server och resursgrupp. Du kan använda standardinställningarna och den lägsta prisnivån. En fördel jämfört med att skapa en server är att du kan ange namn och lösenord för administratörsanvändaren, vilket krävs för att skapa och läsa in tabeller i ett senare steg.

   ![Ny databassida](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Klicka på **Skapa** för att distribuera den nya servern och databasen. Vänta tills servern och databasen har distribuerats.

4. Öppna SQL Database-sidan för den nya databasen, om den inte redan är öppen. Resursnamnet ska vara *SQL database* och inte *SQL Server*.

  ![SQL-databassida](./media/search-indexer-tutorial/hotels-db.png)

4. Klicka på **Verktyg** > **Frågeredigeraren**.

5. Klicka på **Logga in** och ange användarnamnet och lösenordet för serveradministratören.

6. Klicka på **Öppna fråga** och navigera till platsen för *hotels.sql*. 

7. Markera filen och klicka på **Öppna**. Skriptet bör se ut ungefär som på följande skärmbild:

  ![SQL-skript](./media/search-indexer-tutorial/sql-script.png)

8. Klicka på **Kör** för att köra frågan. Nu bör du se ett meddelande om att frågan lyckades för tre rader i resultatfönstret.

9. Om du vill returnera en raduppsättning från den här tabellen kan du köra följande fråga som ett verifieringssteg:

   ```sql
   SELECT HotelId, HotelName, Tags FROM Hotels
   ```
   Prototypfrågan `SELECT * FROM Hotels` fungerar inte i frågeredigeraren. Exempeldata innehåller geografiska koordinater i fältet Location (Plats), som inte hanteras i redigeraren för tillfället. Om du vill ha en lista med andra kolumner att fråga kan du köra den här instruktionen: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Nu när du har en extern datauppsättning kopierar du ADO.NET-anslutningssträngen för databasen. På SQL Database-sidan för din databas går du till **Inställningar** > **Anslutningssträngar** och kopierar ADO.NET-anslutningssträngen.
 
  ADO.NET-anslutningssträngen ser ut som i följande exempel. Den har ändrats så att den har ett giltigt databasnamn, användarnamn och lösenord.

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. Klistra in anslutningssträngen i "AzureSqlConnectionString" som tredje post i filen **appsettings.json** i Visual Studio.

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Om index- och indexerarkod

Nu kan du bygga och köra koden. Innan du gör det kan du ägna en stund åt att studera index- och indexerardefinitionerna för det här exemplet. Den relevanta koden finns i två filer:

  + **hotel.cs**, som innehåller det schema som definierar indexet
  + **Program.cs**, som innehåller funktioner för att skapa och hantera strukturer i din tjänst

### <a name="in-hotelcs"></a>I hotel.cs

Indexschemat definierar fältsamlingen, inklusive attribut som anger tillåtna åtgärder, till exempel om ett fält är fulltextsökbart, filtrerbart eller sorterbart som i följande fältdefinition för HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Ett schema kan även innehålla andra element, till exempel poängprofiler för att ge högre sökpoäng, anpassade analysverktyg och andra konstruktioner. Men för vårt ändamål har schemat en enkel definition med endast de fält som finns i exempeldatauppsättningarna.

I den här kursen hämtar indexeraren data från en datakälla. Du kan koppla flera indexerare till samma index för att skapa ett konsoliderat sökbart index från flera datakällor och indexerare. Du kan använda samma index/indexerar-par, bara byta ut datakällorna, eller ett index med olika indexerare och kombinationer av datakällor, beroende på var du behöver flexibiliteten.

### <a name="in-programcs"></a>I Program.cs

Huvudprogrammet innehåller funktioner för alla tre representativa datakällor. Om man bara ser på Azure SQL Database står följande objekt ut:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

Alla objekt som du kan visa, konfigurera och ta bort i Azure Search innehåller index, indexerare och datakällor (*hotels*, *azure-sql-indexer* och *azure-sql*). 

Kolumnen *AzureSqlHighWaterMarkColumnName* är värd att nämna eftersom den visar information om identifiering av ändringar som används av indexeraren för att avgöra om en rad har ändrats sedan den senaste indexeringsarbetsbelastningen. [Principer för identifiering av ändringar](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) stöds endast i indexerare och varierar beroende på datakällan. För Azure SQL Database finns två principer att välja bland beroende på dina databaskrav.

Följande kod visar de metoder i Program.cs som används för att skapa en datakälla och indexerare. Koden söker efter och tar bort befintliga resurser med samma namn, under förutsättning att du kan köra det här programmet flera gånger.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Observera att anrop till indexerar-API:t är plattformsagnostiska med undantag för [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), som anger vilken typ av crawler som ska anropas.

## <a name="run-the-indexer"></a>Köra indexeraren

I det här steget kompilerar och kör du programmet. 

1. In Solution Explorer högerklickar du på **DotNetHowToIndexers** och väljer **Build (Kompilera)**.
2. Högerklicka på **DotNetHowToIndexers** följt av **Debug (Felsök)** > **Start new instance (Starta ny instans)**.

Programmet körs i felsökningsläge. I ett konsolfönster rapporteras status för varje åtgärd.

  ![SQL-skript](./media/search-indexer-tutorial/console-output.png)

Koden körs lokalt i Visual Studio, ansluter till din söktjänst på Azure, vilken i sin tur använder anslutningssträngen för att ansluta till Azure SQL Database och hämta datauppsättningen. Med så här många åtgärder finns flera potentiella felpunkter, men om det uppstår ett fel kontrollerar du först:

+ Informationen för den söktjänstanslutning som du anger. Den är begränsad till tjänstnamnet i den här kursen. Om du har angett en fullständig URL avbryts åtgärderna när index skapas och det uppstår ett anslutningsfel.

+ Informationen för databasanslutningen i **appsettings.json**. Den bör vara den ADO.NET-anslutningssträng som du fick från portalen och ha ändrats så att den innehåller ett användarnamn och ett lösenord som är giltiga för din databas. Användarkontot måste ha behörighet att hämta data.

+ Resursbegränsningar. Tänk på att delade (gratis) tjänster har en gräns på tre index, indexerare och datakällor. När maxgränsen har uppnåtts för en tjänst går det inte att skapa nya objekt.

## <a name="search-the-index"></a>Söka i indexet 

På översiktssidan för söktjänsten i Azure Portal klickar du på **Sökutforskaren** längst upp för att skicka ett par frågor för det nya indexet.

1. Klicka på **Ändra index** längst upp för att välja indexet *hotels*.

2. Klicka på **Sök** för att utfärda en tom sökning. 

  De tre posterna i ditt index returneras som JSON-dokument. Sökutforskaren returnerar dokument i JSON så att du kan se hela strukturen.

3. Ange sedan en söksträng: `search=river&$count=true`. 

  Den här frågan kör en fulltextsökning på termen `river`, och resultatet innefattar en räkning av matchande dokument. Att returnera antalet matchande dokument är användbart i testscenarier när du har ett stort index med tusentals eller miljontals dokument. I det här fallet är det bara ett dokument som matchar frågan.

4. Slutligen anger du en söksträng som begränsar JSON-utdata till intresseområden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

  Svaret på frågan begränsas till valda fält, vilket ger mer koncisa utdata.

## <a name="view-indexer-configuration"></a>Visa indexerarkonfiguration

Alla indexerare, inklusive den som du just har skapat programmässigt, visas i portalen. Du kan öppna en indexerardefinition och visa dess datakälla, eller konfigurera ett uppdateringsschema för att hämta nya och ändrade rader.

1. Öppna tjänstöversiktssidan för Azure Search-tjänsten.
2. Rulla ned till panelerna för **indexerare** och **datakällor**.
3. Klicka på en panel för att öppna en lista för varje resurs. Du kan välja enskilda indexerare eller datakällor om du vill visa eller ändra konfigurationsinställningarna.

  ![Paneler för indexerare och datakällor](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker använda dessa tjänster längre följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure Portal. 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På resursgruppsidan klickar du på **Ta bort resursgrupp**, skriver namnet på resursen som ska tas bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information och specifika uppgifter för andra datakällor som stöds finns i följande artiklar:

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexera CSV-blobbar med Azure Search Blob-indexeraren](search-howto-index-csv-blobs.md)
* [Indexera JSON-blobbar med Azure Search Blob-indexeraren](search-howto-index-json-blobs.md)

