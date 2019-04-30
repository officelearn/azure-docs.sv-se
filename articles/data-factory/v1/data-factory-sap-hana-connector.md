---
title: Flytta data från SAP HANA med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från SAP HANA med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 96d16552cfadca9b345d0f0cd0a344249897f571
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258444"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Flytta data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-sap-hana-connector.md)
> * [Version 2 (aktuell version)](../connector-sap-hana.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [SAP HANA-anslutningsappen i V2](../connector-sap-business-warehouse.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en lokal SAP HANA. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från ett datalager för lokal SAP HANA till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flyttar data från en SAP HANA till datalager, men inte för att flytta data från andra datalager till en SAP HANA.

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Den här anslutningen har stöd för någon version av SAP HANA-databas. Det har stöd för kopiering av data från HANA-informationsmodeller (som vyer för analys och beräkning) och raden/kolumnen tabeller med SQL-frågor.

För att aktivera anslutning till SAP HANA-instans måste du installera följande komponenter:
- **Data Management Gateway**: Data Factory-tjänsten stöder anslutning till lokala datalager (inklusive SAP HANA) med hjälp av en komponent som kallas Gateway för datahantering. Läs mer om Data Management Gateway samt stegvisa instruktioner för hur du konfigurerar gatewayen, i [flytta data mellan lokala data datalager till molnet datalager](data-factory-move-data-between-onprem-and-cloud.md) artikeln. Gateway krävs även om Azure IaaS-dator (VM) är värd för SAP HANA. Du kan installera gatewayen på samma virtuella dator som datalager eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.
- **SAP HANA ODBC-drivrutinen** på gateway-datorn. Du kan hämta SAP HANA ODBC-drivrutinen från den [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA-klienten för Windows**. 

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett datalager för lokal SAP HANA med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från en lokal SAP HANA, se [JSON-exempel: Kopiera data från SAP HANA till Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för ett datalager för SAP HANA:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för SAP HANA-länkad tjänst.

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP HANA-instans finns. Om servern använder en anpassad port, ange `server:port`. | string | Ja
authenticationType | Typ av autentisering. | sträng. ”Grundläggande” eller ”Windows” | Ja 
användarnamn | Namnet på den användare som har åtkomst till SAP-server | string | Ja
lösenord | Lösenordet för användaren. | string | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till en lokal SAP HANA-instans. | string | Ja
encryptedCredential | Strängen som krypterade autentiseringsuppgifter. | string | Nej

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Det finns inga typspecifika egenskaper som stöds för SAP HANA-datauppsättningen av typen **RelationalTable**. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, inkommande och utgående tabeller är principer är tillgängliga för alla typer av aktiviteter.

Å andra sidan Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

När källan i kopieringsaktiviteten är av typen **RelationalSource** (som inkluderar SAP HANA), följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger SQL-frågan som läser data från SAP HANA-instans. | SQL-fråga. | Ja |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON-exempel: Kopiera data från SAP HANA till Azure Blob
I följande exempel innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Detta exempel visar hur du kopierar data från en lokal SAP HANA till Azure Blob Storage. Men du kan kopiera data **direkt** till någon av mottagare visas [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.  

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Stegvisa instruktioner för att skapa data factory omfattas inte. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner.

Exemplet har följande data factory-entiteter:

1. En länkad tjänst av typen [SapHana](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SAP HANA-instans till en Azure-blob per timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Konfigurera data management gateway som ett första steg. Anvisningarna finns i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

### <a name="sap-hana-linked-service"></a>SAP HANA-länkad tjänst
Den här länkade tjänsten länkar din SAP HANA-instans till datafabriken. Typegenskapen har angetts **SapHana**. Avsnittet typeProperties anslutningsinformation för SAP HANA-instans.

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
Den här länkade tjänsten länkar ditt Azure Storage-konto till datafabriken. Typegenskapen har angetts **AzureStorage**. Avsnittet typeProperties anslutningsinformation för Azure Storage-kontot.

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

### <a name="sap-hana-input-dataset"></a>Indatauppsättning för SAP HANA

Den här datauppsättningen definierar SAP HANA-datauppsättning. Du anger vilken typ av Data Factory-datauppsättningen till **RelationalTable**. För närvarande kan anger du inte några typspecifika egenskaper för en SAP HANA-datauppsättning. Frågan i Kopieringsaktiviteten definitionen anger vilka data som ska läsa från SAP HANA-instans. 

Externa egenskapen true informerar Data Factory-tjänsten att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

Frekvens och intervall egenskaper definierar schemat. I det här fallet läses data från SAP HANA-instans per timme. 

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
Den här datauppsättningen definierar Azure Blob-datauppsättningen för utdata. Typegenskapen har angetts till AzureBlob. Avsnittet typeProperties innehåller data som kopieras från SAP HANA-instans ska lagras. Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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


### <a name="pipeline-with-copy-activity"></a>Pipeline med en Kopieringsaktivitet

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **RelationalSource** (för SAP HANA källan) och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer vilka data under den senaste timmen att kopiera.

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


### <a name="type-mapping-for-sap-hana"></a>Mappning för SAP HANA
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i två steg:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data från SAP HANA, används följande mappningar från SAP HANA-typer till .NET-typer.

SAP HANA-typ | .NET-baserade typ
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
VERKLIGA | Single
DOUBLE-VÄRDE | Single
DECIMAL | Decimal
BOOLESKT VÄRDE | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TIME | TimeSpan
TIDSSTÄMPEL | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Kända begränsningar
Det finns några kända begränsningar när du kopierar data från SAP HANA:

- NVARCHAR-strängar trunkeras till högst 4 000 Unicode-tecken
- SMALLDECIMAL stöds inte
- VARBINARY stöds inte
- Giltiga datum är mellan 1899-12-30 och 9999-12-31

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
