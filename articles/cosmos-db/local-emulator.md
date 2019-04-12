---
title: Utveckla lokalt med Azure Cosmos-emulatorn
description: Med Azure Cosmos-emulatorn kan du utveckla och testa ditt program lokalt för kostnad, utan att skapa en Azure-prenumeration.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
author: deborahc
ms.author: dech
ms.openlocfilehash: 3d535c71480693d0424c6697776a1ddbf37b47c5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493220"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Använda Azure Cosmos-emulatorn för lokal utveckling och testning

Azure Cosmos-emulatorn tillhandahåller en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utvecklingsändamål. Med Azure Cosmos-emulatorn kan du utveckla och testa ditt program lokalt, utan att skapa en Azure-prenumeration och utan kostnad. När du är nöjd med hur programmet fungerar i Azure Cosmos-emulatorn kan växla du till ett Azure Cosmos-konto i molnet.

Du kan utveckla med hjälp av Azure Cosmos-emulatorn med [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), och [tabell](local-emulator.md#table-api) API-konton. Men just nu vyn Datautforskaren i emulatorn fullständigt stöd för klienter för SQL API endast. 

## <a name="how-the-emulator-works"></a>Så här fungerar emulatorn

Azure Cosmos-emulatorn ger en hifi-emulering av Azure Cosmos DB-tjänsten. Den stöder samma funktioner som Azure Cosmos DB, inklusive stöd för att skapa och fråga efter data, etablering och skalning behållare och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos-emulatorn och distribuera dem till Azure i global skala genom att bara göra en enda konfigurationen av anslutningens slutpunkt för Azure Cosmos DB.

Även om emuleringen av Azure Cosmos DB-tjänsten är verklighetstrogen så implementeras emulatorn på ett annat sätt än tjänsten. Till exempel använder emulatorn standardkomponenter i operativsystemet som det lokala filsystemet för beständighet och HTTP-protokollstacken för anslutningar. Funktioner som förlitar sig på Azure-infrastrukturen som global replikering, en siffra millisekunds fördröjning för läsningar/skrivningar och justerbara konsekvensnivåer kan inte användas.

Du kan migrera data mellan Azure Cosmos-emulatorn och Azure Cosmos DB-tjänsten med hjälp av den [Migreringsverktyget för Azure Cosmos DB Data](https://github.com/azure/azure-documentdb-datamigrationtool).

Du kan köra Azure Cosmos-emulatorn på Windows Docker-behållaren, se den [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) för kommandot docker pull och [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) för källkod emulatorn.

## <a name="differences-between-the-emulator-and-the-service"></a>Skillnader mellan emulatorn och tjänsten

Eftersom Azure Cosmos-emulatorn är en emulerade miljö som körs på den lokala utvecklararbetsstation, finns det vissa skillnader i funktionalitet mellan emulatorn och ett Azure Cosmos-konto i molnet:

* Datautforskaren i emulatorn stöder för närvarande klienter för SQL-API. Data Explorer-vy och åtgärder för Azure Cosmos DB API: er, till exempel MongoDB, Table, diagram och Cassandra-API: er stöds inte fullt ut.
* Azure Cosmos-emulatorn stöder bara ett enda fast konto och en välkänd huvudnyckel. Nycklar är inte möjligt i Azure Cosmos-emulatorn men standardnyckeln kan ändras med hjälp av kommandoradsalternativet.
* Azure Cosmos-emulatorn är inte en skalbar tjänst och stöder inte ett stort antal behållare.
* Azure Cosmos-emulatorn inte erbjuder olika [Azure Cosmos DB-konsekvensnivåer](consistency-levels.md).
* Azure Cosmos-emulatorn inte erbjuder [replikering av flera regioner](distribute-data-globally.md).
* Eftersom din kopia av Azure Cosmos-emulatorn inte kanske alltid är uppdaterade med de senaste ändringarna i Azure Cosmos DB-tjänsten, du bör använda den [kapacitetsplaneringsverktyget för Azure Cosmos DB](https://www.documentdb.com/capacityplanner) till att beräkna produktionen programmets (ru) dataflödesbehov.
* När du använder Azure Cosmos-Emulator, som standard, kan du skapa upp till 25 fast storlek behållare (stöds bara med Azure Cosmos DB SDK: er) eller 5 obegränsad behållare med hjälp av Azure Cosmos-emulatorn. Mer information om att ändra värdet finns i [Setting the PartitionCount value](#set-partitioncount) (Ange PartitionCount-värdet).

## <a name="system-requirements"></a>Systemkrav

Azure Cosmos-emulatorn har följande krav för maskinvara och programvara:

* Programvarukrav
  * Windows Server 2012 R2, Windows Server 2016 eller Windows 10
  * 64-bitars operativsystem
* Lägsta maskinvarukrav
  * 2 GB RAM-minne
  * 10 GB ledigt hårddiskutrymme

## <a name="installation"></a>Installation

Du kan hämta och installera Azure Cosmos-emulatorn från den [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) eller du kan köra emulatorn på Docker för Windows. Instruktioner om hur du använder emulatorn i Docker för Windows finns i [Köra med Docker](#running-on-docker).

> [!NOTE]
> För att installera, konfigurera och köra Azure Cosmos-emulatorn, måste du ha administratörsbehörighet på datorn. Emulatorn ska skapa/Lägg till ett certifikat och även ange brandväggsreglerna för att kunna köra dess tjänster. Därför är det nödvändigt för emulatorn för att kunna köra sådana åtgärder.

## <a name="running-on-windows"></a>Köra i Windows

Om du vill starta Azure Cosmos-emulatorn startknappen i eller tryck på Windows-tangenten. Börja skriva **Azure Cosmos-emulatorn**, och välj emulatorn från listan med program.

![Välj Start-knappen eller tryck på Windows, börjar du skriva ** Azure Cosmos-emulatorn ** och välj emulatorn från listan med program](./media/local-emulator/database-local-emulator-start.png)

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows. ![Azure Cosmos DB lokala emulatorn uppgift fältet meddelande](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos-emulatorn som standard körs på den lokala datorn (”localhost”) som lyssnar på port 8081.

Azure Cosmos-emulatorn har installerats på `C:\Program Files\Azure Cosmos DB Emulator` som standard. Du kan också starta och stoppa emulatorn från kommandoraden. För mer information, se [kommandoradsverktygsreferensen](#command-line).

## <a name="start-data-explorer"></a>Starta Datautforskaren

När Azure Cosmos-emulatorn startas, öppnas automatiskt Azure Cosmos-Datautforskaren i webbläsaren. Adressen visas som `https://localhost:8081/_explorer/index.html`. Om du stänger du Utforskaren och vill öppna det igen senare, kan du öppna URL: en i webbläsaren eller starta från Azure Cosmos-emulatorn i Windows-ikon som visas nedan.

![Azure Cosmos lokala emulatorn data explorer starta](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Leta efter uppdateringar

Datautforskaren anger om det finns en ny uppdatering som går att ladda ned.

> [!NOTE]
> Data som skapats i någon version av Azure Cosmos-emulatorn (se %LOCALAPPDATA%\CosmosDBEmulator eller data sökväg valfria inställningar) är inte säkert att vara tillgänglig när du använder en annan version. Om du vill spara dina data långsiktigt rekommenderas att du lagrar dessa data i ett Azure Cosmos-konto och inte i Azure Cosmos-emulatorn.

## <a name="authenticating-requests"></a>Autentisera förfrågningar

Som med Azure Cosmos DB i molnet, måste varje begäran som du gör mot Azure Cosmos-emulatorn autentiseras. Azure Cosmos-emulatorn stöder ett enda fast konto och en välkänd autentiseringsnyckel för huvudnyckeln för autentisering. Det här kontot och nyckeln är de enda autentiseringsuppgifter som tillåts för användning med Azure Cosmos-emulatorn. De är:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Huvudnyckeln som stöds av Azure Cosmos-emulatorn är avsedd att användas endast med emulatorn. Du kan inte använda ditt Azure Cosmos DB-konto för produktion och en nyckel med Azure Cosmos-emulatorn.

> [!NOTE]
> Om du har startat emulatorn med alternativet /Key kan sedan använda den genererade nyckeln i stället för `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Läs mer om /Key alternativet [kommandoradsverktyget referens.](#command-line-syntax)

Som med Azure Cosmos DB stöder Azure Cosmos-emulatorn endast säker kommunikation via SSL.

## <a name="running-on-a-local-network"></a>Köra på ett lokalt nätverk

Du kan köra emulatorn på ett lokalt nätverk. Om du vill aktivera nätverksåtkomst, ange den `/AllowNetworkAccess` alternativet på den [kommandoradsverktyget](#command-line-syntax), vilket kräver att du anger `/Key=key_string` eller `/KeyFile=file_name`. Du kan använda `/GenKeyFile=file_name` att generera en fil med en slumpmässig nyckel förskott. Sedan kan du skicka den till `/KeyFile=file_name` eller `/Key=contents_of_file`.

Om du vill aktivera nätverksåtkomst för första gången användaren stänga av emulatorn och ta bort den emulatorn datakatalog (% LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Utveckla med emulatorn

### <a name="sql-api"></a>API för SQL

När du har Azure Cosmos-emulatorn som körs på skrivbordet kan du använda någon stöds [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) eller [REST API för Azure Cosmos DB](/rest/api/cosmos-db/) att interagera med emulatorn. Azure Cosmos-emulatorn innehåller också en inbyggd Datautforskaren där du kan skapa en behållare för SQL-API eller Cosmos DB för Mongo DB API och visa och redigera objekt utan att behöva skriva någon kod.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

Om du använder [Azure Cosmos DB-API för MongoDB](mongodb-introduction.md), Använd följande anslutningssträng:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabell-API

När du har Azure Cosmos-emulatorn som körs på skrivbordet kan du använda den [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) att interagera med emulatorn. Starta emulatorn från Kommandotolken som administratör med ”/ EnableTableEndpoint”. Kör sedan följande kod för att ansluta till tabell-API-konto:

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

Starta emulatorn från en administratörskommandotolk med ”/ EnableCassandraEndpoint”. Alternativt kan du också ange miljövariabeln `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Installera Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Installera Cassandra CLI/CQLSH](http://cassandra.apache.org/download/)

* Kör följande kommandon i en vanlig kommandotolk:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Kör följande kommandon för att ansluta till Cassandra-slutpunkt i CQLSH-gränssnittet:

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

Starta emulatorn från en administratörskommandotolk med ”/ EnableGremlinEndpoint”. Alternativt kan du också ange miljövariabeln `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installera apache tinkerpop-gremlin-konsolen-3.3.4](http://tinkerpop.apache.org/downloads.html)

* Skapa en databas ”db1” och en samling ”coll1”; i emulatorn Data Explorer Välj för Partitionsnyckeln ”/ name”

* Kör följande kommandon i en vanlig kommandotolk:

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

* Kör följande kommandon för att ansluta till Gremlin-slutpunkten i Gremlin-gränssnittet:

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

För att hämta ett certifikat som ska användas med språk och körningar som inte integrerar med Windows Certificate Store måste du exportera det med Windows Certificate Manager. Du kan starta den genom att köra certlm.msc eller Följ steg för steg-instruktioner i [exportera Emulatorcertifikat för Azure Cosmos](./local-emulator-export-ssl-certificates.md). När certifikathanteraren körs öppnar du personliga certifikat som du ser nedan och exporterar certifikatet med det egna namnet ”DocumentDBEmulatorCertificate” som en BASE-64-kodad X.509-fil (.cer).

![SSL-certifikat för den lokala Azure DB Cosmos DB-emulatorn](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Följ instruktionerna i avsnittet [om att lägga till ett certifikat i Java CA-certifikatarkiv](https://docs.microsoft.com/azure/java-add-certificate-ca-store) för att importera X.509-certifikatet till standardcertifikatarkivet för Java. När du har importerat certifikatet till certifikatarkivet kommer klienter för SQL och Azure Cosmos DB API för MongoDB att kunna ansluta till Azure Cosmos-emulatorn.

SSL-verifieringen inaktiveras när du ansluter till emulatorn från Python- och Node.js-SDK:er.

## <a id="command-line"></a>Kommandoradsverktygsreferens
Installationen kan kan du använda kommandoraden att starta och stoppa emulatorn, konfigurera alternativ och utföra andra åtgärder.

### <a name="command-line-syntax"></a>Syntax för kommandorad

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Om du vill visa en lista över alternativ skriver du `CosmosDB.Emulator.exe /?` i kommandotolken.

|**Alternativ** | **Beskrivning** | **Kommando**| **Argument**|
|---|---|---|---|
|[Inga argument] | Startar Azure Cosmos-emulatorn med standardinställningar. |CosmosDB.Emulator.exe| |
|[Hjälp] |Visar en lista över kommandoradsargument som stöds.|CosmosDB.Emulator.exe /? | |
| GetStatus |Hämtar status för Azure Cosmos-emulatorn. Statusen visas med slutkoden: 1 = Startar, 2 = Körs, 3 = Stoppad. En negativ slutkod anger att ett fel har uppstått. Inga andra utdata produceras. | CosmosDB.Emulator.exe /GetStatus| |
| Avstängning| Stänger av Azure Cosmos-emulatorn.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Anger den sökväg där du kan lagra filer. Standardvärdet är % LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: En åtkomlig sökväg |
|Port | Anger det portnummer som ska användas för emulatorn. Standardvärdet är 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>: Enskilt portnummer |
| MongoPort | Anger det portnummer som ska användas för API för MongoDB-kompatibilitet. Standardvärdet är 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>: Enskilt portnummer|
| CassandraPort | Anger det portnummer som ska användas för Cassandra-slutpunkt. Standardvärdet är 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: Enskilt portnummer |
| ComputePort | Ange det portnummer som ska användas för Compute Interop-Gateway-tjänsten. Gatewayens HTTP-slutpunkt avsökningsporten beräknas som ComputePort + 79. ComputePort och ComputePort + 79 måste därför vara öppen och tillgänglig. Standardvärdena är 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: Enskilt portnummer |
| EnableCassandraEndpoint | Gör det möjligt för API för Cassandra | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| EnableGremlinEndpoint | Gör att Gremlin-API | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Portnummer för Gremlin-slutpunkten. Standardvärdet är 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<port\>: Enskilt portnummer |
|TablePort | Portnummer för Azure Table-slutpunkt. Standardvärdet är 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<port\>: Enskilt portnummer|
| Nyckelfil | Läsa auktoriseringsnyckeln från den angivna filen. Använd /GenKeyFile-alternativet för att generera en nyckelfil | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Sökvägen till filen |
| ResetDataPath | Rekursivt tar bort alla filer i den angivna sökvägen. Om du inte anger en sökväg, standard %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=<path>] | \<Sökvägen\>: Filsökväg  |
| StartTraces  |  Börja samla in spårningsloggar för felsökning. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Sluta samla in spårningsloggar för felsökning. | CosmosDB.Emulator.exe /StopTraces  | |
|EnableTableEndpoint | Aktiverar Azure tabell-API | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|FailOnSslCertificateNameMismatch | Som standard genererar emulatorn dess självsignerat SSL-certifikat, om SAN-nätverk för den certifikatet inte innehåller emulatorn värdens domännamn, lokala IPv4-adress, ”localhost” och ”127.0.0.1”. Med det här alternativet misslyckas emulatorn vid start i stället. Du bör sedan använda alternativet /GenCert för att skapa och installera ett nytt självsignerat SSL-certifikat. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Generera och installera ett nytt självsignerat SSL-certifikat. Du kan också inklusive en kommaavgränsad lista över ytterligare DNS-namn för att komma åt emulatorn över nätverket. | CosmosDB.Emulator.exe /GenCert [ \<kommaavgränsad lista över ytterligare dns-namn\>] | |
| DirectPorts |Anger portarna som ska användas för direktanslutning. Standardvärdena är 10251, 10252, 10253, 10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Kommaavgränsad lista över 4 portar |
| Nyckel |Auktoriseringsnyckel för emulatorn. Nyckeln måste vara en base-64-kodning av en 64 bytes vektor. | CosmosDB.Emulator.exe /Key:\<key\> | \<nyckel\>: Nyckeln måste vara en base-64-kodning av en 64 bytes vektor|
| EnableRateLimiting | Anger att begränsande beteende för förfrågningsfrekvens är aktiverat. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Anger att begränsande beteende för förfrågningsfrekvens är inaktiverat. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Visa inte emulatorns användargränssnitt. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Visa inte datautforskaren vid start. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Anger det maximala antalet partitionerad behållare. Se [ändra antalet behållare](#set-partitioncount) för mer information. | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Maximalt antal tillåtna enskild partition behållare. Standardvärdet är 25. Maxvärdet är 250 GB.|
| DefaultPartitionCount| Anger standardvärdet för antal partitioner för en partitionerad behållare. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> standardvärdet är 25.|
| AllowNetworkAccess | Ger åtkomst till emulatorn över ett nätverk. Du måste även skicka /Key=\<key_string\> eller /KeyFile=\<file_name\> för att aktivera nätverksåtkomst. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> eller CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Justera inte brandväggsregler när /AllowNetworkAccess alternativet används. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Generera en ny auktoriseringsnyckel och spara den i den angivna filen. Den genererade nyckeln kan användas med alternativen /Key eller /KeyFile. | CosmosDB.Emulator.exe /GenKeyFile=\<sökväg till nyckelfilen\> | |
| Konsekvens | Ställ in konsekvensnivå för kontot. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<konsekvens\>: Värdet måste ha någon av följande [konsekvensnivåer](consistency-levels.md): Session, Stark, Eventuell eller BoundedStaleness. Standardvärdet är Session. |
| ? | Visa hjälpmeddelandet.| | |

## <a id="set-partitioncount"></a>Ändra antalet behållare

Som standard kan du skapa upp till 25 fast storlek behållare (stöds bara med Azure Cosmos DB SDK: er) eller 5 obegränsad behållare med hjälp av Azure Cosmos-emulatorn. Genom att ändra den **PartitionCount** värde, som du kan skapa upp till 250 fast storlek behållare eller 50 obegränsade behållare eller en kombination av båda som inte överstiger 250 fast storlek behållare (där en obegränsad behållare = 5 fast storlek behållare). Vi rekommenderar dock inte för att konfigurera emulatorn att köra med fler än 200 fast storlek behållare. På grund av det arbetet som läggs till disk-i/o-åtgärder, som leda till oväntade tidsgränser när slutpunkten API: er.


Om du försöker skapa en behållare när det aktuella antalet partitioner har överskridits utlöser emulatorn ett undantag för ServiceUnavailable, med följande meddelande.

”Tyvärr kan vi för närvarande märker av stor efterfrågan i den här regionen, och kan inte slutföra din begäran just nu. Vi arbetar kontinuerligt för att göra mer och mer kapacitet online och rekommenderar att du vill försöka igen.
Tveka inte att e-post askcosmosdb@microsoft.com när som helst eller av någon anledning. Aktivitets-ID: 12345678-1234-1234-1234-123456789abc"

Om du vill ändra antalet behållare som är tillgängliga i Azure Cosmos-emulatorn kör du följande steg:

1. Ta bort alla lokala data i Azure Cosmos-emulatorn genom att högerklicka på den **Azure Cosmos DB-emulatorn** -ikonen i systemfältet och sedan klicka på **återställa Data...** .
2. Ta bort alla emulatorn data i den här mappen `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Avsluta alla öppna instanser genom att högerklicka på ikonen för **Azure Cosmos DB-emulator** i meddelandefältet och klicka sedan på **Avsluta**. Det kan ta någon minut för alla instanser att avslutas.
4. Installera den senaste versionen av den [Azure Cosmos-emulatorn](https://aka.ms/cosmosdb-emulator).
5. Starta emulatorn med PartitionCount-flaggan genom att ställa in ett värde <= 250. Till exempel: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Kontrollera emulatorn

Emulatorn levereras med en PowerShell-modul för att starta, stoppa, avinstallera och hämta status för tjänsten. Kör följande cmdlet för att använda PowerShell-modulen:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

eller placera den `PSModules` på din `PSModulesPath` och importera den som visas i följande kommando:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Här följer en sammanfattning av kommandon för att styra emulatorn från PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Kommentarer**

Returnerar ett av följande värden för ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running, or ServiceControllerStatus.Stopped.

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

Azure Cosmos-emulatorn kan köras på Docker för Windows. Emulatorn fungerar inte i Docker för Oracle Linux.

När du har installerat [Docker för Windows](https://www.docker.com/docker-windows) växlar du till Windows-containrar genom att högerklicka på Docker-ikonen i verktygsfältet och välja **Växla till Windows-containrar**.

Hämta därefter emulatoravbildningen från Docker-hubben genom att köra följande kommando i ditt favoritgränssnitt.

```bash
docker pull microsoft/azure-cosmosdb-emulator
```
Starta avbildningen genom att köra följande kommandon.

Från kommandoraden:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator
```

Från PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator

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


## <a name="troubleshooting"></a>Felsökning

Använd följande tips för att felsöka problem som kan uppstå med Azure Cosmos-emulatorn:

- Om du har installerat en ny version av emulatorn och fel uppstår ska du återställa dina data. Du kan återställa dina data genom att högerklicka på Azure Cosmos-emulatorn-ikonen i systemfältet och sedan klicka på Återställ Data... Om detta inte löser felen, kan du avinstallera emulatorn och alla äldre versioner av emulatorn om hittades, ta bort katalogen ”c:\Program\Microsoft files\Azure Cosmos DB-emulatorn” och installera om emulatorn. I [Avinstallera den lokala emulatorn](#uninstall) finns instruktioner.

- Om Azure Cosmos-emulatorn kraschar, samla in filer med felsökningsdumpar från mappen '% LOCALAPPDATA%\CrashDumps', komprimeras och bifogar dem i ett e-postmeddelande till [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Om det uppstår krascher i `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, det kan vara ett symtom där prestandaräknarna är skadad. Vanligtvis köra följande kommando från Kommandotolken som administratör åtgärdar problemet:

  ```cmd
  lodctr /R
   ```

- Om du stöter på ett anslutningsfel ska du [samla in spårningsfiler](#trace-files), komprimera dem och bifoga dem i ett e-postmeddelande till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Om du ser ett meddelande om att **tjänsten inte är tillgänglig** kanske emulatorn misslyckas med att initiera nätverksstacken. Se efter om du har Pulse Secure-klienten eller Juniper-nätverksklienten installerad, eftersom deras nätverksfilterdrivrutiner kan orsaka problemet. Avinstallation av nätverksfilterdrivrutiner från tredje part åtgärdar vanligen problemet. Du kan också starta emulatorn med /DisableRIO som växlar nätverkskommunikation emulatorn till vanliga Winsock. 

- Om datorn försätts i viloläge eller om operativsystemet uppdateras när emulatorn körs kan du se meddelandet **Tjänsten är inte tillgänglig just nu**. Återställning av emulatorn data genom att högerklicka på ikonen som visas i meddelandefältet i windows och väljer **återställa Data**.

### <a id="trace-files"></a>Samla in spårningsfiler

För att samla in felsökningsspårningar kör du följande kommandon från en administrativ kommandotolk:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Titta på systemfältet att kontrollera att programmet har avslutats. Det kan ta ett tag. Du kan även klicka **avsluta** i användargränssnittet för Azure Cosmos-emulatorn.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Återskapa problemet. Om Datautforskaren inte fungerar behöver du vara vänta tills webbläsaren öppnas i några sekunder för att upptäcka felet.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Gå till `%ProgramFiles%\Azure Cosmos DB Emulator` och leta rätt på filen docdbemulator_000001.etl.
7. Skicka .etl-filen tillsammans med reproduktionssteg till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) för felsökning.

### <a id="uninstall"></a>Avinstallera den lokala emulatorn

1. Avsluta alla öppna instanser av den lokala emulatorn genom att högerklicka på Azure Cosmos-emulatorn-ikonen i systemfältet och sedan klicka på Avsluta. Det kan ta någon minut för alla instanser att avslutas.
2. I Windows-sökrutan skriver du **Appar och funktioner** och klickar på resultatet för **Appar och funktioner (systeminställningar)**.
3. I listan över appar bläddrar du till **Azure Cosmos DB-emulatorn**, väljer den, klickar på **Avinstallera** och bekräftar sedan och klickar på **Avinstallera** igen.
4. När appen är avinstallerad navigerar du till `%LOCALAPPDATA%\CosmosDBEmulator` och tar bort mappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda den lokala emulatorn för kostnadsfri lokal utveckling. Du kan nu fortsätta till nästa självstudie och lära dig att exportera SSL-certifikat för emulatorn.

> [!div class="nextstepaction"]
> [Exportera certifikat för Azure Cosmos-emulatorn](local-emulator-export-ssl-certificates.md)
