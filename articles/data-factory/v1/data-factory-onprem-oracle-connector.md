---
title: Kopiera data till/från Oracle med hjälp av Data Factory | Microsoft Docs
description: Lär dig mer om att kopiera data till och från Oracle-databas som är lokalt med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 64e8a20f72d451908c12751c0f8062bf4ae86370
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Kopiera data till och från lokala Oracle med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-onprem-oracle-connector.md)
> * [Version 2 – förhandsversion](../connector-oracle.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Oracle-anslutningen i V2](../connector-oracle.md).


Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data till och från en lokal Oracle-databas. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från en Oracle-databas** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till en Oracle-databas**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Förutsättningar
Data Factory stöder anslutning till lokal Oracle källor med hjälp av Data Management Gateway. Se [Data Management Gateway](data-factory-data-management-gateway.md) artikeln innehåller information om Data Management Gateway och [flytta data från lokalt till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner om hur du konfigurerar gatewayen som en pipeline för data att flytta data.

Gateway krävs även om Oracle finns i en Azure IaaS-VM. Du kan installera gatewayen på samma IaaS-VM som dataarkiv eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.

> [!NOTE]
> Se [felsökning av problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning /-gateway relaterade problem.

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Den här anslutningen Oracle stöder två versioner av drivrutiner:

- **Microsoft-drivrutin för Oracle (rekommenderas)**: från Data Management Gateway version 2.7, en drivrutin för Oracle installeras automatiskt tillsammans med gatewayen, så du inte behöver dessutom hantera drivrutinen för att Microsoft upprätta en anslutning till Oracle och du kan också prestanda förbättras kopiera med hjälp av den här drivrutinen. Nedan versioner av Oracle stöds databaser:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10,2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> Microsoft-drivrutin för Oracle stöder för närvarande endast kopiering av data från Oracle men inte skriva till Oracle. Och notera Testa anslutning kapaciteten i fliken Data Management Gateway-diagnostik inte har stöd för den här drivrutinen. Du kan också använda guiden Kopiera för att verifiera anslutningen.
>

- **Oracle Data Provider för .NET:** du kan också välja att använda Oracle Data Provider för att kopiera data från/till Oracle. Den här komponenten ingår i [Oracle Data Access-komponenter för Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installera rätt version (32/64-bitars) på den dator där gatewayen har installerats. [Oracle-dataprovidern .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) har åtkomst till Oracle Database 10 g version 2 eller senare.

    Om du väljer ”XCopy-Installation”, följer du anvisningarna i Viktigt.htm. Vi rekommenderar att du väljer att installationsprogrammet med användargränssnitt (icke-XCopy en).

    När du har installerat providern, **starta om** Data Management Gateway-värdtjänsten på datorn med tjänster appleten (eller) Data Management Gateway Configuration Manager.  

Om du använder guiden Kopiera för att skapa pipelinen kopia, kommer typen drivrutin att automatiskt identifiera. Microsoft-drivrutinen används som standard om inte din gatewayversionen är lägre än 2.7 eller Oracle som mottagare som du anger.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till eller från en lokal Oracle-databas med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Till exempel om du kopierar data från en Oralce-databas till en Azure blob storage måste skapa du två länkade tjänster om du vill länka till Oracle-databasen och Azure storage-konto till din data factory. Länkad tjänst-egenskaper som är specifika för Oracle finns [länkade tjänstegenskaper](#linked-service-properties) avsnitt.
3. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange tabellen i Oracle-databasen som innehåller indata. Och du skapar en annan dataset för att ange blob-behållaren och mappen som innehåller de data som kopieras från Oracle-databasen. Egenskaper för datamängd som är specifika för Oracle, se [egenskaper för datamängd](#dataset-properties) avsnitt.
4. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du OracleSource som en källa och BlobSink som en mottagare för kopieringsaktiviteten. På samma sätt om du kopierar från Azure Blob Storage till Oracle-databas, använder du BlobSource och OracleSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Oracle-databasen, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en lokal Oracle-databas finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-oracle-database) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-enheter:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Oracle länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesOracle** |Ja |
| driverType | Ange vilka drivrutiner som ska använda för att kopiera data från/till Oracle-databas. Tillåtna värden är **Microsoft** eller **ODP** (standard). Se [stöds version och vilka installationsalternativ](#supported-versions-and-installation) avsnitt för mer information. | Nej |
| connectionString | Ange information som behövs för att ansluta till Oracle-databasinstans för egenskapen connectionString. | Ja |
| gatewayName | Namnet på gatewayen som används för att ansluta till lokal Oracle-server |Ja |

**Exempel: använda Microsoft-drivrutinen:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exempel: med ODP drivrutin**

Referera till [platsen](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) för tillåtna format.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen (Oracle, Azure blob, Azure-tabellen, osv.).

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. Avsnittet typeProperties för datauppsättningen av typen OracleTable har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databas som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** av **OracleSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar endast en inmatning och ger en enda utdata.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

### <a name="oraclesource"></a>OracleSource
I en Kopieringsaktivitet när källan är av typen **OracleSource** följande egenskaper är tillgängliga i **typeProperties** avsnitt:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från mytable prefix <br/><br/>Om inget annat anges, SQL-instruktionen som körs: Välj * från mytable prefix |Nej (om **tableName** av **dataset** har angetts) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel: 00:30:00 (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antalet rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. |En frågesats. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktiviteten med genereras automatiskt segment-ID, som används för att rensa data för ett visst segment när köras på nytt. |Kolumnnamnet för en kolumn med datatypen för binary(32). |Nej |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>JSON-exempel för att kopiera data till och från Oracle-databas
I följande exempel innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från/till en Oracle-databas till och från Azure Blob Storage. Dock datan kan kopieras till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Exempel: Kopiera data från Oracle till Azure-Blob

Exemplet har följande data factory enheter:

1. En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) som källa och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en tabell i en lokal Oracle-databas till en blobb per timme. Mer information om olika egenskaper som används i exemplet finns i dokumentationen i exemplen i följande avsnitt.

**Oracle länkad tjänst:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob storage länkade tjänsten:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Oracle inkommande datauppsättningen:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Oracle och innehåller en kolumn med namnet ”timestampcolumn” för tid series-data.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {     
            "externalData": {        
                "retryInterval": "00:01:00",    
                "retryTimeout": "00:10:00",       
                "maximumRetry": 3       
            }     
        }
    }
}
```

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline med kopieringsaktiviteten:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och har schemalagts att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **OracleSource** och **sink** är inställd på **BlobSink**.  SQL-frågan som anges med **oracleReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Exempel: Kopiera data från Azure Blob för Oracle
Det här exemplet visas hur du kopierar data från ett Azure Blob Storage till en lokal Oracle-databas. Dock datan kan kopieras **direkt** från någon av de källor som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.  

Exemplet har följande data factory enheter:

1. En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) som källa [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en blobb till en tabell i en lokal Oracle-databas i timmen. Mer information om olika egenskaper som används i exemplet finns i dokumentationen i exemplen i följande avsnitt.

**Oracle länkad tjänst:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob storage länkade tjänsten:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Azure Blob-inkommande datamängd**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och som en dagdel av starttiden filnamn används i timmen som en del av starttiden. ”externa”: ”true” inställningen informerar Data Factory-tjänsten att den här tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Oracle datamängd för utdata:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Oracle. Skapa tabellen i Oracle med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabell varje timme.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline med kopieringsaktiviteten:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Felsökningstips
### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework dataprovider

Du ser följande **felmeddelande**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Möjliga orsaker:**

1. .NET Framework-dataprovider för Oracle har inte installerats.
2. .NET Framework-dataprovider för Oracle har installerats på .NET Framework 2.0 och kan inte hittas i .NET Framework 4.0-mappar.

**Lösning/lösning:**

1. Om du inte har installerat .NET-dataprovider för Oracle, [installera den](http://www.oracle.com/technetwork/topics/dotnet/downloads/) och försök scenariot.
2. Om du får felmeddelandet även när du har installerat providern gör du följande steg:
   1. Öppna datorn config av .NET 2.0 från mappen: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Sök efter **Oracle Data Provider för .NET**, och du ska kunna hitta en post som visas i följande exempel under **system.data** -> **DbProviderFactories**”:<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle dataprovider för .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Kopiera den här posten till machine.config-filen i mappen följande v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config och ändrar version till 4.xxx.x.x.
4. Installera ”< ODP.NET installerad sökväg > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll” i den globala sammansättningscachen (GAC) genom att köra `gacutil /i [provider path]`. ## felsökningstips

### <a name="problem-2-datetime-formatting"></a>Problem 2: datetime formatering

Du ser följande **felmeddelande**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Lösning/lösning:**

Du kan behöva justera frågesträngen i din kopieringsaktiviteten baserat på hur datum är konfigurerade i Oracle-databas som visas i följande exempel (med funktionen to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mappning för Oracle
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När du flyttar data från Oracle, används följande mappningar från Oracle-datatyp till .NET-typ och vice versa.

| Oracle-datatyp | .NET framework-datatyp |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(endast stöds på Oracle 10g och högre när Microsoft drivrutin) |
| CHAR |Sträng |
| CLOB |Sträng |
| DATE |DateTime |
| FLYTTAL |Decimal, sträng (om precision > 28) |
| HELTAL |Decimal, sträng (om precision > 28) |
| INTERVALL ÅR, MÅNAD |Int32 |
| INTERVALL DAG TILL ANDRA |TimeSpan |
| LÅNG |Sträng |
| LÅNGT RÅDATA |Byte[] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| ANTAL |Decimal, sträng (om precision > 28) |
| NVARCHAR2 |Sträng |
| RAW |Byte[] |
| ROWID |Sträng |
| TIDSSTÄMPEL |DateTime |
| TIDSSTÄMPEL MED LOKALA TIDSZON |DateTime |
| TIDSSTÄMPEL MED TIDSZON |DateTime |
| OSIGNERAT HELTAL |Tal |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Datatypen **intervall år till månad** och **intervall dag till andra** stöds inte när du använder Microsoft-drivrutinen.

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
