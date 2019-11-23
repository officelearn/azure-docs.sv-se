---
title: 'Tutorial: Index data in C# from Azure SQL databases'
titleSuffix: Azure Cognitive Search
description: In this C# tutorial, connect to Azure SQL database, extract searchable data, and load it into an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406717"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Import Azure SQL database in C# using Azure Cognitive Search indexers

Learn how to configure an indexer for extracting searchable data from a sample Azure SQL database. [Indexers](search-indexer-overview.md) are a component of Azure Cognitive Search that crawl external data sources, populating a [search index](search-what-is-an-index.md) with content. Of all indexers, the indexer for Azure SQL Database is the most widely used. 

Kunskaper i indexerarkonfiguration är användbara eftersom det gör att du inte behöver skriva och underhålla lika mycket kod. I stället för att förbereda och push-överföra en schemakompatibel JSON-datauppsättning kan du bifoga en indexerare till en datakälla, låta indexeraren extrahera data och infoga dem i ett index och, om du vill, köra indexeraren enligt ett återkommande schema för att hämta ändringar i den underliggande källan.

In this tutorial, use the [Azure Cognitive Search .NET client libraries](https://aka.ms/search-sdk) and a .NET Core console application to perform the following tasks:

> [!div class="checklist"]
> * Lägga till söktjänstinformation i programinställningar
> * Förbereda en extern datauppsättning i Azure SQL-databas 
> * Granska index- och indexerardefinitionerna i exempelkod
> * Köra indexerarkod för att importera data
> * Söka i indexet
> * Visa indexerarkonfiguration i portalen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

The following services, tools, and data are used in this quickstart. 

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stores the external data source used by an indexer. I exempellösningen finns en SQL-datafil för att skapa tabellen. Steps for creating the service and database are provided in this tutorial.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), any edition, can be used to run the sample solution. Sample code and instructions were tested on the free Community edition.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) provides the sample solution, located in the Azure samples GitHub repository. Download and extract the solution. By default, solutions are read-only. Right-click the solution and clear the read-only attribute so that you can modify files.

> [!Note]
> If you are using the free Azure Cognitive Search service, you are limited to three indexes, three indexers, and three data sources. I den här kursen skapar du en av varje. Se till att det finns utrymme på din tjänst för de nya resurserna.

## <a name="get-a-key-and-url"></a>Get a key and URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-connections"></a>Konfigurera anslutningar
Anslutningsinformationen för nödvändiga tjänster anges i filen **appsettings.json** i lösningen. 

1. In Visual Studio, open the **DotNetHowToIndexers.sln** file.

1. In Solution Explorer, open **appsettings.json** so that you can populate each setting.  

The first two entries you can fill in right now, using the URL and admin keys for your Azure Cognitive Search service. Given an endpoint of `https://mydemo.search.windows.net`, the service name to provide is `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

The last entry requires an existing database. You'll create it in the next step.

## <a name="prepare-sample-data"></a>Prepare sample data

I det här steget skapar du en extern datakälla som indexeraren kan crawla. Du kan använda Azure-portalen och filen *hotels.sql* från exemplet för att skapa datauppsättningen i Azure SQL Database. Azure Cognitive Search consumes flattened rowsets, such as one generated from a view or query. SQL-filen i exempellösningen skapar och fyller i en enskild tabell.

Följande övning utgår ifrån att det inte finns någon server eller databas, och du instrueras att skapa dessa i steg 2. Om du har en befintlig resurs kan du lägga till hotels-tabellen i den, med början i steg 4.

1. [Sign in to the Azure portal](https://portal.azure.com/). 

2. Find or create an **Azure SQL Database** to create a database, server, and resource group. Du kan använda standardinställningarna och den lägsta prisnivån. En fördel jämfört med att skapa en server är att du kan ange namn och lösenord för administratörsanvändaren, vilket krävs för att skapa och läsa in tabeller i ett senare steg.

   ![Ny databassida](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Klicka på **Skapa** för att distribuera den nya servern och databasen. Vänta tills servern och databasen har distribuerats.

4. Öppna SQL Database-sidan för den nya databasen, om den inte redan är öppen. Resursnamnet ska vara *SQL database* och inte *SQL Server*.

   ![SQL-databassida](./media/search-indexer-tutorial/hotels-db.png)

4. On the navigation pane, click **Query editor (preview)** .

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
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Förstå koden

Once the data and configuration settings are in place, the sample program in **DotNetHowToIndexers.sln** is ready to build and run. Innan du gör det kan du ägna en stund åt att studera index- och indexerardefinitionerna för det här exemplet. Den relevanta koden finns i två filer:

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

I den här kursen hämtar indexeraren data från en datakälla. In practice, you can attach multiple indexers to the same index, creating a consolidated searchable index from multiple data sources. Du kan använda samma index/indexerar-par, bara byta ut datakällorna, eller ett index med olika indexerare och kombinationer av datakällor, beroende på var du behöver flexibiliteten.

### <a name="in-programcs"></a>I Program.cs

The main program includes logic for creating a client, an index, a data source, and an indexer. Koden söker efter och tar bort befintliga resurser med samma namn, under förutsättning att du kan köra det här programmet flera gånger.

The data source object is configured with settings that are specific to Azure SQL database resources, including [incremental indexing](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) for leveraging the built-in [change detection features](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) of Azure SQL. The demo hotels database in Azure SQL has a "soft delete" column named **IsDeleted**. When this column is set to true in the database, the indexer removes the corresponding document from the Azure Cognitive Search index.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

An indexer object is platform-agnostic, where  configuration, scheduling, and invocation are the same regardless of the source. This example indexer includes a schedule, a reset option that clears indexer history, and calls a method to create and run the indexer immediately.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Köra indexeraren

I det här steget kompilerar och kör du programmet. 

1. In Solution Explorer högerklickar du på **DotNetHowToIndexers** och väljer **Build (Kompilera)** .
2. Högerklicka på **DotNetHowToIndexers** följt av **Debug (Felsök)**  > **Start new instance (Starta ny instans)** .

Programmet körs i felsökningsläge. I ett konsolfönster rapporteras status för varje åtgärd.

  ![SQL-skript](./media/search-indexer-tutorial/console-output.png)

Koden körs lokalt i Visual Studio, ansluter till din söktjänst på Azure, vilken i sin tur använder anslutningssträngen för att ansluta till Azure SQL Database och hämta datauppsättningen. Med så här många åtgärder finns flera potentiella felpunkter, men om det uppstår ett fel kontrollerar du först:

+ Informationen för den söktjänstanslutning som du anger. Den är begränsad till tjänstnamnet i den här kursen. Om du har angett en fullständig URL avbryts åtgärderna när index skapas och det uppstår ett anslutningsfel.

+ Informationen för databasanslutningen i **appsettings.json**. Den bör vara den ADO.NET-anslutningssträng som du fick från portalen och ha ändrats så att den innehåller ett användarnamn och ett lösenord som är giltiga för din databas. Användarkontot måste ha behörighet att hämta data.

+ Resursbegränsningar. Recall that the Free tier has limits of 3 indexes, indexers, and data sources. När maxgränsen har uppnåtts för en tjänst går det inte att skapa nya objekt.

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

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, click the links for **Indexes**, **Indexers**, and **Data Sources**.
3. Select individual objects to view or modify configuration settings.

   ![Paneler för indexerare och datakällor](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. In the portal, the resource group name is on the Overview page of Azure Cognitive Search service.

## <a name="next-steps"></a>Nästa steg

You can attach AI enrichment algorithms to an indexer pipeline. I nästa steg fortsätter du med följande självstudie:

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)