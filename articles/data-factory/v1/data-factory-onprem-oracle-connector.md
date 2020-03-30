---
title: Kopiera data till eller från Oracle med hjälp av Data Factory
description: Lär dig hur du kopierar data till eller från en lokal Oracle-databas med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265862"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopiera data till eller från Oracle lokalt med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-oracle-connector.md)
> * [Version 2 (aktuell version)](../connector-oracle.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av Azure Data Factory-tjänsten läser du [Oracle-anslutning i V2](../connector-oracle.md).


I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att flytta data till eller från en lokal Oracle-databas. Artikeln bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md), som ger en allmän översikt över dataflyttning med hjälp av Kopiera aktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data *från en Oracle-databas* till följande datalager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager *till en Oracle-databas:*

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Krav

Data Factory stöder anslutning till lokala Oracle-källor med hjälp av Data Management Gateway. Mer information om Data Management Gateway finns i [Data Management Gateway.](data-factory-data-management-gateway.md) Stegvisa instruktioner om hur du konfigurerar gatewayen i en datapipeline för att flytta data finns i [Flytta data från lokalt till moln](data-factory-move-data-between-onprem-and-cloud.md).

Gatewayen krävs även om Oracle finns i en Azure-infrastruktur som en virtuell tjänst (IaaS). Du kan installera gatewayen på samma IaaS VM som datalagret eller på en annan virtuell dator, så länge gatewayen kan ansluta till databasen.

