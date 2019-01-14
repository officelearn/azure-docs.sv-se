---
title: Kopiera data från MongoDB med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Mongo DB till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 1ffd1b96e721707f69c47a7cbf11d60f17f3a7d2
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105431"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiera data från MongoDB med Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuell version](connector-mongodb.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

>[!IMPORTANT]
>ADF versionen en ny anslutning, MongoDB, vilket ger bättre interna MongoDB stöd för att jämföra implementeringen av ODBC-baserade, referera till [MongoDB-anslutningsappen](connector-mongodb.md) artikeln på information. Den här äldre MongoDB-anslutningen hålls stöds som – är bakåt compability och för alla nya arbetsbelastningar, Använd för den nya anslutningen.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder MongoDB-anslutningsappen:

- MongoDB **version 2.4, 2.6, 3.0, 3.2, 3.4 och 3.6**.
- Kopiera data med hjälp av **grundläggande** eller **anonym** autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill kopiera data från en MongoDB-databas som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) artikeln om du vill få mer detaljerad information. Integreringskörningen innehåller en inbyggd MongoDB-drivrutin, måste du därför inte installera några drivrutinen manuellt när du kopierar data från MongoDB.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för MongoDB-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för MongoDB-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till: **MongoDb** |Ja |
| server |IP-adressen eller värdnamnet namnet på MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klientanslutningar. |Nej (standard är 27017) |
| databaseName |Namnet på MongoDB-databasen som du vill komma åt. |Ja |
| authenticationType | Typ av autentisering som används för att ansluta till MongoDB-databasen.<br/>Tillåtna värden är: **Grundläggande**, och **anonym**. |Ja |
| användarnamn |Användarkonto för att få åtkomst till MongoDB. |Ja (om du använder grundläggande autentisering). |
| lösenord |Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (om du använder grundläggande autentisering). |
| authSource |Namnet på MongoDB-databasen som du vill använda för att kontrollera dina autentiseringsuppgifter för autentisering. |Nej. Standardinställningen är att använda administratörskontot och databasen som anges med egenskapen databaseName för grundläggande autentisering. |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är FALSKT.  | Nej |
| allowSelfSignedServerCert | Anger om du vill tillåta självsignerade certifikat från servern. Standardvärdet är FALSKT.  | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper har stöd för MongoDB-datauppsättning:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **MongoDbCollection** | Ja |
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
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källa.

### <a name="mongodb-as-source"></a>MongoDB som källa

Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **MongoDbSource** | Ja |
| DocumentDB |Använd anpassade SQL-92 fråga för att läsa data. Till exempel: Välj * från MyTable. |Nej (om ”collectionName” i datauppsättningen har angetts) |

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
                "type": "MongoDbSource",
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
> Ange när SQL-fråga, närmare på DateTime-format. Till exempel: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` eller använda parametern `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schemat av Data Factory

Azure Data Factory-tjänsten skapar schema från en MongoDB-samling med hjälp av den **senaste 100 dokumenten** i samlingen. Om dokumenten 100 inte innehåller fullständig schemat, att vissa kolumner ignoreras under kopieringen.

## <a name="data-type-mapping-for-mongodb"></a>Datatypen mappning för MongoDB

När du kopierar data från MongoDB, används följande mappningar från MongoDB-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Datatypen för MongoDB | Data factory tillfälliga datatyp |
|:--- |:--- |
| Binär |Byte] |
| Boolesk |Boolesk |
| Date |DateTime |
| NumberDouble |Double-värde |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectId |Sträng |
| Sträng |Sträng |
| UUID |GUID |
| Objekt |Renormalized till att platta ut kolumner med ”_” som kapslade avgränsare |

> [!NOTE]
> Mer information om stöd för matriser med virtuella tabeller, som avser [stöd för komplexa typer med hjälp av virtuella tabeller](#support-for-complex-types-using-virtual-tables) avsnittet.
>
> För närvarande stöds inte följande datatyper för MongoDB: DBPointer, JavaScript, Max per minut nyckel reguljärt uttryck, symboler, tidsstämpel, odefinierad.

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer med hjälp av virtuella tabeller

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från MongoDB-databasen. För komplexa typer, till exempel matriser eller objekt med olika typer i dokumenten normaliserar drivrutinen igen data till motsvarande virtuella tabeller. Om en tabell innehåller sådana kolumner, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som den verkliga tabellen utom de komplexa typen kolumnerna. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje kolumn för komplex typ, vilket utökar kapslade data. Virtuella tabeller namnges med namnet på tabellen verkliga, avgränsare ”_” och namnet på den matris eller ett objekt.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt den Avnormaliserade data. Du kan komma åt innehållet i MongoDB-matriser genom att fråga och ansluta till virtuella tabeller.

### <a name="example"></a>Exempel

Till exempel är ExampleTable här en MongoDB-tabell som har en kolumn med en matris med objekt i varje cell – fakturor, och en kolumn med en matris med skalära typer – betyg.

| _id | Kundens namn | Fakturor | Servicenivå | Klassificeringar |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: ”123” objekt: ”toaster”, price: ”456” rabatt: ”0.2”}, {invoice_id: ”124” objekt: ”vara”, price: ”1235” rabatt: ”0.2”}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: ”135” objekt: ”kylskåp”, price: ”12543” rabatt: ”0,0”}] |Guld |[1,2] |

Drivrutinen skulle generera flera virtuella tabeller som representerar en enda tabell. Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas i exemplet. Bastabellen innehåller alla data för den ursprungliga tabellen, men data från matriser har utelämnats och utökas i virtuella tabeller.

| _id | Kundens namn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

Följande tabeller visar virtuella tabeller som representerar de ursprungliga matriserna i det här exemplet. Dessa tabeller innehåller följande:

* En referens tillbaka till den ursprungliga primärnyckelkolumnen som motsvarar raden i den ursprungliga matrisen (via kolumnen _id)
* En indikation om placeringen av data inom den ursprungliga matrisen
* Utökade data för varje element i matrisen

**Tabell ”ExampleTable_Invoices”:**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Objekt | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |vara |1235 |0.2 |
| 2222 |0 |135 |kylskåp |12543 |0.0 |

**Tabell ”ExampleTable_Ratings”:**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
