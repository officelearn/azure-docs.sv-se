---
title: Kopiera data till eller från Oracle med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data till/från Oracle-databas som är lokalt med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10535e75a32a9f95e759340cf14d693f43639473
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856849"
---
# <a name="copy-data-to-or-from-on-premises-oracle-using-azure-data-factory"></a>Kopieringsdata till eller från en lokal Oracle med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-oracle-connector.md)
> * [Version 2 (aktuell version)](../connector-oracle.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Oracle-anslutningsapp i V2](../connector-oracle.md).


Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till/från en lokal Oracle-databas. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från en Oracle-databas** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till en Oracle-databas**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Förutsättningar
Data Factory stöder anslutning till en lokal Oracle-datakällor som använder Data Management Gateway. Se [Data Management Gateway](data-factory-data-management-gateway.md) du lär dig om Data Management Gateway och [flytta data från lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner om att konfigurera gatewayen en datapipeline Flytta data.

Gateway krävs även om Oracle finns i en Azure IaaS-VM. Du kan installera gatewayen på samma IaaS VM som datalager eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.

> [!NOTE]
> Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning/gateway-relaterade problem.

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Den här Oracle-anslutningsappen stöder två versioner av drivrutiner:

- **Microsoft-drivrutin för Oracle (rekommenderas)**: från och med Data Management Gateway version 2.7, en drivrutin för Oracle installeras automatiskt tillsammans med gatewayen, så du inte behöver dessutom hantera drivrutinen för att Microsoft upprätta en anslutning till Oracle och du kan också använda ger kopieringen bättre prestanda med hjälp av den här drivrutinen. Nedan versioner av Oracle stöds databaser:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10,2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!NOTE]
> Oracle-proxyserver stöds inte.

> [!IMPORTANT]
> Microsoft-drivrutin för Oracle stöder för närvarande endast kopiering av data från Oracle, men inte skriva till Oracle. Och anteckna kapaciteten för test-anslutning i fliken Data Management Gateway-diagnostik inte har stöd för den här drivrutinen. Du kan också använda Kopieringsguiden för att verifiera anslutningen.
>

