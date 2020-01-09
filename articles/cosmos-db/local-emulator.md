---
title: Utveckla lokalt med Azure Cosmos-emulatorn
description: Med Azure Cosmos-emulatorn kan du utveckla och testa ditt program lokalt utan kostnad, utan att skapa en Azure-prenumeration.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/26/2019
ms.openlocfilehash: 1c352ad5d18f891cd82d90eef7d0a8c6c3d1cdb9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441680"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Använd Azure Cosmos-emulatorn för lokal utveckling och testning

Azure Cosmos-emulatorn tillhandahåller en lokal miljö som emulerar Azure Cosmos DB tjänst i utvecklings syfte. Med Azure Cosmos-emulatorn kan du utveckla och testa ditt program lokalt, utan att du behöver skapa en Azure-prenumeration eller debitera några kostnader. När du är nöjd med hur ditt program fungerar i Azure Cosmos-emulatorn kan du växla till att använda ett Azure Cosmos-konto i molnet.

Du kan utveckla med Azure Cosmos-emulatorn med [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)och [Table](local-emulator.md#table-api) API-konton. Men just nu stöder Datautforskaren vyn i emulatorn endast klienter för SQL API. 

## <a name="how-the-emulator-works"></a>Så här fungerar emulatorn

Azure Cosmos-emulatorn tillhandahåller en effektiv emulering av Azure Cosmos DBs tjänsten. Det stöder identiska funktioner som Azure Cosmos DB, inklusive stöd för att skapa och fråga efter data, etablering och skalning av behållare och körning av lagrade procedurer och utlösare. Du kan utveckla och testa program med Azure Cosmos-emulatorn och distribuera dem till Azure i global skala genom att bara göra en enda konfigurations ändring av anslutnings slut punkten för Azure Cosmos DB.

Även om emuleringen av Azure Cosmos DB-tjänsten är verklighetstrogen så implementeras emulatorn på ett annat sätt än tjänsten. Till exempel använder emulatorn standardkomponenter i operativsystemet som det lokala filsystemet för beständighet och HTTP-protokollstacken för anslutningar. Funktioner som förlitar sig på Azure-infrastruktur som global replikering, ensiffriga millisekunder för läsningar/skrivningar och justerbara konsekvens nivåer är inte tillämpliga.

Du kan migrera data mellan Azure Cosmos-emulatorn och den Azure Cosmos DB tjänsten med hjälp av [verktyget Azure Cosmos DB datamigrering](https://github.com/azure/azure-documentdb-datamigrationtool).

Du kan köra Azure Cosmos-emulatorn på Windows Docker-behållaren, se [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) för kommandona Docker pull och [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) för `Dockerfile` och mer information.

## <a name="differences-between-the-emulator-and-the-service"></a>Skillnader mellan emulatorn och tjänsten

Eftersom Azure Cosmos-emulatorn tillhandahåller en emulerad miljö som körs på den lokala Developer-arbetsstationen finns det vissa skillnader i funktionalitet mellan emulatorn och ett Azure Cosmos-konto i molnet:

* För närvarande har Datautforskaren i emulatorn stöd för klienter för SQL API. Datautforskaren vyer och åtgärder för Azure Cosmos DB-API: er som MongoDB-, Table-, Graph-och Cassandra-API: er stöds inte fullt ut.
* Azure Cosmos-emulatorn har endast stöd för ett fast konto och en välkänd huvud nyckel. Det går inte att skapa nya nycklar i Azure Cosmos-emulatorn, men standard nyckeln kan ändras med hjälp av kommando rads alternativet.
* Azure Cosmos-emulatorn är inte en skalbar tjänst och har inte stöd för ett stort antal behållare.
* Azure Cosmos-emulatorn erbjuder inte olika [Azure Cosmos DB konsekvens nivåer](consistency-levels.md).
* Azure Cosmos-emulatorn erbjuder inte [replikering i flera regioner](distribute-data-globally.md).
* Eftersom din kopia av Azure Cosmos-emulatorn kanske inte alltid är uppdaterad med de senaste ändringarna i Azure Cosmos DBs tjänsten, bör du referera till [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) för att korrekt beräkna ru: er-behoven i ditt program.
* När du använder Azure Cosmos-emulatorn kan du som standard skapa upp till 25 behållare för fast storlek (stöds endast med Azure Cosmos DB SDK: er) eller 5 obegränsade behållare med hjälp av Azure Cosmos-emulatorn. Mer information om att ändra värdet finns i [Setting the PartitionCount value](#set-partitioncount) (Ange PartitionCount-värdet).

## <a name="system-requirements"></a>Systemkrav

Azure Cosmos-emulatorn har följande maskinvaru-och program varu krav:

* Programvarukrav
  * Windows Server 2012 R2, Windows Server 2016 eller Windows 10
  * 64-bitars operativsystem
* Lägsta maskinvarukrav
  * 2 GB RAM-minne
  * 10 GB ledigt hårddiskutrymme

## <a name="installation"></a>Installation

Du kan ladda ned och installera Azure Cosmos-emulatorn från [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) eller så kan du köra emulatorn på Docker för Windows. Instruktioner om hur du använder emulatorn i Docker för Windows finns i [Köra med Docker](#running-on-docker).

> [!NOTE]
> För att installera, konfigurera och köra Azure Cosmos-emulatorn måste du ha administratörs behörighet på datorn. Emulatorn skapar/lägger till ett certifikat och anger även brand Väggs reglerna för att kunna köra dess tjänster. Därför är det nödvändigt att emulatorn kan utföra sådana åtgärder.

## <a name="running-on-windows"></a>Köra i Windows

Starta Azure Cosmos-emulatorn genom att välja Start-knappen eller trycka på Windows-tangenten. Börja skriva **Azure Cosmos-emulatorn**och välj emulatorn från listan över program.

![Välj Start-knappen eller tryck på Windows-tangenten, börja skriva * * Azure Cosmos-emulator * * och välj emulatorn från listan över program](./media/local-emulator/database-local-emulator-start.png)

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows. ![Avisering om aktivitets fältet i Azure Cosmos DB lokalt emulator](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos-emulatorn körs som standard på den lokala datorn ("localhost") som lyssnar på port 8081.

Azure Cosmos-emulatorn installeras som standard `C:\Program Files\Azure Cosmos DB Emulator`. Du kan också starta och stoppa emulatorn från kommandoraden. För mer information, se [kommandoradsverktygsreferensen](#command-line).

## <a name="start-data-explorer"></a>Starta Datautforskaren

När Azure Cosmos-emulatorn startar öppnas automatiskt Azure Cosmos-Datautforskaren i webbläsaren. Adressen visas som `https://localhost:8081/_explorer/index.html`. Om du stänger Utforskaren och vill öppna den igen senare kan du antingen öppna webb adressen i webbläsaren eller starta den från Azure Cosmos-emulatorn i Windows-ikonen som visas nedan.

![Starta Azure Cosmos Local mula data Explorer](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Leta efter uppdateringar

Datautforskaren anger om det finns en ny uppdatering som går att ladda ned.

> [!NOTE]
> Data som skapats i en version av Azure Cosmos-emulatorn (se%LOCALAPPDATA%\CosmosDBEmulator eller valfria inställningar för data Sök vägar) är inte garanterat tillgängliga när du använder en annan version. Om du behöver spara dina data på lång sikt rekommenderar vi att du lagrar dessa data i ett Azure Cosmos-konto i stället för i Azure Cosmos-emulatorn.

## <a name="authenticating-requests"></a>Autentisera förfrågningar

Precis som med Azure Cosmos DB i molnet måste varje begäran som du gör mot Azure Cosmos-emulatorn autentiseras. Azure Cosmos-emulatorn har stöd för ett enda fast konto och en välkänd autentiseringsnyckel för huvud nyckel autentisering. Det här kontot och nyckeln är de enda autentiseringsuppgifterna som tillåts för användning med Azure Cosmos-emulatorn. De är:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Huvud nyckeln som stöds av Azure Cosmos-emulatorn är endast avsedd för användning med emulatorn. Du kan inte använda ditt produktions Azure Cosmos DB konto och nyckel med Azure Cosmos-emulatorn.

> [!NOTE]
> Om du har startat emulatorn med alternativet/Key använder du den genererade nyckeln i stället för `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Mer information om alternativet/Key finns i [kommando rads verktyg referens.](#command-line)

Precis som med Azure Cosmos DB stöder Azure Cosmos-emulatorn endast säker kommunikation via SSL.

## <a name="running-on-a-local-network"></a>Köra på ett lokalt nätverk

Du kan köra emulatorn på ett lokalt nätverk. Om du vill aktivera nätverks åtkomst anger du alternativet `/AllowNetworkAccess` på [kommando raden](#command-line-syntax), vilket även kräver att du anger `/Key=key_string` eller `/KeyFile=file_name`. Du kan använda `/GenKeyFile=file_name` för att skapa en fil med en slumpmässig nyckel. Sedan kan du skicka det till `/KeyFile=file_name` eller `/Key=contents_of_file`.

För att aktivera nätverks åtkomst för första gången ska användaren stänga av emulatorn och ta bort emulatorns data katalog (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Utveckla med emulatorn

### <a name="sql-api"></a>API för SQL

När du har Azure Cosmos-emulatorn som körs på Skriv bordet kan du använda alla [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) : er som stöds eller [Azure Cosmos DB REST API](/rest/api/cosmos-db/) för att interagera med emulatorn. Azure Cosmos-emulatorn innehåller också en inbyggd Datautforskaren som gör att du kan skapa behållare för SQL API eller Cosmos DB för mongo DB API, samt Visa och redigera objekt utan att skriva någon kod.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

När du har Azure Cosmos-emulatorn igång på Skriv bordet kan du använda [Azure Cosmos DBS API för MongoDB](mongodb-introduction.md) för att interagera med emulatorn. Starta emulator från kommando tolken som administratör med "/EnableMongoDbEndpoint". Använd sedan följande anslutnings sträng för att ansluta till MongoDB-API-kontot:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabell-API

När du har Azure Cosmos-emulatorn som körs på Skriv bordet kan du använda [Azure Cosmos DB tabell-API SDK](table-storage-how-to-use-dotnet.md) för att interagera med emulatorn. Starta emulator från kommando tolken som administratör med "/EnableTableEndpoint". Kör sedan följande kod för att ansluta till tabell-API-kontot:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API för Cassandra

Starta emulatorn från en administratörs kommando tolk med "/EnableCassandraEndpoint". Du kan också ställa in miljövariabeln `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Installera python 2,7](https://www.python.org/downloads/release/python-2716/)

* [Installera Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Kör följande kommandon i ett vanligt kommando tolks fönster:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* I CQLSH-gränssnittet kör du följande kommandon för att ansluta till Cassandra-slutpunkten:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Gremlin-API

Starta emulatorn från en administratörs kommando tolk med "/EnableGremlinEndpoint". Du kan också ställa in miljövariabeln `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installera Apache-tinkerpop-Gremlin-Console-3.3.4](https://tinkerpop.apache.org/downloads.html)

* I emulatorns Datautforskaren skapar du en databas "DB1" och en samling "coll1". för partitionsnyckel väljer du "/name"

* Kör följande kommandon i ett vanligt kommando tolks fönster:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* Kör följande kommandon i Gremlin-gränssnittet för att ansluta till Gremlin-slutpunkten:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Exportera SSL-certifikatet

.NET-språk och -körning använder Windows Certificate Store för att säkert ansluta till den lokala Azure Cosmos DB-emulatorn. Andra språk har sin egen metod för att hantera och använda certifikat. Java använder sitt eget [certifikatarkiv](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) medan Python använder [socketomslutningar](https://docs.python.org/2/library/ssl.html).

För att hämta ett certifikat som ska användas med språk och körningar som inte integrerar med Windows Certificate Store måste du exportera det med Windows Certificate Manager. Du kan starta den genom att köra certlm. msc eller följa anvisningarna i steg för steg i [Exportera Azure Cosmos-emulatorns certifikat](./local-emulator-export-ssl-certificates.md). När certifikathanteraren körs öppnar du personliga certifikat som du ser nedan och exporterar certifikatet med det egna namnet ”DocumentDBEmulatorCertificate” som en BASE-64-kodad X.509-fil (.cer).

![SSL-certifikat för den lokala Azure DB Cosmos DB-emulatorn](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Följ instruktionerna i avsnittet [om att lägga till ett certifikat i Java CA-certifikatarkiv](https://docs.microsoft.com/azure/java-add-certificate-ca-store) för att importera X.509-certifikatet till standardcertifikatarkivet för Java. När certifikatet har importer ATS till certifikat arkivet kommer klienter för SQL och Azure Cosmos DBs API för MongoDB att kunna ansluta till Azure Cosmos-emulatorn.

SSL-verifieringen inaktiveras när du ansluter till emulatorn från Python- och Node.js-SDK:er.

## <a id="command-line"></a>Kommandoradsverktygsreferens
Från installations platsen kan du använda kommando raden för att starta och stoppa emulatorn, konfigurera alternativ och utföra andra åtgärder.

### <a name="command-line-syntax"></a>Syntax för kommandorad

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Om du vill visa en lista över alternativ skriver du `Microsoft.Azure.Cosmos.Emulator.exe /?` i kommandotolken.

|**Alternativ** | **Beskrivning** | **Kommando**| **Argument**|
|---|---|---|---|
|[Inga argument] | Startar Azure Cosmos-emulatorn med standardinställningar. |Microsoft. Azure. Cosmos. emulator. exe| |
|[Hjälp] |Visar en lista över kommandoradsargument som stöds.|Microsoft. Azure. Cosmos. emulator. exe/? | |
| GetStatus |Hämtar status för Azure Cosmos-emulatorn. Statusen visas med slutkoden: 1 = Startar, 2 = Körs, 3 = Stoppad. En negativ slutkod anger att ett fel har uppstått. Inga andra utdata produceras. | Microsoft. Azure. Cosmos. emulator. exe/GetStatus| |
| Avstängning| Stänger av Azure Cosmos-emulatorn.| Microsoft. Azure. Cosmos. emulator. exe/shutdown | |
|DataPath | Anger den sökväg där du kan lagra filer. Standardvärdet är%LocalAppdata%\CosmosDBEmulator. | Microsoft. Azure. Cosmos. emulator. exe/DataPath =\<Datapath\> | \<datapath\>: En åtkomlig sökväg |
|Port | Anger det portnummer som ska användas för emulatorn. Standardvärdet är 8081. |Microsoft. Azure. Cosmos. emulator. exe/port =\<port\> | \<port\>: Enskilt portnummer |
| ComputePort | Angett det port nummer som ska användas för tjänsten Compute interop Gateway. Gatewayens HTTP-slutpunkt avsöknings port beräknas som ComputePort + 79. Därför måste ComputePort och ComputePort + 79 vara öppna och tillgängliga. Standardvärdet är 8900. | Microsoft. Azure. Cosmos. emulator. exe/ComputePort =\<ComputePort\> | \<computeport\>: enskilt port nummer |
| EnableMongoDbEndpoint = 3,2 | Aktiverar MongoDB API 3,2 | Microsoft. Azure. Cosmos. emulator. exe/EnableMongoDbEndpoint = 3,2 | |
| EnableMongoDbEndpoint = 3.6 | Aktiverar MongoDB API 3,6 | Microsoft. Azure. Cosmos. emulator. exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | Anger det portnummer som ska användas för API för MongoDB-kompatibilitet. Standardvärdet är 10255. |Microsoft. Azure. Cosmos. emulator. exe/MongoPort =\<MongoPort\>|\<mongoport\>: Enskilt portnumber|
| EnableCassandraEndpoint | Aktiverar API för Cassandra | Microsoft. Azure. Cosmos. emulator. exe/EnableCassandraEndpoint | |
| CassandraPort | Anger det port nummer som ska användas för Cassandra-slutpunkten. Standardvärdet är 10350. | Microsoft. Azure. Cosmos. emulator. exe/CassandraPort =\<CassandraPort\> | \<cassandraport\>: enskilt port nummer |
| EnableGremlinEndpoint | Aktiverar Gremlin-API | Microsoft. Azure. Cosmos. emulator. exe/EnableGremlinEndpoint | |
| GremlinPort | Port nummer som ska användas för Gremlin-slutpunkten. Standardvärdet är 8901. | Microsoft. Azure. Cosmos. emulator. exe/GremlinPort =\<port\> | \<port\>: Enskilt portnummer |
|EnableTableEndpoint | Aktiverar Azure Tabell-API | Microsoft. Azure. Cosmos. emulator. exe/EnableTableEndpoint | |
|TablePort | Port nummer som ska användas för Azure Table-slutpunkten. Standardvärdet är 8902. | Microsoft. Azure. Cosmos. emulator. exe/TablePort =\<port\> | \<port\>: Enskilt portnummer|
| KeyFile | Läs verifierings nyckel från den angivna filen. Använd alternativet/GenKeyFile för att generera en KeyFile | Microsoft. Azure. Cosmos. emulator. exe/KeyFile =\<file_name\> | \<file_name\>: sökvägen till filen |
| ResetDataPath | Tar rekursivt bort alla filer på den angivna sökvägen. Om du inte anger en sökväg, används%LOCALAPPDATA%\CosmosDbEmulator som standard | Microsoft. Azure. Cosmos. emulator. exe/ResetDataPath =\<sökväg > | \<sökväg\>: fil Sök väg  |
| StartTraces  |  Börja samla in fel söknings spårnings loggar med LOGMAN. | Microsoft. Azure. Cosmos. emulator. exe/StartTraces | |
| StopTraces     | Stoppa insamling av fel söknings spårnings loggar med LOGMAN. | Microsoft. Azure. Cosmos. emulator. exe/StopTraces  | |
| StartWprTraces  |  Börja samla in fel söknings spårnings loggar med Windows Performance inspelnings verktyg. | Microsoft. Azure. Cosmos. emulator. exe/StartWprTraces | |
| StopWprTraces     | Avbryt insamlingen av fel söknings spårnings loggar med Windows Performance inspelnings verktyg. | Microsoft. Azure. Cosmos. emulator. exe/StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Som standard återskapar emulatorn sitt självsignerade SSL-certifikat om certifikatets SAN inte innehåller emulatorns domän namn, lokal IPv4-adress, localhost och 127.0.0.1. Med det här alternativet går det inte att starta emulatorn vid start i stället. Du bör sedan använda alternativet/GenCert för att skapa och installera ett nytt självsignerat SSL-certifikat. | Microsoft. Azure. Cosmos. emulator. exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Skapa och installera ett nytt självsignerat SSL-certifikat. Du kan också inkludera en kommaavgränsad lista över ytterligare DNS-namn för att få åtkomst till emulatorn över nätverket. | Microsoft. Azure. Cosmos. emulator. exe/GenCert =\<DNS-Names\> |\<DNS-namn\>: valfri kommaavgränsad lista över ytterligare DNS-namn  |
| DirectPorts |Anger portarna som ska användas för direktanslutning. Standardvärdena är 10251, 10252, 10253, 10254. | Microsoft. Azure. Cosmos. emulator. exe/DirectPorts:\<DirectPorts\> | \<directports\>: Kommaavgränsad lista över 4 portar |
| Nyckel |Auktoriseringsnyckel för emulatorn. Nyckeln måste vara en base-64-kodning av en 64 bytes vektor. | Microsoft. Azure. Cosmos. emulator. exe/Key:\<nyckel\> | \<key\>: Nyckeln måste vara en base-64-kodning av en 64 bytes vektor|
| EnableRateLimiting | Anger att begränsande beteende för förfrågningsfrekvens är aktiverat. |Microsoft. Azure. Cosmos. emulator. exe/EnableRateLimiting | |
| DisableRateLimiting |Anger att begränsande beteende för förfrågningsfrekvens är inaktiverat. |Microsoft. Azure. Cosmos. emulator. exe/DisableRateLimiting | |
| NoUI | Visa inte emulatorns användargränssnitt. | Microsoft. Azure. Cosmos. emulator. exe/NoUI | |
| NoExplorer | Visa inte datautforskaren vid start. |Microsoft. Azure. Cosmos. emulator. exe/NoExplorer | | 
| PartitionCount | Anger det högsta antalet partitionerade behållare. Mer information finns i [ändra antalet behållare](#set-partitioncount) . | Microsoft. Azure. Cosmos. emulator. exe/PartitionCount =\<PartitionCount\> | \<partitioncount\>: maximalt antal tillåtna behållare för enskilda partitioner. Standardvärdet är 25. Maxvärdet är 250 GB.|
| DefaultPartitionCount| Anger standardvärdet för antalet partitioner för en partitionerad behållare. | Microsoft. Azure. Cosmos. emulator. exe/DefaultPartitionCount =\<DefaultPartitionCount\> | \<defaultpartitioncount\> standardvärdet är 25.|
| AllowNetworkAccess | Ger åtkomst till emulatorn över ett nätverk. Du måste även skicka /Key=\<key_string\> eller /KeyFile=\<file_name\> för att aktivera nätverksåtkomst. | Microsoft. Azure. Cosmos. emulator. exe/AllowNetworkAccess/Key =\<key_string\> eller Microsoft. Azure. Cosmos. emulator. exe/AllowNetworkAccess/KeyFile =\<file_name\>| |
| NoFirewall | Ändra inte brand Väggs regler när alternativet/AllowNetworkAccess används. |Microsoft. Azure. Cosmos. emulator. exe/NoFirewall | |
| GenKeyFile | Generera en ny auktoriseringsnyckel och spara den i den angivna filen. Den genererade nyckeln kan användas med alternativen /Key eller /KeyFile. | Microsoft. Azure. Cosmos. emulator. exe/GenKeyFile =\<sökväg till nyckel filen\> | |
| Konsekvens | Ställ in konsekvensnivå för kontot. | Microsoft. Azure. Cosmos. emulator. exe/Consistency =\<konsekvens\> | \<consistency\>: Värdet måste ha någon av följande [konsekvensnivåer](consistency-levels.md): session, stark, eventuell eller BoundedStaleness. Standardvärdet är Session. |
| ? | Visa hjälpmeddelandet.| | |

## <a id="set-partitioncount"></a>Ändra antalet behållare

Som standard kan du skapa upp till 25 behållare med fast storlek (stöds endast med Azure Cosmos DB SDK: er) eller 5 obegränsade behållare med Azure Cosmos-emulatorn. Genom att ändra **PartitionCount** -värdet kan du skapa upp till 250 fast storleks behållare eller 50 obegränsade behållare, eller någon kombination av de två som inte överstiger 250 fasta storleks behållare (där en obegränsad container = 5 fast storleks behållare). Det rekommenderas dock inte att ställa in emulatorn så att den körs med fler än 200 behållare med fast storlek. På grund av den omkostnader som läggs till i diskens IO-åtgärder, vilket leder till oförutsägbara tids gränser vid användning av slut punkts-API: er.

Om du försöker skapa en behållare när det aktuella antalet partitioner har överskridits, genererar emulatorn ett ServiceUnavailable-undantag med följande meddelande.

"Tyvärr har vi för närvarande hög efter frågan i den här regionen och kan inte slutföra din begäran för tillfället. Vi arbetar kontinuerligt för att få mer och mer kapacitet online och uppmana dig att försöka igen.
Ta inte ovilliga till e-askcosmosdb@microsoft.com när som helst eller av någon anledning.
ActivityId: 12345678-1234-1234-1234-123456789abc "

Kör följande steg för att ändra antalet behållare som är tillgängliga i Azure Cosmos-emulatorn:

1. Ta bort alla lokala Azure Cosmos-emulator-data genom att högerklicka på ikonen **Azure Cosmos DB emulator** i system fältet och sedan klicka på **Återställ data.** .
2. Ta bort alla emulator-data i den här mappen `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Avsluta alla öppna instanser genom att högerklicka på ikonen för **Azure Cosmos DB-emulator** i meddelandefältet och klicka sedan på **Avsluta**. Det kan ta någon minut för alla instanser att avslutas.
4. Installera den senaste versionen av [Azure Cosmos-emulatorn](https://aka.ms/cosmosdb-emulator).
5. Starta emulatorn med PartitionCount-flaggan genom att ställa in ett värde <= 250. Till exempel: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Kontrollera emulatorn

Emulatorn levereras med en PowerShell-modul för att starta, stoppa, avinstallera och hämta tjänstens status. Kör följande cmdlet för att använda PowerShell-modulen:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

eller placera `PSModules` katalogen på `PSModulesPath` och importera den enligt följande kommando:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Här följer en sammanfattning av kommandon för att styra emulatorn från PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Kommentarer**

Returnerar någon av dessa ServiceControllerStatus-värden: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running eller ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Kommentarer**

Startar emulatorn. Som standard väntar kommandot till emulatorn är redo att ta emot begäranden. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort den startar emulatorn.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Kommentarer**

Stoppar emulatorn. Som standard väntar kommandot tills emulatorn är helt avstängd. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort emulatorn börjar stängas av.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Kommentarer**

Avinstallerar emulatorn och tar eventuellt bort allt innehåll i $env:LOCALAPPDATA\CosmosDbEmulator.
Cmdleten garanterar att emulatorn stoppas innan den avinstalleras.

## <a name="running-on-docker"></a>Köra på Docker

Azure Cosmos-emulatorn kan köras i Docker för Windows. Emulatorn fungerar inte i Docker för Oracle Linux.

När du har installerat [Docker för Windows](https://www.docker.com/docker-windows) växlar du till Windows-containrar genom att högerklicka på Docker-ikonen i verktygsfältet och välja **Växla till Windows-containrar**.

Hämta därefter emulatoravbildningen från Docker-hubben genom att köra följande kommando i ditt favoritgränssnitt.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Starta avbildningen genom att köra följande kommandon.

Från kommandoraden:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Om du ser ett port konflikt fel (den angivna porten används redan) när du kör kommandot Docker Run kan du skicka en anpassad port genom att ändra port numren. Du kan till exempel ändra "-p 8081:8081" till "-p 443:8081"

Från PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

Svaret ser ut ungefär så här:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Använd nu slutpunkten och huvudnyckeln från svaret i din klient och importera SSL-certifikatet till din värd. För att importera SSL-certifikatet gör du följande från en kommandotolk för administratör:

Från kommandoraden:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Från PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Om du stänger det interaktiva gränssnittet när emulatorn har startats stängs emulatorns container.

Öppna Datautforskaren genom att gå till följande URL i webbläsaren. Emulatorns slutpunkt finns i svarsmeddelandet ovan.

    https://<emulator endpoint provided in response>/_explorer/index.html

Om du har ett .NET-klient program som körs på en Linux Docker-behållare och om du kör Azure Cosmos-emulatorn på en värddator, i det här fallet kan du inte ansluta till Azure Cosmos-kontot från emulatorn. Eftersom appen inte körs på värddatorn går det inte att lägga till det certifikat som är registrerat på den Linux-behållare som matchar emulatorns slut punkt. 

Som en lösning kan du inaktivera verifieringen av serverns SSL-certifikat från klient programmet genom att skicka en `HttpClientHandler` instans som visas i följande exempel för .NET-kod. Den här lösningen kan bara användas om du använder `Microsoft.Azure.DocumentDB` NuGet-paketet, så stöds det inte med `Microsoft.Azure.Cosmos` NuGet-paketet:
 
 ```csharp
var httpHandler = new HttpClientHandler()
{
    ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
};
 
using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
{
    RunDatabaseDemo(client).GetAwaiter().GetResult();
}
```

Förutom att inaktivera verifiering av SSL-certifikat är det viktigt att du startar emulatorn med alternativet `/allownetworkaccess` och emulatorns slut punkt är tillgänglig från värd-IP-adressen i stället för `host.docker.internal` DNS.

## Körs på Mac eller Linux<a id="mac"></a>

För närvarande kan Cosmos-emulatorn endast köras i Windows. Användare som kör Mac eller Linux kan köra emulatorn på en virtuell Windows-dator som värd för en hypervisor, till exempel paralleller eller VirtualBox. Nedan visas stegen för att aktivera detta.

Kör kommandot nedan i den virtuella Windows-datorn och anteckna IPv4-adressen.

```cmd
ipconfig.exe
```

I ditt program måste du ändra URI för DocumentClient-objektet för att kunna använda IPv4-adressen som returnerades av `ipconfig.exe`. Nästa steg är att kringgå CA-verifieringen när du skapar DocumentClient-objektet. För det här måste du ange en HttpClientHandler för DocumentClient-konstruktorn, som har den egna implementeringen för ServerCertificateCustomValidationCallback.

Nedan visas ett exempel på hur koden ska se ut.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

I den virtuella Windows-datorn startar du slutligen Cosmos-emulatorn från kommando raden med hjälp av följande alternativ.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>Felsöka

Använd följande tips för att felsöka problem som kan uppstå med Azure Cosmos-emulatorn:

- Om du har installerat en ny version av emulatorn och fel uppstår ska du återställa dina data. Du kan återställa dina data genom att högerklicka på ikonen för Azure Cosmos-emulatorn i system fältet och sedan klicka på Återställ data.... Om detta inte löser felen kan du avinstallera emulatorn och eventuella äldre versioner av emulatorn om de hittas, ta bort katalogen C:\Program files\Azure Cosmos DB emulator och installera om emulatorn. I [Avinstallera den lokala emulatorn](#uninstall) finns instruktioner.

- Om Azure Cosmos-emulatorn kraschar samlar du in dumpa filer från mappen%LOCALAPPDATA%\CrashDumps, komprimerar dem och bifogar dem till ett e-postmeddelande till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Om du upplever krascher i `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`kan detta vara ett symtom där prestanda räknarna är i ett skadat tillstånd. Genom att köra följande kommando från en administratörs kommando tolk åtgärdar du problemet:

  ```cmd
  lodctr /R
   ```

- Om du stöter på ett anslutningsfel ska du [samla in spårningsfiler](#trace-files), komprimera dem och bifoga dem i ett e-postmeddelande till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Om du ser ett meddelande om att **tjänsten inte är tillgänglig** kanske emulatorn misslyckas med att initiera nätverksstacken. Se efter om du har Pulse Secure-klienten eller Juniper-nätverksklienten installerad, eftersom deras nätverksfilterdrivrutiner kan orsaka problemet. Avinstallation av nätverksfilterdrivrutiner från tredje part åtgärdar vanligen problemet. Du kan också starta emulatorn med/DisableRIO, som växlar kommunikationen mellan emulatorn och den vanliga Winsock-anslutningen. 

- Om datorn försätts i viloläge eller om operativsystemet uppdateras när emulatorn körs kan du se meddelandet **Tjänsten är inte tillgänglig just nu**. Återställ emulatorns data genom att högerklicka på ikonen som visas i meddelande fältet i Windows och välj **Återställ data**.

### <a id="trace-files"></a>Samla in spårningsfiler

För att samla in felsökningsspårningar kör du följande kommandon från en administrativ kommandotolk:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Titta på systemfältet att kontrollera att programmet har avslutats. Det kan ta ett tag. Du kan också klicka på **Avsluta** i användar gränssnittet för Azure Cosmos-emulatorn.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Återskapa problemet. Om Datautforskaren inte fungerar behöver du vara vänta tills webbläsaren öppnas i några sekunder för att upptäcka felet.
5. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
6. Gå till `%ProgramFiles%\Azure Cosmos DB Emulator` och leta rätt på filen docdbemulator_000001.etl.
7. Skicka .etl-filen tillsammans med reproduktionssteg till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) för felsökning.

### <a id="uninstall"></a>Avinstallera den lokala emulatorn

1. Avsluta alla öppna instanser av den lokala emulatorn genom att högerklicka på ikonen för Azure Cosmos-emulatorn i system fältet och sedan klicka på avsluta. Det kan ta någon minut för alla instanser att avslutas.
2. I Windows-sökrutan skriver du **Appar och funktioner** och klickar på resultatet för **Appar och funktioner (systeminställningar)** .
3. I listan över appar bläddrar du till **Azure Cosmos DB-emulatorn**, väljer den, klickar på **Avinstallera** och bekräftar sedan och klickar på **Avinstallera** igen.
4. När appen är avinstallerad navigerar du till `%LOCALAPPDATA%\CosmosDBEmulator` och tar bort mappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda den lokala emulatorn för kostnadsfri lokal utveckling. Du kan nu fortsätta till nästa självstudie och lära dig att exportera SSL-certifikat för emulatorn.

> [!div class="nextstepaction"]
> [Exportera Azure Cosmos-emulatorns certifikat](local-emulator-export-ssl-certificates.md)
