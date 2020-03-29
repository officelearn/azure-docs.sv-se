---
title: Flytta data från DB2 med hjälp av Azure Data Factory
description: Lär dig hur du flyttar data från en lokal DB2-databas med hjälp av Azure Data Factory Copy Activity
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931789"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Flytta data från DB2 med hjälp av Azure Data Factory Copy Activity
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-db2-connector.md)
> * [Version 2 (aktuell version)](../connector-db2.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [DB2-anslutning i V2](../connector-db2.md).


I den här artikeln beskrivs hur du kan använda Kopiera aktivitet i Azure Data Factory för att kopiera data från en lokal DB2-databas till ett datalager. Du kan kopiera data till alla butiker som visas som en diskho som stöds i artikeln [Data Factory dataförflyttningsaktiviteter.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Det här avsnittet bygger på datafabriken, som innehåller en översikt över dataförflyttningar med hjälp av Kopiera aktivitet och en lista över kombinationer av datalager som stöds. 

Data Factory stöder för närvarande endast flytta data från en DB2-databas till ett [diskbänksdatalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Det går inte att flytta data från andra datalager till en DB2-databas.

## <a name="prerequisites"></a>Krav
Data Factory stöder anslutning till en lokal DB2-databas med hjälp av [datahanteringsgatewayen](data-factory-data-management-gateway.md). Stegvisa instruktioner för att konfigurera gatewaydatapipelinen för att flytta data finns i artikeln [Flytta data från lokalt till moln.](data-factory-move-data-between-onprem-and-cloud.md)

En gateway krävs även om DB2 finns på Azure IaaS VM. Du kan installera gatewayen på samma IaaS VM som datalagret. Om gatewayen kan ansluta till databasen kan du installera gatewayen på en annan virtuell dator.

Datahanteringsgatewayen tillhandahåller en inbyggd DB2-drivrutin, så du behöver inte installera en drivrutin manuellt för att kopiera data från DB2.

> [!NOTE]
> Tips om felsökning av anslutnings- och gatewayproblem finns i artikeln [Felsöka gatewayproblem.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)


## <a name="supported-versions"></a>Versioner som stöds
Data Factory DB2-anslutningen stöder följande IBM DB2-plattformar och versioner med DRDA-versioner (Distributed Relational Database Architecture) (Distributed Relational Database Architecture) 9, 10 och 11:

* IBM DB2 för z/OS version 11.1
* IBM DB2 för z/OS version 10.1
* IBM DB2 för i (AS400) version 7.2
* IBM DB2 för i (AS400) version 7.1
* IBM DB2 för Linux, UNIX och Windows (LUW) version 11
* IBM DB2 för LUW version 10.5
* IBM DB2 för LUW version 10.1

> [!TIP]
> Om felmeddelandet "Paketet som motsvarar en SQL-programbegäran hittades inte. SQLSTATE=51002 SQLCODE=-805," anledningen är ett nödvändigt paket skapas inte för den normala användaren på operativsystemet. LÃ¶s problemet genom att fÃ¶ã¶ga instruktionerna fÃ¶r DB2-servertypen:
> - DB2 för i (AS400): Låt en strömanvändare skapa samlingen för den vanliga användaren innan du kör Kopiera aktivitet. Om du vill skapa samlingen använder du kommandot:`create collection <username>`
> - DB2 för z/OS eller LUW: Använd en hög behörighet konto - en privilegierad energianvändare eller administratör som har paketmyndigheter och BIND, BINDADD, GRANT EXECUTE TO PUBLIC behörigheter - för att köra kopian en gång. Det nödvändiga paketet skapas automatiskt under kopian. Efteråt kan du växla tillbaka till den vanliga användaren för dina efterföljande kopieringskörningar.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet för att flytta data från ett lokalt DB2-datalager med hjälp av olika verktyg och API:er: 

- Det enklaste sättet att skapa en pipeline är att använda Azure Data Factory Copy Wizard. En snabb genomgång av hur du skapar en pipeline med hjälp av kopieringsguiden finns i [självstudiekursen: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md). 
- Du kan också använda verktyg för att skapa en pipeline, inklusive Visual Studio, Azure PowerShell, en Azure Resource Manager-mall, .NET API och REST API. Steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet finns i [självstudien Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa länkade tjänster för att länka in- och utdatalager till datafabriken.
2. Skapa datauppsättningar för att representera in- och utdata för kopieringen. 
3. Skapa en pipeline med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder kopieringsguiden skapas JSON-definitioner för datafabrikens länkade tjänster, datauppsättningar och pipeline-entiteter automatiskt åt dig. När du använder verktyg eller API:er (förutom .NET API) definierar du datafabrikentiteterna med hjälp av JSON-formatet. JSON-exemplet: Kopiera data från DB2 till Azure Blob-lagring visar JSON-definitionerna för datafabrikentiteterna som används för att kopiera data från ett lokalt DB2-datalager.

I följande avsnitt finns information om JSON-egenskaperna som används för att definiera de datafabrikentiteter som är specifika för ett DB2-datalager.

## <a name="db2-linked-service-properties"></a>DB2-länkade tjänstegenskaper
I följande tabell visas de JSON-egenskaper som är specifika för en DB2-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Typ** |Den här egenskapen måste anges till **OnPremisesDb2**. |Ja |
| **Server** |Namnet på DB2-servern. |Ja |
| **Databas** |Namnet på DB2-databasen. |Ja |
| **Schemat** |Namnet på schemat i DB2-databasen. Den här egenskapen är skiftlägeskänslig. |Inga |
| **authenticationType** |Den typ av autentisering som används för att ansluta till DB2-databasen. De möjliga värdena är: Anonym, Basic och Windows. |Ja |
| **Användarnamn** |Namnet på användarkontot om du använder Basic- eller Windows-autentisering. |Inga |
| **lösenord** |Lösenordet för användarkontot. |Inga |
| **gatewayName (gatewayName)** |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala DB2-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En lista över de avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt, till exempel **struktur,** **tillgänglighet**och **principen** för en datauppsättning JSON, liknar varandra för alla datauppsättningstyper (Azure SQL, Azure Blob storage, Azure Table Storage och så vidare).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet **typeProperties** för en datauppsättning av typen **RelationalTable**, som innehåller DB2-datauppsättningen, har följande egenskap:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Tablename** |Namnet på tabellen i DB2-databasinstansen som den länkade tjänsten refererar till. Den här egenskapen är skiftlägeskänslig. |Nej (om **query** frågeegenskapen för en kopieringsaktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En lista över de avsnitt och egenskaper som är tillgängliga för att definiera kopieringsaktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Kopiera egenskaper för aktivitet, till exempel **namn,** **beskrivning,** **indatatabell,** **utdatatabell** och **princip**, är tillgängliga för alla typer av aktiviteter. Vilka egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar för varje aktivitetstyp. För Kopieringsaktivitet varierar egenskaperna beroende på vilka typer av datakällor och diskhoar.

När källan är av typen **RelationalSource** (som innehåller DB2) för kopieringsaktivitet är följande egenskaper tillgängliga i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **Fråga** |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `"query": "select * from "MySchema"."MyTable""` |Nej (om egenskapen **tableName** för en datauppsättning har angetts) |

> [!NOTE]
> Schema- och tabellnamn är skiftlägeskänsliga. I frågesatsen omsluter du egenskapsnamn med hjälp av "" (dubbla citattecken).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON exempel: Kopiera data från DB2 till Azure Blob storage
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplet visar hur du kopierar data från en DB2-databas till Blob-lagring. Data kan dock kopieras till [alla datalagermottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Azure Data Factory Copy Activity.

Exemplet har följande datafabrikentiteter:

- En DB2 länkad tjänst av typen [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- En Azure Blob storage-länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder egenskaperna [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från en fråga resulterar i en DB2-databas till en Azure-blob varje timme. De JSON-egenskaper som används i exemplet beskrivs i de avsnitt som följer entitetsdefinitionerna.

Som ett första steg installerar och konfigurerar du en datagateway. Instruktioner finns i artikeln [Flytta data mellan lokala platser och moln.](data-factory-move-data-between-onprem-and-cloud.md)

**DB2 länkad tjänst**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Azure Blob lagringslänkade tjänst**

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

**DB2 indatauppsättning**

Exemplet förutsätter att du har skapat en tabell i DB2 med namnet "MyTable" som har en kolumn med namnet "tidsstämpel" för tidsseriedata.

Den **externa** egenskapen är inställd på "true". Den här inställningen informerar datafabrikstjänsten om att den här datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken. Observera att **egenskapen type** är inställd på **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob **frequency** varje timme genom att ange frekvensegenskapen till "Timme" och **intervallegenskapen** till 1. Egenskapen **folderPath** för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delarna av år, månad, dag och timme i starttiden.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline för kopieringsaktiviteten**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda angivna in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer data från tabellen "Order".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Typmappning för DB2
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy Activity automatiska typkonverteringar från källtyp till sink-typ med hjälp av följande tvåstegsmetod:

1. Konvertera från en inbyggd källtyp till en .NET-typ
2. Konvertera från en .NET-typ till en inbyggd sink-typ

Följande mappningar används när Kopiera aktivitet konverterar data från en DB2-typ till en .NET-typ:

| Databastyp för DB2 | .NET-ramtyp |
| --- | --- |
| Smallint |Int16 (int16) |
| Integer |Int32 |
| Bigint |Int64 |
| Verkliga |Enkel |
| Double |Double |
| Float (Flyttal) |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Datum |DateTime |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Xml |Byte[] |
| Char |String |
| Varchar |String |
| LongVarChar (Olikart) |String |
| DB2DynArray |String |
| Binär |Byte[] |
| Varbinary |Byte[] |
| LongVarBinary (LångVarBinary) |Byte[] |
| Grafisk |String |
| Vargrafiska |String |
| LongVarGraphic (olikartade) |String |
| Clob |String |
| Blob |Byte[] |
| DbClob (D.) |String |
| Smallint |Int16 (int16) |
| Integer |Int32 |
| Bigint |Int64 |
| Verkliga |Enkel |
| Double |Double |
| Float (Flyttal) |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Datum |DateTime |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättningen till kolumner i sink-datauppsättningen finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Repeterbara läsningar från relationskällor
När du kopierar data från ett relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera **egenskapen** för återförsök för en datauppsättning för att köra ett segment igen när ett fel inträffar. Kontrollera att samma data läss oavsett hur många gånger segmentet körs igen och oavsett hur du kör segmentet igen. Mer information finns i [Repeterbara läsningar från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestanda för kopieringsaktivitet och sätt att optimera prestanda i [guiden Kopiera aktivitetsprestanda och justering](data-factory-copy-activity-performance.md).
