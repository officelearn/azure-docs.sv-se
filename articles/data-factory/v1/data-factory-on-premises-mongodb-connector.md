---
title: "Flytta data från MongoDB med hjälp av Data Factory | Microsoft Docs"
description: "Läs mer om hur du flyttar data från MongoDB-databas med Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20df17ba01cfc18ce751491d154d7401001e706e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Flytta data från MongoDB med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-on-premises-mongodb-connector.md)
> * [Version 2 – förhandsversion](../connector-mongodb.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [MongoDB-anslutningen i V2](../connector-mongodb.md).


Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en lokal MongoDB-databas. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från ett lokalt MongoDB-dataarkiv till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flytta data från ett dataarkiv som MongoDB till andra databaser, men inte för att flytta data från andra datalager till en MongoDB-datalagret. 

## <a name="prerequisites"></a>Förutsättningar
För Azure Data Factory-tjänsten för att kunna ansluta till din lokala MongoDB-databas måste du installera följande komponenter:

- MongoDB-versioner som stöds är: 2.4, 2.6, 3.0 och 3.2.
- Data Management Gateway på samma dator som värd för databasen eller på en separat dator att undvika konkurrerar om resurser med databasen. Data Management Gateway är en programvara som ansluter lokala datakällor till molntjänster i en säker och hanterad sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) artikeln för information om Data Management Gateway. Se [flytta data från lokalt till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner om hur du konfigurerar gatewayen som en pipeline för data att flytta data.

    När du installerar gateway installeras automatiskt en Microsoft MongoDB ODBC-drivrutinen används för att ansluta till MongoDB.

    > [!NOTE]
    > Du måste använda gateway för att ansluta till MongoDB, även om den finns i Azure IaaS-VM. Om du försöker ansluta till en instans av MongoDB som finns i molnet kan du också installera gateway-instans i IaaS-VM.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en lokal MongoDB-datalagret med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett lokalt MongoDB-dataarkiv finns [JSON-exempel: kopiera data från MongoDB till Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till MongoDB källa:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för **OnPremisesMongoDB** länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesMongoDb** |Ja |
| server |IP-adressen eller värdnamnet namnet på MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter anslutningar. |Valfritt, standardvärdet: 27017 |
| AuthenticationType |Grundläggande eller anonym. |Ja |
| användarnamn |Användarkonto för att få åtkomst till MongoDB. |Ja (om grundläggande autentisering används). |
| lösenord |Lösenord för användaren. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på MongoDB-databas som du vill använda för att kontrollera autentiseringsuppgifterna för autentisering. |Valfritt (om grundläggande autentisering används). standard: använder administratörskonto och databasen som anges med egenskapen databaseName. |
| DatabaseName |Namnet på den MongoDB-databas som du vill komma åt. |Ja |
| gatewayName |Namnet på den gateway som har åtkomst till datalagret. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats av gateway. |Valfri |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. TypeProperties avsnittet för dataset av typen **MongoDbCollection** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Samlingsnamn |Namnet på samlingen i MongoDB-databas. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten å andra sidan varierar med varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

När källan är av typen **MongoDbSource** följande egenskaper finns i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågesträngen. Till exempel: Välj * från mytable prefix. |Nej (om **samlingsnamn** av **dataset** har angetts) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-exempel: kopiera data från MongoDB till Azure-Blob
Det här exemplet innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en lokal MongoDB till ett Azure Blob Storage. Dock datan kan kopieras till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

Exemplet har följande data factory enheter:

1. En länkad tjänst av typen [OnPremisesMongoDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [MongoDbCollection](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [MongoDbSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i MongoDB-databas till en blobb varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Konfigurera som ett första steg data management gateway enligt anvisningarna i den [Data Management Gateway](data-factory-data-management-gateway.md) artikel.

**MongoDB länkade tjänsten:**

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

**Azure Storage länkade tjänsten:**

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

**MongoDB inkommande dataset:** inställningen ”externa”: ”true” informerar Data Factory-tjänsten att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```json
{
     "name":  "MongoDbInputDataset",
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

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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

**Kopiera aktivitet i en pipeline med MongoDB källa och mottagare för Blob:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad att använda ovanstående indata och utdata datauppsättningar och schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **MongoDbSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **frågan** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
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


## <a name="schema-by-data-factory"></a>Schema som Data Factory
Azure Data Factory-tjänsten skapar schema från en MongoDB-samling med hjälp av de senaste 100 dokumenten i samlingen. Om dokumenten 100 inte innehåller fullständig schemat, kan vissa kolumner ignoreras under kopieringen.

## <a name="type-mapping-for-mongodb"></a>Mappning för MongoDB
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När data flyttas till MongoDB används följande mappningar från MongoDB-typer till .NET-typer.

| MongoDB-typ | .NET framework-typ |
| --- | --- |
| Binär |Byte[] |
| Boolesk |Boolesk |
| Date |DateTime |
| NumberDouble |Dubbel |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectId |Sträng |
| Sträng |Sträng |
| UUID |GUID |
| Objekt |Renormalized förenkla i kolumner med ”_” som kapslad avgränsare |

> [!NOTE]
> Mer information om stöd för matriser med virtuella tabeller, referera till [stöd för komplexa typer som använder virtuella tabeller](#support-for-complex-types-using-virtual-tables) nedan.

För närvarande följande MongoDB-datatyper stöds inte: DBPointer, JavaScript, Max per minut nyckel, reguljärt uttryck, symboler, Timestamp, Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer som använder virtuella tabeller
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från din MongoDB-databas. För komplexa typer som matriser eller objekt med olika typer i dokumenten normaliserar drivrutinen igen data i motsvarande virtuella tabeller. Om en tabell innehåller sådana kolumner, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som verkliga tabell utom komplex typ-kolumner. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje kolumn för komplex typ, som utökar kapslade data. Virtuella tabeller namnges med namnet på tabellen verkliga, avgränsare ”_” och namnet på den matris eller ett objekt.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt Avnormaliserade data. Se avsnittet nedan information. Du kan komma åt innehållet i MongoDB matriser genom att fråga och ansluta till virtuella tabeller.

Du kan använda den [guiden Kopiera](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivt visa listan över tabeller i MongoDB-databas inklusive virtuella tabeller och förhandsgranska data som ingår. Du kan också skapa en fråga i guiden Kopiera och validera om du vill se resultatet.

### <a name="example"></a>Exempel
Till exempel är ”ExampleTable” nedan en MongoDB-tabell som har en kolumn med en matris med objekt i varje cell – fakturor och en kolumn med en matris med skalära typer – klassificeringar.

| _id | Kundens namn | Fakturor | Servicenivå | Klassificering |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: ”123” objektet: ”toaster”, pris: ”456” rabatt: ”0,2”}, {invoice_id: ”124” objektet: ”vara”, pris: rabatt ”1235”: ”0,2”}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: objektet ”135”: ”kombinerad kyl”, pris: ”12543” rabatt: ”0,0”}] |Guld |[1,2] |

Drivrutinen skulle generera flera virtuella tabeller som representerar denna tabell. Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” nedan. Bastabellen innehåller alla data för den ursprungliga tabellen, men data från matriserna har utelämnats och utökas i virtuella tabeller.

| _id | Kundens namn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

Följande tabeller visar virtuella tabeller som representerar de ursprungliga matriserna i exemplet. Dessa tabeller innehåller följande:

* En referens till den ursprungliga primärnyckelkolumnen som motsvarar raden i den ursprungliga matrisen (via kolumnen _id)
* Uppgift om placeringen av data i den ursprungliga matrisen
* Utökade data för varje element i matrisen

Tabell ”ExampleTable_Invoices”:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Objektet | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |vara |1235 |0.2 |
| 2222 |0 |135 |kombinerad kyl |12543 |0.0 |

Tabell ”ExampleTable_Ratings”:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se [flytta data mellan lokalt och i molnet](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner för att skapa en pipeline för data som flyttas data från ett lokalt datalager till ett Azure dataarkiv.
