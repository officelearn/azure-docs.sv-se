---
title: Verktyg för Azure Cosmos DB | Microsoft Docs
description: Lär dig använda Migreringsverktyg för öppen källkod Azure Cosmos DB data för att importera data till Azure Cosmos DB från olika källor, inklusive MongoDB, SQL Server, Table storage, Amazon DynamoDB, CSV och JSON-filer. CSV-fil för JSON-konvertering.
keywords: CSV-fil för json Migreringsverktyg för databasen, konvertera csv till json
services: cosmos-db
author: andrewhoh
manager: kfile
editor: monicar
documentationcenter: ''
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 9c4908775ab9a471201cc4bb0c35ab863f5d6d50
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB: Verktyg för migrering av

Den här kursen innehåller instruktioner om hur du använder verktyget Azure Cosmos DB datamigrering som kan importera data från olika källor till Azure Cosmos DB samlingar och tabeller. Du kan importera från JSON filer, CSV-filer, SQL, MongoDB, Azure Table storage, Amazon DynamoDB och även Azure SQL DB-API Cosmos-samlingar och du migrerar data till samlingar och tabeller för med Azure Cosmos DB. Verktyget migrering av Data kan också användas när du migrerar från en enda partition samling till en samling med flera partition för SQL-API.

Vilka API ska du använda med Azure Cosmos DB? 
* **[SQL API](documentdb-introduction.md)**  -du kan använda något av alternativen för datakällan i verktyget datamigrering för att importera data.
* **[Tabell API](table-introduction.md)**  -du kan använda datamigreringsverktyget eller AzCopy för att importera data. Se [importerar data för användning med Azure Cosmos DB tabell API](table-import.md) för mer information.
* **[MongoDB API](mongodb-introduction.md)**  -av datamigrering verktyget stöder för närvarande inte Azure Cosmos DB MongoDB API som en källa eller som ett mål. Om du vill migrera data i eller utanför MongoDB API-samlingar i Azure Cosmos DB finns [Azure Cosmos DB: hur du migrerar data MongoDB-API: t](mongodb-migrate.md) anvisningar. Du kan fortfarande använda Migreringsverktyget för Data för att exportera data från MongoDB till Azure SQL DB-API Cosmos samlingar för användning med SQL-API. 
* **[Diagram över API](graph-introduction.md)**  -verktyget för migrering av Data är inte ett stöds Importverktyg för Graph API konton just nu. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Installera verktyget datamigrering
> * Importera data från olika datakällor
> * Exportera från Azure Cosmos DB till JSON

## <a id="Prerequisites"></a>Förhandskrav
Innan du följer anvisningarna i den här artikeln bör du kontrollera att du har följande installerat:

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) eller högre.

## <a id="Overviewl"></a>Översikt över
Verktyget datamigrering är en öppen källkod som importerar data till Azure Cosmos DB från olika källor, inklusive:

* JSON-filer
* MongoDB
* SQL Server
* CSV-filer
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure DB Cosmos-samlingar

Medan Importverktyget innehåller ett grafiskt användargränssnitt (dtui.exe), kan den också drivas från kommandoraden (dt.exe). Faktum är är ett alternativ till utdata associerat kommando när du har installerat en import via Användargränssnittet. Tabell källdata (t.ex. SQL Server- eller CSV-filer) kan omvandlas så att hierarkiska relationer (underdokument) kan skapas under importen. Vill du fortsätta läsa vill lära dig mer om alternativ för exempel på kommandorader för att importera från varje källa och mål alternativ visning importera resultat.

