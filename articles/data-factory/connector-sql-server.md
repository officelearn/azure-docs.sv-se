---
title: Kopiera data till och från SQL Server
description: Lär dig mer om hur du flyttar data till och från SQL Server databas som finns lokalt eller i en virtuell Azure-dator med hjälp av Azure Data Factory.
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
ms.date: 10/24/2019
ms.openlocfilehash: 9c9064778f29e9f53f48d8be2f127fe51e936af4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444223"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiera data till och från SQL Server med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuell version](connector-sql-server.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till en SQL Server-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SQL Server anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Du kan kopiera data från en SQL Server-databas till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till en SQL Server databas. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SQL Server-anslutningen:

- SQL Server version 2016, 2014, 2012, 2008 R2, 2008 och 2005.
- Kopiera data med SQL eller Windows-autentisering.
- Som källa hämtar data med hjälp av en SQL-fråga eller en lagrad procedur.
- Som mottagare lägger du till data i en mål tabell eller anropar en lagrad procedur med anpassad logik under kopieringen.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) stöds inte.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) stöds inte av den här anslutningen nu. För att lösa problemet kan du använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) för hämtning av ODBC-drivrutiner och konfiguration av anslutnings strängar.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom i gång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SQL Server Database Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten SQL Server:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **SQLServer**. | Ja |
| connectionString |Ange **ConnectionString** -information som behövs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. Se följande exempel.<br/>Du kan också ange ett lösen ord i Azure Key Vault. Om det är SQL-autentisering, hämtar du `password`-konfigurationen från anslutnings strängen. Mer information finns i JSON-exemplet som följer tabellen och [lagrar autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Ange ett användar namn om du använder Windows-autentisering. Ett exempel är **domainname\\username**. |Inga |
| password |Ange ett lösen ord för det användar konto som du har angett som användar namn. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Azure Data Factory. Eller så kan du [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). |Inga |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) används för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure integration Runtime. |Inga |

>[!TIP]
>Om du träffar ett fel med felkoden "UserErrorFailedToConnectToSqlServer" och ett meddelande som "databasens sessionsgräns är XXX och har nåtts," Lägg till `Pooling=false` i anslutnings strängen och försök igen.

**Exempel 1: Använd SQL-autentisering**

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

**Exempel 2: Använd SQL-autentisering med ett lösen ord i Azure Key Vault**

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

**Exempel 3: Använd Windows-autentisering**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server data uppsättningen.

