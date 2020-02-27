---
title: 'Självstudie: indexera data i C# från Azure SQL-databaser'
titleSuffix: Azure Cognitive Search
description: I den C# här självstudien kan du ansluta till Azure SQL Database, extrahera sökbara data och läsa in den i ett Azure kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650120"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Självstudie: indexera Azure SQL-data C# i med Azure kognitiv sökning indexerare

Använd C#, konfigurera en [indexerare](search-indexer-overview.md) som extraherar sökbara data från Azure SQL Database och skickar den till ett sökindex. I den här självstudien används [Azure kognitiv sökning .net-klient bibliotek](https://aka.ms/search-sdk) och ett .net Core-konsol program för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en data källa som ansluter till Azure SQL Database
> * Konfigurera en indexerare
> * Köra en indexerare för att läsa in data i ett index
> * Fråga ett index som ett verifierings steg

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-source-code"></a>Hämta käll kod

Käll koden för den här självstudien finns i mappen [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) i GitHub-lagringsplatsen [Azure-samples/search-dotNet-kom-igång](https://github.com/Azure-Samples/search-dotnet-getting-started) .

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

API-anrop kräver tjänst-URL och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**får du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

## <a name="set-up-connections"></a>Konfigurera anslutningar

1. Starta Visual Studio och öppna **DotNetHowToIndexers. SLN**.

1. I Solution Explorer öppnar du **appSettings. JSON** och ersätter plats hållar värden med anslutnings information till din Sök tjänst. Om den fullständiga URL: en är "https://my-demo-service.search.windows.net" är tjänst namnet som ska tillhandahållas "min-demo-service".

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

Den sista posten kräver en befintlig databas. Du skapar den i nästa steg.

## <a name="prepare-sample-data"></a>Förbereda exempel data

I det här steget skapar du en extern data källa på Azure SQL Database som en indexerare kan crawla. Du kan använda Azure-portalen och filen *hotels.sql* från exemplet för att skapa datauppsättningen i Azure SQL Database. Azure Kognitiv sökning använder sammanslagna rad uppsättningar, till exempel en som genereras från en vy eller fråga. SQL-filen i exempellösningen skapar och fyller i en enskild tabell.

Om du har en befintlig Azure SQL Database resurs kan du lägga till hotell tabellen till den från och med steg 4.

1. [Logga](https://portal.azure.com/)in på Azure Portal.

1. Hitta eller skapa en **SQL Database**. Du kan använda standardinställningarna och den lägsta prisnivån. En fördel jämfört med att skapa en server är att du kan ange namn och lösenord för administratörsanvändaren, vilket krävs för att skapa och läsa in tabeller i ett senare steg.

   ![Sidan ny databas](./media/search-indexer-tutorial/indexer-new-sqldb.png "Ny databassida")

1. Klicka på **Granska + skapa** för att distribuera den nya servern och databasen. Vänta tills servern och databasen har distribuerats.

1. Klicka på **Frågeredigeraren (för hands version)** i navigerings fönstret och ange användar namn och lösen ord för Server administratören. 

   Om åtkomst nekas kopierar du klientens IP-adress från fel meddelandet och klickar sedan på länken **Ange server brand vägg** för att lägga till en regel som tillåter åtkomst från klient datorn med hjälp av klientens IP-adress för intervallet. Det kan ta flera minuter innan regeln börjar gälla.

1. I Frågeredigeraren klickar du på **Öppna fråga** och navigerar till platsen för *Hotels. SQL* -filen på den lokala datorn. 

1. Markera filen och klicka på **Öppna**. Skriptet bör se ut ungefär som på följande skärmbild:

   ![SQL-skript](./media/search-indexer-tutorial/sql-script.png "SQL-skript")

1. Klicka på **Kör** för att köra frågan. Nu bör du se ett meddelande om att frågan lyckades för tre rader i resultatfönstret.

1. Om du vill returnera en raduppsättning från den här tabellen kan du köra följande fråga som ett verifieringssteg:

    ```sql
    SELECT * FROM Hotels
    ```

1. Kopiera ADO.NET-anslutningssträngen för databasen. Under **inställningar** > **anslutnings strängar**, kopierar du anslutnings strängen ADO.net, som liknar exemplet nedan.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Klistra in anslutningssträngen i "AzureSqlConnectionString" som tredje post i filen **appsettings.json** i Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Ange ditt lösen ord i anslutnings strängen i filen **appSettings. JSON** . Databas-och användar namn kommer att kopieras över i anslutnings strängen, men lösen ordet måste anges manuellt.

## <a name="build-the-solution"></a>Skapa lösningen

Tryck på F5 för att bygga lösningen. Programmet körs i felsökningsläge. I ett konsolfönster rapporteras status för varje åtgärd.

   ![Konsolutdata](./media/search-indexer-tutorial/console-output.png "Konsolutdata")

Din kod körs lokalt i Visual Studio, ansluter till din Sök tjänst på Azure, som i sin tur ansluter till Azure SQL Database och hämtar data uppsättningen. Med det här många åtgärder finns det flera möjliga punkter av felet. Om ett fel uppstår kontrollerar du först följande villkor:

+ Informationen för den söktjänstanslutning som du anger. Den är begränsad till tjänstnamnet i den här kursen. Om du har angett en fullständig URL avbryts åtgärderna när index skapas och det uppstår ett anslutningsfel.

+ Informationen för databasanslutningen i **appsettings.json**. Den bör vara den ADO.NET-anslutningssträng som du fick från portalen och ha ändrats så att den innehåller ett användarnamn och ett lösenord som är giltiga för din databas. Användarkontot måste ha behörighet att hämta data. Din lokala klient-IP-adress måste vara tillåten åtkomst.

+ Resursbegränsningar. Kom ihåg att den kostnads fria nivån har en gräns på 3 index, indexerare och data källor. När maxgränsen har uppnåtts för en tjänst går det inte att skapa nya objekt.

## <a name="check-results"></a>Kontrol lera resultat

Använd Azure Portal för att kontrol lera att objektet skapas och Använd sedan **Sök Utforskaren** för att fråga indexet.

1. [Logga](https://portal.azure.com/)in på Azure Portal och på sidan **Översikt** för Search-tjänsten öppnar du varje lista i tur och ett för att kontrol lera att objektet har skapats. **Index**, **indexerare**och **data källor** kommer att ha "Hotels", "Azure-SQL-Indexer" och "Azure-SQL".

   ![Paneler för indexerare och datakällor](./media/search-indexer-tutorial/tiles-portal.png)

1. Välj hotell indexet. **Sök Utforskaren** är den första fliken på sidan hotell. 

1. Klicka på **Sök** för att skicka en tom fråga. 

   De tre posterna i ditt index returneras som JSON-dokument. Sökutforskaren returnerar dokument i JSON så att du kan se hela strukturen.

   ![Fråga ett index](./media/search-indexer-tutorial/portal-search.png "Fråga ett index")
   
1. Ange sedan en söksträng: `search=river&$count=true`. 

   Den här frågan kör en fulltextsökning på termen `river`, och resultatet innefattar en räkning av matchande dokument. Att returnera antalet matchande dokument är användbart i testscenarier när du har ett stort index med tusentals eller miljontals dokument. I det här fallet är det bara ett dokument som matchar frågan.

1. Slutligen anger du en söksträng som begränsar JSON-utdata till intresseområden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Svaret på frågan begränsas till valda fält, vilket ger mer koncisa utdata.

## <a name="explore-the-code"></a>Utforska koden

Nu när du förstår vad exempel koden skapar ska vi gå tillbaka till lösningen för att granska koden. Relevant kod finns i två filer:

  + **Hotel.cs**, som innehåller ett schema som definierar indexet
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

### <a name="in-programcs"></a>I Program.cs

Huvud programmet innehåller logik för att skapa en klient, ett index, en data källa och en indexerare. Koden söker efter och tar bort befintliga resurser med samma namn, under förutsättning att du kan köra det här programmet flera gånger.

Datakällobjektet konfigureras med inställningar som är speciella för Azure SQL Database-resurser, inklusive [delvis eller stegvis indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) för att använda inbyggda [funktioner för ändrings identifiering](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) i Azure SQL. Demon hotell-databasen i Azure SQL har en "mjuk borttagning"-kolumn med namnet **IsDeleted**. När den här kolumnen har angetts till sant i databasen, tar indexeraren bort motsvarande dokument från Azure Kognitiv sökning-indexet.

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

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som har lämnats igång kostar dig pengar. Du kan ta bort resurser individuellt eller ta bort resurs gruppen för att ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

I Azure Kognitiv sökning är indexerare tillgängliga för flera Azure-datakällor. I nästa steg ska du utforska indexeraren för Azure Blob Storage.

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)