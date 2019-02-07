---
title: Flytta data från DB2 med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig att flytta data från en lokal DB2-databas med hjälp av Azure Data Factory Kopieringsaktivitet
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e190faca778f4a65a3bd4a29d05c01a89ee7e11
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816738"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Flytta data från DB2 med hjälp av Azure Data Factory Kopieringsaktivitet
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-db2-connector.md)
> * [Version 2 (aktuell version)](../connector-db2.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [DB2-koppling i V2](../connector-db2.md).


Den här artikeln beskrivs hur du kan använda Kopieringsaktivitet i Azure Data Factory för att kopiera data från en lokal DB2-databas till ett datalager. Du kan kopiera data till alla som har listats som en mottagare som stöds i den [Data Factory dataförflyttningsaktiviteter](data-factory-data-movement-activities.md#supported-data-stores-and-formats) artikeln. Det här avsnittet bygger på Data Factory-artikel, som visar en översikt över dataförflyttning med hjälp av Kopieringsaktivitet och en lista över data som stöds store kombinationer. 

Data Factory stöder för närvarande endast flyttar data från en DB2-databas till en [mottagarens datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Flytta data från andra data lagrar till en DB2 databasen inte stöds.

## <a name="prerequisites"></a>Förutsättningar
Data Factory stöder anslutning till en lokal DB2-databas med hjälp av den [datahanteringsgateway](data-factory-data-management-gateway.md). Stegvisa anvisningar om hur du ställer in datapipeline gateway att flytta dina data finns i den [flytta data från lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

En gateway krävs även om DB2 finns på virtuella Azure IaaS-datorer. Du kan installera gatewayen på samma IaaS-VM som dataarkiv. Om gatewayen kan ansluta till databasen, kan du installera gatewayen på en annan virtuell dator.

Data management gateway innehåller en inbyggd DB2-drivrutin, så du inte behöver installera en drivrutin för att kopiera data från DB2 manuellt.

> [!NOTE]
> Tips om hur du felsöker anslutning och problem med gateway finns i den [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) artikeln.


## <a name="supported-versions"></a>Versioner som stöds
Data Factory DB2-anslutningsapp stöd för följande IBM DB2-plattformar och versioner med distribuerade Relational Database Architecture (DRDA) SQL Access Manager-versioner 9, 10 och 11:

* IBM DB2 för z/OS-version 11.1
* IBM DB2 för z/OS version 10.1
* IBM DB2 för i (AS400) version 7.2
* IBM DB2 för i (AS400) version 7.1
* IBM DB2 för Linux, UNIX- och Windows (LUW) version 11
* IBM DB2 för LUW version 10,5
* IBM DB2 för LUW version 10.1

> [!TIP]
> Om du får felmeddelandet ”det paket som motsvarar en SQL-instruktionsförfrågan hittades inte. SQLSTATE = 51002 SQLCODE =-805 ”, orsaken är ett nödvändigt paket inte skapas för normal användare på datorns operativsystem. Lös problemet genom att följa instruktionerna för din servertyp DB2:
> - DB2 för i (AS400): Låt en användare skapa samling för normal användare innan du kör Kopieringsaktiviteten. Använd kommandot för att skapa samlingen: `create collection <username>`
> - DB2 för z/OS eller LUW: Använda ett Privilegierade konto – en privilegierad användare eller administratör som har paketet myndigheter och BIND, BINDADD, BEVILJA köra till offentliga behörigheter – och köra kopian en gång. Det nödvändiga paketet skapas automatiskt under kopieringen. Därefter kan du gå tillbaka till normal användare för efterföljande kopia-körningar.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett datalager för den lokala DB2 med olika verktyg och API: er: 

- Det enklaste sättet att skapa en pipeline är att använda Azure Data Factory-Kopieringsguiden. En snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera finns i den [självstudien: Skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md). 
- Du kan också använda verktyg för att skapa en pipeline, inklusive Azure-portalen, Visual Studio, Azure PowerShell, en Azure Resource Manager-mall, .NET-API och REST-API. Stegvisa anvisningar om hur du skapar en pipeline med en Kopieringsaktivitet finns i den [Kopieringsaktiviteten självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa länkade tjänster för att länka indata och utdata datalager till datafabriken.
2. Skapa datauppsättningar som representerar inkommande och utgående data för kopieringen. 
3. Skapa en pipeline med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden Kopiera, JSON-definitioner för Data Factory-länkade skapas tjänster, datauppsättningar och pipeline entiteter automatiskt åt dig. När du använder verktyg eller API: er (med undantag för .NET-API) kan definiera du Data Factory-entiteter med hjälp av JSON-format. JSON-exempel: Kopieringsdata från DB2 till Azure Blob storage visar JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager för den lokala DB2.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för en DB2-datalagret.

## <a name="db2-linked-service-properties"></a>DB2 länkade tjänstegenskaper
I följande tabell visas de JSON-egenskaper som är specifika för en DB2-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **typ** |Den här egenskapen måste anges till **OnPremisesDb2**. |Ja |
| **server** |Namnet på DB2-servern. |Ja |
| **database** |Namnet på DB2-databas. |Ja |
| **schema** |Namnet på schemat i DB2-databas. Den här egenskapen är skiftlägeskänsligt. |Nej |
| **authenticationType** |Typ av autentisering som används för att ansluta till DB2-databas. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| **användarnamn** |Namnet på användarkontot om du använder grundläggande eller Windows-autentisering. |Nej |
| **Lösenord** |Lösenordet för användarkontot. |Nej |
| **gatewayName** |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala DB2-databas. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt, till exempel **struktur**, **tillgänglighet**, och **princip** för en datauppsättning JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure Blob storage, Azure Table storage och så vidare).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för en datauppsättning av typen **RelationalTable**, vilket inkluderar DB2-datauppsättningen har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **tableName** |Namnet på tabellen i DB2-databasinstansen som den länkade tjänsten refererar till. Den här egenskapen är skiftlägeskänsligt. |Nej (om den **fråga** egenskapen för en Kopieringsaktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En lista över avsnitt och egenskaper som är tillgängliga för att definiera kopieringsaktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Kopiera egenskaper för aktivitet, till exempel **namn**, **beskrivning**, **indata** tabellen **matar ut** tabellen, och **princip**, är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens variera för varje aktivitetstyp av. Egenskaperna varierar beroende på vilka typer av datakällor och mottagare för Kopieringsaktiviteten.

För Kopieringsaktiviteten, när källan är av typen **RelationalSource** (som innehåller DB2), följande egenskaper är tillgängliga i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **Fråga** |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `"query": "select * from "MySchema"."MyTable""` |Nej (om den **tableName** egenskapen för en datauppsättning som har angetts) |

> [!NOTE]
> Schema och tabellnamn är skiftlägeskänsliga. I frågeuttryck, omsluta egenskapsnamn med hjälp av ”” (dubbla citattecken).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-exempel: Kopiera data från DB2 till Azure Blob storage
Det här exemplet innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplet visar hur du kopierar data från en DB2-databas till Blob storage. Dock datan kan kopieras till [alla data som stöds lagra Mottagartyp](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Azure Data Factory Kopieringsaktivitet.

Exemplet har följande Data Factory-entiteter:

- En DB2-länkad tjänst av typen [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Azure Blob storage-länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Indata [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder den [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) egenskaper

Exemplet kopierar data från ett frågeresultat i en DB2-databas till en Azure-blob per timme. JSON-egenskaper som används i exemplet beskrivs i avsnitten som följer entitetsdefinitioner för.

Installera och konfigurera en gateway som ett första steg. Anvisningar finns i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

**DB2-länkad tjänst**

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

**Azure Blob storage-länkade tjänst**

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

**Indatauppsättning för DB2**

Exemplet förutsätter att du har skapat en tabell i DB2 med namnet ”MyTable” som har en kolumn med rubriken ”timestamp” för time series-data.

Den **externa** egenskapen anges till ”true”. Den här inställningen informerar Data Factory-tjänsten att den här datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory. Observera att den **typ** är inställd på **RelationalTable**.


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

Data skrivs till en ny blob varje timme genom att ange den **frekvens** egenskap ”Hour” och **intervall** egenskapen till 1. Den **folderPath** egenskapen för blob utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timme delar av starttiden.

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

Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad att använda angivna indata och utdata datauppsättningar och som är schemalagd att köras varje timme. I JSON-definition för pipelinen den **källa** är **RelationalSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer data från tabellen ”Order”.

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

## <a name="type-mapping-for-db2"></a>Mappning för DB2
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel, Kopieringsaktivitet utför automatisk konverteringar från källtyp till mottagare typ genom att använda följande metod i två steg:

1. Konvertera från en typ av interna datakälla till en .NET-typ
2. Konvertera från en .NET-typ till en intern mottagare

Följande mappningar används när Kopieringsaktiviteten konverterar data från en DB2-typ till en .NET-typ:

| DB2-databastyp | .NET framework-typ |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Flyttal |Double |
| decimaltal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Date |DateTime |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binär |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Bild |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Flyttal |Double |
| decimaltal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Date |DateTime |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Om du vill lära dig mer om att mappa kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Repeterbara läsningar från relationella källor
När du kopierar data från en relationsdatabas, Tänk repeterbarhet att undvika oväntade resultat. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsök **princip** -egenskapen för en datauppsättning för att köra en sektor när ett fel uppstår. Se till att samma data läses oavsett hur många gånger sektorn ska köras på nytt och oavsett hur du vill köra sektorn. Mer information finns i [Repeatable läser från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestanda för Kopieringsaktiviteten och sätt att optimera prestanda i den [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md).