Följande egenskaper stöds för att kopiera data från och till en SQL Server databas:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **SqlServerTable**. | Ja |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| table | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table`för nya arbets belastningar. | Nej för källa, Ja för mottagare |

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

En fullständig lista över avsnitt och egenskaper som kan användas för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server källa och mottagare.

### <a name="sql-server-as-a-source"></a>SQL Server som källa

Om du vill kopiera data från SQL Server anger du käll typen i kopierings aktiviteten till **SqlSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets Källa:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **SqlSource**. | Ja |
| sqlReaderQuery |Använda anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade procedur som läser data från käll tabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Inga |
| storedProcedureParameters |De här parametrarna är för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

**Poäng till Anmärkning:**

- Om **sqlReaderQuery** har angetts för **SqlSource**kör kopierings aktiviteten den här frågan mot SQL Server källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger någon av **sqlReaderQuery** eller **sqlReaderStoredProcedureName**används kolumnerna som definierats i avsnittet "struktur" i JSON-datauppsättnings-JSON för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot SQL Server. Om definitionen för data uppsättningen inte har "struktur" är alla kolumner markerade från tabellen.

**Exempel: Använd SQL-fråga**

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

**Exempel: använda en lagrad procedur**

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

**Definitionen av den lagrade proceduren**

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

### <a name="sql-server-as-a-sink"></a>SQL Server som mottagare

> [!TIP]
> Lär dig mer om Skriv beteenden, konfigurationer och bästa metoder som stöds av [bästa praxis för att läsa in data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Om du vill kopiera data till SQL Server anger du mottagar typen i kopierings aktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **SqlSink**. | Ja |
| writeBatchSize |Antal rader som ska infogas i SQL-tabellen *per batch*.<br/>Tillåtna värden är heltal för antalet rader. Som standard bestämmer Azure Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. |Inga |
| writeBatchTimeout |Den här egenskapen anger vänte tiden för åtgärden Infoga som ska slutföras innan tids gränsen uppnås.<br/>Tillåtna värden är för TimeSpan. Ett exempel är "00:30:00" i 30 minuter. Om inget värde anges används standardvärdet "02:00:00" för timeout. |Inga |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till SQL Server. Den anropas bara en gång per kopierings körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. |Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en mål tabell. <br/>Den här lagrade proceduren *anropas per batch*. För åtgärder som bara körs en gång och som inte har något att göra med källdata, till exempel ta bort eller trunkera, använder du egenskapen `preCopyScript`. | Inga |
| storedProcedureTableTypeParameterName |Parameter namnet för den tabell typ som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Det tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktiviteten gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga de data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-och värdepar. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Inga |
| tableOption | Anger om mottagar tabellen ska skapas automatiskt om den inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när Sink anger lagrad procedur eller mellanlagrad kopia har kon figurer ATS i kopierings aktiviteten. Tillåtna värden är: `none` (standard) `autoCreate`. |Inga |

**Exempel 1: Lägg till data**

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

**Exempel 2: anropa en lagrad procedur under kopiering**

Läs mer om att [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink).

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

När du kopierar data till SQL Server kan du behöva olika Skriv beteenden:

- [Lägg till](#append-data): mina källdata har endast nya poster.
- [Upsert](#upsert-data): mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill läsa in hela dimensions tabellen på nytt varje tillfälle.
- [Skriv med anpassad logik](#write-data-with-custom-logic): Jag behöver extra bearbetning före den slutliga infogningen i mål tabellen.

Se respektive avsnitt för hur du konfigurerar i Azure Data Factory och metod tips.

### <a name="append-data"></a>Lägg till data

Att lägga till data är standard beteendet för den här SQL Server Sink-anslutningen. Azure Data Factory skapar en Mass infogning för att skriva till tabellen effektivt. Du kan konfigurera källan och mottagaren efter detta i kopierings aktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data som ska kopieras använder du följande metod för att göra en upsert: 

- Använd först en [tillfällig tabell](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) för att överföra alla poster med hjälp av kopierings aktiviteten. Eftersom åtgärder mot temporära tabeller inte loggas kan du läsa in miljon tals poster på några sekunder.
- Kör en lagrad procedur aktivitet i Azure Data Factory om du vill tillämpa en [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) -eller INSERT-/Update-instruktion. Använd Temp-tabellen som en källa för att utföra alla uppdateringar eller infogningar som en enskild transaktion. På det här sättet minskar antalet tur och utloggning-åtgärder. I slutet av den lagrade procedur aktiviteten kan den temporära tabellen trunkeras så att den är redo för nästa upsert-cykel.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopierings aktivitet** som är länkad till en **lagrad procedur aktivitet**. Den tidigare kopierar data från käll arkivet till en tillfällig databas tabell, till exempel **# #UpsertTempTable**, som tabell namn i data uppsättningen. Sedan anropar den senare en lagrad procedur för att koppla källdata från Temp-tabellen till mål tabellen och rensa Temp-tabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med SAMMANSLAGNINGs logik, som i följande exempel, som pekas från föregående lagrade procedur aktivitet. Anta att målet är **marknadsförings** tabellen med tre kolumner: **profil**, **State**och **Category**. Gör upsert baserat på kolumnen **profil** .

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

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i kopierings aktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden kör varje rad i käll tabellen i stället för att använda Mass infogning som standard metod i kopierings aktiviteten, vilket inte är lämpligt för storskaliga upsert.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i en kopierings aktivitets mottagare. I det här fallet kör Azure Data Factory skriptet först för varje kopierings aktivitet som körs. Sedan kör den kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med den senaste informationen anger du ett skript för att först ta bort alla poster innan du läser in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i avsnittet [upsert data](#upsert-data) . När du behöver använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen, för stor skala, kan du göra något av följande: 

- Läs in till en temporär tabell och anropa sedan en lagrad procedur. 
- Anropa en lagrad procedur under kopieringen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till en SQL Server databas kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar. Funktionen för lagrade procedurer utnyttjar [tabell värdes parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> När en lagrad procedur anropas bearbetas data raden efter rad i stället för med en Mass åtgärd som vi inte rekommenderar för storskalig kopiering. Lär dig mer från [bästa praxis för att läsa in data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Du kan använda en lagrad procedur när inbyggda kopierings metoder inte fungerar. Ett exempel är när du vill använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen. Några extra bearbetnings exempel är när du vill slå samman kolumner, leta upp ytterligare värden och infoga data i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i den SQL Server databasen. Anta att indata och **marknadsförings** tabellen för mottagare har tre kolumner: **profil**, **State**och **Category**. Gör upsert baserat på kolumnen **profil** och Använd den bara för en viss kategori som kallas "ProduktA".

1. I databasen definierar du tabell typen med samma namn som **sqlWriterTableType**. Schemat för tabell typen är detsamma som det schema som returnerades av indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från din angivna källa och sammanfogar dem i utdatatabellen. Parameter namnet för tabell typen i den lagrade proceduren är samma som **TableName** som definierats i data uppsättningen.

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

3. I Azure Data Factory definierar du avsnittet **SQL-mottagare** i kopierings aktiviteten enligt följande:

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

## <a name="data-type-mapping-for-sql-server"></a>Data typs mappning för SQL Server

När du kopierar data från och till SQL Server, används följande mappningar från SQL Server data typer för att Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| SQL Server data typ | Azure Data Factory data typen Interim |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Flyttal |Double |
| mallar |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Enkel |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> För data typer som mappas till en decimal-interimistisk-typ, stöds för närvarande Azure Data Factory precision upp till 28. Om du har data som kräver precision som är större än 28, bör du överväga att konvertera till en sträng i en SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Felsöka anslutningsproblem

1. Konfigurera SQL Server-instansen så att den accepterar fjärr anslutningar. Starta **SQL Server Management Studio**, högerklicka på **Server**och välj **Egenskaper**. Välj **anslutningar** i listan och markera kryss rutan **Tillåt fjärr anslutningar till den här servern** .

    ![Aktivera fjärranslutningar](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Detaljerade anvisningar finns i [Konfigurera konfigurations alternativet för fjärråtkomstservern](https://msdn.microsoft.com/library/ms191464.aspx).

2. Starta **Konfigurationshanteraren för SQL Server**. Expandera **SQL Server nätverks konfiguration** för den instans du vill ha och välj **protokoll för MSSQLSERVER**. Protokollen visas i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och välja **Aktivera**.

    ![Aktivera TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Mer information och alternativa sätt att aktivera TCP/IP-protokollet finns i [Aktivera eller inaktivera ett Server nätverks protokoll](https://msdn.microsoft.com/library/ms191294.aspx).

3. I samma fönster dubbelklickar du på **TCP/IP** för att starta fönstret **Egenskaper för TCP/IP** .
4. Växla till fliken **IP-adresser** . Rulla nedåt för att se avsnittet **IPAll** . Skriv ner **TCP-porten**. Standardvärdet är **1433**.
5. Skapa en **regel för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten. 
6. **Verifiera anslutning**: om du vill ansluta till SQL Server med ett fullständigt kvalificerat namn använder du SQL Server Management Studio från en annan dator. Ett exempel är `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
