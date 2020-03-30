---
title: Kopiera data till och från SQL Server
description: Lär dig mer om hur du flyttar data till och från SQL Server-databas som är lokalt eller i en Virtuell Azure-dator med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: e7f91d60a0e181690859da64e491c4c0fa1e1a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238722"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiera data till och från SQL Server med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuell version](connector-sql-server.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från och till en SQL Server-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SQL Server-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Du kan kopiera data från en SQL Server-databas till alla sink-datalager som stöds. Du kan också kopiera data från alla källdatalager som stöds till en SQL Server-databas. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här SQL Server-anslutningen stöder:

- SQL Server-versionerna 2016, 2014, 2012, 2008 R2, 2008 och 2005.
- Kopiera data med SQL- eller Windows-autentisering.
- Som källa hämtar du data med hjälp av en SQL-fråga eller en lagrad procedur.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik under kopiering.

[Sql Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) stöds inte.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) stöds inte av den här anslutningen nu. Du kan arbeta runt genom att använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) med konfigurationer för hämtning av ODBC-drivrutiner och anslutningssträngar.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för SQL Server-databasanslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den SQL Server-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **SqlServer**. | Ja |
| Connectionstring |Ange **connectionString-information** som behövs för att ansluta till SQL Server-databasen med hjälp av antingen SQL-autentisering eller Windows-autentisering. Se följande exempel.<br/>Du kan också placera ett lösenord i Azure Key Vault. Om det är SQL-autentisering drar du ut konfigurationen `password` ur anslutningssträngen. Mer information finns i JSON-exemplet som följer tabellen och [Store-autentiseringsuppgifterna i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Ange ett användarnamn om du använder Windows-autentisering. Ett exempel är **\\domännamnsanvändarnamn**. |Inga |
| password |Ange ett lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som **SecureString** för att lagra det säkert i Azure Data Factory. Du kan också [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Inga |
| connectVia (på) | Den här [integrationskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om inget anges används standardkörningen för Azure-integrering. |Inga |

>[!TIP]
>Om du har träffat ett fel med felkoden "UserErrorFailedToConnectToSqlServer" och ett meddelande som "Sessionsgränsen för databasen är XXX och har nåtts", lägger du `Pooling=false` till anslutningssträngen och försöker igen.

**Exempel 1: Använda SQL-autentisering**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: Använda SQL-autentisering med ett lösenord i Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 3: Använda Windows-autentisering**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server-datauppsättningen.

Så här kopierar du data från och till en SQL Server-databas:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **SqlServerTable**. | Ja |
| Schemat | Namnet på schemat. |Nej för källa, Ja för diskho  |
| tabell | Namn på tabellen/vyn. |Nej för källa, Ja för diskho  |
| tableName | Namn på tabellen/vyn med schema. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table`. | Nej för källa, Ja för diskho |

**Exempel**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server-källan och diskhon.

### <a name="sql-server-as-a-source"></a>SQL Server som källa

Om du vill kopiera data från SQL Server anger du källtypen i kopieringsaktiviteten till **SqlSource**. Följande egenskaper stöds i källavsnittet för kopieringsaktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **SqlSource**. | Ja |
| sqlReaderQuery |Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade proceduren som läser data från källtabellen. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. |Inga |
| storedProcedureParameters |Dessa parametrar är för den lagrade proceduren.<br/>Tillåtna värden är namn- eller värdepar. Parametrarnas namn och hölje skall stämma överens med namn och hölje för parametrarna för den lagrade proceduren. |Inga |
| isoleringNivå | Anger transaktionslåsningsbeteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted** (standard), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Se [det här dokumentet](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) för mer information. | Inga |

**Punkter att notera:**

- Om **sqlReaderQuery** har angetts för **SqlSource**körs den här frågan mot SQL Server-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger **antingen sqlReaderQuery** eller **sqlReaderStoredProcedureName**används kolumnerna som definieras i avsnittet "struktur" i datauppsättningen JSON för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot SQL Server. Om datauppsättningsdefinitionen inte har "struktur" markeras alla kolumner i tabellen.

**Exempel: Använda SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

**Exempel: Använda en lagrad procedur**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

**Den lagrade procedurens definition**

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

### <a name="sql-server-as-a-sink"></a>SQL Server som en diskho

> [!TIP]
> Läs mer om skrivbeteenden, konfigurationer och metodtips som stöds från [Bästa praxis för att läsa in data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Om du vill kopiera data till SQL Server anger du sink-typen i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet kopiera aktivitetsmottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **SqlSink**. | Ja |
| skriverBatchSize |Antal rader som ska infogas i SQL-tabellen *per batch*.<br/>Tillåtna värden är heltal för antalet rader. Som standard bestämmer Azure Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken. |Inga |
| skriverBatchTimeout |Den här egenskapen anger väntetiden för batchinfogningen för att slutföras innan den time out.<br/>Tillåtna värden är för tidsspannet. Ett exempel är "00:30:00" i 30 minuter. Om inget värde anges är tidsgränsen som standard "02:00:00". |Inga |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopieringsaktiviteten som ska köras innan data skrivs till SQL Server. Det anropas bara en gång per kopieringskörning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en måltabell. <br/>Den här *lagrade*proceduren anropas per batch . För åtgärder som körs bara en gång och inte har något att göra `preCopyScript` med källdata, till exempel ta bort eller trunkera, använder du egenskapen. | Inga |
| storedProcedureTableTypeParameterName |Parameternamnet för den tabelltyp som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Tabelltypens namn som ska användas i den lagrade proceduren. Kopieringsaktiviteten gör de data som flyttas tillgängliga i en temporär tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- och värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. | Inga |
| tabellOption | Anger om sink-tabellen ska skapas automatiskt om det inte finns baserat på källschemat. Skapandet av automatisk tabell stöds inte när diskho anger lagrad procedur eller stegvis kopia konfigureras i kopieringsaktivitet. Tillåtna värden `none` är: `autoCreate`(standard), . |Inga |

**Exempel 1: Lägga till data**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exempel 2: Anropa en lagrad procedur under kopiering**

Läs mer från [Anropa en lagrad procedur från en SQL-diskho](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
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
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Bästa praxis för att läsa in data i SQL Server

När du kopierar data till SQL Server kan du behöva olika skrivbeteenden:

- [Lägg till](#append-data): Källdata har bara nya poster.
- [Upsert](#upsert-data): Källdata har både infogningar och uppdateringar.
- [Skriv över:](#overwrite-the-entire-table)Jag vill ladda om hela dimensionstabellen varje gång.
- [Skriv med anpassad logik:](#write-data-with-custom-logic)Jag behöver extra bearbetning innan den slutliga insättningen i måltabellen.

Se respektive avsnitt för hur du konfigurerar i Azure Data Factory och metodtips.

### <a name="append-data"></a>Lägga till data

Lägga till data är standardbeteendet för den här SQL Server-sink-anslutningen. Azure Data Factory gör en massinfogning för att skriva till din tabell effektivt. Du kan konfigurera källan och handfatet i kopian.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data att kopiera använder du följande metod för att göra en upsert: 

- Använd först en [tillfällig tabell](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) för att massladda alla poster med hjälp av kopieringsaktiviteten. Eftersom åtgärder mot temporära tabeller inte loggas kan du läsa in miljontals poster på några sekunder.
- Kör en lagrad proceduraktivitet i Azure Data Factory för att tillämpa en [MERGE-](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) eller INSERT/UPDATE-sats. Använd temp-tabellen som källa för att utföra alla uppdateringar eller infogningar som en enda transaktion. På så sätt minskar antalet tur- och retur- och loggoperationer. I slutet av den lagrade proceduraktiviteten kan temporära tabellen trunkeras för att vara redo för nästa upsert-cykel.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopiera aktivitet** kedjad med en aktivitet **för lagrad procedur**. Den förstnämnda kopior data från källarkivet till en databas tillfällig tabell, till exempel **##UpsertTempTable**, som tabellnamn i datauppsättningen. Sedan anropar den senare en lagrad procedur för att sammanfoga källdata från temporärtabellen till måltabellen och rensa temporärtabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med MERGE-logik, till exempel följande exempel, som pekas på från föregående lagrad proceduraktivitet. Anta att målet är tabellen **Marknadsföring** med tre kolumner: **ProfileID**, **State**och **Category**. Gör upsert baserat på **profileid-kolumnen.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i kopieringsaktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden körs varje rad i källtabellen i stället för att använda massinfogning som standardmetod i kopieringsaktiviteten, vilket inte är lämpligt för storskalig upsert.

### <a name="overwrite-the-entire-table"></a>Skriva över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i en kopia aktivitetsmottagare. I det här fallet, för varje kopieringsaktivitet som körs, azure data factory kör skriptet först. Sedan körs kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med de senaste data anger du ett skript för att först ta bort alla poster innan du massinläsning av nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar dem som beskrivs i avsnittet [Upsert-data.](#upsert-data) När du behöver använda extra bearbetning innan den slutliga insättningen av källdata i måltabellen, för stor skala, kan du göra en av två saker: 

- Läs in till en tillfällig tabell och anropa sedan en lagrad procedur. 
- Anropa en lagrad procedur under kopieringen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Anropa en lagrad procedur från en SQL-diskho

När du kopierar data till en SQL Server-databas kan du också konfigurera och anropa en användarspecificerad lagrad procedur med ytterligare parametrar. Funktionen lagrad procedur utnyttjar [tabellvärdade parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> När du anropar en lagrad procedur bearbetas dataraden för rad i stället för med hjälp av en massåtgärd, som vi inte rekommenderar för storskalig kopia. Läs mer från [Bästa praxis för att läsa in data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Du kan använda en lagrad procedur när inbyggda kopieringsmekanismer inte tjänar syftet. Ett exempel är när du vill använda extra bearbetning innan den slutliga insättningen av källdata i måltabellen. Några extra bearbetningsexempel är när du vill sammanfoga kolumner, slå upp ytterligare värden och infoga data i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i SQL Server-databasen. Anta att indata och tabellen **för användningsrukonamnning** har tre kolumner vardera: **ProfileID**, **State**och **Category**. Gör upsert baserat på **ProfileID-kolumnen** och tillämpa den endast för en viss kategori som kallas "ProductA".

1. I databasen definierar du tabelltypen med samma namn som **sqlWriterTableType**. Tabelltypens schema är detsamma som schemat som returneras av indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från den angivna källan och sammanfogas till utdatatabellen. Parameternamnet för tabelltypen i den lagrade proceduren är detsamma som **tabellnamn** som definierats i datauppsättningen.

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
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. I Azure Data Factory definierar du **avsnittet SQL sink** i kopieringsaktiviteten enligt följande:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mappning av datatyp för SQL Server

När du kopierar data från och till SQL Server används följande mappningar från SQL Server-datatyper till Azure Data Factory interimsdatatyper. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| SQL Server-datatyp | Interimdatatyp för Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |Sträng, Röding[] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| Datumtidsdatum |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary(max)) |Byte[] |
| Float (Flyttal) |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, Röding[] |
| ntext |Sträng, Röding[] |
| numeric |Decimal |
| nvarchar |Sträng, Röding[] |
| real |Enkel |
| Rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 (int16) |
| smallmoney |Decimal |
| Sql_variant |Objekt |
| text |Sträng, Röding[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 (int16) |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Röding[] |
| xml |Xml |

>[!NOTE]
> För datatyper som mappas till decimal interimstypen stöder Azure Data Factory precision upp till 28. Om du har data som kräver precision som är större än 28 kan du överväga att konvertera till en sträng i en SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Aktivitetsegenskaper för GetMetadata

Om du vill veta mer om egenskaperna kontrollerar du [GetMetadata-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Felsöka anslutningsproblem

1. Konfigurera SQL Server-instansen så att fjärranslutningar accepteras. Starta **SQL Server Management Studio**, högerklicka på **servern**och välj **Egenskaper**. Markera **Anslutningar** i listan och markera kryssrutan **Tillåt fjärranslutningar till den här servern.**

    ![Aktivera fjärranslutningar](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Detaljerade steg finns i [Konfigurera konfigurationsalternativet för fjärråtkomstservern](https://msdn.microsoft.com/library/ms191464.aspx).

2. Starta **SQL Server Configuration Manager**. Expandera **SQL Server-nätverkskonfigurationen** för den instans du vill använda och välj **Protokoll för MSSQLSERVER**. Protokoll visas i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och välja **Aktivera**.

    ![Aktivera TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Mer information och alternativa sätt att aktivera TCP/IP-protokoll finns i [Aktivera eller inaktivera ett servernätverksprotokoll](https://msdn.microsoft.com/library/ms191294.aspx).

3. I samma fönster dubbelklickar du på **TCP/IP** för att starta fönstret **TCP/IP-egenskaper.**
4. Växla till fliken **IP-adresser.** **IPAll** Skriv ner **TCP-porten**. Standardvärdet är **1433**.
5. Skapa en **regel för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten. 
6. **Verifiera anslutning:** Om du vill ansluta till SQL Server med ett fullständigt kvalificerat namn använder du SQL Server Management Studio från en annan dator. Ett exempel är `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [i Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