> [!NOTE]
> Tips om felsökningsproblem som är relaterade till anslutning och gateway finns i [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds

Den här Oracle-anslutningen stöder två versioner av drivrutiner:

- **Microsoft-drivrutin för Oracle (rekommenderas)**: Med början i Data Management Gateway version 2.7 installeras en Microsoft-drivrutin för Oracle automatiskt med gatewayen. Du behöver inte installera eller uppdatera drivrutinen för att upprätta anslutning till Oracle. Du kan också uppleva bättre kopieringsprestanda med den här drivrutinen. Dessa versioner av Oracle-databaser stöds:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle-proxyserver stöds inte.

    > [!IMPORTANT]
    > För närvarande stöder Microsoft-drivrutinen för Oracle endast kopiering av data från Oracle. Drivrutinen stöder inte att skriva till Oracle. Testanslutningsfunktionen på fliken **Diagnostik för datahanteringsgateway** stöder inte den här drivrutinen. Du kan också använda guiden Kopiera för att validera anslutningen.
    >

- **Oracle-dataleverantör för .NET:** Du kan använda Oracles dataleverantör för att kopiera data från eller till Oracle. Den här komponenten ingår i [Oracle Data Access Components för Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Installera den relevanta versionen (32- eller 64-bitars) på datorn där gatewayen är installerad. [Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) kan komma åt Oracle Database 10g Release 2 och senare versioner.

    Om du väljer **XCopy-installation**slutför du stegen som beskrivs i filen readme.htm. Vi rekommenderar att du väljer installationsprogrammet som har användargränssnittet (inte XCopy-installationsprogrammet).

    När du har installerat providern startar du om värdtjänsten för Data Management Gateway på datorn med hjälp av appleten Services eller Konfigurationshanteraren för datahanteringsgateway.

Om du använder kopieringsguiden för att skapa kopieringspipelinen bestäms drivrutinstypen automatiskt. Microsoft-drivrutinen används som standard, såvida inte gateway-versionen är tidigare än version 2.7 eller om du väljer Oracle som diskbänk.

## <a name="get-started"></a>Komma igång

Du kan skapa en pipeline som har en kopieringsaktivitet. Pipelinen flyttar data till eller från en lokal Oracle-databas med hjälp av olika verktyg eller API:er.

Det enklaste sättet att skapa en pipeline är att använda kopieringsguiden. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda något av följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, en **Azure Resource Manager-mall,** **.NET API**eller REST **API**. Se [självstudien Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner om hur du skapar en pipeline som har en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna gör du följande för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en Oracle-databas till Azure Blob-lagring skapar du två länkade tjänster för att länka din Oracle-databas och Azure-lagringskonto till din datafabrik. Länkade tjänstegenskaper som är specifika för Oracle finns i [Länkade tjänstegenskaper](#linked-service-properties).
3. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet i föregående steg skapar du en datauppsättning för att ange tabellen i Oracle-databasen som innehåller indata. Du skapar en annan datauppsättning för att ange blob-behållaren och mappen som innehåller data som kopierats från Oracle-databasen. Datauppsättningsegenskaper som är specifika för Oracle finns i [Egenskaper för Datauppsättning](#dataset-properties).
4. Skapa en **pipeline** som har en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I föregående exempel använder du **OracleSource** som källa och **BlobSink** som en mottagare för kopieringsaktiviteten. Om du kopierar från Azure Blob-lagring till en Oracle-databas använder du **blobSource** och **OracleSink** i kopieringsaktiviteten. Egenskaper för kopieringsaktivitet som är specifika för en Oracle-databas finns i [Kopiera aktivitetsegenskaper](#copy-activity-properties). Om du vill ha mer information om hur du använder ett datalager som källa eller diskho väljer du länken för datalagret i föregående avsnitt.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter automatiskt för dig: länkade tjänster, datauppsättningar och pipelinen. När du använder verktyg eller API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Exempel som har JSON-definitioner för Data Factory-entiteter som du använder för att kopiera data till eller från en lokal Oracle-databas finns i JSON-exempel.

I följande avsnitt finns information om JSON-egenskaper som du använder för att definiera datafabrikentiteter.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell beskrivs JSON-element som är specifika för oracle-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen **Type** måste anges till **OnPremisesOracle**. |Ja |
| driverType | Ange vilken drivrutin som ska användas för att kopiera data från eller till en Oracle-databas. Tillåtna värden är **Microsoft** och **ODP** (standard). Se [Version och installation som stöds](#supported-versions-and-installation) för drivrutinsinformation. | Inga |
| Connectionstring | Ange den information som behövs för att ansluta till Oracle-databasinstansen för egenskapen **connectionString.** | Ja |
| gatewayName (gatewayName) | Namnet på gatewayen som används för att ansluta till den lokala Oracle-servern. |Ja |

**Exempel: Använda Microsoft-drivrutinen**

> [!TIP]
> Om du ser ett felmeddelande med "ORA-01025: UPI parameter out of range" `WireProtocolMode=1` och din Oracle är version 8i lägger du till anslutningssträngen och försöker igen:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exempel: Använda ODP-drivrutinen**

Mer information om tillåtna format finns i [Oracles dataleverantör för .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Skapa datauppsättningar](data-factory-create-datasets.md).

Avsnitten i en datauppsättning JSON-fil, till exempel struktur, tillgänglighet och princip, är liknande för alla datauppsättningstyper (till exempel för Oracle, Azure Blob storage och Azure Table storage).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet **typeProperties** för datauppsättningen av typen **OracleTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databasen som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** eller **OracleSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Skapa pipelines](data-factory-create-pipelines.md).

Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en indata och producerar bara en utdata.

Egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitetstyp. Egenskaperna Kopiera aktivitet varierar beroende på typ av källa och diskho.

### <a name="oraclesource"></a>OracleSource (Orakl)

När källan är **oracleSource-typ** i Kopiera aktivitet är följande egenskaper tillgängliga i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| orakelLäsareQuery |Använd den anpassade frågan för att läsa data. |En SQL-frågesträng. Till exempel "välj \* från **MyTable**". <br/><br/>Om inget anges körs det här SQL-uttrycket: "välj \* från **MyTable"** |Inga<br />(om **tabellNamn för** datauppsättning har **angetts)** |

### <a name="oraclesink"></a>OrakelSink

**OracleSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| skriverBatchTimeout |Väntetiden för batchinsatsen att slutföras innan den time out. |**Gått**<br/><br/> Exempel: 00:30:00 (30 minuter) |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når värdet **för writeBatchSize**. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Anger en fråga om kopieringsaktivitet som ska köras så att data för ett visst segment rensas. |En frågesats. |Inga |
| sliceIdentifierColumnName |Anger kolumnnamnet för Kopiera aktivitet som ska fyllas med en automatiskt skapad utsnittsidentifierare. Värdet för **segmentIdentifierColumnName** används för att rensa data för ett visst segment när du kör igen. |Kolumnnamnet på en kolumn som har datatyp av **binär(32)**. |Inga |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON exempel för kopiering av data till och från Oracle-databasen

Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data från eller till en Oracle-databas och till eller från Azure Blob-lagring. Data kan dock kopieras till någon av de diskhoar som anges i [datalager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopiera aktivitet i Azure Data Factory.

**Exempel: Kopiera data från Oracle till Azure Blob-lagring**

Exemplet har följande datafabrikentiteter:

* En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) som källa och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en tabell i en lokal Oracle-databas till en blob varje timme. Mer information om olika egenskaper som används i exemplet finns i avsnitten som följer exemplen.

**Oracles länkade tjänst**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob lagringslänkade tjänst**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle-indatauppsättning**

Exemplet förutsätter att du har skapat en tabell med namnet **MyTable** i Oracle. Den innehåller en kolumn som kallas **tidsstämpelpcolumn** för tidsseriedata.

Inställningen **extern**: **true** informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och att datauppsättningen inte produceras av en aktivitet i datafabriken.

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

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob varje timme **(frekvens:** **timme,** **intervall:** **1**). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder starttiden för år, månad, dag och timme.

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

**Pipeline med en kopieringsaktivitet**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagda att köras varje timme. I JSON-definitionen är **källtypen** inställd på **OracleSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som du anger med egenskapen **oracleReaderQuery** väljer de data som ska kopieras under den senaste timmen.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
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

**Exempel: Kopiera data från Azure Blob-lagring till Oracle**

Det här exemplet visar hur du kopierar data från ett Azure Blob-lagringskonto till en lokal Oracle-databas. Du kan dock kopiera data *direkt* från någon av de källor som anges i [datalager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopiera aktivitet i Azure Data Factory.

Exemplet har följande datafabrikentiteter:

* En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) som har en kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) som källa [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en blob till en tabell i en lokal Oracle-databas varje timme. Mer information om olika egenskaper som används i exemplet finns i avsnitten som följer exemplen.

**Oracles länkade tjänst**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob lagringslänkade tjänst**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure blob indatauppsättning**

Data hämtas från en ny blob varje timme **(frekvens:** **timme,** **intervall:** **1**). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder års-, månads- och dagdelen av starttiden. Filnamnet använder timdelen av starttiden. Inställningen **extern**: **true** informerar datafabrikstjänsten om att den här tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Oracle-utdatauppsättning**

Exemplet förutsätter att du har skapat en tabell med namnet **MyTable** i Oracle. Skapa tabellen i Oracle med samma antal kolumner som du förväntar dig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

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

**Pipeline med en kopieringsaktivitet**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **källtypen** inställd på **BlobSource** och **sink-typen** är inställd på **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
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

### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework-dataprovider

**Felmeddelande**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Möjliga orsaker**

* .NET Framework Data Provider för Oracle installerades inte.
* .NET Framework Data Provider för Oracle installerades i .NET Framework 2.0 och finns inte i .NET Framework 4.0-mapparna.

**Upplösning**

* Om du inte har installerat .NET Provider för Oracle [installerar du den](https://www.oracle.com/technetwork/topics/dotnet/downloads/)och försöker sedan igen scenariot.
* Om felmeddelandet visas även efter installation av leverantören gör du följande:
    1. Öppna datorns konfigurationsfil för .NET 2.0 från mappen\><systemdisken :\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Sök efter **Oracle-dataleverantör för .NET**. Du bör kunna hitta en post som visas i följande exempel under **system.data** > **DbProviderFactories:**`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Kopiera den här posten till filen machine.config i följande .NET 4.0-mapp: <systemdisken\>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ändra sedan versionen till 4.xxx.x.x.
* Installera <ODP.NET installerad sökväg\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll i den globala sammansättningscachen (GAC) genom att köra **gacutil /i [provider path]**.

### <a name="problem-2-datetime-formatting"></a>Problem 2: Datum-/tidsformatering

**Felmeddelande**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Upplösning**

Du kan behöva justera frågesträngen i kopieringsaktiviteten baserat på hur datum konfigureras i Oracle-databasen. Här är ett exempel (med **funktionen to_date):**

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Typmappning för Oracle

Som nämnts i [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md)utför Kopiera aktivitet automatiska typkonverteringar från källtyper till sinktyper med hjälp av följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till TYPEN .NET.
2. Konvertera från TYPEN .NET till den ursprungliga sink-typen.

När du flyttar data från Oracle används följande mappningar från Oracle-datatypen till TYPEN .NET och vice versa:

| Oracle-datatyp | .NET Framework-datatyp |
| --- | --- |
| BFILE (BFILE) |Byte[] |
| Blob |Byte[]<br/>(stöds endast på Oracle 10g och senare versioner när du använder en Microsoft-drivrutin) |
| CHAR |String |
| Clob |String |
| DATE |DateTime |
| Flyta |Decimal, Sträng (om precision > 28) |
| INTEGER |Decimal, Sträng (om precision > 28) |
| INTERVALL ÅR TILL MÅNAD |Int32 |
| INTERVALL DAG TILL SEKUND |TimeSpan |
| Lång |String |
| LÅNG RÅ |Byte[] |
| Nchar |String |
| NCLOB (på andra sätt) |String |
| Nummer |Decimal, Sträng (om precision > 28) |
| NVARCHAR2 (olika) |String |
| Raw |Byte[] |
| ROWID (RADID) |String |
| TIMESTAMP |DateTime |
| TIDSSTÄMPEL MED LOKAL TIDSZON |DateTime |
| TIDSSTÄMPEL MED TIDSZON |DateTime |
| OSIGNERAT HELTAL |Tal |
| VARCHAR2 (OLIKA) |String |
| XML |String |

> [!NOTE]
> Datatyper **INTERVALL ÅR TILL MÅNAD** OCH INTERVALL DAG TILL **SEKUND** stöds inte när du använder en Microsoft-drivrutin.

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner

Mer information om hur du mappar kolumner i källdatauppsättningen till kolumner i sink-datauppsättningen finns [i Mappa datauppsättningskolumner i Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor

När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt. Du kan också konfigurera en återförsöksprincip för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs igen, antingen manuellt eller av en återförsöksprincip, kontrollerar du att samma data läss oavsett hur många gånger ett segment körs. Mer information finns i [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering

Läs [prestanda- och justeringsguiden för kopieringsaktivitet](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory. Du kan också lära dig om olika sätt att optimera den.
