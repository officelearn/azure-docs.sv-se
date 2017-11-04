---
title: "Flytta data från SAP Business Warehouse med hjälp av Azure Data Factory | Microsoft Docs"
description: "Läs mer om hur du flyttar data från SAP Business Warehouse med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6bbae79e59a200897f465e1381fea57a7ecde3f1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Flytta data från SAP Business Warehouse med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-sap-business-warehouse-connector.md)
> * [Version 2 – förhandsversion](../connector-sap-business-warehouse.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [SAP Business Warehouse-kopplingen i V2](../connector-sap-business-warehouse.md).


Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en lokal SAP Business Warehouse (BW). Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från en lokal SAP Business Warehouse dataarkiv till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flytta data från en SAP Business Warehouse till andra databaser, men inte för att flytta data från andra datalager till en SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Den här anslutningen har stöd för SAP Business Warehouse version 7.x. Det stöder kopiering av data från InfoCubes och QueryCubes (inklusive BEx frågor) med hjälp av MDX-frågor.

Om du vill aktivera anslutning till SAP BW-instans, installera följande komponenter:
- **Data Management Gateway**: Data Factory-tjänsten har stöd för att ansluta till lokala data Arkiv (inklusive SAP Business Warehouse) med hjälp av en komponent som kallas Data Management Gateway. Mer information om Data Management Gateway samt stegvisa instruktioner för hur du konfigurerar en gateway, se [flytta mellan lokala data datalager till molnet datalagret](data-factory-move-data-between-onprem-and-cloud.md) artikel. Gateway krävs även om SAP Business Warehouse finns i en Azure IaaS-virtuella (VM). Du kan installera gatewayen på samma virtuella dator som dataarkiv eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.
- **SAP NetWeaver biblioteket** på gateway-datorn. Du kan hämta SAP Netweaver-bibliotek från SAP-administratören eller direkt från den [SAP Software Download Center](https://support.sap.com/swdc). Sök efter den **SAP Obs #1025361** få hämtningsplatsen för den senaste versionen. Kontrollera att arkitekturen för SAP NetWeaver biblioteket (32-bitars eller 64-bitars) matchar din gateway-installation. Installera alla filer som ingår i SAP NetWeaver RFC SDK enligt SAP-kommentar. Bibliotek för SAP NetWeaver ingår också i klientverktyg för SAP-installationen.

> [!TIP]
> Placera DLL: er som extraheras från NetWeaver RFC SDK i mappen system32.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en lokal Cassandra data store med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från en lokal SAP Business Warehouse finns [JSON-exempel: kopiera data från SAP Business Warehouse till Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till ett SAP BW-datalager:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för SAP Business Warehouse (BW) länkad tjänst.

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP BW-instansen finns. | Sträng | Ja
systemNumber | Systemnummer för SAP BW-system. | Två siffror decimaltal representeras som en sträng. | Ja
clientId | Klient-ID för klienten i systemets SAP-W. | Tre siffror decimaltal representeras som en sträng. | Ja
användarnamn | Namnet på den användare som har åtkomst till SAP-server | Sträng | Ja
lösenord | Lösenord för användaren. | Sträng | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SAP BW-instans. | Sträng | Ja
encryptedCredential | Strängen som krypterade autentiseringsuppgifter. | Sträng | Nej

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. Det finns inga typspecifika egenskaper som stöds för SAP BW datauppsättningen av typen **RelationalTable**. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper, till exempel namn, beskrivning, inkommande och utgående tabeller är principer är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar med varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

När datakällan i en Kopieringsaktivitet är av typen **RelationalSource** (som innehåller SAP BW), följande egenskaper finns i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger MDX-fråga för att läsa data från SAP BW-instans. | MDX-fråga. | Ja |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-exempel: kopiera data från SAP Business Warehouse till Azure-Blob
I följande exempel innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det här exemplet visas hur du kopierar data från en lokal SAP Business Warehouse till ett Azure Blob Storage. Dock datan kan kopieras **direkt** till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.  

> [!IMPORTANT]
> Det här exemplet innehåller JSON kodavsnitt. Stegvisa instruktioner för att skapa datafabriken inkluderas inte. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner.

Exemplet har följande data factory enheter:

1. En länkad tjänst av typen [SapBw](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SAP Business Warehouse-instans till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg bör du konfigurera data management gateway. Anvisningarna är i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse länkade tjänsten
Det här länkade tjänsten länkar SAP BW-instans till datafabriken. Egenskapen type har angetts **SapBw**. Avsnittet typeProperties innehåller anslutningsinformation för SAP BW-instans. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>SAP BW inkommande dataset
Den här datauppsättningen definierar SAP Business Warehouse-datauppsättningen. Du kan ange vilken typ av Data Factory-dataset för att **RelationalTable**. För närvarande kan anger du inte några typspecifika egenskaper för en SAP BW datauppsättning. Frågan i aktivitetsdefinitionen kopiera anger vilka data som ska läsas från SAP BW-instans. 

Egenskapen true externa informerar Data Factory-tjänsten att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

Frekvensen och intervall egenskaper definierar schemat. I det här fallet läses data från SAP BW-instans varje timme. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Den här datauppsättningen definierar Azure Blob-datamängd för utdata. Egenskapen type har angetts till AzureBlob. Avsnittet typeProperties innehåller data som kopieras från SAP BW-instans ska lagras. Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **RelationalSource** (för SAP BW källa) och **sink** är inställd på **BlobSink**. Frågan som angetts för den **frågan** egenskapen väljer vilka data under den senaste timmen att kopiera.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Mappning för SAP BW
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i två steg:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När du flyttar data från SAP BW, används följande mappningar från SAP BW typer för .NET-typer.

Datatypen i ABAP ordlista | .NET-datatyp
-------------------------------- | --------------
ACCP |  int
CHAR | Sträng
CLNT | Sträng
AKTUELLT DATUM | Decimal
CUKY | Sträng
DEC | Decimal
FLTP | dubbla
INT1 | Mottagna byte
INT2 | Int16
INT4 | int
LANG | Sträng
LCHR | Sträng
LRAW | byte]
PREC | Int16
QUAN | Decimal
RÅDATA | byte]
RAWSTRING | byte]
STRÄNG | Sträng
ENHET | Sträng
DATS | Sträng
NUMC | Sträng
TIMS | Sträng

> [!NOTE]
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
