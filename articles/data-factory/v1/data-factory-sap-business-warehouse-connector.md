---
title: Flytta data från SAP Business Warehouse med Azure Data Factory
description: Läs mer om hur du flyttar data från SAP Business Warehouse med Azure Data Factory.
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
ms.openlocfilehash: 108bdf057cd375e28b10a6838ec5c8c6f57749a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281059"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Flytta data från SAP Business Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sap-business-warehouse-connector.md)
> * [Version 2 (aktuell version)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [SAP Business Warehouse-anslutningsappen i V2](../connector-sap-business-warehouse.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från ett lokalt SAP Business Warehouse (BW). Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett lokalt SAP Business Warehouse-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data factory stöder för närvarande endast att flytta data från ett SAP Business Warehouse till andra datalager, men inte för att flytta data från andra datalager till ett SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
Den här anslutningen stöder SAP Business Warehouse version 7.x. Den stöder kopiering av data från InfoCubes och QueryCubes (inklusive BEx-frågor) med MDX-frågor.

Så här aktiverar du anslutningen till SAP BW-instansen genom att installera följande komponenter:
- **Data Management Gateway:** Data Factory-tjänsten stöder anslutning till lokala datalager (inklusive SAP Business Warehouse) med hjälp av en komponent som kallas Data Management Gateway. Mer information om Data Management Gateway och steg-för-steg-instruktioner för att konfigurera gatewayen finns i [Flytta data mellan lokalt datalager till molndatalagringsartikel.](data-factory-move-data-between-onprem-and-cloud.md) Gateway krävs även om SAP Business Warehouse finns i en virtuell Azure IaaS-dator (VM). Du kan installera gatewayen på samma virtuella dator som datalagret eller på en annan virtuell dator så länge gatewayen kan ansluta till databasen.
- **SAP NetWeaver-biblioteket** på gateway-datorn. Du kan hämta SAP Netweaver-biblioteket från din SAP-administratör eller direkt från [SAP Software Download Center](https://support.sap.com/swdc). Sök efter **SAP Note-#1025361** för att hämta hämtningsplatsen för den senaste versionen. Kontrollera att arkitekturen för SAP NetWeaver-biblioteket (32- eller 64-bitars) matchar gatewayinstallationen. Installera sedan alla filer som ingår i SAP NetWeaver RFC SDK enligt SAP Note. SAP NetWeaver-biblioteket ingår också i SAP Client Tools-installationen.

> [!TIP]
> Placera dlls extraheras från NetWeaver RFC SDK i system32 mapp.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt Cassandra-datalager med hjälp av olika verktyg/API:er. 

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet. 

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. 
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från ett lokalt SAP Business Warehouse finns i [JSON-exempel: Kopiera data från SAP Business Warehouse till Azure Blob-avsnittet](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) i den här artikeln. 

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ett SAP BW-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för TJÄNSTEN SAP Business Warehouse (BW).

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namn på den server där SAP BW-instansen finns. | sträng | Ja
systemNummer | Systemnummer för SAP BW-systemet. | Tvåsiffrigt decimaltal representeras som en sträng. | Ja
ClientID | Klient-ID för klienten i SAP W-systemet. | Tresiffrigt decimaltal representeras som en sträng. | Ja
användarnamn | Namn på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName (gatewayName) | Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala SAP BW-instansen. | sträng | Ja
krypteradKnuren | Den krypterade referenssträngen. | sträng | Inga

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Det finns inga typspecifika egenskaper som stöds för SAP BW-datauppsättningen för typen **RelationalTable**. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata och utdatatabeller är principer för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan i kopieringsaktivitet är av typen **RelationalSource** (som innehåller SAP BW) är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger mdx-frågan för att läsa data från SAP BW-instansen. | MDX-fråga. | Ja |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-exempel: Kopiera data från SAP Business Warehouse till Azure Blob
I följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det här exemplet visar hur du kopierar data från ett lokalt SAP Business Warehouse till en Azure Blob Storage. Data kan dock kopieras **direkt** till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.  

> [!IMPORTANT]
> Det här exemplet innehåller JSON-utdrag. Den innehåller inte steg-för-steg-instruktioner för att skapa datafabriken. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner.

Exemplet har följande datafabriksenheter:

1. En länkad tjänst av typen [SapBw](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SAP Business Warehouse-instans till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg konfigurerar du datahanteringsgatewayen. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse länkad tjänst
Den här länkade tjänsten länkar din SAP BW-instans till datafabriken. Egenskapen Type är inställd på **SapBw**. Avsnittet typeProperties innehåller anslutningsinformation för SAP BW-instansen. 

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

### <a name="sap-bw-input-dataset"></a>SAP BW-indatauppsättning
Den här datauppsättningen definierar SAP Business Warehouse-datauppsättningen. Du anger typen av datauppsättning för datafabriken på **RelationalTable**. För närvarande anger du inte några typspecifika egenskaper för en SAP BW-datauppsättning. Frågan i definitionen Kopiera aktivitet anger vilka data som ska läsas från SAP BW-instansen. 

Om du ställer in extern egenskap på true informerar datafabrikstjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

Frekvens- och intervallegenskaper definierar schemat. I det här fallet läss data från SAP BW-instansen varje timme. 

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
Den här datauppsättningen definierar utdata Azure Blob-datauppsättningen. Typegenskapen är inställd på AzureBlob. Avsnittet typeProperties anger var data som kopieras från SAP BW-instansen lagras. Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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


### <a name="pipeline-with-copy-activity"></a>Pipeline med kopieringsaktivitet
Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** (för SAP BW-källa) och **sink-typen** är inställd på **BlobSink**. Frågan som angetts **query** för frågeegenskapen väljer de data som ska kopieras under den senaste timmen.

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



### <a name="type-mapping-for-sap-bw"></a>Typmappning för SAP BW
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas från SAP BW används följande mappningar från SAP BW-typer till .NET-typer.

Datatyp i ABAP-ordlistan | .NET-datatyp
-------------------------------- | --------------
ACCP (på andra) |  Int
CHAR | String
Clnt | String
CURR (HÄRD) | Decimal
CUKY (K.) | String
December | Decimal
FLTP (PÅ) | Double
INT1 (INT1) | Byte
INT2 (INT2) | Int16 (int16)
INT4 (INT4) | Int
Lang | String
LCHR (på andra) | String
LRAW (LRAW) | Byte[]
PREC (PREC) | Int16 (int16)
Quan | Decimal
Raw | Byte[]
RÅSNSTRÄNG | Byte[]
Sträng | String
Enhet | String
Dats | String
NUMC (NUMC) | String
TIMS (AVS) | String

> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
