---
title: Flytta data från OData-källor
description: Läs mer om hur du flyttar data från OData-källor med Hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 95f92d4e5616d7754c355610685701a8e089b84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265914"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Flytta data från en OData-källa med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-odata-connector.md)
> * [Version 2 (aktuell version)](../connector-odata.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [OData-anslutningsappen i V2](../connector-odata.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en OData-källa. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från en OData-källa till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data factory stöder för närvarande endast att flytta data från en OData-källa till andra datalager, men inte för att flytta data från andra datalager till en OData-källa.

## <a name="supported-versions-and-authentication-types"></a>Versioner och autentiseringstyper som stöds
Den här OData-anslutningen stöder OData version 3.0 och 4.0 och du kan kopiera data från både moln-OData- och lokala OData-källor. För det senare måste du installera Data Management Gateway. Mer information om Data Management Gateway finns i [Flytta data mellan lokal och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

Nedanstående autentiseringstyper stöds:

* För **cloud** att komma åt moln-OData-feed kan du använda anonym, grundläggande (användarnamn och lösenord) eller Azure Active Directory-baserad OAuth-autentisering.
* Om du vill komma åt **den lokala OData-feeden** kan du använda anonym, grundläggande (användarnamn och lösenord) eller Windows-autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från en OData-källa med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från en OData-källa finns i [JSON-exempel: Kopiera data från OData-källa till Azure Blob-avsnittet](#json-example-copy-data-from-odata-source-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för OData-källa:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
I följande tabell beskrivs beskrivning av JSON-element som är specifika för OData-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OData** |Ja |
| url |Url till OData-tjänsten. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till OData-källan. <br/><br/> För moln-OData är möjliga värden Anonym, Basic och OAuth (obs Azure Data Factory stöder för närvarande endast Azure Active Directory-baserad OAuth). <br/><br/> För lokala OData är möjliga värden Anonyma, Grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder grundläggande autentisering. |Ja (endast om du använder grundläggande autentisering) |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Ja (endast om du använder grundläggande autentisering) |
| auktoriseradRedential |Om du använder OAuth klickar du på **Knappen Auktorisera** i guiden Kopia av Data fabrik eller Redigerare och anger autentiseringsuppgifterna, så genereras värdet för den här egenskapen automatiskt. |Ja (endast om du använder OAuth-autentisering) |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala OData-tjänsten. Ange bara om du kopierar data från den lokala OData-källan. |Inga |

### <a name="using-basic-authentication"></a>Använda grundläggande autentisering
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Använda Windows-autentisering som använder lokal OData-källa
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Använda OAuth-autentisering som använder moln-OData-källa
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **ODataResource** (som innehåller OData-datauppsättning) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| path |Sökväg till OData-resursen |Inga |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet typEgenskaper i aktiviteten å andra sidan varierar beroende på varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan är av typen **RelationalSource** (som innehåller OData) är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Exempel | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |"?$select=Namn, Beskrivning&$top=5" |Inga |

## <a name="type-mapping-for-odata"></a>Typmappning för OData
Som nämns i artikeln [för dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod.

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas från OData används följande mappningar från OData-typer till .NET-typ.

| OData-datatyp | .NET-typ |
| --- | --- |
| Edm.Binary (Edm.Binary) |Byte[] |
| Edm.Boolean |Bool |
| Edm.Byte |Byte[] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |Double |
| Edm.Singel |Enkel |
| Edm.Guid (edm.guid) |GUID |
| Edm.Int16 (på andra) |Int16 (int16) |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte (edm.sbyte) |Int16 (int16) |
| Edm.String |String |
| Edm.Time (på Edm.Time) |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> OData-komplexa datatyper t.ex.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON exempel: Kopiera data från OData källa till Azure Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från en OData-källa till en Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory. Exemplet har följande datafabrikentiteter:

1. En länkad tjänst av typen [OData](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [ODataResource](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från att fråga mot en OData-källa till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**OData länkad tjänst:** I det här exemplet används anonym autentisering. Se [OData-länkat tjänstavsnitt](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Azure Storage-länkad tjänst:**

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

**OData-indatauppsättning:**

Inställningen "extern": "true" informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Det är valfritt att ange **sökvägen** i datauppsättningsdefinitionen.

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopiera aktivitet i en pipeline med OData-källa och Blob-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de senaste (senaste) data från OData-källan.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Det är valfritt att ange **frågan** i pipelinedefinitionen. **URL:en** som tjänsten Data Factory använder för att hämta data är: URL som anges i den länkade tjänsten (obligatoriskt) + sökväg som anges i datauppsättningen (valfritt) + fråga i pipelinen (valfritt).

### <a name="type-mapping-for-odata"></a>Typmappning för OData
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas från OData-datalager mappas OData-datatyper till .NET-typer.

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
