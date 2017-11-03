---
title: "Kopiera data från MongoDB med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från Mongo DB till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 6f11d4184b5edbcb80411f146ae01ecfe62b6e91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiera data från MongoDB med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-on-premises-mongodb-connector.md)
> * [Version 2 – förhandsversion](connector-mongodb.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [MongoDB-anslutningen i V1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från MongoDB-databas till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här MongoDB-anslutningen:

- MongoDB **version 2.4, 2.6, 3.0 och 3.2**.
- Kopiera data med hjälp av **grundläggande** eller **anonym** autentisering.

## <a name="prerequisites"></a>Krav

Om du vill kopiera data från en MongoDB-databas som inte är offentligt tillgänglig, måste du konfigurera en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikel mer information. Integration Runtime innehåller en inbyggd MongoDB-drivrutin, därför behöver du inte installera en drivrutin manuellt när du kopierar data från/till MongoDB.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till MongoDB-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för MongoDB länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **MongoDb** |Ja |
| server |IP-adressen eller värdnamnet namnet på MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter anslutningar. |Nej (standard är 27017) |
| DatabaseName |Namnet på den MongoDB-databas som du vill komma åt. |Ja |
| AuthenticationType | Typ av autentisering som används för att ansluta till MongoDB-databasen.<br/>Tillåtna värden är: **grundläggande**, och **anonym**. |Ja |
| användarnamn |Användarkonto för att få åtkomst till MongoDB. |Ja (om grundläggande autentisering används). |
| lösenord |Lösenord för användaren. Markera det här fältet som SecureString. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på MongoDB-databas som du vill använda för att kontrollera autentiseringsuppgifterna för autentisering. |Nej. Standardvärdet är att använda administratörskontot och databasen som anges med egenskapen databaseName för grundläggande autentisering. |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB dataset.

Ange typegenskapen för dataset för att kopiera data från MongoDB, **MongoDbCollection**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **MongoDbCollection** | Ja |
| Samlingsnamn |Namnet på samlingen i MongoDB-databas. |Ja |

**Exempel:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }

```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källa.

### <a name="mongodb-as-source"></a>MongoDB som källa

Om du vill kopiera data från MongoDB, anger du datakällan i kopieringsaktiviteten till **MongoDbSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **MongoDbSource** | Ja |
| DocumentDB |Använd anpassade SQL-92-fråga för att läsa data. Till exempel: Välj * från mytable prefix. |Nej (om ”samlingsnamn” i datamängden har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Ange när SQL-frågan titta närmare på DateTime-format. Exempel: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="schema-by-data-factory"></a>Schema som Data Factory

Azure Data Factory-tjänsten skapar schema från en MongoDB-samling med hjälp av den **senaste 100 dokument** i samlingen. Om dokumenten 100 inte innehåller fullständig schemat, kan vissa kolumner ignoreras under kopieringen.

## <a name="data-type-mapping-for-mongodb"></a>Datatypen mappning för MongoDB

När du kopierar data från MongoDB, används följande mappningar från MongoDB-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Datatypen för MongoDB | Data factory tillfälliga datatyp |
|:--- |:--- |
| Binär |byte] |
| Booleskt värde |Booleskt värde |
| Date |Datum och tid |
| NumberDouble |dubbla |
| NumberInt |Int32 |
| NumberLong |Int64 |
| Objekt-ID |Sträng |
| Sträng |Sträng |
| UUID |GUID |
| Objekt |Renormalized förenkla i kolumner med ”_” som kapslad avgränsare |

> [!NOTE]
> Mer information om stöd för matriser med virtuella tabeller, referera till [stöd för komplexa typer som använder virtuella tabeller](#support-for-complex-types-using-virtual-tables) avsnitt.
>
> För närvarande följande MongoDB-datatyper stöds inte: DBPointer, JavaScript, Max per minut nyckel, reguljärt uttryck, symboler, Timestamp, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer som använder virtuella tabeller

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från din MongoDB-databas. För komplexa typer som matriser eller objekt med olika typer i dokumenten normaliserar drivrutinen igen data i motsvarande virtuella tabeller. Om en tabell innehåller sådana kolumner, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som verkliga tabell utom komplex typ-kolumner. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje kolumn för komplex typ, som utökar kapslade data. Virtuella tabeller namnges med namnet på tabellen verkliga, avgränsare ”_” och namnet på den matris eller ett objekt.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt Avnormaliserade data. Du kan komma åt innehållet i MongoDB matriser genom att fråga och ansluta till virtuella tabeller.

### <a name="example"></a>Exempel

Till exempel är ExampleTable här en MongoDB-tabell som har en kolumn med en matris med objekt i varje cell – fakturor, och en kolumn med en matris med skalära typer – klassificeringar.

| _id | Kundens namn | Fakturor | Servicenivå | Klassificering |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: ”123” objektet: ”toaster”, pris: ”456” rabatt: ”0,2”}, {invoice_id: ”124” objektet: ”vara”, pris: rabatt ”1235”: ”0,2”}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: objektet ”135”: ”kombinerad kyl”, pris: ”12543” rabatt: ”0,0”}] |Guld |[1,2] |

Drivrutinen skulle generera flera virtuella tabeller som representerar denna tabell. Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas i exemplet. Bastabellen innehåller alla data för den ursprungliga tabellen, men data från matriserna har utelämnats och utökas i virtuella tabeller.

| _id | Kundens namn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

Följande tabeller visar virtuella tabeller som representerar de ursprungliga matriserna i exemplet. Dessa tabeller innehåller följande:

* En referens till den ursprungliga primärnyckelkolumnen som motsvarar raden i den ursprungliga matrisen (via kolumnen _id)
* Uppgift om placeringen av data i den ursprungliga matrisen
* Utökade data för varje element i matrisen

**Tabell ”ExampleTable_Invoices”:**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Objektet | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |vara |1235 |0.2 |
| 2222 |0 |135 |kombinerad kyl |12543 |0.0 |

**Tabell ”ExampleTable_Ratings”:**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).