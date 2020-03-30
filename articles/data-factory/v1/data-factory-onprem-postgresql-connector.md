---
title: Flytta data från PostgreSQL med Azure Data Factory
description: Läs mer om hur du flyttar data från PostgreSQL-databasen med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37c83e77cadae002ff701a08c4b36a86f7cab9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281241"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Flytta data från PostgreSQL med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-postgresql-connector.md)
> * [Version 2 (aktuell version)](../connector-postgresql.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [PostgreSQL-anslutning i V2](../connector-postgresql.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal PostgreSQL-databas. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett lokalt PostgreSQL-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som sänkor av kopieringsaktiviteten finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data factory stöder för närvarande att flytta data från en PostgreSQL-databas till andra datalager, men inte för att flytta data från andra datalager till en PostgreSQL-databas.

## <a name="prerequisites"></a>Krav

Data Factory-tjänsten stöder anslutning till lokala PostgreSQL-källor med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) om du vill veta mer om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen.

Gateway krävs även om PostgreSQL-databasen finns i en Azure IaaS VM. Du kan installera gateway på samma IaaS VM som datalagret eller på en annan virtuell dator så länge gatewayen kan ansluta till databasen.

> [!NOTE]
> Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för tips om felsökning av anslutnings-/gatewayrelaterade problem.

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
För datahanteringsgateway för att ansluta till PostgreSQL-databasen installerar du [Ngpsql-dataleverantören för PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) med version mellan 2.0.12 och 3.1.9 på samma system som Data Management Gateway. PostgreSQL version 7.4 och högre stöds.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt PostgreSQL-datalager med hjälp av olika verktyg/API:er.

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.
- Du kan också använda följande verktyg för att skapa en pipeline:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-mall
  - .NET-API
  - REST API

    Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett lokalt PostgreSQL-datalager finns i [JSON-exempel: Kopiera data från PostgreSQL till Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ett PostgreSQL-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för PostgreSQL-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesPostgreSql** |Ja |
| server |Namnet på PostgreSQL-servern. |Ja |
| databas |Namn på PostgreSQL-databasen. |Ja |
| Schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänsligt. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder Grundläggande autentisering eller Windows-autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namn på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper.

Avsnittet typeProperties är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **RelationalTable** (som innehåller PostgreSQL-datauppsättning) har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i postgreSQL-databasinstansen som länkad tjänst refererar till. TableName är skiftlägeskänsligt. |Nej (om **frågan om** **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan är av typen **RelationalSource** (som innehåller PostgreSQL) är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nej (om **tabellNamn för** datauppsättning har **angetts)** |

> [!NOTE]
> Schema- och tabellnamn är skiftlägeskänsliga. Bifoga dem i `""` (dubbla citattecken) i frågan.

**Exempel:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON exempel: Kopiera data från PostgreSQL till Azure Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från PostgreSQL-databasen till Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-utdrag. Den innehåller inte steg-för-steg-instruktioner för att skapa datafabriken. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner.

Exemplet har följande datafabriksenheter:

1. En länkad tjänst av typen [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopiera aktivitet som använder [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en fråga i PostgreSQL-databasen till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg ställer du in datahanteringsgatewayen. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

**PostgreSQL länkad tjänst:**

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
**Azure Blob storage-länkad tjänst:**

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
**PostgreSQL indatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i PostgreSQL och den innehåller en kolumn som kallas "tidsstämpel" för tidsseriedata.

Inställningen `"external": true` informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**Pipeline med kopieringsaktivitet:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer data från tabellen public.usstates i PostgreSQL-databasen.

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
## <a name="type-mapping-for-postgresql"></a>Typmappning för PostgreSQL
Som nämnts i [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln Kopiera aktivitet utför automatiska typkonverteringar från källtyper till sinktyper med följande 2-stegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas till PostgreSQL används följande mappningar från PostgreSQL-typ till .NET-typ.

| Databastyp för PostgreSQL | PostalgresSQL-alias | .NET-ramtyp |
| --- | --- | --- |
| abstime (mycket) | |Datumtid |
| bigint |int8 (int8) |Int64 |
| bigserial (bigserial) |seriell8 |Int64 |
| bit [(n)] | |Byte[], Sträng |
| lite varierande [ (n) ] |varbit (varbit) |Byte[], Sträng |
| boolean |bool |Boolean |
| Rutan | |Byte[], Sträng |
| bytea (på ett sätt) | |Byte[], Sträng |
| tecken [(n)] |tecken [(n)] |String |
| teckenvariering [(n)] |varchar [(n)] |String |
| Cid | |String |
| cidr | |String |
| cirkel | |Byte[], Sträng |
| date | |Datumtid |
| datumområde | |String |
| dubbel precision |float8 (flottör) |Double |
| inet | |Byte[], Sträng |
| intarry (intarry) | |String |
| int4range (int4range) | |String |
| int8range (int8range) | |String |
| heltal |int, int4 |Int32 |
| intervall [fält] [(p)] | |Tidsintervall |
| json | |String |
| jsonb (et) | |Byte[] |
| linje | |Byte[], Sträng |
| Lseg (0, 200 | |Byte[], Sträng |
| macaddr (macaddr) | |Byte[], Sträng |
| money | |Decimal |
| numerisk [(p, s)] |decimal [(p, s)] |Decimal |
| numrange (olika) | |String |
| Oid | |Int32 |
| path | |Byte[], Sträng |
| pg_lsn | |Int64 |
| Punkt | |Byte[], Sträng |
| Polygon | |Byte[], Sträng |
| real |float4 (flottör4) |Enkel |
| smallint |int2 (int2) |Int16 (int16) |
| smallserial |seriell2 |Int16 (int16) |
| Seriell |serie 4 |Int32 |
| text | |String |

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