## <a id="Install"></a>Installation
Källkoden för migrering verktyget finns på GitHub i [den här lagringsplatsen](https://github.com/azure/azure-documentdb-datamigrationtool). Du kan hämta och kompilera lösningen lokalt eller [ladda ned en före kompilerade binär](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip), köra antingen:

* **Dtui.exe**: grafiskt gränssnittsversionen av verktyget
* **DT.exe**: kommandoraden versionen av verktyget

## <a name="select-data-source"></a>Välj datakälla

När du har installerat verktyget är det dags att importera dina data. Vilken typ av data du vill importera?

* [JSON-filer](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB exportfilerna](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-filer](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [BLOB](#BlobImport)
* [Azure DB Cosmos-samlingar](#SQLSource)
* [HBase](#HBaseSource)
* [Azure DB Cosmos-massimport](#SQLBulkImport)
* [Azure DB Cosmos sekventiella post import](#DocumentDSeqTarget)


## <a id="JSON"></a>Importera JSON-filer
JSON-filen källa Importverktyget alternativet kan du importera en eller flera dokument JSON-filer eller JSON-filer att var och en innehåller en matris av JSON-dokument. När du lägger till mapparna som innehåller JSON-filer som ska importeras, har du möjlighet att rekursivt söker efter filer i undermappar.

![Skärmbild av JSON-Filalternativ - Databasverktyg för migrering](./media/import-data/jsonsource.png)

Här följer några exempel som kommandoraden ska importera JSON-filer:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Importera från MongoDB

> [!IMPORTANT]
> Om du importerar till ett Azure DB som Cosmos-konto med stöd för MongoDB, följer du dessa [instruktioner](mongodb-migrate.md).
> 
> 

MongoDB källa Importverktyget alternativet kan du importera från en enskild MongoDB-samling och du kan också filtrera dokument med hjälp av en fråga och/eller ändra dokumentets struktur med hjälp av en projektion.  

![Skärmbild av MongoDB alternativ](./media/import-data/mongodbsource.png)

Anslutningssträngen är i formatet standard MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet MongoDB-instansen är tillgänglig.
> 
> 

Ange namnet på samlingen som data ska importeras. Du kan om du vill ange eller ange en fil för en fråga (t.ex. {pop: {$gt: 5000}}) och/eller projektion (t.ex. {loc:0}) att både filtrera och utforma data som ska importeras.

Här följer några exempel som kommandoraden ska importera från MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Importera MongoDB exportfilerna

> [!IMPORTANT]
> Om du importerar till ett Azure DB som Cosmos-konto med stöd för MongoDB, följer du dessa [instruktioner](mongodb-migrate.md).
> 
> 

MongoDB export JSON källa Importverktyget filalternativet kan du importera en eller flera JSON-filer som skapas från verktyget mongoexport.  

![Skärmbild av MongoDB exportalternativ för källa](./media/import-data/mongodbexportsource.png)

När du lägger till mapparna som innehåller MongoDB export JSON-filer för import, har du möjlighet att rekursivt söker efter filer i undermappar.

Här följer ett exempel på kommandoraden att importera från MongoDB export JSON-filer:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Importera från SQLServer
SQL-Importverktyget källalternativet kan du importera från en enskild SQL Server-databas och du kan också filtrera poster som ska importeras med hjälp av en fråga. Du kan dessutom ändra dokumentets struktur genom att ange kapslade avgränsare (Mer information om det finns en liten stund).  

![Skärmbild av SQL - alternativ för databas Migreringsverktyg](./media/import-data/sqlexportsource.png)

Formatet för anslutningssträngen är standard SQL connection-strängformat.

> [!NOTE]
> Använd kommandot Kontrollera så att SQL Server-instansen som anges i strängen anslutningsfältet kan nås.
> 
> 

Egenskapen kapslade avgränsare används för att skapa hierarkiska relationer (underordnade dokument) under importen. Överväg följande SQL-fråga:

*Välj OMVANDLINGEN (BusinessEntityID AS varchar) som Id, namn, adresstyp som [Address.AddressType], AddressLine1 som [Address.AddressLine1], ort som [Address.Location.City], StateProvinceName som [Address.Location.StateProvinceName], postnummer som [ Address.PostalCode] CountryRegionName som [Address.CountryRegionName] från Sales.vStoreWithAddresses där adresstyp = 'Huvudkontoret'*

Som returnerar följande resultat (den partiella):

![Skärmbild av SQL-frågeresultat](./media/import-data/sqlqueryresults.png)

Observera alias som Address.AddressType och Address.Location.StateProvinceName. Genom att ange kapslade avgränsare av '.', Importverktyget skapar adress och Address.Location underdokument under importen. Här är ett exempel på ett resulterande dokument i Azure Cosmos DB:

*{”id”: ”956”, ”Name”: ”ökad försäljning och tjänst”, ”adress”: {”adresstyp”: ”Main Office”, ”AddressLine1”: ”#500 75 O'Connor gata”, ”plats”: {”stad”: ”Ottawa”, ”StateProvinceName”: ”Ontario”}, ”postnummer”: ”K4B 1S2”, ”CountryRegionName” ”: Kanada ”}}*

Här följer några exempel som kommandoraden ska importera från SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Importera CSV-filer och konvertera CSV till JSON
Alternativet Importverktyget källa för CSV-fil kan du importera en eller flera CSV-filer. När du lägger till mapparna som innehåller CSV-filer för import, har du möjlighet att rekursivt söker efter filer i undermappar.

![Skärmbild av CSV - alternativ för CSV-fil för JSON](media/import-data/csvsource.png)

Liknar SQL-källans, egenskapen kapslade avgränsare kan användas för att skapa hierarkiska relationer (underordnade dokument) under importen. Överväg följande CSV-huvud rad- och rader:

![Skärmbild av CSV Exempelposter - CSV-fil för JSON](./media/import-data/csvsample.png)

Observera alias som DomainInfo.Domain_Name och RedirectInfo.Redirecting. Genom att ange kapslade avgränsare av '.', Importverktyget skapar DomainInfo och RedirectInfo underdokument under importen. Här är ett exempel på ett resulterande dokument i Azure Cosmos DB:

*{”DomainInfo”: {”Domain_Name”: ”ACUS.GOV”, ”Domain_Name_Address” ”:http://www.ACUS.GOV”}, ”Federal myndighet” ”: administrativa konferens för USA”, ”RedirectInfo”: {”omdirigera”: ”0”, ”Redirect_Destination” ”:”}, ”id” ”: 9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d ”}*

Importverktyget försöker att härleda typinformation för ociterade värden i CSV-filer (inom citattecken värden behandlas alltid som strängar).  Typer som identifieras i följande ordning: nummer, datetime, booleskt värde.  

Det finns två saker att Observera om CSV-import:

1. Som standard ociterade värden alltid bort för flikar och blanksteg, medan inom citattecken värden sparas som-är. Det här beteendet kan åsidosättas med kryssrutan Rensa inom citattecken värden eller kommandoradsalternativet /s.TrimQuoted.
2. Som standard behandlas en ociterade null som ett null-värde. Det här beteendet kan åsidosättas (det vill säga behandla en ociterade null som en ”null-sträng) med behandla onoterade NULL som sträng kryssrutan eller kommandoradsalternativet /s.NoUnquotedNulls.

Här följer ett exempel på kommandoraden för CSV-import:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Importera från Azure Table storage
Azure Table storage källalternativet Importverktyget kan du importera från en enskild Azure Table storage tabell. Du kan också kan du filtrera tabellentiteter som ska importeras. 

Data som importeras från Azure Table Storage kan vara utdata till Azure Cosmos DB tabeller och entiteter för användning med tabell-API eller samlingar och dokument för användning med SQL-API. . Tabell-API är endast tillgänglig som ett mål i kommandoradsverktyget, du kan inte exportera tabell-API: et med hjälp av användargränssnittet för migrering av Data-verktyget. Mer information finns i [importerar data för användning med Azure Cosmos DB tabell API](table-import.md). 

![Skärmbild av Azure Table storage alternativ](./media/import-data/azuretablesource.png)

Formatet för anslutningssträngen för Azure Table storage är:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet i Azure Table storage instansen kan nås.
> 
> 

Ange namnet på tabellen Azure från att importera från. Du kan du ange en [filter](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Azure Table storage källalternativet Importverktyget har följande alternativ:

1. Inkludera interna fält
   1. Innehåller alla - alla interna fält (PartitionKey, RowKey och tidsstämpel)
   2. Ingen - undanta alla interna fält
   3. RowKey - bara innehålla fältet RowKey
2. Välj kolumner
   1. Azure Table storage filter stöder inte projektioner. Om du vill importera bara specifika Azure Table-Entitetsegenskaper lägger du till dem i listan Välj kolumner. Alla andra Entitetsegenskaper ignoreras.

Här följer ett exempel på kommandoraden att importera från Azure Table storage:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Importera från Amazon DynamoDB
Amazon DynamoDB källa Importverktyget alternativet kan du importera från en enskild tabell Amazon DynamoDB och du kan också filtrera enheterna som ska importeras. Flera mallar tillhandahålls så att ställa in en import är så enkel som möjligt.

![Skärmbild av Amazon DynamoDB alternativ - Databasverktyg för migrering](./media/import-data/dynamodbsource1.png)

![Skärmbild av Amazon DynamoDB alternativ - Databasverktyg för migrering](./media/import-data/dynamodbsource2.png)

Formatet för anslutningssträngen Amazon DynamoDB är:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet Amazon DynamoDB instansen kan nås.
> 
> 

Här följer ett exempel på kommandoraden att importera från Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Importera från Azure Blob storage
JSON-fil, MongoDB exportfilen och CSV-filen Importverktyget alternativ kan du importera en eller flera filer från Azure Blob storage. Ange ett reguljärt uttryck för att välja filer att importera när du har angett en URL för Blob-behållaren och Kontonyckel.

![Skärmbild av Blob alternativ för källa](./media/import-data/blobsource.png)

Här är kommandoradsverktyget exempel för att importera JSON-filer från Azure Blob storage:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>Importera från en SQL-API-samling
Azure Cosmos DB källa Importverktyget alternativet kan du importera data från en eller flera Azure Cosmos DB samlingar och du kan också filtrera dokument med hjälp av en fråga.  

![Skärmbild av Azure Cosmos DB alternativ](./media/import-data/documentdbsource.png)

Formatet för anslutningssträngen för Azure Cosmos DB är:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Anslutningssträngen för Azure DB som Cosmos-konto kan hämtas från sidan nycklar i Azure-portalen, enligt beskrivningen i [så här hanterar du ett konto i Azure Cosmos DB](manage-account.md), men namnet på databasen måste läggas till i anslutningssträngen i följande format:

    Database=<CosmosDB Database>;

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet Azure DB som Cosmos-instansen är tillgänglig.
> 
> 

Ange namnet på samlingen för att importera data från för att importera från en enda Azure DB som Cosmos-samling. Ange ett reguljärt uttryck för att matcha samlingsnamn för en eller flera för att importera från flera Azure Cosmos DB samlingar (till exempel collection01 | collection02 | collection03). Alternativt kan du ange eller ange en fil för en fråga till både filter och form data som ska importeras.

> [!NOTE]
> Eftersom fältet samling accepterar reguljära uttryck om du importerar från en enda samling vars namn innehåller specialtecken i reguljära uttryck, måste dessa tecken hoppas därför.
> 
> 

Importverktyget för Azure Cosmos DB källalternativet har avancerade alternativ:

1. Ta med intern fält: Anger om du vill inkludera Azure Cosmos DB dokumentegenskaper system i exporten (till exempel _rid, _ts) eller inte.
2. Antalet återförsök vid fel: Anger antalet gånger för att försöka anslutningen till Azure Cosmos DB vid tillfälligt fel (till exempel anslutning avbrott i nätverket).
3. Återförsöksintervall: Anger hur länge väntetiden mellan anslutningsförsök görs till Azure Cosmos DB vid tillfälligt fel (till exempel anslutning avbrott i nätverket).
4. Anslutningsläge: Anger Anslutningsläge ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutning lägena är snabbare, medan gateway-läge är mer brandvägg eget eftersom den endast använder port 443.

![Skärmbild av Azure Cosmos DB-datakälla avancerade alternativ](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Importverktyget standard anslutningsläge DirectTcp. Om det uppstår brandväggsproblem med kan växla till anslutningsläge Gateway, eftersom den kräver endast port 443.
> 
> 

Här följer några kommandoradsverktyget exempel för att importera från Azure Cosmos DB:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Azure Cosmos DB Data importera verktyget även stöd för import av data från den [Azure Cosmos DB emulatorn](local-emulator.md). När du importerar data från en lokal emulator sägs slutpunkten `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Importera från HBase
HBase källa Importverktyget alternativet kan du importera data från en HBase-tabell och du kan också filtrera data. Flera mallar tillhandahålls så att ställa in en import är så enkel som möjligt.

![Skärmbild av HBase alternativ](./media/import-data/hbasesource1.png)

![Skärmbild av HBase alternativ](./media/import-data/hbasesource2.png)

Formatet för anslutningssträngen HBase Stargate är:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet HBase-instansen är tillgänglig.
> 
> 

Här följer ett exempel på kommandoraden att importera från HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>Importera SQL-API: et (massimport)
Importverktyget för Azure Cosmos DB samtidigt kan du importera från något av alternativen tillgänglig källa med en Azure Cosmos DB lagrade proceduren för effektivitet. Verktyget stöder import till en enda partitionerad Azure Cosmos DB samling samt delat import genom vilken data är partitionerad över flera samlingar för en partitionerad Azure Cosmos DB. Mer information om partitionering data finns [partitionering och skalning i Azure Cosmos DB](partition-data.md). Verktyget skapar, kör och sedan tar bort den lagrade proceduren från samling(ar) för målet.  

![Skärmbild av Azure Cosmos DB bulk-alternativ](./media/import-data/documentdbbulk.png)

Formatet för anslutningssträngen för Azure Cosmos DB är:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Anslutningssträngen för Azure DB som Cosmos-konto kan hämtas från sidan nycklar i Azure-portalen, enligt beskrivningen i [så här hanterar du ett konto i Azure Cosmos DB](manage-account.md), men namnet på databasen måste läggas till i anslutningssträngen i följande format:

    Database=<CosmosDB Database>;

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet Azure DB som Cosmos-instansen är tillgänglig.
> 
> 

Ange namnet på samlingen för att importera data från och klicka på knappen Lägg till om du vill importera till en enda samling. Om du vill importera till flera samlingar, ange varje samlingsnamn individuellt eller Använd följande syntax för att ange flera samlingar: *collection_prefix*[startIndex - end index]. Tänk på följande riktlinjer när du anger flera samlingar via ovannämnda syntax:

1. Endast heltal intervallet namnet mönster stöds. Till exempel ange samlingen [0-3] skapas följande samlingar: collection0, collection1, collection2 collection3.
2. Du kan använda en förkortad syntax: samlingen [3] skapar samma uppsättning samlingar som nämns i steg 1.
3. Mer än en ersättning kan anges. Till exempel samlingen [0-1] [0-9] genererar 20 samlingsnamn med nollor (collection01... 02... 03).

När samlingen namn har angetts, väljer du önskad genomflödet av samling(ar) (400 RUs till 10 000 RUs). Välj en högre genomströmning för bästa prestanda för import. Läs mer om prestandanivåer [prestandanivåer i Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Prestanda genomströmning inställningen gäller bara skapa en samling. Om den angivna samlingen finns redan, ändras inte dess genomflöde.
> 
> 

När du importerar till flera samlingar, stöder importera verktyget hash-baserad horisontell partitionering. I det här scenariot, ange egenskapen document som du vill använda som partitionsnyckel (om Partitionsnyckeln är tomt dokument är delat slumpmässigt över mål samlingarna).

Du kan ange vilket fält i import-källa som ska användas som egenskapen Azure Cosmos DB dokument-id vid import (Observera att om dokument inte innehåller den här egenskapen, sedan importera verktyget genererar ett GUID som egenskapsvärdet id).

Det finns ett antal avancerade alternativ under importen. Först, medan verktyget innehåller en standard massimport lagrad procedur (BulkInsert.js), kan du ange egna importera lagrade proceduren:

 ![Skärmbild av Azure Cosmos DB bulk insert sproc alternativet](./media/import-data/bulkinsertsp.png)

När du importerar datum typer (till exempel från SQL Server eller MongoDB) kan välja du dessutom mellan tre importalternativ:

 ![Skärmbild av Azure Cosmos DB datum tid importalternativ](./media/import-data/datetimeoptions.png)

* Sträng: Spara som ett strängvärde
* Epok: Spara som en epok numeriskt värde
* Både: Spara både sträng och epok numeriska värden. Det här alternativet skapas en underdokument, till exempel: ”date_joined”: {”värde” ”: 2013-10-21T21:17:25.2410000Z” ”, epok”: 1382390245}

Importverktyget för Azure Cosmos DB Bulk har följande ytterligare avancerade alternativ:

1. Batchstorlek: Verktyget som standard en batchstorlek 50.  Om de dokument som ska importeras är stor kan du sänka batchstorleken. Om de dokument som ska importeras är liten kan du däremot du höja batchstorleken.
2. Maxstorlek för skript (byte): verktyget som standard max skript storleken 512 KB.
3. Inaktivera automatisk generering: Om alla dokument som ska importeras innehåller ett ID-fält kan det här alternativet kan öka prestanda. Ett unikt id-fält saknas dokument har inte importerats.
4. Uppdatera befintliga dokument: Verktyget som standard inte ersätta befintliga dokument med id-konflikter. Det här alternativet kan du skriva över befintliga dokument med matchande ID: n. Den här funktionen är användbart för schemalagd datauppsättning migreringar som uppdaterar befintliga dokument.
5. Antalet återförsök vid fel: Anger antalet gånger för att försöka anslutningen till Azure Cosmos DB vid tillfälligt fel (till exempel anslutning avbrott i nätverket).
6. Återförsöksintervall: Anger hur länge väntetiden mellan anslutningsförsök görs till Azure Cosmos DB vid tillfälligt fel (till exempel anslutning avbrott i nätverket).
7. Anslutningsläge: Anger Anslutningsläge ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutning lägena är snabbare, medan gateway-läge är mer brandvägg eget eftersom den endast använder port 443.

![Skärmbild av Azure Cosmos DB massimport avancerade alternativ](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Importverktyget standard anslutningsläge DirectTcp. Om det uppstår brandväggsproblem med kan växla till anslutningsläge Gateway, eftersom den kräver endast port 443.
> 
> 

## <a id="SQLSeqTarget"></a>Importera SQL-API: et (sekventiella post Import)
Importverktyget för Azure Cosmos DB sekventiella post kan du importera från något av alternativen på grundval av post med tillgängliga källservrar. Du kan välja det här alternativet om du importerar till en befintlig samling som har uppnått sin kvot av lagrade procedurer. Verktyget stöder import till en enda (enskild partition och flera partition) Azure Cosmos DB-samling som delat import genom vilken data är partitionerad över flera enskild partition och/eller flera partition Azure DB som Cosmos-samlingar. Mer information om partitionering data finns [partitionering och skalning i Azure Cosmos DB](partition-data.md).

![Skärmbild av Azure Cosmos DB importalternativ för sekventiella poster](./media/import-data/documentdbsequential.png)

Formatet för anslutningssträngen för Azure Cosmos DB är:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Anslutningssträngen för Azure DB som Cosmos-konto kan hämtas från sidan nycklar i Azure-portalen, enligt beskrivningen i [så här hanterar du ett konto i Azure Cosmos DB](manage-account.md), men namnet på databasen måste läggas till i anslutningssträngen i följande format:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Använd kommandot Kontrollera så att den angivna i strängen anslutningsfältet Azure DB som Cosmos-instansen är tillgänglig.
> 
> 

Ange namnet på den samling som data ska importeras och klicka på Lägg till om du vill importera till en enda samling. Om du vill importera till flera samlingar, ange varje samlingsnamn individuellt eller Använd följande syntax för att ange flera samlingar: *collection_prefix*[startIndex - end index]. Tänk på följande riktlinjer när du anger flera samlingar via ovannämnda syntax:

1. Endast heltal intervallet namnet mönster stöds. Till exempel ange samlingen [0-3] skapas följande samlingar: collection0, collection1, collection2 collection3.
2. Du kan använda en förkortad syntax: samlingen [3] skapar samma uppsättning samlingar som nämns i steg 1.
3. Mer än en ersättning kan anges. Till exempel samlingen [0-1] [0-9] skapar 20 samlingsnamn med nollor (collection01... 02... 03).

När samlingen namn har angetts, väljer du önskad genomflödet av samling(ar) (400 RUs till 250 000 RUs). Välj en högre genomströmning för bästa prestanda för import. Läs mer om prestandanivåer [prestandanivåer i Azure Cosmos DB](performance-levels.md). Import till samlingar med genomströmning > 10 000 RUs kräver en partitionsnyckel. Om du vill ha mer än 250 000 RUs, måste du in en begäran till ditt konto ökade Portal.

> [!NOTE]
> Genomströmning inställningen gäller bara skapa en samling. Om den angivna samlingen finns redan, ändras inte dess genomflöde.
> 
> 

När du importerar till flera samlingar, stöder importera verktyget hash-baserad horisontell partitionering. I det här scenariot, ange egenskapen document som du vill använda som partitionsnyckel (om Partitionsnyckeln är tomt dokument är delat slumpmässigt över mål samlingarna).

Du kan ange vilket fält i import-källa som ska användas som egenskapen Azure Cosmos DB dokument-id vid import (Observera att om dokument inte innehåller den här egenskapen, sedan importera verktyget genererar ett GUID som egenskapsvärdet id).

Det finns ett antal avancerade alternativ under importen. När du importerar datum typer (till exempel från SQL Server eller MongoDB) måste välja du först mellan tre importalternativ:

 ![Skärmbild av Azure Cosmos DB datum tid importalternativ](./media/import-data/datetimeoptions.png)

* Sträng: Spara som ett strängvärde
* Epok: Spara som en epok numeriskt värde
* Både: Spara både sträng och epok numeriska värden. Det här alternativet skapas en underdokument, till exempel: ”date_joined”: {”värde” ”: 2013-10-21T21:17:25.2410000Z” ”, epok”: 1382390245}

Azure Cosmos DB - sekventiella post Importverktyget har följande ytterligare avancerade alternativ:

1. Antalet parallella begäranden: verktyget som standard två parallella begäranden. Överväg att öka antalet parallella begäranden om dokument som ska importeras är små. Observera att det här antalet utlöses för mycket importen kan uppstå om begränsning.
2. Inaktivera automatisk generering: Om alla dokument som ska importeras innehåller ett ID-fält kan det här alternativet kan öka prestanda. Ett unikt id-fält saknas dokument har inte importerats.
3. Uppdatera befintliga dokument: Verktyget som standard inte ersätta befintliga dokument med id-konflikter. Det här alternativet kan du skriva över befintliga dokument med matchande ID: n. Den här funktionen är användbart för schemalagd datauppsättning migreringar som uppdaterar befintliga dokument.
4. Antalet återförsök vid fel: Anger antalet gånger för att försöka anslutningen till Azure Cosmos DB vid tillfälligt fel (till exempel anslutning avbrott i nätverket).
5. Återförsöksintervall: Anger hur länge väntetiden mellan anslutningsförsök görs till Azure Cosmos DB vid tillfälligt fel (till exempel anslutning avbrott i nätverket).
6. Anslutningsläge: Anger Anslutningsläge ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutning lägena är snabbare, medan gateway-läge är mer brandvägg eget eftersom den endast använder port 443.

![Skärmbild av Azure Cosmos DB sekventiella post importera avancerade alternativ](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Importverktyget standard anslutningsläge DirectTcp. Om det uppstår brandväggsproblem med kan växla till anslutningsläge Gateway, eftersom den kräver endast port 443.
> 
> 

## <a id="IndexingPolicy"></a>Ange en indexprincip
När du tillåter Migreringsverktyget att skapa Azure SQL DB-API Cosmos samlingar under importen anger du indexprincip samlingarna. Gå till avsnittet indexering princip i avsnittet avancerade alternativ i Azure Cosmos DB massimport och alternativ för Azure Cosmos DB sekventiella poster.

![Skärmbild av Azure Cosmos DB indexering princip avancerade alternativ](./media/import-data/indexingpolicy1.png)

Med principer för indexering avancerade alternativ, kan du välja en indexering principfil, manuellt ange ett indexprincip, eller välj från en uppsättning standardmallar (genom att högerklicka i textrutan indexering princip).

Verktyget ger principmallarna är:

* Som standard. Den här principen är bäst när du utför likhetsfrågor för strängar och använda ORDER BY, intervalls och likhetsfrågor för tal. Den här principen har en lägre Omkostnad för indexlagring än intervall.
* Intervall. Den här principen är bäst när du använder ORDER BY, intervalls och likhetsfrågor för både tal och strängar. Den här principen har en högre Omkostnad för indexlagring än standard eller Hash.

![Skärmbild av Azure Cosmos DB indexering princip avancerade alternativ](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Om du inte anger en indexprincip används standardprincipen. Mer information om principer för fulltextindexering finns [Azure Cosmos DB indexering principer](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportera till JSON-fil
Exportverktyget Azure Cosmos DB JSON kan du exportera någon av de tillgängliga alternativ till en JSON-fil som innehåller en matris av JSON-dokument. Verktyget hanterar export du eller kan du visa det resulterande migrering kommandot och kör kommandot själv. Den resulterande JSON-filen vara lagrade lokalt eller i Azure Blob storage.

![Skärmbild av Azure Cosmos DB JSON lokal fil exportalternativ](./media/import-data/jsontarget.png)

![Skärmbild av Azure Cosmos DB JSON Azure Blob storage-exportalternativ](./media/import-data/jsontarget2.png)

Du kan välja att prettify resulterande JSON som ökar storleken på resulterande dokumentet när innehållet mer läsbar.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Avancerad konfiguration
Ange platsen för filen som du vill att eventuella fel som skrivs på skärmen avancerad konfiguration. Följande regler gäller för den här sidan:

1. Om ett filnamn inte anges returneras alla fel på resultatsidan.
2. Om ett filnamn anges utan en katalog, sedan filen skapas (eller skrivs över) i den aktuella katalogen i miljön.
3. Om du väljer en befintlig fil och sedan filen skrivs över finns det inget alternativ för append.

Välj om du vill logga alla, kritisk, eller inga felmeddelanden. Slutligen besluta hur ofta den på skärmen överföring meddelandet uppdateras med dess förlopp.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Bekräfta importera inställningar och visa kommandoraden
1. När du har angett information om källdatorn och målet information avancerad konfiguration, granska översikt över migreringen och du kan också visa/kopiera kommandot resulterande migrering (kopiera kommandot är användbart för att automatisera importåtgärder):
   
    ![Skärmbild av översiktsskärm](./media/import-data/summary.png)
   
    ![Skärmbild av översiktsskärm](./media/import-data/summarycommand.png)
2. När du är nöjd med din käll- och alternativ klickar du på **importera**. Förfluten tid, antal överförda och felinformation (om du inte anger ett filnamn i avancerad konfiguration) uppdatera importen pågår. När installationen är klar kan du exportera resultaten (till exempel för att åtgärda eventuella importfel).
   
    ![Skärmbild av Azure Cosmos DB JSON exportalternativ](./media/import-data/viewresults.png)
3. Du kan också starta en ny import antingen behålla de befintliga inställningarna (till exempel sträng information, käll- och alternativ för anslutning osv.) eller återställa alla värden.
   
    ![Skärmbild av Azure Cosmos DB JSON exportalternativ](./media/import-data/newimport.png)

## <a name="next-steps"></a>Nästa steg

I kursen får har du gjort följande uppgifter:

> [!div class="checklist"]
> * Installerat verktyget för migrering av Data
> * Importerade data från olika datakällor
> * Exporteras från Azure Cosmos DB till JSON

Du kan nu gå vidare till nästa kurs och lär dig hur du frågar efter data med hjälp av Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Hur du frågar efter data?](../cosmos-db/tutorial-query-sql-api.md)
