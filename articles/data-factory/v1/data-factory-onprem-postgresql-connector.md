---
title: Flytta data från PostgreSQL med hjälp av Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från PostgreSQL-databas med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ee466c85b68ebc72dbd55849db84a473d584ffb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Flytta data från PostgreSQL med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-onprem-postgresql-connector.md)
> * [Version 2 – förhandsversion](../connector-postgresql.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [PostgreSQL-anslutningen i V2](../connector-postgresql.md).


Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en lokal PostgreSQL-databas. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från ett dataarkiv för lokala PostgreSQL till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten finns [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data factory stöder för närvarande flytta data från en PostgreSQL-databas till andra databaser, men inte för att flytta data från andra datalager till en PostgreSQL-databas. 

## <a name="prerequisites"></a>Förutsättningar

Data Factory-tjänsten stöder anslutning till lokala PostgreSQL källor med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikeln innehåller information om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar en gateway.

Gateway krävs även om PostgreSQL-databasen finns i en Azure IaaS-VM. Du kan installera gatewayen på samma IaaS-VM som dataarkiv eller på en annan virtuell dator som en gateway kan ansluta till databasen.

> [!NOTE]
> Se [felsökning av problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning /-gateway relaterade problem.

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Data Management Gateway att ansluta till PostgreSQL-databasen, installera den [Ngpsql dataprovider för PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) med version mellan 2.0.12 och 3.1.9 på samma system som Data Management Gateway. PostgreSQL version 7.4 och senare stöds.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från ett dataarkiv för lokala PostgreSQL med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: 
    - Azure Portal
    - Visual Studio
    - Azure PowerShell
    - Azure Resource Manager-mall
    - .NET-API
    - REST-API

     Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett dataarkiv för lokala PostgreSQL finns [JSON-exempel: kopiera data från PostgreSQL till Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter i en PostgreSQL-datalager:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för PostgreSQL länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesPostgreSql** |Ja |
| server |Namnet på PostgreSQL-server. |Ja |
| databas |Namnet på PostgreSQL-databas. |Ja |
| schema |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänslig. |Nej |
| AuthenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden är: anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen.

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. TypeProperties avsnittet för dataset av typen **RelationalTable** (som omfattar PostgreSQL dataset) har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i PostgreSQL-databasinstansen som den länkade tjänsten refererar till. TableName är skiftlägeskänslig. |Nej (om **frågan** av **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

När datakällan är av typen **RelationalSource** (som omfattar PostgreSQL), följande egenskaper finns i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nej (om **tableName** av **dataset** har angetts) |

> [!NOTE]
> Schema och tabellnamn är skiftlägeskänsliga. Innesluts i `""` (dubbla citattecken) i frågan.  

**Exempel:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-exempel: kopiera data från PostgreSQL till Azure-Blob
Det här exemplet innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från PostgreSQL-databas till Azure Blob Storage. Dock datan kan kopieras till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.   

> [!IMPORTANT]
> Det här exemplet innehåller JSON kodavsnitt. Stegvisa instruktioner för att skapa datafabriken inkluderas inte. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner.

Exemplet har följande data factory enheter:

1. En länkad tjänst av typen [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Den [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i PostgreSQL-databas till en blobb varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg bör du ställa in data management gateway. Anvisningarna är i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

**PostgreSQL länkade tjänsten:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Azure Blob storage länkade tjänsten:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**PostgreSQL inkommande datauppsättningen:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i PostgreSQL och innehåller en kolumn med namnet ”tidsstämpel” för tid series-data.

Ange `"external": true` informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline med kopieringsaktiviteten:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och har schemalagts att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **RelationalSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **frågan** egenskapen väljer vilka data från tabellen public.usstates i PostgreSQL-databas.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mappning för PostgreSQL
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När data flyttas till PostgreSQL, används följande mappningar från PostgreSQL-typ för .NET-typ.

| Typ av PostgreSQL-databas | PostgresSQL alias | .NET framework-typ |
| --- | --- | --- |
| abstime | |DateTime | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bitars [(n)] | |Byte [], sträng | &nbsp;
| bit varierande [(n)] |varbit |Byte [], sträng |
| boolesk |bool |Boolesk |
| Rutan | |Byte [], sträng |&nbsp;
| bytea | |Byte [], sträng |&nbsp;
| tecknet [(n)] |char [(n)] |Sträng |
| tecknet varierande [(n)] |varchar [(n)] |Sträng |
| CID | |Sträng |&nbsp;
| cidr | |Sträng |&nbsp;
| cirkel | |Byte [], sträng |&nbsp;
| datum | |DateTime |&nbsp;
| DateRange | |Sträng |&nbsp;
| dubbel precision |FLOAT8 |Dubbel |
| inet | |Byte [], sträng |&nbsp;
| intarry | |Sträng |&nbsp;
| int4range | |Sträng |&nbsp;
| int8range | |Sträng |&nbsp;
| heltal |int, int4 |Int32 |
| intervallet [fält] [(p)] | |Tidsintervall |&nbsp;
| json | |Sträng |&nbsp;
| jsonb | |Byte[] |&nbsp;
| raden | |Byte [], sträng |&nbsp;
| lseg | |Byte [], sträng |&nbsp;
| macaddr | |Byte [], sträng |&nbsp;
| Money | |Decimal |&nbsp;
| numeriska [(p, s)] |decimal [(p, s)] |Decimal |
| numrange | |Sträng |&nbsp;
| oid | |Int32 |&nbsp;
| sökväg | |Byte [], sträng |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| punkt | |Byte [], sträng |&nbsp;
| polygon | |Byte [], sträng |&nbsp;
| Verklig |FLOAT4 |Ogift |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| serie |serial4 |Int32 |
| Text | |Sträng |&nbsp;

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
