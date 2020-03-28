---
title: 'Självstudiekurs: Indexera data från Azure SQL-databaser i C # '
titleSuffix: Azure Cognitive Search
description: I den här C#-självstudien ansluter du till Azure SQL-databas, extraherar sökbara data och läser in den i ett Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 7660c89032ea3ef8371655b94b75c1f60603ee32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78193976"
---
# <a name="tutorial-use-c-to-index-data-from-sql-databases-in-azure-cognitive-search"></a>Självstudiekurs: Använd C# för att indexera data från SQL-databaser i Azure Cognitive Search

Konfigurera en [indexerare](search-indexer-overview.md) för att extrahera sökbara data från Azure SQL-databas och skicka dem till ett sökindex i Azure Cognitive Search. 

Den här självstudien använder C# och [.NET SDK](https://aka.ms/search-sdk) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en datakälla som ansluter till Azure SQL Database
> * Skapa en indexerare
> * Köra en indexerare för att läsa in data i ett index
> * Fråga ett index som ett verifieringssteg

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnadsfria tjänsten för den här självstudien. En kostnadsfri söktjänst begränsar dig till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Innan du börjar, se till att du har utrymme på din tjänst för att acceptera de nya resurserna.

## <a name="download-files"></a>Hämta filer

Källkoden för den här självstudien finns i mappen [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) i [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub-databasen.

## <a name="1---create-services"></a>1 - Skapa tjänster

Den här självstudien använder Azure Cognitive Search för indexering och frågor och Azure SQL Database som en extern datakälla. Skapa om möjligt både tjänster i samma region och resursgrupp för närhet och hanterbarhet. I praktiken kan Azure SQL Database finnas i alla regioner.

### <a name="start-with-azure-sql-database"></a>Börja med Azure SQL Database

Skapa i det här steget en extern datakälla på Azure SQL Database som en indexerare kan genomsöka. Du kan använda Azure-portalen och *hotels.sql-filen* från exempelhämtningen för att skapa datauppsättningen i Azure SQL Database. Azure Cognitive Search förbrukar förenklade raduppsättningar, till exempel en som genereras från en vy eller fråga. SQL-filen i exempellösningen skapar och fyller i en enskild tabell.

Om du har en befintlig Azure SQL Database-resurs kan du lägga till hotelltabellen i den, med början i steg 4.

1. [Logga in på Azure-portalen](https://portal.azure.com/).

1. Söka efter eller skapa en **SQL-databas**. Du kan använda standardinställningarna och den lägsta prisnivån. En fördel jämfört med att skapa en server är att du kan ange namn och lösenord för administratörsanvändaren, vilket krävs för att skapa och läsa in tabeller i ett senare steg.

   ![Ny databassida](./media/search-indexer-tutorial/indexer-new-sqldb.png "Ny databassida")

1. Klicka på **Granska + skapa** om du vill distribuera den nya servern och databasen. Vänta tills servern och databasen har distribuerats.

1. Klicka på **Frågeredigeraren (förhandsgranskning)** i navigeringsfönstret och ange serveradministratörens användarnamn och lösenord. 

   Om åtkomst nekas kopierar du klient-IP-adressen från felmeddelandet och klickar sedan på länken **Ange serverbrandvägg** för att lägga till en regel som tillåter åtkomst från klientdatorn med hjälp av klient-IP för intervallet. Det kan ta flera minuter innan regeln börjar gälla.

1. I Frågeredigeraren klickar du på **Öppna fråga** och navigerar till platsen för *hotels.sql-filen* på den lokala datorn. 

1. Markera filen och klicka på **Öppna**. Skriptet bör se ut ungefär som på följande skärmbild:

   ![SQL-skript](./media/search-indexer-tutorial/sql-script.png "SQL-skript")

1. Klicka på **Kör** för att köra frågan. Nu bör du se ett meddelande om att frågan lyckades för tre rader i resultatfönstret.

1. Om du vill returnera en raduppsättning från den här tabellen kan du köra följande fråga som ett verifieringssteg:

    ```sql
    SELECT * FROM Hotels
    ```

1. Kopiera den ADO.NET anslutningssträngen för databasen. Under **Inställningar** > **Anslutningssträngar kopierar**du ADO.NET anslutningssträngen, liknande exemplet nedan.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Du behöver den här anslutningssträngen i nästa övning och konfigurerar din miljö.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Nästa komponent är Azure Cognitive Search, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnadsfria nivån för att slutföra den här genomgången. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Skaffa en api-nyckel och URL för Azure Cognitive Search

API-anrop kräver tjänstens URL och en åtkomstnyckel. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

   ![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomstnyckel")

## <a name="2---set-up-your-environment"></a>2 - Konfigurera din miljö

1. Starta Visual Studio och öppna **DotNetHowToIndexers.sln**.

1. Öppna **appsettings.json** i Solution Explorer för att tillhandahålla anslutningsinformation.

1. För `searchServiceName`, om denhttps://my-demo-service.search.windows.netfullständiga webbadressen är " ", är tjänstnamnet som ska tillhandahållas "my-demo-service".

1. För `AzureSqlConnectionString`är strängformatet ungefär så här:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Kontrollera att anslutningssträngen innehåller ett giltigt lösenord i anslutningssträngen. Databasen och användarnamnen kopieras över, men lösenordet måste anges manuellt.

## <a name="3---create-the-pipeline"></a>3 - Skapa pipelinen

Indexerare kräver ett datakällobjekt och ett index. Relevant kod finns i två filer:

  + **hotel.cs**innehåller ett schema som definierar indexet
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

Huvudprogrammet innehåller logik för att skapa en klient, ett index, en datakälla och en indexerare. Koden söker efter och tar bort befintliga resurser med samma namn, under förutsättning att du kan köra det här programmet flera gånger.

Datakällobjektet är konfigurerat med inställningar som är specifika för Azure SQL-databasresurser, inklusive [partiell eller inkrementell indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) för att utnyttja de inbyggda [ändringsidentifieringsfunktionerna](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) i Azure SQL. Demohotelldatabasen i Azure SQL har kolumnen "mjuk borttagning" med namnet **IsDeleted**. När den här kolumnen är inställd på true i databasen tar indexeraren bort motsvarande dokument från Azure Cognitive Search-indexet.

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

Ett indexeringsobjekt är plattformsoberoende, där konfiguration, schemaläggning och anrop är desamma oavsett källa. Det här exemplet innehåller indexeraren ett schema, ett återställningsalternativ som rensar indexerarens historik och anropar en metod för att skapa och köra indexeraren omedelbart.

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

## <a name="4---build-the-solution"></a>4 - Bygg lösningen

Tryck på F5 för att bygga och köra lösningen. Programmet körs i felsökningsläge. I ett konsolfönster rapporteras status för varje åtgärd.

   ![Konsolutdata](./media/search-indexer-tutorial/console-output.png "Konsolutdata")

Koden körs lokalt i Visual Studio, som ansluter till söktjänsten på Azure, som i sin tur ansluter till Azure SQL Database och hämtar datauppsättningen. Med så många operationer finns det flera möjliga felpunkter. Om du får ett felmeddelande kontrollerar du först följande villkor:

+ Informationen för den söktjänstanslutning som du anger. Den är begränsad till tjänstnamnet i den här kursen. Om du har angett en fullständig URL avbryts åtgärderna när index skapas och det uppstår ett anslutningsfel.

+ Informationen för databasanslutningen i **appsettings.json**. Den bör vara den ADO.NET-anslutningssträng som du fick från portalen och ha ändrats så att den innehåller ett användarnamn och ett lösenord som är giltiga för din databas. Användarkontot måste ha behörighet att hämta data. Din lokala klient-IP-adress måste tillåtas åtkomst.

+ Resursbegränsningar. Kom ihåg att den kostnadsfria nivån har gränser för 3 index, indexerare och datakällor. När maxgränsen har uppnåtts för en tjänst går det inte att skapa nya objekt.

## <a name="5---search"></a>5 - Sök

Använd Azure-portalen för att verifiera att objektet skapas och sedan använda **Sökutforskaren** för att fråga index.

1. [Logga in på Azure-portalen](https://portal.azure.com/)och öppna varje lista i tur och ordning i sidan **Översikt** i söktjänsten för att verifiera att objektet skapas. **Indexerar,** **indexerare**och **datakällor** kommer att ha "hotell", "azure-sql-indexer" respektive "azure-sql".

   ![Paneler för indexerare och datakällor](./media/search-indexer-tutorial/tiles-portal.png)

1. Välj hotellindex. På hotellsidan är **Sökutforskaren** den första fliken. 

1. Klicka på **Sök** om du vill utfärda en tom fråga. 

   De tre posterna i ditt index returneras som JSON-dokument. Sökutforskaren returnerar dokument i JSON så att du kan se hela strukturen.

   ![Fråga ett index](./media/search-indexer-tutorial/portal-search.png "Fråga ett index")
   
1. Ange sedan en söksträng: `search=river&$count=true`. 

   Den här frågan kör en fulltextsökning på termen `river`, och resultatet innefattar en räkning av matchande dokument. Att returnera antalet matchande dokument är användbart i testscenarier när du har ett stort index med tusentals eller miljontals dokument. I det här fallet är det bara ett dokument som matchar frågan.

1. Slutligen anger du en söksträng som begränsar JSON-utdata till intresseområden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Svaret på frågan begränsas till valda fält, vilket ger mer koncisa utdata.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella utvecklingsstadierna är den mest praktiska metoden för designiteration att ta bort objekten från Azure Cognitive Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempelkoden för den här självstudien söker efter befintliga objekt och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare och datakällor.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken Alla resurser eller Resursgrupper i fönstret för vänsternavigering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med grunderna i SQL Database indexering, låt oss ta en närmare titt på indexeringskonfiguration.

> [!div class="nextstepaction"]
> [Konfigurera en Azure SQL-databasindexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)