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
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0149b15fdfbd9fd7a3c9f9c099db9d505d27d1c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623068"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Flytta data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-sap-hana-connector.md)
> * [Version 2 – förhandsversion](../connector-sap-hana.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [SAP HANA-anslutningen i V2](../connector-sap-business-warehouse.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en lokal SAP HANA. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från en lokal SAP HANA-dataarkiv till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flytta data från en SAP HANA till andra databaser, men inte för att flytta data från andra datalager till en SAP HANA.

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Den här anslutningen har stöd för någon version av SAP HANA-databas. Det stöder kopiering av data från HANA informationsmodeller (till exempel analys- och vyer) och raden/kolumnen tabeller med SQL-frågor.

Om du vill aktivera anslutning till SAP HANA-instans, installera följande komponenter:
- **Data Management Gateway**: Data Factory-tjänsten har stöd för att ansluta till lokala data Arkiv (inklusive SAP HANA) med hjälp av en komponent som kallas Data Management Gateway. Mer information om Data Management Gateway samt stegvisa instruktioner för hur du konfigurerar en gateway, se [flytta mellan lokala data datalager till molnet datalagret](data-factory-move-data-between-onprem-and-cloud.md) artikel. Gateway krävs även om SAP HANA finns i en Azure IaaS-virtuella (VM). Du kan installera gatewayen på samma virtuella dator som dataarkiv eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.
- **SAP HANA ODBC-drivrutinen** på gateway-datorn. Du kan hämta SAP HANA ODBC-drivrutinen från den [SAP Software Download Center](https://support.sap.com/swdc). Sökning med nyckelordet **SAP HANA-klienten för Windows**. 

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en lokal SAP HANA-datalagret med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från en lokal SAP HANA finns [JSON-exempel: kopiera data från SAP HANA till Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till ett SAP HANA-datalager:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för SAP HANA länkad tjänst.

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP HANA-instansen finns. Om servern använder en anpassad port, ange `server:port`. | sträng | Ja
AuthenticationType | Typ av autentisering. | Sträng. ”Basic” eller ”Windows” | Ja 
användarnamn | Namnet på den användare som har åtkomst till SAP-server | sträng | Ja
lösenord | Lösenord för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SAP HANA-instans. | sträng | Ja
encryptedCredential | Strängen som krypterade autentiseringsuppgifter. | sträng | Nej

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. Det finns inga typspecifika egenskaper som stöds för SAP HANA-dataset av typen **RelationalTable**. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper, till exempel namn, beskrivning, inkommande och utgående tabeller är principer är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar med varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

När datakällan i en Kopieringsaktivitet är av typen **RelationalSource** (som innehåller SAP HANA), följande egenskaper finns i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger SQL-frågan som läser data från SAP HANA-instans. | SQL-frågan. | Ja |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON-exempel: kopiera data från SAP HANA till Azure-Blob
I följande exempel innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det här exemplet visas hur du kopierar data från en lokal SAP HANA till ett Azure Blob Storage. Dock datan kan kopieras **direkt** till någon av sänkor visas [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.  

> [!IMPORTANT]
> Det här exemplet innehåller JSON kodavsnitt. Stegvisa instruktioner för att skapa datafabriken inkluderas inte. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner.

Exemplet har följande data factory enheter:

1. En länkad tjänst av typen [SapHana](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SAP HANA-instans till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg bör du konfigurera data management gateway. Anvisningarna är i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

### <a name="sap-hana-linked-service"></a>SAP HANA länkad tjänst
Det här länkade tjänsten länkar SAP HANA-instans till datafabriken. Egenskapen type har angetts **SapHana**. Avsnittet typeProperties innehåller anslutningsinformation för SAP HANA-instans.

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
Det här länkade tjänsten länkar Azure Storage-konto till datafabriken. Egenskapen type har angetts **AzureStorage**. Avsnittet typeProperties innehåller anslutningsinformation för Azure Storage-konto.

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

### <a name="sap-hana-input-dataset"></a>SAP HANA inkommande dataset

Den här datauppsättningen definierar SAP HANA-datauppsättningen. Du kan ange vilken typ av Data Factory-dataset för att **RelationalTable**. För närvarande kan anger du inte några typspecifika egenskaper för en SAP HANA-datamängd. Frågan i aktivitetsdefinitionen kopiera anger vilka data som ska läsas från SAP HANA-instans. 

Egenskapen true externa informerar Data Factory-tjänsten att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

Frekvensen och intervall egenskaper definierar schemat. I det här fallet läses data från SAP HANA-instans varje timme. 

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
Den här datauppsättningen definierar Azure Blob-datamängd för utdata. Egenskapen type har angetts till AzureBlob. Avsnittet typeProperties innehåller data som kopieras från SAP HANA-instans ska lagras. Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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


### <a name="pipeline-with-copy-activity"></a>Pipeline med kopieringsaktiviteten

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **RelationalSource** (för SAP HANA-källa) och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **frågan** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i två steg:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När du flyttar data från SAP HANA, används följande mappningar från SAP HANA-typer till .NET-typer.

SAP HANA-typ | .NET baserat typ
------------- | ---------------
TINYINT | Mottagna byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
VERKLIG | Enkel
DUBBEL | Enkel
DECIMAL | Decimal
BOOLESKT VÄRDE | Mottagna byte
VARCHAR | Sträng
NVARCHAR | Sträng
CLOB | byte]
ALPHANUM | Sträng
BLOB | byte]
DATE | DateTime
TID | TimeSpan
TIDSSTÄMPEL | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Kända begränsningar
Det finns några kända begränsningar när du kopierar data från SAP HANA:

- NVARCHAR strängar trunkeras till högst 4 000 Unicode-tecken
- SMALLDECIMAL stöds inte
- VARBINARY stöds inte
- Giltiga datum är mellan 1899, 12, 30 och 9999-12-31

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
