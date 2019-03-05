---
title: Kopiera data till eller från Oracle med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data till eller från en lokal Oracle-databas med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 28227ce9ebc5680f68c05cb1296a8ba35eac2c74
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338356"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopiera data till eller från Oracle på plats med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-oracle-connector.md)
> * [Version 2 (aktuell version)](../connector-oracle.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av Azure Data Factory-tjänsten finns i [Oracle-anslutningsapp i V2](../connector-oracle.md).


Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till eller från en lokal Oracle-databas. Artikeln bygger vidare på [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md), som anger en allmän översikt över dataförflyttning med hjälp av Kopieringsaktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data *från en Oracle-databas* till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager *till en Oracle-databas*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Förutsättningar

Data Factory stöder anslutning till en lokal Oracle källor med hjälp av Data Management Gateway. Se [Data Management Gateway](data-factory-data-management-gateway.md) mer information om Data Management Gateway. Stegvisa instruktioner om hur du ställer in gateway i en datapipeline för att flytta data finns i [flytta data från lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md).

Gatewayen krävs även om Oracle finns i en Azure-infrastruktur som en tjänst (IaaS) VM. Du kan installera gatewayen på samma IaaS VM som datalager eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.

> [!NOTE]
> Tips om hur du felsöker problem som är relaterade till anslutningen, och gatewayen finns i [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation

Den här Oracle-anslutningsappen stöder två versioner av drivrutiner:

- **Microsoft-drivrutin för Oracle (rekommenderas)**: Från och med Data Management Gateway version 2.7, installeras en Microsoft-drivrutin för Oracle automatiskt med gatewayen. Du behöver inte installera eller uppdatera för att upprätta en anslutning till Oracle-drivrutinen. Du kan också använda ger kopieringen bättre prestanda med hjälp av den här drivrutinen. Dessa versioner av Oracle-databaser som stöds:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10,2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle-proxyserver stöds inte.

    > [!IMPORTANT]
    > Microsoft driver för Oracle stöder för närvarande endast kopiering av data från Oracle. Drivrutinen stöder inte skriva till Oracle. Testa anslutningen-funktionen på Data Management Gateway **diagnostik** fliken stöder inte den här drivrutinen. Du kan också använda guiden Kopiera för att verifiera anslutningarna.
    >

- **Oracle dataprovider för .NET**: Du kan använda Oracle Data Provider för att kopiera data från eller till Oracle. Den här komponenten som ingår i [Oracle Data Access-komponenter för Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installera den aktuella versionen (32-bitars eller 64-bitars) på den dator där gatewayen är installerad. [Oracle dataprovider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) kan komma åt Oracle Database 10 g Release 2 och senare versioner.

    Om du väljer **XCopy Installation**, Slutför stegen som beskrivs i filen readme.htm. Vi rekommenderar att du väljer det installationsprogram som inte har användargränssnitt (inte XCopy installer).

    När du har installerat providern kan du starta om tjänsten Data Management Gateway på din dator med hjälp av tjänster-appleten eller Data Management Gateway Configuration Manager.

Om du använder guiden Kopiera för att skapa kopieringspipelinen, är Drivrutinstyp autodetermined. Microsoft-drivrutinen används som standard, om inte din gateway-version är äldre än version 2.7 eller du väljer Oracle som mottagare.

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline med en Kopieringsaktivitet. Pipelinen flyttar data till eller från en lokal Oracle-databas med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda guiden Kopiera. Se [självstudien: Skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden kopieringsdata.

Du kan också använda en av följande verktyg för att skapa en pipeline: den **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, en **Azure Resource Manager mallen**, **.NET API**, eller **REST API**. Se den [Kopieringsaktiviteten självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) stegvisa instruktioner om hur du skapar en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, utför du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från en Oracle-databas till Azure Blob storage, skapa två länkade tjänster för att länka din Oracle database och Azure storage-konto till datafabriken. Länkade tjänstegenskaper som är specifika för Oracle, se [länkade tjänstegenskaper](#linked-service-properties).
3. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det här exemplet i föregående steg skapar du en datauppsättning för att ange tabellen i Oracle-databasen som innehåller indata. Skapar du en annan datauppsättning för att ange blob-behållaren och mappen som innehåller de data som kopieras från Oracle-databas. Egenskaper för datamängd som är specifika för Oracle, se [egenskaper för datamängd](#dataset-properties).
4. Skapa en **pipeline** som har en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I föregående exempel använder **OracleSource** som källa och **BlobSink** som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure Blob storage till en Oracle-databas måste du använda **BlobSource** och **OracleSink** i kopieringsaktiviteten. Kopieringsaktivitet egenskaper som är specifika för en Oracle-databas, se [Kopieringsaktiviteten egenskaper](#copy-activity-properties). Mer information om hur du använder ett datalager som källa eller mottagare väljer du länken till ditt datalager i föregående avsnitt.

När du använder guiden JSON-definitioner för dessa Data Factory-entiteter skapas automatiskt åt dig: länkade tjänster, datauppsättningar och pipeline. När du använder verktyg eller API: er (förutom för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Exempel som har JSON-definitioner för Data Factory-entiteter som används för att kopiera data till eller från en lokal Oracle-databas, finns i JSON-exempel.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell beskrivs JSON-element som är specifika för Oracle-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Den **typ** egenskapen måste anges till **OnPremisesOracle**. |Ja |
| driverType | Ange vilka drivrutiner som ska använda för att kopiera data från eller till en Oracle-databas. Tillåtna värden är **Microsoft** och **ODP** (standard). Se [stöds version och vilka](#supported-versions-and-installation) för mer information. | Nej |
| connectionString | Ange den information som behövs för att ansluta till Oracle database-instans för den **connectionString** egenskapen. | Ja |
| gatewayName | Namnet på den gateway som används för att ansluta till en lokal Oracle-server. |Ja |

**Exempel: Med hjälp av Microsoft-drivrutin**

> [!TIP]
> Om du ser ett fel som säger ”ORA-01025: UPI parametern är utanför intervallet ”och din Oracle är version 8i, lägga till `WireProtocolMode=1` till din anslutningssträng och försök igen:

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

**Exempel: Med den ODP-drivrutinen**

Läs mer om tillåtna format, i [Oracle dataprovider för .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [skapar datauppsättningar](data-factory-create-datasets.md).

Avsnitt i en datauppsättning JSON-fil som struktur, tillgänglighet och princip, är liknande för alla datauppsättningstyper av (till exempel för Oracle, Azure Blob storage och Azure Table storage).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **OracleTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databas som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** eller **OracleSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipelines](data-factory-create-pipelines.md).

Egenskaper som namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en inmatning och ger endast en utdata.

Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens varierar med varje aktivitetstyp av. Kopiera Aktivitetsegenskaper varierar beroende på vilken typ av källa och mottagare.

### <a name="oraclesource"></a>OracleSource

I Kopieringsaktiviteten när källan är den **OracleSource** typ, följande egenskaper är tillgängliga i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd anpassad fråga för att läsa data. |En SQL-sträng. Till exempel ”Välj \* från **MyTable**”. <br/><br/>Om inte anges den här SQL-instruktionen körs ”: Välj \* från **MyTable**” |Nej<br />(om **tableName** av **datauppsättning** har angetts) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch Infoga åtgärden har slutförts innan tidsgränsen uppnås. |**timespan**<br/><br/> Exempel: 00:30:00 (30 minuter) |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når värdet för **writeBatchSize**. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Anger en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Anger kolumnens namn för Kopieringsaktiviteten att fylla med en identifierare som genererats automatiskt sektorn. Värdet för **sliceIdentifierColumnName** används för att rensa data för en viss sektor när köras på nytt. |Kolumnnamnet för en kolumn med datatypen för **binary(32)**. |Nej |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-exempel för att kopiera data till och från Oracle-databasen

I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data från eller till en Oracle-databas och till eller från Azure Blob storage. Dock datan kan kopieras till någon av de mottagare som anges i [datalager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopieringsaktivitet i Azure Data Factory.

**Exempel: Kopiera data från Oracle till Azure Blob storage**

Exemplet har följande Data Factory-entiteter:

* En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) som källa och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en tabell i en lokal Oracle-databas till en blobb per timme. Mer information om olika egenskaper som används i exemplet finns i avsnitten som följer exemplen.

**Oracle länkad tjänst**

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

**Azure Blob storage-länkade tjänst**

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

**Indatauppsättning för Oracle**

Exemplet förutsätter att du har skapat en tabell med namnet **MyTable** i Oracle. Den innehåller en kolumn med namnet **timestampcolumn** för time series-data.

Ange **externa**: **SANT** informerar Data Factory-tjänsten att datauppsättningen är extern till data factory och att datauppsättningen inte produceras av en aktivitet i data factory.

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

Data skrivs till en ny blob varje timme (**frekvens**: **timme**, **intervall**: **1**). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timme som en del av starttiden.

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

**Pipeline med en Kopieringsaktivitet**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för att använda uppsättningar för indata och utdata och schemalagd för att köras varje timme. I pipeline-JSON-definitionen i **källa** är **OracleSource** och **mottagare** är **BlobSink**. SQL-fråga som du anger med hjälp av den **oracleReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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

**Exempel: Kopiera data från Azure Blob storage till Oracle**

Detta exempel visar hur du kopierar data från ett Azure Blob storage-konto till en lokal Oracle-databas. Men du kan kopiera data *direkt* från någon av de källor som anges i [datalager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopieringsaktivitet i Azure Data Factory.

Exemplet har följande Data Factory-entiteter:

* En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) som har en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) som källa [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en blob till en tabell i en lokal Oracle-databas varje timme. Mer information om olika egenskaper som används i exemplet finns i avsnitten som följer exemplen.

**Oracle länkad tjänst**

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

**Azure Blob storage-länkade tjänst**

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

**Indatauppsättning för Azure-blobb**

Data hämtas från en ny blob varje timme (**frekvens**: **timme**, **intervall**: **1**). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder den år, månad och dag för starttiden. Namnet på filen använder timme en del av starttiden. Inställningen **externa**: **SANT** informerar Data Factory-tjänsten att den här tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

**Utdatauppsättning för Oracle**

Exemplet förutsätter att du har skapat en tabell med namnet **MyTable** i Oracle. Skapa tabell i Oracle med samma antal kolumner som du förväntar dig att blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

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

**Pipeline med en Kopieringsaktivitet**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för att använda uppsättningar för indata och utdata och schemalagd för att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **OracleSink**.

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

### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework dataprovider

**Felmeddelande**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Möjliga orsaker**

* .NET Framework-dataprovidern för Oracle har inte installerats.
* .NET Framework-dataprovidern för Oracle har installerats på .NET Framework 2.0 och hittas inte i .NET Framework 4.0-mappar.

**Lösning**

* Om du inte har installerat .NET-Provider för Oracle, [installera den](http://www.oracle.com/technetwork/topics/dotnet/downloads/), och försök sedan scenariot.
* Om du ser felmeddelandet även när du har installerat providern, gör du följande:
    1. Öppna datorkonfigurationsfilen för .NET 2.0 från mappen < systemdisken\>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Sök efter **Oracle dataprovider för .NET**. Du ska kunna hitta en post som du ser i följande exempel under **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Kopiera den här posten i Machine.config i .NET 4.0 följande mapp: < systemdisken\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Sedan kan ändra versionen till 4.xxx.x.x.
* Installera < ODP.NET installerat sökväg\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll i den globala sammansättningscachen (GAC) genom att köra **gacutil /i [providersökvägen]**.

### <a name="problem-2-datetime-formatting"></a>Problem 2: Datum/tid

**Felmeddelande**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Lösning**

Du kan behöva justera frågesträngen i din kopieringsaktiviteten baserat på hur datum är konfigurerade i Oracle-databas. Här är ett exempel (med hjälp av den **to_date** funktionen):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mappning för Oracle

Som vi nämnde i [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md), Kopieringsaktivitet utför automatisk konverteringar från typer av datakällor till mottagare typer med hjälp av följande metod i två steg:

1. Konvertera från interna källtyper till .NET-typen.
2. Konvertera från .NET-typ till interna mottagartyp.

När du flyttar data från Oracle, används följande mappningar från Oracle-datatyp till .NET-typ och vice versa:

| Oracle-datatypen | .NET framework-datatypen |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(stöds bara på Oracle 10g och senare versioner när du använder en Microsoft-drivrutin) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLYTTAL |Decimal, sträng (om precision > 28) |
| HELTAL |Decimal, sträng (om precision > 28) |
| INTERVALL ÅRETS MÅNAD |Int32 |
| INTERVALL DAG TILL ANDRA |TimeSpan |
| LONG |String |
| LÄNGE RÅDATA |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, sträng (om precision > 28) |
| NVARCHAR2 |String |
| RÅDATA |Byte[] |
| RAD-ID |String |
| TIMESTAMP |DateTime |
| TIDSSTÄMPEL MED LOKALA TIDSZON |DateTime |
| TIDSSTÄMPEL MED TIDSZON |DateTime |
| HELTALET |Tal |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Datatyper **intervall år till månad** och **intervall dag till andra** stöds inte när du använder en Microsoft-drivrutin.

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare

Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättningen för mottagaren finns [mappning av kolumner för datauppsättningar i Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor

När du kopierar data från relationella datalager, Tänk repeterbarhet att undvika oväntade resultat. Du kan manuellt köra en sektor i Azure Data Factory. Du kan också konfigurera en återförsöksprincip för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt, antingen manuellt eller genom en återförsöksprincip, se till att samma data är körs läsa oavsett hur många gånger en sektor. Mer information finns i [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering

Se den [Kopieringsaktiviteten prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer som påverkar prestandan för dataflytt (Kopieringsaktivitet) i Azure Data Factory. Du kan också lära dig om olika sätt att optimera den.