- **Oracle Data Provider för .NET:** du kan också välja att använda Oracle Data Provider för att kopiera data från/till Oracle. Den här komponenten som ingår i [Oracle Data Access-komponenter för Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installera rätt version (32/64-bitars) på den dator där gatewayen är installerad. [Oracle dataprovider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) har åtkomst till Oracle Database 10 g version 2 eller senare.

    Om du väljer ”XCopy-Installation”, följer du stegen i Viktigt.htm. Vi rekommenderar att du väljer att installationsprogrammet med användargränssnitt (icke-XCopy en).

    När du har installerat providern, **starta om** tjänsten Data Management Gateway på din dator med hjälp av tjänster-appleten (eller) Data Management Gateway Configuration Manager.  

Om du använder Kopieringsguiden för att skapa kopieringspipelinen, blir Drivrutinstyp auto bestäms. Microsoft-drivrutinen används som standard om inte din gateway-versionen är lägre än 2.7 eller du väljer Oracle som mottagare.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till/från en lokal Oracle-databas med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudie: skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från en Oralce-databas till Azure blob storage, skapa du två länkade tjänster för att länka din Oracle database och Azure storage-konto till datafabriken. Länkade tjänstegenskaper som är specifika för Oracle, se [länkade tjänstegenskaper](#linked-service-properties) avsnittet.
3. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange tabellen i Oracle-databasen som innehåller indata. Och skapar du en annan datauppsättning för att ange blob-behållaren och mappen som innehåller de data som kopieras från Oracle-databas. Egenskaper för datamängd som är specifika för Oracle, se [egenskaper för datamängd](#dataset-properties) avsnittet.
4. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I exemplet som tidigare nämnts, använder du OracleSource som en källa och BlobSink som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure Blob Storage till Oracle-databas, använder du BlobSource och OracleSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Oracle-databas, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet. Mer information om hur du använder ett datalager som en källa eller mottagare klickar du på länken i föregående avsnitt för ditt datalager. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en lokal Oracle-databas finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-oracle-database) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Oracle länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **OnPremisesOracle** |Ja |
| driverType | Ange vilka drivrutiner som ska använda för att kopiera data från/till Oracle-databas. Tillåtna värden är **Microsoft** eller **ODP** (standard). Se [stöds version och vilka](#supported-versions-and-installation) avsnittet med mer information. | Nej |
| connectionString | Ange information som behövs för att ansluta till Oracle Database-instans för connectionString-egenskapen. | Ja |
| gatewayName | Namnet på den gateway som används för att ansluta till en lokal Oracle-server |Ja |

**Exempel: använda Microsoft-drivrutin:**

>[!TIP]
>Om du stöter på fel som säger ”ORA-01025: UPI parametern är utanför intervallet” och din Oracle är av version 8i, lägga till `WireProtocolMode=1` till din anslutningssträng och försök igen.

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

**Exempel: med ODP drivrutinen**

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Oracle, Azure-blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättningen av typen OracleTable har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databas som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** av **OracleSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en inmatning och ger endast en utdata.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

### <a name="oraclesource"></a>OracleSource
I kopieringsaktiviteten när källan är av typen **OracleSource** följande egenskaper är tillgängliga i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från MyTable <br/><br/>Om inte anges, SQL-instruktionen som körs: Välj * från MyTable |Nej (om **tableName** av **datauppsättning** har angetts) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |tidsintervall<br/><br/> Exempel: 00:30:00 (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktivitet för att fylla med automatiskt genererad sektorn identifierare som används för att rensa data för en viss sektor när köras på nytt. |Kolumnnamnet på en kolumn med datatypen för binary(32). |Nej |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>JSON-exempel för att kopiera data till och från Oracle-databas
I följande exempel innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från/till en Oracle-databas till och från Azure Blob Storage. Dock datan kan kopieras till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Exempel: Kopiera data från Oracle till Azure Blob

Exemplet har följande data factory-entiteter:

1. En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) som källa och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) som mottagare.

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

**Azure Blob storage-länkade tjänst:**

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

**Oracle datauppsättningen för indata:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i Oracle och innehåller en kolumn med namnet ”timestampcolumn” för time series-data.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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

**Pipeline med Kopieringsaktivitet:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **OracleSource** och **mottagare** är **BlobSink**.  SQL-frågan som angetts med **oracleReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Exempel: Kopiera data från Azure Blob till Oracle
Det här exemplet visar hur du kopierar data från Azure Blob Storage till en lokal Oracle-databas. Men du kan kopiera data **direkt** från någon av de källor som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.  

Exemplet har följande data factory-entiteter:

1. En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) som källa [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en blob till en tabell i en lokal Oracle-databas varje timme. Mer information om olika egenskaper som används i exemplet finns i dokumentationen i exemplen i följande avsnitt.

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

**Azure Blob storage-länkade tjänst:**
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

**Indatauppsättning för Azure-Blobb**

Data hämtas från en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dag för starttiden och filnamnet använder timme en del av starttiden. ”external”: ”true” inställningen informerar Data Factory-tjänsten att den här tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

**Oracle-datauppsättningen för utdata:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i Oracle. Skapa tabell i Oracle med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

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

**Pipeline med Kopieringsaktivitet:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **OracleSink**.  

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

1. .NET Framework-dataprovidern för Oracle har inte installerats.
2. .NET Framework-dataprovidern för Oracle har installerats på .NET Framework 2.0 och kan inte hittas i .NET Framework 4.0-mappar.

**Lösning/lösning:**

1. Om du inte har installerat .NET-Provider för Oracle, [installera den](http://www.oracle.com/technetwork/topics/dotnet/downloads/) och försök sedan scenariot.
2. Om du får ett felmeddelande även när du har installerat providern kan du göra följande:
   1. Öppna machine konfiguration av .NET 2.0 från mappen: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Sök efter **Oracle Data Provider för .NET**, och du ska kunna hitta en post som du ser i följande exempel under **system.data** -> **DbProviderFactories**”:<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle dataprovider för .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Kopiera den här posten i Machine.config i mappen följande v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config och ändrar versionen för att 4.xxx.x.x.
4. Installera ”< ODP.NET installerad sökväg > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll” i den globala sammansättningscachen (GAC) genom att köra `gacutil /i [provider path]`. ## felsökningstips

### <a name="problem-2-datetime-formatting"></a>Problem 2: datetime formatering

Du ser följande **felmeddelande**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Lösning/lösning:**

Du kan behöva justera frågesträngen i din kopieringsaktiviteten baserat på hur datum är konfigurerade i Oracle-databas som visas i följande exempel (med funktionen to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mappning för Oracle
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln Kopieringsaktivitet utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data från Oracle, används följande mappningar från Oracle-datatypen till .NET-typ och vice versa.

| Oracle-datatypen | .NET framework-datatypen |
| --- | --- |
| BFILE |Byte] |
| BLOB |Byte]<br/>(stöds bara på Oracle 10g och högre när med hjälp av Microsoft-drivrutin) |
| CHAR |Sträng |
| CLOB |Sträng |
| DATE |DateTime |
| FLYTTAL |Decimal, sträng (om precision > 28) |
| HELTAL |Decimal, sträng (om precision > 28) |
| INTERVALL ÅRETS MÅNAD |Int32 |
| INTERVALL DAG TILL ANDRA |Tidsintervall |
| LÅNG |Sträng |
| LÄNGE RÅDATA |Byte] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| ANTAL |Decimal, sträng (om precision > 28) |
| NVARCHAR2 |Sträng |
| RÅDATA |Byte] |
| RAD-ID |Sträng |
| TIDSSTÄMPEL |DateTime |
| TIDSSTÄMPEL MED LOKALA TIDSZON |DateTime |
| TIDSSTÄMPEL MED TIDSZON |DateTime |
| HELTALET |Tal |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Datatypen **intervall år till månad** och **intervall dag till andra** stöds inte när du använder Microsoft-drivrutin.

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
