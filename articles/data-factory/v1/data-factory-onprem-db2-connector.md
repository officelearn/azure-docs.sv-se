---
title: Flytta data från DB2 med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig att flytta data från en lokal DB2-databas med hjälp av Azure Data Factory-Kopieringsaktiviteten
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fc4ce0a2ae33e99ecede371d9f17fb9a63851f64
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622031"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Flytta data från DB2 med hjälp av Azure Data Factory-Kopieringsaktiviteten
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-onprem-db2-connector.md)
> * [Version 2 – förhandsversion](../connector-db2.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [DB2-koppling i V2](../connector-db2.md).


Den här artikeln beskriver hur du kan använda Kopieringsaktiviteten i Azure Data Factory för att kopiera data från en lokal DB2-databas till ett datalager. Du kan kopiera data till en butik som har listats som en mottagare som stöds i den [Data Factory data movement aktiviteter](data-factory-data-movement-activities.md#supported-data-stores-and-formats) artikel. Det här avsnittet bygger på Data Factory-artikel som visar en översikt över data flyttas med hjälp av Kopieringsaktiviteten och visar store kombinationer av data som stöds. 

Data Factory stöder för närvarande endast flytta data från en DB2-databas till en [stöds sink datalagret](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Flytta data från andra data sparas till en DB2-databas inte stöds.

## <a name="prerequisites"></a>Förutsättningar
Data Factory stöder anslutning till en lokal DB2-databas med hjälp av den [data management gateway](data-factory-data-management-gateway.md). Stegvisa instruktioner för att konfigurera gateway data pipelinen för att flytta data, finns det [flytta data från lokalt till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikel.

En gateway krävs även om DB2 finns på Azure IaaS-VM. Du kan installera gatewayen på samma IaaS-VM som dataarkiv. Om gatewayen kan ansluta till databasen, kan du installera gatewayen på en annan virtuell dator.

Data management gateway innehåller en inbyggd DB2-drivrutin, så du inte behöver installera en drivrutin för att kopiera data från DB2 manuellt.

> [!NOTE]
> Tips om hur du felsöker anslutning och gateway-problem finns i den [felsökning av problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) artikel.


## <a name="supported-versions"></a>Versioner som stöds
Data Factory DB2-koppling stöder följande IBM DB2-plattformar och versioner med distribuerade relationella Database Architecture (DRDA) SQL åtkomst Manager version 9, 10 och 11:

* IBM DB2 för z/OS-versionen 11,1
* IBM DB2 för z/OS-version 10.1
* IBM DB2 för i (AS400) version 7.2
* IBM DB2 för i (AS400) version 7.1
* IBM DB2 för Linux, UNIX- och Windows (LUW) version 11
* IBM DB2 för LUW version 10.5
* IBM DB2 för LUW version 10.1

> [!TIP]
> Om du får felmeddelandet ”det paket som motsvarar en SQL-instruktionen Körningsbegäran hittades inte. SQLSTATE = 51002 SQLCODE =-805 ”, orsaken är ett nödvändigt paket inte skapas för normal användare i Operativsystemet. Lös problemet genom att följa dessa instruktioner för din servertyp DB2:
> - DB2 för i (AS400): gör en privilegierad användare skapa samling för normal användare innan du kör Kopieringsaktiviteten. Använd kommandot för att skapa samlingen: `create collection <username>`
> - DB2 för z/OS eller LUW: Använd ett konto för privilegierade--privilegierad användare eller administratör som har paketet myndigheter och BIND BINDADD, BEVILJA EXECUTE till offentliga behörigheter--ska köras en gång kopian. Det nödvändiga paketet skapas automatiskt under kopieringen. Därefter kan du växla tillbaka till normal användare för efterföljande kopia-körs.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet för att flytta data från ett dataarkiv för lokala DB2 med hjälp av olika verktyg och API: er: 

- Det enklaste sättet att skapa en pipeline är att använda guiden Kopiera Azure Data Factory. En snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md). 
- Du kan också använda verktyg för att skapa en pipeline, inklusive Azure-portalen, Visual Studio, Azure PowerShell, en Azure Resource Manager-mall, .NET-API och REST-API. Stegvisa instruktioner för att skapa en pipeline med en kopieringsaktiviteten, finns det [Kopieringsaktiviteten kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa länkade tjänster du vill länka indata och utdata datalager till din data factory.
2. Skapa datauppsättningar som representerar inkommande och utgående data för kopieringen. 
3. Skapa en pipeline med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden Kopiera, JSON definitioner för Datafabriken länkade skapas automatiskt tjänster, datauppsättningar och pipeline entiteter för dig. När du använder verktyg eller API: er (utom .NET-API) kan definiera du Data Factory-enheter med hjälp av JSON-format. Den [JSON-exempel: kopiera data från DB2 till Azure Blob storage](#json-example-copy-data-from-db2-to-azure-blob) visar JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett lokalt DB2-dataarkiv.

Följande avsnitt innehåller information om de JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för en DB2-databas.

## <a name="db2-linked-service-properties"></a>DB2 länkade tjänstens egenskaper
I följande tabell visas JSON-egenskaper som är specifika för en DB2 länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **typ** |Den här egenskapen måste anges till **OnPremisesDb2**. |Ja |
| **Server** |Namnet på DB2-servern. |Ja |
| **Databasen** |Namnet på DB2-databasen. |Ja |
| **Schemat** |Namnet på schemat i DB2-databasen. Den här egenskapen är skiftlägeskänsliga. |Nej |
| **AuthenticationType** |Typ av autentisering som används för att ansluta till DB2-databasen. Möjliga värden är: anonym, grundläggande och Windows. |Ja |
| **Användarnamn** |Namnet för användarkontot om du använder grundläggande eller Windows-autentisering. |Nej |
| **Lösenord** |Lösenordet för användarkontot. |Nej |
| **gatewayName** |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala DB2-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar, finns det [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitten som **struktur**, **tillgänglighet**, och **princip** för en dataset JSON är liknande för alla typer av dataset Azure SQL (Azure Blob storage, Azure Table storage och så vidare).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. Den **typeProperties** avsnittet för en dataset av typen **RelationalTable**, som innehåller DB2-dataset har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **tableName** |Namnet på tabellen i DB2-databasinstansen som den länkade tjänsten refererar till. Den här egenskapen är skiftlägeskänsliga. |Nej (om den **frågan** -egenskapen för en kopia aktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En lista över avsnitt och egenskaper som är tillgängliga för att definiera kopiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Kopiera egenskaper för aktivitet som **namn**, **beskrivning**, **indata** tabellen **matar ut** tabellen och **princip**, är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar för varje aktivitetstyp. För Kopieringsaktivitet kan variera egenskaperna beroende på vilka typer av datakällor och sänkor.

För Kopieringsaktiviteten när källan är av typen **RelationalSource** (som omfattar DB2), följande egenskaper är tillgängliga i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **Frågan** |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `"query": "select * from "MySchema"."MyTable""` |Nej (om den **tableName** -egenskapen för en dataset har angetts) |

> [!NOTE]
> Schema och tabellnamn är skiftlägeskänsliga. I frågeuttrycket omge egenskapsnamn med hjälp av ”” (dubbla citattecken).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-exempel: kopiera data från DB2 till Azure Blob storage
Det här exemplet innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplet visar hur du kopierar data från en DB2-databas till Blob storage. Dock datan kan kopieras till [stöds data lagra Mottagartypen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Azure Data Factory-Kopieringsaktiviteten.

Exemplet har följande Data Factory-enheter:

- En DB2 länkade tjänsten av typen [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Ett Azure Blob storage länkade tjänsten av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Indata [dataset](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder den [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) egenskaper

Exemplet kopierar data från ett frågeresultat i en DB2-databas till en Azure blob varje timme. JSON-egenskaper som används i samplet som beskrivs i avsnitten som följer entitet definitioner.

Som ett första steg bör du installera och konfigurera en datagateway. Anvisningar finns i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

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

**Azure Blob länkade lagringstjänsten**

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

**DB2 inkommande dataset**

Exemplet förutsätter att du har skapat en tabell i DB2 med namnet ”mytable” som prefix som har en kolumn med rubriken ”tidsstämpel” för tid series-data.

Den **externa** egenskapen har värdet ”true”. Den här inställningen informerar Data Factory-tjänsten att den här datauppsättningen är extern till data factory och inte tillverkas av en aktivitet i datafabriken. Observera att den **typen** egenskap är inställd på **RelationalTable**.


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

Data skrivs till en ny blob varje timme genom att ange den **frekvens** egenskapen till ”timme” och **intervall** egenskap till 1. Den **folderPath** egenskapen för blob utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timme delar av starttiden.

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

Pipelinen innehåller en kopia aktivitet som är konfigurerad att använda angivna indata och utdata datauppsättningar och som är schemalagda att köras varje timme. I JSON-definitionen för pipelinen, den **källa** är inställd på **RelationalSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **frågan** egenskapen väljer vilka data från tabellen ”Order”.

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
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln Kopieringsaktiviteten utför automatisk konverteringar från källtyp till sink typ genom att använda följande metod i två steg:

1. Konvertera från en inbyggd typ till en .NET-typ
2. Konvertera från en .NET-typ till en intern Mottagartypen

Följande mappningar används när Kopieringsaktiviteten konverterar data från en DB2-typ till en .NET-typ:

| Typen för DB2-databas | .NET framework-typ |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Enkel |
| dubbla |dubbla |
| Flyttal |dubbla |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Date |DateTime |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Xml |byte] |
| Char |Sträng |
| VarChar |Sträng |
| LongVarChar |Sträng |
| DB2DynArray |Sträng |
| Binär |byte] |
| VarBinary |byte] |
| LongVarBinary |byte] |
| Bild |Sträng |
| VarGraphic |Sträng |
| LongVarGraphic |Sträng |
| CLOB |Sträng |
| Blob |byte] |
| DbClob |Sträng |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Enkel |
| dubbla |dubbla |
| Flyttal |dubbla |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Date |DateTime |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Xml |byte] |
| Char |Sträng |

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Information om hur du mappar kolumner i datauppsättning för källan till kolumner i datauppsättning för sink finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Repeterbara läsningar från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när du kopierar data från en relationsdatabas. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera den här gången **princip** -egenskapen för en dataset att köra ett segment när ett fel uppstår. Kontrollera att samma data läses oavsett hur många gånger sektorn körs och oavsett hur du köra sektorn. Mer information finns i [Repeatable läser från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestanda för Kopieringsaktiviteten och sätt att optimera prestandan i den [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md).
