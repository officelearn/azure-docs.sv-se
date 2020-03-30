---
title: Flytta data från MongoDB
description: Läs mer om hur du flyttar data från MongoDB-databasen med Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281345"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Flytta data från MongoDB med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-on-premises-mongodb-connector.md)
> * [Version 2 (aktuell version)](../connector-mongodb.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [MongoDB-anslutning i V2](../connector-mongodb.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal MongoDB-databas. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett lokalt MongoDB-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data factory stöder för närvarande endast flytta data från ett MongoDB-datalager till andra datalager, men inte för att flytta data från andra datalager till ett MongoDB-datalager.

## <a name="prerequisites"></a>Krav
För att Azure Data Factory-tjänsten ska kunna ansluta till din lokala MongoDB-databas måste du installera följande komponenter:

- MongoDB-versioner som stöds är: 2.4, 2.6, 3.0, 3.2, 3.4 och 3.6.
- Data Management Gateway på samma dator som är värd för databasen eller på en separat dator för att undvika att konkurrera om resurser med databasen. Data Management Gateway är en programvara som ansluter lokala datakällor till molntjänster på ett säkert och hanterat sätt. Mer information om Data Management Gateway finns i artikeln Data [Management Gateway.](data-factory-data-management-gateway.md) Se [Flytta data från lokal till molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner om hur du konfigurerar gatewayen en datapipeline för att flytta data.

    När du installerar gatewayen installeras automatiskt en Microsoft MongoDB ODBC-drivrutin som används för att ansluta till MongoDB.

    > [!NOTE]
    > Du måste använda gatewayen för att ansluta till MongoDB även om den finns i virtuella Azure IaaS-datorer. Om du försöker ansluta till en instans av MongoDB som finns i molnet kan du också installera gateway-instansen i IaaS VM.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt MongoDB-datalager med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett lokalt MongoDB-datalager finns i [JSON-exempel: Kopiera data från MongoDB till Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för MongoDB-källan:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för **OnPremisesMongoDB-länkad** tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesMongoDb** |Ja |
| server |IP-adress eller värdnamn för MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klientanslutningar. |Valfritt standardvärde: 27017 |
| authenticationType |Grundläggande eller anonym. |Ja |
| användarnamn |Användarkonto för att komma åt MongoDB. |Ja (om grundläggande autentisering används). |
| password |Lösenordet för användaren. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på den MongoDB-databas som du vill använda för att kontrollera dina autentiseringsuppgifter. |Valfritt (om grundläggande autentisering används). standard: använder administratörskontot och databasen som anges med hjälp av egenskapen databaseName. |
| Databasename |Namn på den MongoDB-databas som du vill komma åt. |Ja |
| gatewayName (gatewayName) |Namn på den gateway som kommer åt datalagret. |Ja |
| krypteradKnuren |Autentiseringsuppgifter krypteras av gateway. |Valfri |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **MongoDbCollection** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| collectionName (samlingsnamn) |Namn på samlingen i MongoDB-databasen. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet **typEgenskaper** i aktiviteten å andra sidan varierar beroende på varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan är av typen **MongoDbSource** är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92-frågesträngen. Till exempel: välj * från MyTable. |Nej (om **insamlingNamn** för datauppsättning har **angetts)** |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON exempel: Kopiera data från MongoDB till Azure Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en lokal MongoDB till en Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

Exemplet har följande datafabriksenheter:

1. En länkad tjänst av typen [OnPremisesMongoDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [MongoDbCollection](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [MongoDbSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en fråga resultera i MongoDB-databas till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg konfigurerar du datahanteringsgatewayen enligt instruktionerna i [datahanteringsgateway-artikeln.](data-factory-data-management-gateway.md)

**MongoDB länkad tjänst:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Azure Storage-länkad tjänst:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**MongoDB indatauppsättning:** Inställningen "extern": "true" informerar datafabrikstjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med MongoDB-källa och Blob-diskho:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda datauppsättningarna ovan in- och utdata och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen är **källtypen** inställd på **MongoDbSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de data som ska kopieras under den senaste timmen.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schema efter datafabrik
Azure Data Factory-tjänsten härleder schema från en MongoDB-samling med hjälp av de senaste 100 dokumenten i samlingen. Om dessa 100 dokument inte innehåller ett fullständigt schema kan vissa kolumner ignoreras under kopieringen.

## <a name="type-mapping-for-mongodb"></a>Typmappning för MongoDB
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas till MongoDB används följande mappningar från MongoDB-typer till .NET-typer.

| MongoDB-typ | .NET-ramtyp |
| --- | --- |
| Binär |Byte[] |
| Boolean |Boolean |
| Datum |DateTime |
| AntalDouble |Double |
| NumberInt (olika) |Int32 |
| AntalLångt |Int64 |
| ObjectID |String |
| String |String |
| Uuid |GUID |
| Objekt |Renormalized till platta kolumner med "_" som kapslad avgränsare |

> [!NOTE]
> Mer information om stöd för matriser med hjälp av virtuella tabeller finns i [Stöd för komplexa typer med hjälp av](#support-for-complex-types-using-virtual-tables) avsnittet virtuella tabeller nedan.

För närvarande stöds inte följande MongoDB-datatyper: DBPointer, JavaScript, Max/Min-tangenten, Reguljärt uttryck, Symbol, Tidsstämpel, Odefinierad

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer med hjälp av virtuella tabeller
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från din MongoDB-databas. För komplexa typer, till exempel matriser eller objekt med olika typer i dokumenten, normaliserar drivrutinen data till motsvarande virtuella tabeller. Om en tabell innehåller sådana kolumner genererar drivrutinen följande virtuella tabeller:

* En **bastabell**som innehåller samma data som den verkliga tabellen förutom de komplexa typkolumnerna. Bastabellen använder samma namn som den verkliga tabellen som den representerar.
* En **virtuell tabell** för varje komplex typkolumn, som expanderar de kapslade data. De virtuella tabellerna namnges med namnet på den verkliga tabellen, en avgränsare "_" och namnet på matrisen eller objektet.

Virtuella tabeller refererar till data i den verkliga tabellen, vilket gör att drivrutinen kan komma åt denormaliserade data. Se Exempel avsnitt nedan detaljer. Du kan komma åt innehållet i MongoDB-matriser genom att fråga och ansluta till de virtuella tabellerna.

Du kan använda [kopieringsguiden](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) för att intuitivt visa listan över tabeller i MongoDB-databasen, inklusive de virtuella tabellerna, och förhandsgranska data inuti. Du kan också skapa en fråga i kopieringsguiden och validera för att se resultatet.

### <a name="example"></a>Exempel
Exempeltabell nedan är till exempel en MongoDB-tabell som har en kolumn med en matris med objekt i varje cell – Fakturor och en kolumn med en matris med skaltyper – Betyg.

| _id | Kundens namn | Fakturor | Servicenivå | Klassificeringar |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", artikel:"brödrost", pris:"456", rabatt:"0.2"}, {invoice_id:"124", artikel:"ugn", pris: "1235", rabatt: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", artikel:"kylskåp", pris: "12543", rabatt: "0.0"}] |Guld |[1,2] |

Drivrutinen skulle generera flera virtuella tabeller för att representera den här enstaka tabellen. Den första virtuella tabellen är bastabellen "ExampleTable", som visas nedan. Bastabellen innehåller alla data i den ursprungliga tabellen, men data från matriserna har utelämnats och expanderats i de virtuella tabellerna.

| _id | Kundens namn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

I följande tabeller visas de virtuella tabeller som representerar de ursprungliga matriserna i exemplet. Dessa tabeller innehåller följande:

* En referens tillbaka till den ursprungliga primärnyckelkolumnen som motsvarar raden i den ursprungliga matrisen (via _id kolumnen)
* En indikation på datapositionen i den ursprungliga matrisen
* De expanderade data för varje element i matrisen

Tabell "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | objekt | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Brödrost |456 |0.2 |
| 1111 |1 |124 |Ugn |1235 |0.2 |
| 2222 |0 |135 |Kylskåp |12543 |0.0 |

Tabell "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Efterföljande moment
Se [Flytta data mellan lokal och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner för att skapa en datapipeline som flyttar data från ett lokalt datalager till ett Azure-datalager.
