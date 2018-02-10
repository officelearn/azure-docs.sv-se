---
title: "Kopiera data till/från Azure SQL Data Warehouse med hjälp av Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från stöds källa lagrar till Azure SQL Data Warehouse (eller) från SQL Data Warehouse stöds sink lagrar med hjälp av Data Factory."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: dc11ac2ce92fe2b7d3cb51bf60c6b4bd9a5be18d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 – förhandsversion](connector-azure-sql-data-warehouse.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till och från ett Azure SQL Data Warehouse. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Azure SQL Data Warehouse-kopplingen i V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från/till Azure SQL Data Warehouse till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till Azure SQL Data Warehouse. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure SQL Data Warehouse-anslutningen:

- Kopierar data med hjälp av SQL-autentisering.
- Hämtar data med hjälp av SQL-fråga eller en lagrad procedur som källa.
- Som mottagare som läser in data med hjälp av **PolyBase** eller bulk insert. Gamla är **rekommenderas** för bättre prestanda för kopia.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Azure SQL Data Warehouse-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Azure SQL Data Warehouse länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureSqlDW** | Ja |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för egenskapen connectionString. Endast grundläggande autentisering stöds. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |


> [!IMPORTANT]
> Konfigurera [Azure SQL Data Warehouse brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) och databasservern till [ge Azure-tjänster åtkomst till servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Dessutom om du vill kopiera data till Azure SQL Data Warehouse från utanför Azure inklusive från lokala datakällor med Self-hosted integrering Runtime, konfigurera rätt IP-adressintervall för den dator som skickar data till Azure SQL Data Datalager.

**Exempel:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse dataset.

Om du vill kopiera data från/till Azure SQL Data Warehouse, anger du typegenskapen för datamängden som **AzureSqlDWTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **AzureSqlDWTable** | Ja |
| tableName |Namnet på den tabell eller vy i Azure SQL Data Warehouse-instans som den länkade tjänsten refererar till. | Ja |

**Exempel:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse källa och mottagare.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse som källa

Om du vill kopiera data från Azure SQL Data Warehouse, anger du datakällan i kopieringsaktiviteten för **SqlDWSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SqlDWSource** | Ja |
| sqlReaderQuery |Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

**Pekar på Observera:**

- Om den **sqlReaderQuery** har angetts för SqlSource, kopiera-aktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).
- Om du inte anger ”sqlReaderQuery” eller ”sqlReaderStoredProcedureName” kolumner som har definierats i avsnittet ”struktur” i datauppsättningen JSON används för att skapa en fråga (`select column1, column2 from mytable`) att köra mot Azure SQL Data Warehouse. Om datauppsättningsdefinitionen inte har ”struktur”, markeras alla kolumner från tabellen.
- När du använder **sqlReaderStoredProcedureName**, du måste ange en **tableName** egenskap i datauppsättningen JSON.

**Exempel: använda SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse som mottagare

Om du vill kopiera data till Azure SQL Data Warehouse, anger du sink i kopieringsaktiviteten till **SqlDWSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **SqlDWSink** | Ja |
| allowPolyBase |Anger om du vill använda PolyBase (i förekommande fall) i stället för BULKINSERT mekanism. <br/><br/> **Med PolyBase är det rekommenderade sättet att läsa in data till SQL Data Warehouse.** Se [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet begränsningar och information.<br/><br/>Tillåtna värden är: **SANT** (standard), och **FALSKT**.  |Nej |
| polyBaseSettings |En grupp egenskaper som kan anges när den **allowPolybase** egenskap är inställd på **SANT**. |Nej |
| rejectValue |Anger det antal eller procentandelen rader som kan avvisas innan frågan misslyckas.<br/><br/>Mer information om den PolyBase avvisa alternativ i den **argument** avsnitt i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) avsnittet. <br/><br/>Tillåtna värden är: 0 (standard), 1, 2,... |Nej |
| rejectType |Anger om alternativet rejectValue har angetts som ett litteralvärde eller ett procentvärde.<br/><br/>Tillåtna värden är: **värdet** (standard), och **procentandel**. |Nej |
| rejectSampleValue |Anger antalet rader som ska hämtas innan PolyBase beräknar andelen Avvisade rader.<br/><br/>Tillåtna värden är: 1, 2,... |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du hanterar saknade värden i avgränsade textfiler när PolyBase hämtar data från filen.<br/><br/>Mer information om denna egenskap från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är: **SANT**, **FALSKT** (standard). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. Gäller endast när PolyBase inte används.<br/><br/>Tillåtna värden är: heltal (antalet rader). |Nej (standard är 10000) |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. Gäller endast när PolyBase inte används.<br/><br/>Tillåtna värden är: timespan. Exempel ”: 00: 30:00” (30 minuter). |Nej |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan skrivning av data till Azure SQL Data Warehouse i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |(#repeatability-under-kopia). |En frågesats. |Nej |

**Exempel:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Mer information om hur du använder PolyBase för att läsa in i SQL Data Warehouse effektivt från nästa avsnitt.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data till Azure SQL Data Warehouse

Med hjälp av  **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)**  är ett effektivt sätt för att läsa in stora mängder data till Azure SQL Data Warehouse med hög genomströmning. Du kan se en stor vinst i genomflödet med PolyBase i stället för BULKINSERT standardmekanism. Se [kopiera prestanda referensnummer](copy-activity-performance.md#performance-reference) med detaljerad jämförelse. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](connector-azure-sql-data-warehouse.md).

* Om datakällan finns i **Azure Blob- eller Azure Data Lake Store**, och formatet som är kompatibelt med PolyBase, du kan kopiera direkt till Azure SQL Data Warehouse med PolyBase. Se  **[direkt kopia med PolyBase](#direct-copy-using-polybase)**  med information.
* Om din käll-datalagret och format inte stöds ursprungligen av PolyBase, kan du använda den  **[mellanlagrad kopia med PolyBase](#staged-copy-using-polybase)**  funktion i stället. Det ger dig bättre genomströmning genom att automatiskt konvertera data till PolyBase-kompatibelt format och lagra data i Azure Blob storage. Data hämtas sedan till SQL Data Warehouse.

### <a name="direct-copy-using-polybase"></a>Direkt kopia med PolyBase

SQL Data Warehouse PolyBase stöd direkt för Azure Blob och Azure Data Lake Store (med tjänstens huvudnamn) som källa och krav för fil-format. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet, kopiera du direkt från datalagret för källan till Azure SQL Data Warehouse med PolyBase. Annars kan du använda [mellanlagrad kopia med PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Om du vill kopiera data från Data Lake Store till SQL Data Warehouse effektivt, mer information från [Azure Data Factory gör det även enklare och praktiskt att få insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, kontrollerar du inställningarna för Azure Data Factory och faller automatiskt tillbaka till BULKINSERT mekanism för flytt av data.

1. **Källan länkade tjänsten** är av typen: **AzureStorage** eller **AzureDataLakeStore**.
2. Den **inkommande dataset** är av typen: **AzureBlob** eller **AzureDataLakeStoreFile**, och skriv `type` egenskaper är **OrcFormat** , **ParquetFormat**, eller **TextFormat** med följande konfigurationer:

   1. `rowDelimiter`måste vara  **\n** .
   2. `nullValue`anges till **tom sträng** (””), eller `treatEmptyAsNull` är inställd på **SANT**.
   3. `encodingName`anges till **utf-8**, vilket är **standard** värde.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, och `skipLineCount` har inte angetts.
   5. `compression`kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Det finns inga `skipHeaderLineCount` inställningen **BlobSource** eller **AzureDataLakeStore** för aktiviteten kopiera i pipelinen.
4. Det finns inga `sliceIdentifierColumnName` inställningen **SqlDWSink** för aktiviteten kopiera i pipelinen. (PolyBase garanterar att alla data har uppdaterats eller ingenting uppdateras i en enda körning. Att uppnå **repeterbarhet**, du kan använda `sqlWriterCleanupScript`).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>Stegvis kopia med PolyBase

När källdata inte uppfyller de kriterier som introducerades i föregående avsnitt, kan du Aktivera kopiering av data via en mellanliggande fristående Azure Blob Storage (inte får vara Premium-lagring). I det här fallet utför Azure Data Factory automatiskt transformationer data som ska uppfylla kraven för data-format i PolyBase, och sedan använda PolyBase för att läsa in data till SQL Data Warehouse och sedan rensa temp data från Blob storage. Se [mellanlagrad kopiera](copy-activity-performance.md#staged-copy) för information om hur kopiering av data via en fristående Azure-Blob fungerar i allmänhet.

Om du vill använda den här funktionen kan du skapa en [Azure länkade lagringstjänsten](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure Storage-konto som har tillfälligt blob-lagring, ange den `enableStaging` och `stagingSettings` egenskaper för aktiviteten kopiera enligt följande kod:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Rekommenderade metoder när du använder PolyBase

Följande avsnitt innehåller ytterligare bästa praxis för de som nämns i [Metodtips för Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Behörighet krävs

Om du vill använda PolyBase kräver att användaren som används för att läsa in data till SQL Data Warehouse har den [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) på måldatabasen. Ett sätt att uppnå som är att lägga till användaren som en medlem i ”db_owner”. Lär dig hur du gör det genom att följa [i det här avsnittet](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ange begränsning Radstorleken och data

Polybase belastningar är begränsade till inläsning rader både mindre än **1 MB** och det går inte att läsa in VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Referera till [här](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Du kanske vill dela källtabellerna lodrätt i flera små där den största Radstorleken på var och en av dem inte överskrider gränsen i källdata med rader av större än 1 MB. Mindre tabeller kan sedan laddas med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklassen

Överväg att för att tilldela användaren som används för att läsa in data till SQL Data Warehouse via PolyBase större resursklassen för att uppnå bästa möjliga genomflöde. Lär dig hur du gör det genom att följa [ändra ett exempel på användaren resurs klassen](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Tabellnamn i Azure SQL Data Warehouse

Följande tabell innehåller exempel på hur du anger den **tableName** egenskap i dataset JSON för olika kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamn | tableName JSON-egenskapen |
| --- | --- | --- |
| dbo |Mytable prefix |Mytable prefix eller dbo. Mytable prefix eller [dbo]. [MyTable] |
| dbo1 |Mytable prefix |dbo1. Mytable prefix eller [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] eller [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Om du ser följande fel kan bero det på ett problem med värdet som angetts för egenskapen tableName. Se tabellen på rätt sätt att ange värden för egenskapen tableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden

PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Att du har en tabell med fyra kolumner och en av dem har definierats med ett standardvärde. Indata måste fortfarande innehålla fyra kolumner. Att tillhandahålla en inkommande dataset 3 kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värde är en speciell typ av standardvärdet. Om kolumnen är nullbar indata (i blob) för kolumnen vara tom (kan inte vara saknas i indata DataSet). PolyBase infogas NULL för dem i Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datatypsmappningen för Azure SQL Data Warehouse

När du kopierar data från/till Azure SQL Data Warehouse, används följande mappningar från Azure SQL Data Warehouse-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Azure SQL Data Warehouse-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| bigint |Int64 |
| Binär |Byte[] |
| bitar |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attributet (varbinary(max)) |Byte[] |
| flyttal |Dubbel |
| Bild |Byte[] |
| int |Int32 |
| Money |Decimal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |Decimal |
| nvarchar |Sträng, Char] |
| Verklig |Ogift |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objektet * |
| Text |Sträng, Char] |
| time |TimeSpan |
| tidsstämpel |Byte[] |
| tinyint |Mottagna byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).