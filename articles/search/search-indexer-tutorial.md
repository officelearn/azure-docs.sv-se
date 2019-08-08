---
title: C#Gång Indexera data från Azure SQL-databaser – Azure Search
description: Ett C# kod exempel som visar hur du ansluter till Azure SQL Database, extraherar sökbara data och läser in den i ett Azure Search index.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c88ff0d7e408e03216707ff9282d640fb7d28500
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840688"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C#Gång Crawla en Azure SQL-databas med hjälp av Azure Search-indexerare

Lär dig hur du konfigurerar en indexerare för att extrahera sökbara data från ett Azure SQL Database-exempel. [Indexerare](search-indexer-overview.md) är en komponent i Azure Search som crawlar externa datakällor och fyller ett [sökindex](search-what-is-an-index.md) med innehåll. För alla indexerare används indexeraren för Azure SQL Database. 

Kunskaper i indexerarkonfiguration är användbara eftersom det gör att du inte behöver skriva och underhålla lika mycket kod. I stället för att förbereda och push-överföra en schemakompatibel JSON-datauppsättning kan du bifoga en indexerare till en datakälla, låta indexeraren extrahera data och infoga dem i ett index och, om du vill, köra indexeraren enligt ett återkommande schema för att hämta ändringar i den underliggande källan.

I den här självstudien använder du [Azure Search .net-klient bibliotek](https://aka.ms/search-sdk) och ett .net Core-konsolprogram för att utföra följande uppgifter:

> [!div class="checklist"]
> * Lägga till söktjänstinformation i programinställningar
> * Förbereda en extern datauppsättning i Azure SQL-databas 
> * Granska index- och indexerardefinitionerna i exempelkod
> * Köra indexerarkod för att importera data
> * Söka i indexet
> * Visa indexerarkonfiguration i portalen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data används i den här snabb starten. 

[Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) lagrar den externa data källa som används av en indexerare. I exempellösningen finns en SQL-datafil för att skapa tabellen. Steg för att skapa tjänsten och databasen finns i den här självstudien.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst, kan användas för att köra exempel lösningen. Exempel kod och instruktioner har testats i den kostnads fria community-versionen.

[Azure-samples/search-dotNet-kom igång](https://github.com/Azure-Samples/search-dotnet-getting-started) tillhandahåller exempel lösningen som finns i Azure samples GitHub-lagringsplatsen. Hämta och extrahera lösningen. Som standard är lösningarna skrivskyddade. Högerklicka på lösningen och ta bort det skrivskyddade attributet så att du kan ändra filerna.

> [!Note]
> Om du använder den kostnadsfria Azure Search-tjänsten är du begränsad till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Se till att det finns utrymme på din tjänst för de nya resurserna.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar** > **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-connections"></a>Konfigurera anslutningar
Anslutningsinformationen för nödvändiga tjänster anges i filen **appsettings.json** i lösningen. 

1. Öppna filen **DotNetHowToIndexers. SLN** i Visual Studio.

1. I Solution Explorer öppnar du **appSettings. JSON** så att du kan fylla i varje inställning.  

De första två posterna du kan fylla i just nu, med URL-adress och administratörs nycklar för din Azure Search-tjänst. Med en slut punkt `https://mydemo.search.windows.net`av, är `mydemo`tjänst namnet som ska tillhandahållas.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Den sista posten kräver en befintlig databas. Du skapar den i nästa steg.

## <a name="prepare-sample-data"></a>Förbereda exempel data

I det här steget skapar du en extern datakälla som indexeraren kan crawla. Du kan använda Azure-portalen och filen *hotels.sql* från exemplet för att skapa datauppsättningen i Azure SQL Database. Azure Search använder utjämnade raduppsättningar, till exempel en som genereras från en vy eller en fråga. SQL-filen i exempellösningen skapar och fyller i en enskild tabell.

Följande övning utgår ifrån att det inte finns någon server eller databas, och du instrueras att skapa dessa i steg 2. Om du har en befintlig resurs kan du lägga till hotels-tabellen i den, med början i steg 4.

1. [Logga](https://portal.azure.com/)in på Azure Portal. 

2. Hitta eller skapa en **Azure SQL Database** för att skapa en databas, server och resurs grupp. Du kan använda standardinställningarna och den lägsta prisnivån. En fördel jämfört med att skapa en server är att du kan ange namn och lösenord för administratörsanvändaren, vilket krävs för att skapa och läsa in tabeller i ett senare steg.

   ![Ny databassida](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Klicka på **Skapa** för att distribuera den nya servern och databasen. Vänta tills servern och databasen har distribuerats.

4. Öppna SQL Database-sidan för den nya databasen, om den inte redan är öppen. Resursnamnet ska vara *SQL database* och inte *SQL Server*.

   ![SQL-databassida](./media/search-indexer-tutorial/hotels-db.png)

4. Klicka på **Frågeredigeraren (för hands version)** i navigerings fönstret.

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

När data-och konfigurations inställningarna är på plats är exempel programmet i **DotNetHowToIndexers. SLN** redo att bygga och köra. Innan du gör det kan du ägna en stund åt att studera index- och indexerardefinitionerna för det här exemplet. Den relevanta koden finns i två filer:

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

I den här kursen hämtar indexeraren data från en datakälla. I praktiken kan du koppla flera indexerare till samma index och skapa ett konsoliderat sökbart index från flera data källor. Du kan använda samma index/indexerar-par, bara byta ut datakällorna, eller ett index med olika indexerare och kombinationer av datakällor, beroende på var du behöver flexibiliteten.

### <a name="in-programcs"></a>I Program.cs

Huvud programmet innehåller logik för att skapa en klient, ett index, en data källa och en indexerare. Koden söker efter och tar bort befintliga resurser med samma namn, under förutsättning att du kan köra det här programmet flera gånger.

Datakällobjektet konfigureras med inställningar som är speciella för Azure SQL Database-resurser, inklusive [stegvis indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) för användning av inbyggda [funktioner för ändrings identifiering](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) i Azure SQL. Demon hotell-databasen i Azure SQL har en "mjuk borttagning"-kolumn med namnet **IsDeleted**. När den här kolumnen har angetts till sant i databasen, tar indexeraren bort motsvarande dokument från Azure Search-indexet.

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

Ett indexerare-objekt är plattforms-oberoende, där konfiguration, schemaläggning och anrop är desamma oavsett källa. Den här exempel indexeraren innehåller ett schema, ett återställnings alternativ som rensar indexerings historiken och anropar en metod för att skapa och köra indexeraren direkt.

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

1. [Logga](https://portal.azure.com/)in på Azure Portal och klicka på länkarna för **index**, indexerare och **data källor**på sidan **Översikt** för Söktjänsten.
3. Välj enskilda objekt om du vill visa eller ändra konfigurations inställningar.

   ![Paneler för indexerare och datakällor](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen ser du resursgruppens namn på översiktssidan för Azure Search-tjänsten.

## <a name="next-steps"></a>Nästa steg

Du kan koppla AI-algoritmer för anrikning till en indexerare-pipeline. I nästa steg fortsätter du med följande självstudie:

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)