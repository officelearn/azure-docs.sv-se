---
title: Utveckla lokalt med Azure Cosmos Emulator
description: Med Azure Cosmos Emulator kan du utveckla och testa ditt program lokalt utan att skapa en Azure-prenumeration.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 9650bb3214c22926427717569f718ca0426ed729
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618752"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Använd Azure Cosmos Emulator för lokal utveckling och testning

Azure Cosmos Emulator tillhandahåller en lokal miljö som emulerar Azure Cosmos DB-tjänsten i utvecklingssyfte. Med Azure Cosmos Emulator kan du utveckla och testa ditt program lokalt, utan att skapa en Azure-prenumeration eller ådra dig några kostnader. När du är nöjd med hur ditt program fungerar i Azure Cosmos Emulator kan du växla till att använda ett Azure Cosmos-konto i molnet.

Du kan utveckla med Azure Cosmos Emulator med [SQL-,](local-emulator.md#sql-api) [Cassandra-,](local-emulator.md#cassandra-api) [MongoDB-,](local-emulator.md#azure-cosmos-dbs-api-for-mongodb) [Gremlin-](local-emulator.md#gremlin-api)och [Table](local-emulator.md#table-api) API-konton. Men just nu stöder datautforskaren i emulatorn endast klienter för SQL API. 

## <a name="how-the-emulator-works"></a>Så här fungerar emulatorn

Azure Cosmos Emulator tillhandahåller en hifi-emulering av Azure Cosmos DB-tjänsten. Den stöder identiska funktioner som Azure Cosmos DB, inklusive stöd för att skapa och fråga data, etablera och skala behållare och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med Azure Cosmos Emulator och distribuera dem till Azure på global nivå genom att bara göra en enda konfigurationsändring till anslutningsslutpunkten för Azure Cosmos DB.

Även om emuleringen av Azure Cosmos DB-tjänsten är verklighetstrogen så implementeras emulatorn på ett annat sätt än tjänsten. Till exempel använder emulatorn standardkomponenter i operativsystemet som det lokala filsystemet för beständighet och HTTP-protokollstacken för anslutningar. Funktioner som är beroende av Azure-infrastruktur som global replikering, ensiffriga millisekunders svarstid för läsningar/skrivningar och avstämbara konsekvensnivåer är inte tillämpliga.

Du kan migrera data mellan Azure Cosmos Emulator och Azure Cosmos DB-tjänsten med hjälp av [Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool).

Du kan köra Azure Cosmos Emulator på Windows Docker-behållaren, se [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) `Dockerfile` för docker pull-kommandot och [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) för och mer information.

## <a name="differences-between-the-emulator-and-the-service"></a>Skillnader mellan emulatorn och tjänsten

Eftersom Azure Cosmos Emulator tillhandahåller en emulerad miljö som körs på den lokala utvecklararbetsstationen finns det vissa skillnader i funktionalitet mellan emulatorn och ett Azure Cosmos-konto i molnet:

* För närvarande stöder Data Explorer i emulatorn klienter för SQL API. Data Explorer-vyn och åtgärder för Azure Cosmos DB API:er som MongoDB, Tabell, Graph och Cassandra API:er stöds inte fullt ut.
* Azure Cosmos Emulator stöder endast ett enda fast konto och en välkänd huvudnyckel. Nyckelförnyelse är inte möjlig i Azure Cosmos Emulator, men standardnyckeln kan ändras med kommandoradsalternativet.
* Azure Cosmos Emulator är inte en skalbar tjänst och stöder inte ett stort antal behållare.
* Azure Cosmos Emulator erbjuder inte olika [Azure Cosmos DB-konsekvensnivåer](consistency-levels.md).
* Azure Cosmos Emulator erbjuder inte [replikering i flera regioner](distribute-data-globally.md).
* Eftersom ditt exemplar av Azure Cosmos Emulator kanske inte alltid är uppdaterat med de senaste ändringarna i Azure Cosmos DB-tjänsten, bör du hänvisa till [Azure Cosmos DB-kapacitetsplaneraren](https://www.documentdb.com/capacityplanner) för att exakt uppskatta behoven av produktionsdataflöde (RU: er) för ditt program.
* När du använder Azure Cosmos Emulator kan du som standard skapa upp till 25 behållare med fast storlek (stöds endast med Azure Cosmos DB SDK: er) eller 5 obegränsade behållare med Azure Cosmos Emulator. Mer information om att ändra värdet finns i [Setting the PartitionCount value](#set-partitioncount) (Ange PartitionCount-värdet).

## <a name="system-requirements"></a>Systemkrav

Azure Cosmos Emulator har följande maskinvaru- och programvarukrav:

* Programvarukrav
  * Windows Server 2012 R2, Windows Server 2016 eller Windows 10
  * 64-bitars operativsystem
* Lägsta maskinvarukrav
  * 2 GB RAM-minne
  * 10 GB ledigt hårddiskutrymme

## <a name="installation"></a>Installation

Du kan hämta och installera Azure Cosmos Emulator från [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) eller köra emulatorn på Docker för Windows. Instruktioner om hur du använder emulatorn i Docker för Windows finns i [Köra med Docker](#running-on-docker).

> [!NOTE]
> Om du vill installera, konfigurera och köra Azure Cosmos Emulator måste du ha administratörsbehörighet på datorn. Emulatorn skapar/lägger till ett certifikat och ställer även in brandväggsreglerna för att kunna köra sina tjänster. Därför är det nödvändigt för emulatorn att kunna utföra sådana operationer.

## <a name="running-on-windows"></a>Köra i Windows

Om du vill starta Azure Cosmos Emulator väljer du Start-knappen eller trycker på Windows-tangenten. Börja skriva **Azure Cosmos Emulator**och välj emulatorn i listan över program.

![Välj Start-knappen eller tryck på Windows-tangenten, börja skriva **Azure Cosmos Emulator**, och välj emulatorn i listan över program](./media/local-emulator/database-local-emulator-start.png)

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows. ![Azure Cosmos DB lokal emulator aktivitetsfält meddelande](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos Emulator körs som standard på den lokala datorn ("localhost") som lyssnar på port 8081.

Azure Cosmos Emulator är `C:\Program Files\Azure Cosmos DB Emulator` installerad till som standard. Du kan också starta och stoppa emulatorn från kommandoraden. För mer information, se [kommandoradsverktygsreferensen](#command-line).

## <a name="start-data-explorer"></a>Starta Datautforskaren

När Azure Cosmos Emulator startar öppnas automatiskt Azure Cosmos Data Explorer i din webbläsare. Adressen visas som `https://localhost:8081/_explorer/index.html`. Om du stänger utforskaren och vill öppna den igen senare kan du antingen öppna URL:en i webbläsaren eller starta den från Azure Cosmos Emulator i Windows-ikonen som visas nedan.

![Azure Cosmos lokala emulator data explorer launcher](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Leta efter uppdateringar

Datautforskaren anger om det finns en ny uppdatering som går att ladda ned.

> [!NOTE]
> Data som skapas i en version av Azure Cosmos Emulator (se %LOCALAPPDATA%\CosmosDBEmulator eller valfria inställningar för datasökvägen) är inte garanterat tillgängliga när du använder en annan version. Om du behöver spara dina data på lång sikt rekommenderar vi att du lagrar dessa data i ett Azure Cosmos-konto i stället för i Azure Cosmos Emulator.

## <a name="authenticating-requests"></a>Autentisera förfrågningar

Precis som med Azure Cosmos DB i molnet måste varje begäran som du gör mot Azure Cosmos Emulator autentiseras. Azure Cosmos Emulator stöder ett enda fast konto och en välkänd autentiseringsnyckel för huvudnyckelautentisering. Det här kontot och nyckeln är de enda autentiseringsuppgifter som tillåts för användning med Azure Cosmos Emulator. De är:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Huvudnyckeln som stöds av Azure Cosmos Emulator är endast avsedd att användas med emulatorn. Du kan inte använda ditt Azure Cosmos DB-konto och nyckel med Azure Cosmos Emulator.

> [!NOTE]
> Om du har startat emulatorn med alternativet /Key använder `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`du den genererade nyckeln i stället för . Mer information om alternativet /Key finns i [Kommandoradsverktygets referens.](#command-line)

Precis som med Azure Cosmos DB stöder Azure Cosmos Emulator endast säker kommunikation via TLS.

## <a name="running-on-a-local-network"></a>Köra på ett lokalt nätverk

Du kan köra emulatorn på ett lokalt nätverk. Om du vill aktivera `/AllowNetworkAccess` nätverksåtkomst anger du alternativet på [kommandoraden](#command-line-syntax), vilket också kräver att du anger `/Key=key_string` eller `/KeyFile=file_name`. Du kan `/GenKeyFile=file_name` använda för att generera en fil med en slumpmässig nyckel i förväg. Då kan du `/KeyFile=file_name` skicka `/Key=contents_of_file`det till eller .

Om du vill aktivera nätverksåtkomst för första gången ska användaren stänga av emulatorn och ta bort emulatorns datakatalog (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Utveckla med emulatorn

### <a name="sql-api"></a>API för SQL

När du har Azure Cosmos Emulator som körs på skrivbordet kan du använda alla [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) som stöds eller [Azure Cosmos DB REST API](/rest/api/cosmos-db/) för att interagera med emulatorn. Azure Cosmos Emulator innehåller också en inbyggd Data Explorer som låter dig skapa behållare för SQL API eller Cosmos DB för Mongo DB API och visa och redigera objekt utan att skriva någon kod.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

När du har Azure Cosmos Emulator som körs på skrivbordet kan du använda [Azure Cosmos DB:s API för MongoDB för](mongodb-introduction.md) att interagera med emulatorn. Starta emulator från kommandotolken som administratör med "/EnableMongoDbEndpoint". Använd sedan följande anslutningssträng för att ansluta till MongoDB API-kontot:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabell-API

När du har Azure Cosmos Emulator som körs på skrivbordet kan du använda [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) för att interagera med emulatorn. Starta emulatorn från kommandotolken som administratör med "/EnableTableEndpoint". Kör sedan följande kod för att ansluta till tabell-API-kontot:

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

### <a name="cassandra-api"></a>Cassandra-API

Starta emulator från en administratörskommandofråga med "/EnableCassandraEndpoint". Alternativt kan du också ställa `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`in miljövariabeln .

* [Installera Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Installera Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Kör följande kommandon i ett vanligt kommandotolksfönster:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* I CQLSH-skalet kör du följande kommandon för att ansluta till Cassandra-slutpunkten:

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

Starta emulator från en administratörskommandofråga med "/EnableGremlinEndpoint". Alternativt kan du också ställa in miljövariabeln`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installera apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* I emulatorns Data Explorer skapa en databas "db1" och en samling "coll1"; för partitionsnyckeln väljer du "/name"

* Kör följande kommandon i ett vanligt kommandotolksfönster:

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

* I gremlinskalet körs följande kommandon för att ansluta till Gremlin-ändpunkten:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Exportera TLS/SSL-certifikatet

.NET-språk och -körning använder Windows Certificate Store för att säkert ansluta till den lokala Azure Cosmos DB-emulatorn. Andra språk har sin egen metod för att hantera och använda certifikat. Java använder sitt eget [certifikatarkiv](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) medan Python använder [socketomslutningar](https://docs.python.org/2/library/ssl.html).

För att hämta ett certifikat som ska användas med språk och körningar som inte integrerar med Windows Certificate Store måste du exportera det med Windows Certificate Manager. Du kan starta den genom att köra certlm.msc eller följa steg för steg-instruktioner i [Exportera Azure Cosmos Emulator-certifikat](./local-emulator-export-ssl-certificates.md). När certifikathanteraren körs öppnar du personliga certifikat som du ser nedan och exporterar certifikatet med det egna namnet ”DocumentDBEmulatorCertificate” som en BASE-64-kodad X.509-fil (.cer).

![Azure Cosmos DB lokal emulator TLS/SSL-certifikat](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Följ instruktionerna i avsnittet [om att lägga till ett certifikat i Java CA-certifikatarkiv](https://docs.microsoft.com/azure/java-add-certificate-ca-store) för att importera X.509-certifikatet till standardcertifikatarkivet för Java. När certifikatet har importerats till certifikatarkivet kan klienter för SQL och Azure Cosmos DB:s API för MongoDB ansluta till Azure Cosmos Emulator.

När du ansluter till emulatorn från Python och Node.js SDK:er inaktiveras TLS-verifieringen.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Kommandoradsverktygsreferens
Från installationsplatsen kan du använda kommandoraden för att starta och stoppa emulatorn, konfigurera alternativ och utföra andra åtgärder.

### <a name="command-line-syntax"></a>Syntax för kommandorad

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Om du vill visa en lista över alternativ skriver du `Microsoft.Azure.Cosmos.Emulator.exe /?` i kommandotolken.

|**Alternativet** | **Beskrivning** | **Kommandot**| **Argument**|
|---|---|---|---|
|[Inga argument] | Startar Azure Cosmos Emulator med standardinställningar. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Hjälp] |Visar en lista över kommandoradsargument som stöds.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Hämtar status för Azure Cosmos Emulator. Statusen visas med slutkoden: 1 = Startar, 2 = Körs, 3 = Stoppad. En negativ slutkod anger att ett fel har uppstått. Inga andra utdata produceras. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Avstängning| Stänger av Azure Cosmos Emulator.| Microsoft.Azure.Cosmos.Emulator.exe /Avstängning | |
|DataPath | Anger den sökväg där du kan lagra filer. Standardvärdet är %LocalAppdata%\CosmosDBEmulator. | Datasökvägen Microsoft.Azure.Cosmos.Emulator.exe\</DataPath=\> | \<datapath\>: En åtkomlig sökväg |
|Port | Anger det portnummer som ska användas för emulatorn. Standardvärdet är 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=-port\<\> | \<port\>: Enskilt portnummer |
| ComputePort (beräkningsport) | Angav det portnummer som ska användas för tjänsten Compute Interop Gateway. Gatewayens HTTP-slutpunktsavsökningsport beräknas som ComputePort + 79. Därför måste ComputePort och ComputePort + 79 vara öppna och tillgängliga. Standardvärdet är 8900. | Computeport för Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<\> | \<computeport\>: Enportnummer |
| AktiveraMongoDbEndpoint=3.2 | Aktiverar MongoDB API 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| AktiveraMongoDbEndpoint=3.6 | Aktiverar MongoDB API 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Anger det portnummer som ska användas för API för MongoDB-kompatibilitet. Standardvärdet är 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: Enskilt portnumber|
| EnableCassandraEndpoint | Aktiverar Cassandra API | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort (på andra) | Anger det portnummer som ska användas för Cassandra-slutpunkten. Standardvärdet är 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Enportnummer |
| AktiveraGremlinEndpoint | Aktiverar Gremlin API | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort (på en) | Portnummer som ska användas för Gremlin-ändpunkten. Standardvärdet är 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=-port\<\> | \<port\>: Enskilt portnummer |
|AktiveratabellEndpoint | Aktiverar Azure Table API | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort (Bordshamn) | Portnummer som ska användas för Slutpunkten för Azure Table. Standardvärdet är 8902. | Port för Microsoft.Azure.Cosmos.Emulator.exe\</TablePort=\> | \<port\>: Enskilt portnummer|
| Nyckelfil | Läs auktoriseringsnyckeln från den angivna filen. Använd alternativet /GenKeyFile för att generera en nyckelfil | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Sökväg till filen |
| ResetDataPath | Tar rekursivt bort alla filer i den angivna sökvägen. Om du inte anger en sökväg är den som standard %LOCALAPPDATA%\CosmosDbEmulator | Sökvägen Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<> | \<sökväg\>: Sökväg för filer  |
| StartTraces  |  Börja samla in felsökningsspårningsloggar med LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Sluta samla in felsökningsspårningsloggar med LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Börja samla in felsökningsspårningsloggar med windows prestandainspelningsverktyg. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces (StopWprTraces)     | Sluta samla in felsökningsspårningsloggar med windows prestandainspelningsverktyg. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Som standard återskapar Emulator sitt självsignerade TLS/SSL-certifikat, om certifikatets SAN inte innehåller emulatorvärdens domännamn, lokal IPv4-adress, "localhost" och "127.0.0.1". Med det här alternativet misslyckas emulatorn vid start i stället. Du bör sedan använda alternativet /GenCert för att skapa och installera ett nytt självsignerat TLS/SSL-certifikat. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert (GenCert) | Generera och installera ett nytt självsignerat TLS/SSL-certifikat. du kan också inkludera en kombiavskiljande lista med ytterligare DNS-namn för åtkomst till Emulatorn över nätverket. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-namn\> |\<dns-namn:\>Valfri kommaavgränsad lista över ytterligare DNS-namn  |
| DirectPorts |Anger portarna som ska användas för direktanslutning. Standardvärdena är 10251, 10252, 10253, 10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Kommaavgränsad lista över 4 portar |
| Nyckel |Auktoriseringsnyckel för emulatorn. Nyckeln måste vara en base-64-kodning av en 64 bytes vektor. | Microsoft.Azure.Cosmos.Emulator.exe /Nyckel:\<nyckel\> | \<key\>: Nyckeln måste vara en base-64-kodning av en 64 bytes vektor|
| EnableRateLimiting | Anger att begränsande beteende för förfrågningsfrekvens är aktiverat. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Anger att begränsande beteende för förfrågningsfrekvens är inaktiverat. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Visa inte emulatorns användargränssnitt. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Visa inte datautforskaren vid start. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Anger det maximala antalet partitionerade behållare. Mer information [finns i Ändra antalet behållare.](#set-partitioncount) | Partitionsräknar i Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<\> | \<partitionsantal\>: Maximalt antal tillåtna enskilda partitionsbehållare. Standardvärdet är 25. Maxvärdet är 250 GB.|
| DefaultPartitionCount| Anger standardantalet partitioner för en partitionerad behållare. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<standardpartitioncount\> | \<Standardvärde för\> standardpartitioncount är 25.|
| AllowNetworkAccess | Ger åtkomst till emulatorn över ett nätverk. Du måste även skicka /Key=\<key_string\> eller /KeyFile=\<file_name\> för att aktivera nätverksåtkomst. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> eller Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Justera inte brandväggsregler när alternativet Tillåten Anslutning används. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Generera en ny auktoriseringsnyckel och spara den i den angivna filen. Den genererade nyckeln kan användas med alternativen /Key eller /KeyFile. | Sökvägen till nyckelfilen i Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<\> | |
| Konsekvens | Ställ in konsekvensnivå för kontot. | Konsekvens för Microsoft.Azure.Cosmos.Emulator.exe\</Konsekvens=\> | \<consistency\>: Värdet måste ha någon av följande [konsekvensnivåer](consistency-levels.md): session, stark, eventuell eller BoundedStaleness. Standardvärdet är Session. |
| ? | Visa hjälpmeddelandet.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Ändra antalet behållare

Som standard kan du skapa upp till 25 behållare med fast storlek (stöds endast med Azure Cosmos DB SDK: er) eller 5 obegränsade behållare med Azure Cosmos Emulator. Genom att ändra **PartitionCount-värdet** kan du skapa upp till 250 behållare med fast storlek eller 50 obegränsade behållare, eller en kombination av de två som inte överstiger 250 behållare med fast storlek (där en obegränsad behållare = 5 behållare i fast storlek). Det rekommenderas dock inte att ställa in emulatorn för att köras med mer än 200 behållare med fast storlek. På grund av de omkostnader som läggs till i disk-IO-åtgärder, vilket resulterar i oförutsägbara timeouts när du använder slutpunkts-API:erna.

Om du försöker skapa en behållare efter att det aktuella partitionsantalet har överskridits, genererar emulatorn ett ServiceUnavailable-undantag med följande meddelande.

"Tyvärr, vi upplever för närvarande hög efterfrågan i denna region, och kan inte uppfylla din begäran just nu. Vi arbetar kontinuerligt för att få mer och mer kapacitet online, och uppmuntra dig att försöka igen.
ActivityId: 12345678-1234-1234-1234-123456789abc"

Om du vill ändra antalet behållare som är tillgängliga i Azure Cosmos Emulator kör du följande steg:

1. Ta bort alla lokala Azure Cosmos Emulator-data genom att högerklicka på azure **Cosmos DB Emulator-ikonen** i systemfältet och klicka sedan på **Återställ data...**.
2. Ta bort alla emulatordata i den här mappen `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Avsluta alla öppna instanser genom att högerklicka på ikonen för **Azure Cosmos DB-emulator** i meddelandefältet och klicka sedan på **Avsluta**. Det kan ta någon minut för alla instanser att avslutas.
4. Installera den senaste versionen av [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator).
5. Starta emulatorn med PartitionCount-flaggan genom att ställa in ett värde <= 250. Till exempel: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Kontrollera emulatorn

Emulatorn levereras med en PowerShell-modul för att starta, stoppa, avinstallera och hämta tjänstens status. Kör följande cmdlet för att använda PowerShell-modulen:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

eller placera `PSModules` katalogen `PSModulesPath` på din och importera den som visas i följande kommando:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Här följer en sammanfattning av kommandon för att styra emulatorn från PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Anmärkningar**

Returnerar någon av dessa ServiceControllerStatus-värden: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running eller ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Anmärkningar**

Startar emulatorn. Som standard väntar kommandot till emulatorn är redo att ta emot begäranden. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort den startar emulatorn.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Anmärkningar**

Stoppar emulatorn. Som standard väntar kommandot tills emulatorn är helt avstängd. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort emulatorn börjar stängas av.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Anmärkningar**

Avinstallerar emulatorn och tar eventuellt bort allt innehåll i $env:LOCALAPPDATA\CosmosDbEmulator.
Cmdleten garanterar att emulatorn stoppas innan den avinstalleras.

## <a name="running-on-docker"></a>Köra på Docker

Azure Cosmos Emulator kan köras på Docker för Windows. Emulatorn fungerar inte i Docker för Oracle Linux.

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
> Om ett portkonfliktfel (angiven port redan används) när du kör kommandot docker run kan du skicka en anpassad port genom att ändra portnumren. Du kan till exempel ändra "-p 8081:8081" till "-p 443:8081"

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

Använd nu slutpunkten och huvudnyckeln från svaret i klienten och importera TLS/SSL-certifikatet till värden. Så här importerar du TLS/SSL-certifikatet från en kommandotolk för administratörer:

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

Om du stänger det interaktiva skalet när emulatorn har startats stängs emulatorns behållare.

Öppna Datautforskaren genom att gå till följande URL i webbläsaren. Emulatorns slutpunkt finns i svarsmeddelandet ovan.

    https://<emulator endpoint provided in response>/_explorer/index.html

Om du har ett .NET-klientprogram som körs på en Linux-docker-behållare och om du kör Azure Cosmos-emulator på en värddator följer du avsnittet nedan för Linux för att importera certifikatet till Linux-docker-behållaren.

## <a name="running-on-mac-or-linux"></a>Köras på Mac eller Linux<a id="mac"></a>

För närvarande cosmos emulator kan bara köras på Windows. Användare som kör Mac eller Linux kan köra emulatorn i en Virtuell Windows-dator värd en hypervisor som Paralleller eller VirtualBox. Nedan följer stegen för att aktivera detta.

I Windows VM kör kommandot nedan och anteckna IPv4-adressen.

```cmd
ipconfig.exe
```

I ditt program måste du ändra URI som används som slutpunkt `ipconfig.exe` för `localhost`att använda IPv4-adressen som returneras av i stället för .

Nästa steg, från windows VM, starta Cosmos-emulatorn från kommandoraden med hjälp av följande alternativ.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Slutligen måste vi importera Emulator CA-certifikatet till Linux- eller Mac-miljön.

### <a name="linux"></a>Linux

Om du arbetar med Linux, .NET reläer på OpenSSL för att göra valideringen:

1. [Exportera certifikatet i PFX-format](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX är tillgängligt när du väljer att exportera den privata nyckeln). 

1. Kopiera pfx-filen till din Linux-miljö.

1. Konvertera PFX-filen till en CRT-fil

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Kopiera CRT-filen till mappen som innehåller anpassade certifikat i din Linux-distribution. Vanligtvis på Debiandistributioner, det `/usr/local/share/ca-certificates/`finns på .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Uppdatera certifikatutfärdarcertifikaten, `/etc/ssl/certs/` som uppdaterar mappen.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Gör så här om du arbetar på Mac:

1. [Exportera certifikatet i PFX-format](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX är tillgängligt när du väljer att exportera den privata nyckeln).

1. Kopiera PFX-filen till mac-miljön.

1. Öppna *programmet Nyckelringsåtkomst* och importera PFX-filen.

1. Öppna listan över certifikat och identifiera den `localhost`med namnet .

1. Öppna snabbmenyn för det aktuella objektet, välj *Hämta objekt* och *välj* >  *Alltid förtroende*när du använder det*här certifikatalternativet* . 

   ![Öppna snabbmenyn för det aktuella objektet, välj Hämta objekt och under Förtroende - När du använder det här certifikatalternativet väljer du Alltid förtroende](./media/local-emulator/mac-trust-certificate.png)

När du har följt dessa steg litar din miljö på certifikatet som används `/AllowNetworkAccess`av emulatorn när du ansluter till IP-adressen som exponeras av .

## <a name="troubleshooting"></a>Felsökning

Använd följande tips för att felsöka problem som du stöter på med Azure Cosmos Emulator:

- Om du har installerat en ny version av emulatorn och fel uppstår ska du återställa dina data. Du kan återställa dina data genom att högerklicka på ikonen Azure Cosmos Emulator i systemfältet och sedan klicka på Återställ data.... Om det inte löser felen kan du avinstallera emulatorn och alla äldre versioner av emulatorn om de hittas, ta bort katalogen "C:\Program files\Azure Cosmos DB Emulator" och installera om emulatorn. I [Avinstallera den lokala emulatorn](#uninstall) finns instruktioner.

- Om Azure Cosmos Emulator kraschar samlar du in dumpfiler från mappen %LOCALAPPDATA%\CrashDumps, komprimerar dem och öppnar en supportbiljett från [Azure-portalen](https://portal.azure.com).

- Om du upplever `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`krascher i kan detta vara ett symptom där prestandaräknare är i ett skadat tillstånd. Om du kör följande kommando från en kommandotolk för administratörer åtgärdas problemet:

  ```cmd
  lodctr /R
   ```

- Om du stöter på ett anslutningsproblem [samlar du in spårningsfiler,](#trace-files)komprimerar dem och öppnar en supportbiljett i [Azure-portalen](https://portal.azure.com).

- Om du ser ett meddelande om att **tjänsten inte är tillgänglig** kanske emulatorn misslyckas med att initiera nätverksstacken. Se efter om du har Pulse Secure-klienten eller Juniper-nätverksklienten installerad, eftersom deras nätverksfilterdrivrutiner kan orsaka problemet. Avinstallation av nätverksfilterdrivrutiner från tredje part åtgärdar vanligen problemet. Alternativt startar emulatorn med /DisableRIO, som kommer att växla emulatornätverkskommunikationen till vanliga Winsock. 

- Om datorn försätts i viloläge eller om operativsystemet uppdateras när emulatorn körs kan du se meddelandet **Tjänsten är inte tillgänglig just nu**. Återställ emulatorns data genom att högerklicka på ikonen som visas i meddelandefältet i Windows och välj **Återställ data**.

### <a name="collect-trace-files"></a><a id="trace-files"></a>Samla in spårningsfiler

För att samla in felsökningsspårningar kör du följande kommandon från en administrativ kommandotolk:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Titta på systemfältet att kontrollera att programmet har avslutats. Det kan ta ett tag. Du kan också bara klicka på **Avsluta** i Azure Cosmos Emulator användargränssnitt.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Återskapa problemet. Om Datautforskaren inte fungerar behöver du vara vänta tills webbläsaren öppnas i några sekunder för att upptäcka felet.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Gå till `%ProgramFiles%\Azure Cosmos DB Emulator` och leta rätt på filen docdbemulator_000001.etl.
8. Öppna en supportbiljett i [Azure-portalen](https://portal.azure.com) och inkludera etl-filen tillsammans med steg för generering.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Avinstallera den lokala emulatorn

1. Avsluta alla öppna instanser av den lokala emulatorn genom att högerklicka på azure Cosmos Emulator-ikonen i systemfältet och klicka sedan på Avsluta. Det kan ta någon minut för alla instanser att avslutas.
2. I Windows-sökrutan skriver du **Appar och funktioner** och klickar på resultatet för **Appar och funktioner (systeminställningar)**.
3. I listan över appar bläddrar du till **Azure Cosmos DB-emulatorn**, väljer den, klickar på **Avinstallera** och bekräftar sedan och klickar på **Avinstallera** igen.
4. När appen är avinstallerad navigerar du till `%LOCALAPPDATA%\CosmosDBEmulator` och tar bort mappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda den lokala emulatorn för kostnadsfri lokal utveckling. Du kan nu gå vidare till nästa självstudie och lära dig hur du exporterar emulator TLS/SSL-certifikat.

> [!div class="nextstepaction"]
> [Exportera Azure Cosmos-emulatorns certifikat](local-emulator-export-ssl-certificates.md)
