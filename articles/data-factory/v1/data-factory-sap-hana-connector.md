---
title: Flytta data från SAP HANA med Azure Data Factory
description: Läs mer om hur du flyttar data från SAP HANA med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 361b98a1cde8ee5dee99a370b46d8fc8e0f5af28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265823"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Flytta data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sap-hana-connector.md)
> * [Version 2 (aktuell version)](../connector-sap-hana.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [SAP HANA-anslutning i V2](../connector-sap-business-warehouse.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal SAP HANA. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett lokalt SAP HANA-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data factory stöder för närvarande endast flytta data från en SAP HANA till andra datalager, men inte för att flytta data från andra datalager till en SAP HANA.

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
Den här anslutningen stöder alla versioner av SAP HANA-databasen. Den stöder kopiering av data från HANA-informationsmodeller (till exempel analytiska vyer och beräkningsvyer) och rad-/kolumntabeller med HJÄLP av SQL-frågor.

Så här aktiverar du anslutningen till SAP HANA-instansen genom att installera följande komponenter:
- **Data Management Gateway:** Data Factory-tjänsten stöder anslutning till lokala datalager (inklusive SAP HANA) med hjälp av en komponent som kallas Data Management Gateway. Mer information om Data Management Gateway och steg-för-steg-instruktioner för att konfigurera gatewayen finns i [Flytta data mellan lokalt datalager till molndatalagringsartikel.](data-factory-move-data-between-onprem-and-cloud.md) Gateway krävs även om SAP HANA finns i en virtuell Azure IaaS-dator (VM). Du kan installera gatewayen på samma virtuella dator som datalagret eller på en annan virtuell dator så länge gatewayen kan ansluta till databasen.
- **SAP HANA ODBC-drivrutin** på gateway-datorn. Du kan hämta SAP HANA ODBC-drivrutinen från [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA CLIENT for Windows**. 

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt SAP HANA-datalager med hjälp av olika verktyg/API:er. 

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet. 

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. 
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från en lokal SAP HANA finns i [JSON-exempel: Kopiera data från SAP HANA till Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) i den här artikeln. 

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ett SAP HANA-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för SAP HANA-länkad tjänst.

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namn på den server där SAP HANA-instansen finns. Om servern använder en anpassad `server:port`port anger du . | sträng | Ja
authenticationType | Typ av autentisering. | Sträng. "Basic" eller "Windows" | Ja 
användarnamn | Namn på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName (gatewayName) | Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala SAP HANA-instansen. | sträng | Ja
krypteradKnuren | Den krypterade referenssträngen. | sträng | Inga

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Det finns inga typspecifika egenskaper som stöds för SAP HANA-datauppsättningen för typen **RelationalTable**. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata och utdatatabeller är principer för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan i kopieringsaktivitet är av typen **RelationalSource** (som inkluderar SAP HANA) är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger den SQL-fråga som ska läsas data från SAP HANA-instansen. | SQL-fråga. | Ja |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON exempel: Kopiera data från SAP HANA till Azure Blob
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det här exemplet visar hur du kopierar data från en lokal SAP HANA till en Azure Blob Storage. Data kan dock kopieras **direkt** till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.  

> [!IMPORTANT]
> Det här exemplet innehåller JSON-utdrag. Den innehåller inte steg-för-steg-instruktioner för att skapa datafabriken. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner.

Exemplet har följande datafabriksenheter:

1. En länkad tjänst av typen [SapHana](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SAP HANA-instans till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg konfigurerar du datahanteringsgatewayen. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

### <a name="sap-hana-linked-service"></a>SAP HANA länkad tjänst
Den här länkade tjänsten länkar din SAP HANA-instans till datafabriken. Egenskapen Type är inställd på **SapHana**. Avsnittet typeProperties innehåller anslutningsinformation för SAP HANA-instansen.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Den här länkade tjänsten länkar ditt Azure Storage-konto till datafabriken. Typegenskapen är inställd på **AzureStorage**. Avsnittet typeProperties innehåller anslutningsinformation för Azure Storage-kontot.

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

### <a name="sap-hana-input-dataset"></a>SAP HANA-indatauppsättning

Den här datauppsättningen definierar SAP HANA-datauppsättningen. Du anger typen av datauppsättning för datafabriken på **RelationalTable**. För närvarande anger du inte några typspecifika egenskaper för en SAP HANA-datauppsättning. Frågan i definitionen Kopiera aktivitet anger vilka data som ska läsas från SAP HANA-instansen. 

Om du ställer in extern egenskap på true informerar datafabrikstjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

Frekvens- och intervallegenskaper definierar schemat. I det här fallet läses data från SAP HANA-instansen varje timme. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb
Den här datauppsättningen definierar utdata Azure Blob-datauppsättningen. Typegenskapen är inställd på AzureBlob. Avsnittet typeProperties anger var data som kopieras från SAP HANA-instansen lagras. Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline med kopieringsaktivitet

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** (för SAP HANA-källa) och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de data som ska kopieras under den senaste timmen.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Typmappning för SAP HANA
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När du flyttar data från SAP HANA används följande mappningar från SAP HANA-typer till .NET-typer.

TYP AV SAP HANA | .NET-baserad typ
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16 (int16)
INT | Int32
BIGINT | Int64
REAL | Enkel
DOUBLE | Enkel
DECIMAL | Decimal
Boolean | Byte
VARCHAR | String
Nvarchar | String
Clob | Byte[]
ALFA-NUMMER | String
Blob | Byte[]
DATE | DateTime
TIME | TimeSpan
TIMESTAMP | DateTime
ANDRADATE | DateTime

## <a name="known-limitations"></a>Kända begränsningar
Det finns några kända begränsningar när du kopierar data från SAP HANA:

- NVARCHAR-strängar trunkeras till högst 4 000 Unicode-tecken
- SMALLDECIMAL stöds inte
- VARBINARY stöds inte
- Giltiga datum är mellan 1899-12-30 och 9999-12-31

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
