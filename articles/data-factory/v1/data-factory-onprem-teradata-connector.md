---
title: Flytta data från Teradata med Azure Data Factory
description: Lär dig mer om Teradata Connector för datafabrikstjänsten som gör att du kan flytta data från Teradata Database
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ecde5784e759ef5259b8c67ed574cef6cae98f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281202"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Flytta data från Teradata med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-teradata-connector.md)
> * [Version 2 (aktuell version)](../connector-teradata.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser [du Teradata-anslutningsappen i V2](../connector-teradata.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal Teradata-databas. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett lokalt Teradata-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Datafabriken stöder för närvarande endast att flytta data från ett Teradata-datalager till andra datalager, men inte för att flytta data från andra datalager till ett Teradata-datalager.

## <a name="prerequisites"></a>Krav
Datafabriken stöder anslutning till lokala Teradatakällor via Data Management Gateway. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) om du vill veta mer om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen.

Gateway krävs även om Teradata finns i en Azure IaaS VM. Du kan installera gatewayen på samma IaaS VM som datalagret eller på en annan virtuell dator så länge gatewayen kan ansluta till databasen.

> [!NOTE]
> Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för tips om felsökning av anslutnings-/gatewayrelaterade problem.

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
För att Data Management Gateway ska kunna ansluta till Teradata-databasen måste du installera [.NET Data Provider för Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) version 14 eller senare på samma system som Data Management Gateway. Teradata version 12 och högre stöds.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt Cassandra-datalager med hjälp av olika verktyg/API:er.

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från ett lokalt Teradata-datalager finns i [JSON-exempel: Kopiera data från Teradata till Azure Blob-avsnittet](#json-example-copy-data-from-teradata-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ett Teradata-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för Teradata-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesTeradata** |Ja |
| server |Namnet på Teradata-servern. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till Teradata-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder Grundläggande autentisering eller Windows-autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala Teradata-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. För närvarande finns det inga typegenskaper som stöds för Teradata-datauppsättningen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan är av typen **RelationalSource** (som innehåller Teradata) är följande egenskaper tillgängliga i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: välj * från MyTable. |Ja |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>JSON exempel: Kopiera data från Teradata till Azure Blob
I följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från Teradata till Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

Exemplet har följande datafabriksenheter:

1. En länkad tjänst av typen [OnPremisesTeradata](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopiera aktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en fråga resultera i Teradata-databas till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg konfigurerar du datahanteringsgatewayen. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

**Teradata länkad tjänst:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
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
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Teradata-indatadatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i Teradata och den innehåller en kolumn som kallas "tidsstämpel" för tidsseriedata.

Inställningen "extern": true informerar datafabrikstjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Pipeline med kopieringsaktivitet:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de data som ska kopieras under den senaste timmen.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Typmappning för Teradata
Som nämns i artikeln [för dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför aktiviteten Kopiera automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas till Teradata används följande mappningar från Teradata-typ till .NET-typ.

| Databastyp för Teradata | .NET-ramtyp |
| --- | --- |
| Char |String |
| Clob |String |
| Grafisk |String |
| Varchar |String |
| Vargrafiska |String |
| Blob |Byte[] |
| Byte |Byte[] |
| VarByte (olika) |Byte[] |
| Bigint |Int64 |
| ByteInt (på ett år) |Int16 (int16) |
| Decimal |Decimal |
| Double |Double |
| Integer |Int32 |
| Tal |Double |
| Smallint |Int16 (int16) |
| Datum |DateTime |
| Tid |TimeSpan |
| Tid med tidszon |String |
| Tidsstämpel |DateTime |
| Tidsstämpel med tidszon |DateTimeOffset |
| Intervalldag |TimeSpan |
| Intervall dag till timme |TimeSpan |
| Intervall dag till minut |TimeSpan |
| Intervall dag till sekund |TimeSpan |
| Intervalltimme |TimeSpan |
| Intervall timme till minut |TimeSpan |
| Intervalltimme till sekund |TimeSpan |
| Intervallminut |TimeSpan |
| Intervall minut till sekund |TimeSpan |
| Intervall andra |TimeSpan |
| Intervallår |String |
| Intervall år till månad |String |
| Intervallmånad |String |
| Period(Datum) |String |
| Period(Tid) |String |
| Period(Tid med tidszon) |String |
| Period(Tidsstämpel) |String |
| Period(Tidsstämpel med tidszon) |String |
| Xml |String |

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
