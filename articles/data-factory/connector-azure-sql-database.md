---
title: "Kopiera data till/från Azure SQL Database med Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från stöds källa datalager till Azure SQL Database (eller) från SQL-databas stöds sink datalager med hjälp av Data Factory."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: b9582ccab1e0c580fe09de39e55ea0660c3866d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-azure-sql-connector.md)
> * [Version 2 – förhandsversion](connector-azure-sql-database.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till en Azure SQL Database. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Azure SQL Database-anslutningen i V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från/till Azure SQL Database till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till Azure SQL Database. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure SQL Database-anslutningen:

- Kopierar data med hjälp av SQL-autentisering.
- Hämtar data med hjälp av SQL-fråga eller en lagrad procedur som källa.
- Som mottagare, bifoga data måltabellen eller anropa en lagrad procedur med egen kod vid kopiering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Azure SQL Database-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Azure SQL Database länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureSqlDatabase** | Ja |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för egenskapen connectionString. Endast grundläggande autentisering stöds. Markera det här fältet som en SecureString. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) i databasservern [ge Azure-tjänster åtkomst till servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Dessutom, om du kopierar data till Azure SQL Database från utanför Azure från lokala datakällor med data factory inklusive själva värdbaserade Integration Runtime konfigurera lämplig IP-adressintervall för den dator som skickar data till Azure SQL Databas.

**Exempel:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database dataset.

Ange typegenskapen för dataset för att kopiera data från/till Azure SQL Database, **AzureSqlTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **AzureSqlTable** | Ja |
| tableName |Namnet på den tabell eller vy i Azure SQL Database-instans som den länkade tjänsten refererar till. | Ja |

**Exempel:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database källa och mottagare.

### <a name="azure-sql-database-as-source"></a>Azure SQL Database som källa

Om du vill kopiera data från Azure SQL Database, anger du källa i kopieringsaktiviteten till **SqlSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SqlSource** | Ja |
| sqlReaderQuery |Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

**Pekar på Observera:**

- Om den **sqlReaderQuery** har angetts för SqlSource, kopiera-aktivitet körs den här frågan mot Azure SQL Database-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).
- Om du inte anger ”sqlReaderQuery” eller ”sqlReaderStoredProcedureName” kolumner som har definierats i avsnittet ”struktur” i datauppsättningen JSON används för att skapa en fråga (`select column1, column2 from mytable`) att köra mot Azure SQL-databasen. Om datauppsättningsdefinitionen inte har ”struktur”, markeras alla kolumner från tabellen.
- När du använder **sqlReaderStoredProcedureName**, du måste ange en **tableName** egenskap i datauppsättningen JSON.

**Exempel: använda SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel: använda lagrad procedur**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Den lagrade proceduren definitionen:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-sink"></a>Azure SQL Database som mottagare

Om du vill kopiera data till Azure SQL Database, anger du sink i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SqlSink** | Ja |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antalet rader). |Nej (standard är 10000) |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: timespan. Exempel ”: 00: 30:00” (30 minuter). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren upserts (uppdateringar/infogar) data i måltabellen. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabell-typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en temporär tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. |Nej |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan skrivning av data till Azure SQL Database i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |

> [!TIP]
> När du kopierar data till Azure SQL Database lägger kopieringsaktiviteten data till tabellen mottagare som standard. Använd den lagrade proceduren för att utföra en UPSERT eller ytterligare affärslogik i SqlSink. Lär dig mer information från [anropar lagrade proceduren för SQL Sink](#invoking-stored-procedure-for-sql-sink).

**Exempel 1: lägga till data**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exempel 2: anropar en lagrad procedur vid kopiering för upsert**

Lär dig mer information från [anropar lagrade proceduren för SQL Sink](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen

Det här avsnittet innehåller ett exempel för att kopiera data från en källtabell utan en identity-kolumn till en måltabell med en identitetskolumn.

**Källtabellen:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Måltabellen:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observera att måltabellen har en identitetskolumn.

**Källa JSON-definitionen för datauppsättning**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Mål JSON-definitionen för datauppsättning**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Observera att som käll- och tabellen har olika schema (målet har en ytterligare kolumn med identity). I det här scenariot måste du ange **struktur** egenskap i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrade procedur från SQL-mottagare

När du kopierar data till Azure SQL Database, en angiven användare lagras kan proceduren konfigureras och anropas med ytterligare parametrar.

En lagrad procedur kan användas när inbyggda kopiera mekanismer inte fyller syftet. Den används vanligtvis när upsert (insert + uppdatering) eller extra bearbetning (sammanslagning kolumner, söka efter ytterligare värden infogning i flera tabeller, etc.) måste göras innan sista infogningen av källdata i måltabellen.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i Azure SQL-databasen. Under förutsättning att indata och tabellen ”marknadsföringsmaterial” mottagare har tre kolumner: profil-ID, tillstånd och kategori. Utföra upsert baserat på kolumnen ”ProfileID” och gäller endast för en specifik kategori.

**Datamängd för utdata**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definiera avsnittet ”SqlSink” i en Kopieringsaktivitet enligt följande.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Definiera den lagrade proceduren med samma namn som ”SqlWriterStoredProcedureName” i din databas. Den hanterar indata från angiven källa och kopplad till utdatatabellen. Observera att parameternamnet på den lagrade proceduren bör vara detsamma som ”tabellnamn”-definieras i dataset.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Definiera tabelltyp med samma namn som sqlWriterTableType i din databas. Observera att schemat för tabelltypen ska vara densamma som schemat som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Den lagrade proceduren funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Datatypsmappningen för Azure SQL Database

När du kopierar data från/till Azure SQL Database, används följande mappningar från Azure SQL Database-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Azure SQL Database-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| bigint |Int64 |
| Binär |byte] |
| bitar |Booleskt värde |
| Char |Sträng, Char] |
| Datum |Datum och tid |
| Datum och tid |Datum och tid |
| datetime2 |Datum och tid |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attributet (varbinary(max)) |byte] |
| flyttal |dubbla |
| Bild |byte] |
| int |Int32 |
| Money |Decimal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |Decimal |
| nvarchar |Sträng, Char] |
| Verklig |Enskild |
| ROWVERSION |byte] |
| smalldatetime |Datum och tid |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objektet * |
| Text |Sträng, Char] |
| time |TimeSpan |
| tidsstämpel |byte] |
| tinyint |Mottagna byte |
| Unik identifierare |GUID |
| varbinary |byte] |
| varchar |Sträng, Char] |
| xml |XML |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).