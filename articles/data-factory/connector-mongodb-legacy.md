---
title: Kopiera data från MongoDB med äldre
description: Lär dig hur du kopierar data från Mongo DB till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 0bdd8d454b979250b57cf657d347309b99a86ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892557"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiera data från MongoDB med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuell version](connector-mongodb.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

>[!IMPORTANT]
>ADF släppa en ny MongoDB-anslutning som ger bättre infödda MongoDB stöd jämföra med denna ODBC-baserade genomförandet, se [MongoDB anslutning](connector-mongodb.md) artikel om detaljer. Den här äldre MongoDB-anslutningen stöds som är för bakåtkompensering, medan du använder den nya anslutningen för alla nya arbetsbelastningar.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB-databasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Specifikt stöder den här MongoDB-kontakten:

- MongoDB **versioner 2.4, 2.6, 3.0, 3.2, 3.4 och 3.6**.
- Kopiera data med **grundläggande** eller **anonym** autentisering.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime tillhandahåller en inbyggd MongoDB-drivrutin, därför behöver du inte installera någon drivrutin manuellt när du kopierar data från MongoDB.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för MongoDB-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för MongoDB-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste ställas in på: **MongoDb** |Ja |
| server |IP-adress eller värdnamn för MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klientanslutningar. |Nej (standard är 27017) |
| Databasename |Namn på den MongoDB-databas som du vill komma åt. |Ja |
| authenticationType | Typ av autentisering som används för att ansluta till MongoDB-databasen.<br/>Tillåtna värden är: **Grundläggande**och **Anonym**. |Ja |
| användarnamn |Användarkonto för att komma åt MongoDB. |Ja (om grundläggande autentisering används). |
| password |Lösenordet för användaren. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (om grundläggande autentisering används). |
| authSource |Namnet på den MongoDB-databas som du vill använda för att kontrollera dina autentiseringsuppgifter. |Nej. För grundläggande autentisering är standard att använda administratörskontot och databasen som anges med hjälp av egenskapen databaseName. |
| enableSsl enableSsl enableSsl enableS | Anger om anslutningarna till servern är krypterade med SSL. Standardvärdet är false.  | Inga |
| tillåtVälsignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **MongoDbCollection** | Ja |
| collectionName (samlingsnamn) |Namn på samlingen i MongoDB-databasen. |Ja |

**Exempel:**

```json
{
    "name": "MongoDbDataset",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källan.

### <a name="mongodb-as-source"></a>MongoDB som källa

Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopians aktivitet måste anges till: **MongoDbSource** | Ja |
| DocumentDB |Använd den anpassade SQL-92-frågan för att läsa data. Till exempel: välj * från MyTable. |Nej (om "collectionName" i datauppsättningen har angetts) |

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
> Var uppmärksam på DateTime-formatet när du anger SQL-frågan. Till exempel: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` eller att använda parameter`SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema efter datafabrik

Azure Data Factory-tjänsten härleder schema från en MongoDB-samling med hjälp av de **senaste 100 dokumenten** i samlingen. Om dessa 100 dokument inte innehåller ett fullständigt schema kan vissa kolumner ignoreras under kopieringen.

## <a name="data-type-mapping-for-mongodb"></a>Mappning av datatyp för MongoDB

Vid kopiering av data från MongoDB används följande mappningar från MongoDB-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| Datatyp för MongoDB | Data fabrik interim datatyp |
|:--- |:--- |
| Binär |Byte[] |
| Boolean |Boolean |
| Datum |DateTime |
| AntalDouble |Double |
| NumberInt (olika) |Int32 |
| AntalLångt |Int64 |
| ObjectID |String |
| String |String |
| Uuid |GUID |
| Objekt |Renormalized till platta kolumner med "_" som kapslad avgränsare |

> [!NOTE]
> Mer information om stöd för matriser med hjälp av virtuella tabeller finns i Stöd för komplexa typer med hjälp av avsnittet [virtuella tabeller.](#support-for-complex-types-using-virtual-tables)
>
> För närvarande stöds inte följande MongoDB-datatyper: DBPointer, JavaScript, Max/Min-tangenten, Reguljärt uttryck, Symbol, Tidsstämpel, Odefinierad.

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer med hjälp av virtuella tabeller

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från din MongoDB-databas. För komplexa typer, till exempel matriser eller objekt med olika typer i dokumenten, normaliserar drivrutinen data till motsvarande virtuella tabeller. Om en tabell innehåller sådana kolumner genererar drivrutinen följande virtuella tabeller:

* En **bastabell**som innehåller samma data som den verkliga tabellen förutom de komplexa typkolumnerna. Bastabellen använder samma namn som den verkliga tabellen som den representerar.
* En **virtuell tabell** för varje komplex typkolumn, som expanderar de kapslade data. De virtuella tabellerna namnges med namnet på den verkliga tabellen, en avgränsare "_" och namnet på matrisen eller objektet.

Virtuella tabeller refererar till data i den verkliga tabellen, vilket gör att drivrutinen kan komma åt denormaliserade data. Du kan komma åt innehållet i MongoDB-matriser genom att fråga och ansluta till de virtuella tabellerna.

### <a name="example"></a>Exempel

Exempeltabell här är till exempel en MongoDB-tabell som har en kolumn med en matris med objekt i varje cell – Fakturor och en kolumn med en matris med skaltyper – Klassificeringar.

| _id | Kundens namn | Fakturor | Servicenivå | Klassificeringar |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", artikel:"brödrost", pris:"456", rabatt:"0.2"}, {invoice_id:"124", artikel:"ugn", pris: "1235", rabatt: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", artikel:"kylskåp", pris: "12543", rabatt: "0.0"}] |Guld |[1,2] |

Drivrutinen skulle generera flera virtuella tabeller för att representera den här enstaka tabellen. Den första virtuella tabellen är bastabellen med namnet "ExampleTable", som visas i exemplet. Bastabellen innehåller alla data i den ursprungliga tabellen, men data från matriserna har utelämnats och expanderats i de virtuella tabellerna.

| _id | Kundens namn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

I följande tabeller visas de virtuella tabeller som representerar de ursprungliga matriserna i exemplet. Dessa tabeller innehåller följande:

* En referens tillbaka till den ursprungliga primärnyckelkolumnen som motsvarar raden i den ursprungliga matrisen (via _id kolumnen)
* En indikation på datapositionen i den ursprungliga matrisen
* De expanderade data för varje element i matrisen

**Tabell "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | objekt | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Brödrost |456 |0.2 |
| 1111 |1 |124 |Ugn |1235 |0.2 |
| 2222 |0 |135 |Kylskåp |12543 |0.0 |

**Tabell "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
