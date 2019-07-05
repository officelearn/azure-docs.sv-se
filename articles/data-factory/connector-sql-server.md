---
title: Kopiera data till och från SQL Server med hjälp av Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data till och från SQL Server-databas som finns lokalt eller i en Azure virtuell dator med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443278"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiera data till och från SQL Server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj versionen av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuell version](connector-sql-server.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till en SQL Server-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från och till en SQL Server-databas till alla datalager för mottagare som stöds. Eller du kan kopiera data från alla dataarkiv till en SQL Server-databas. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SQL Server-anslutningen:

- SQL Server-versioner 2016, 2014, 2012, 2008 R2, 2008 och 2005.
- Kopiera data med hjälp av SQL- eller Windows-autentisering.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik vid kopiering.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) stöds inte av denna koppling nu. Du kan använda för att arbeta runt en [allmän ODBC-anslutningsprogram](connector-odbc.md) och en ODBC-drivrutinen. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) med ODBC-drivrutinen nedladdning och anslutningen sträng konfigurationer.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda data från en SQL Server-databas som inte är offentligt tillgänglig, måste du konfigurera en lokal integration runtime. Mer information finns i [integration runtime med egen värd](create-self-hosted-integration-runtime.md). Integration runtime får en inbyggd drivrutin för SQL Server-databasen. Du behöver inte installera några drivrutinen manuellt när du kopierar data från eller till SQL Server-databasen.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SQL Server database-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SQL Server-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **SqlServer**. | Ja |
| connectionString |Ange **connectionString** information som behövs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. Se följande exempel.<br/>Markera det här fältet som **SecureString** ska lagras på ett säkert sätt i Azure Data Factory. Du kan också ange ett lösenord i Azure Key Vault. Om den är SQL-autentisering kan du hämta den `password` konfiguration av anslutningssträngen. Mer information finns i JSON-exemplet som följer tabellen och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Ange ett användarnamn om du använder Windows-autentisering. Ett exempel är **domainname\\användarnamn**. |Nej |
| password |Ange ett lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som **SecureString** ska lagras på ett säkert sätt i Azure Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Nej |
| connectVia | Detta [integreringskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Du kan använda en lokal integration runtime eller Azure integration runtime om ditt datalager är tillgänglig för allmänheten. Om den inte anges används standard Azure integration runtime. |Nej |

>[!TIP]
>Om du stöter på ett fel med felkod ”UserErrorFailedToConnectToSqlServer” och ett meddelande som ”sessionens tidsgräns för databasen är XXX och har nåtts”, lägger du till `Pooling=false` till din anslutningssträng och försök igen.

**Exempel 1: Använda SQL-autentisering**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
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
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server-datauppsättningen.

För att kopiera data från och till en SQL Server-databas, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till **SqlServerTable**. | Ja |
| tableName |Den här egenskapen är namnet på tabellen eller vyn i SQL Server-databasinstansen som den länkade tjänsten refererar till. | Nej för källa, Ja för mottagare |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för användning att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server-källa och mottagare.

### <a name="sql-server-as-a-source"></a>SQL Server som en källa

För att kopiera data från SQL Server, ange typ av datakälla i kopieringsaktiviteten till **SqlSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitet källa:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till **SqlSource**. | Ja |
| sqlReaderQuery |Använda anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Det är dessa parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

**Saker att Observera:**

- Om **sqlReaderQuery** har angetts för **SqlSource**, Kopieringsaktivitet körs den här frågan mot SQL Server-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar parametrar för den lagrade proceduren.
- Om du inte anger något **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, de kolumner som definierats i avsnittet ”struktur” i datauppsättningen JSON som används för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot SQL Server. Om definitionen för datauppsättningen inte har ”struktur”, markeras alla kolumner från tabellen.

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

**Definitionen för lagrad procedur**

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

### <a name="sql-server-as-a-sink"></a>SQL Server som en mottagare

> [!TIP]
> Mer information om den stöds skrivning beteenden, konfigurationer och bästa praxis från [bästa praxis för inläsning av data i SQL Server](#best-practice-for-loading-data-into-sql-server).

För att kopiera data till SQL Server, ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitet mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **SqlSink**. | Ja |
| writeBatchSize |Antalet rader att infoga i SQL-tabell *per batch*.<br/>Tillåtna värden är heltal för hur många rader. Som standard anger Azure Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken. |Nej |
| writeBatchTimeout |Denna egenskap anger väntetiden batch Infoga åtgärd ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är för tidsangivelse. Ett exempel är ”00: 30:00” i 30 minuter. |Nej |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopieringsaktiviteten ska köras innan du skriver data till SQL Server. Den anropas bara en gång per kopia som kör. Du kan använda den här egenskapen för att rensa upp de förinstallerade data. |Nej |
| sqlWriterStoredProcedureName |Det här namnet är för den lagrade proceduren som definierar hur du använder källdata i måltabellen.<br/>Den här lagrade proceduren är *anropas per batch*. För att göra en åtgärd som körs bara en gång och har inget att göra med källdata, till exempel ta bort eller trunkera använder den `preCopyScript` egenskapen. |Nej |
| storedProcedureParameters |Dessa parametrar används för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrarna måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Den här egenskapen anger ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga de data som ska kopieras med befintliga data. |Nej |

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exempel 2: Anropa en lagrad procedur vid kopiering**

Lär dig mer från [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Bästa praxis för inläsning av data i SQL Server

När du kopierar data till SQL Server, kan du kräva olika skrivning beteende:

- [Lägg till](#append-data): Mina källdata har endast nya poster.
- [Upsert](#upsert-data): Mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill fylla på hela dimensionstabellen varje gång.
- [Skriva med anpassad logik](#write-data-with-custom-logic): Jag behöver ytterligare bearbetning innan den slutliga infogningen i måltabellen.

Finns i respektive avsnitt att konfigurera i Azure Data Factory och bästa praxis.

### <a name="append-data"></a>Lägga till data

Data läggs till är standardbeteendet för den här SQL Server-anslutningen för mottagare. Azure Data Factory har en massinfogning att skriva till din tabell effektivt. Du kan konfigurera källa och mottagare i enlighet med detta i kopieringsaktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data för att kopiera, Använd följande metod för att göra en upsert: 

- Använd först en [temporära tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) till massinläsning alla poster med hjälp av kopieringsaktiviteten. Eftersom åtgärder mot tillfälliga tabeller inte är inloggad, du kan läsa in flera miljoner poster i sekunder.
- Kör en lagrad procedur-aktivitet i Azure Data Factory för att tillämpa en [sammanfoga](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) eller INSERT/UPDATE-instruktion. Använda den temporära tabellen som en källa för att utföra alla uppdaterar eller infogar som en enda transaktion. På så vis minskar antalet sändningar och log operations. Den temporära tabellen kan trunkeras om du vill vara redo för nästa cykel för upsert i slutet av aktivitet för lagrad procedur.

I Azure Data Factory, till exempel skapa en pipeline med en **Kopieringsaktivitet** sammankedjade med en **lagringsprocedur-aktivitet**. Tidigare kopierar data från din källagringen till en tillfällig tabell i databasen exempelvis **##UpsertTempTable**, som tabellnamn i datauppsättningen. Det senare sedan anropar en lagrad procedur för att sammanfoga information från den temporära tabellen i måltabellen och rensa den temporära tabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definiera en lagrad procedur med MERGE logik som i följande exempel, som refereras till från den föregående aktiviteten lagrad procedur i din databas. Anta att målet är den **marknadsföring** tabell med tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn.

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

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i kopieringsaktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden körs varje rad i källtabellen istället för att använda massinfogning som standard-metod i kopieringsaktiviteten, vilket inte är lämplig för storskalig upsert.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera den **preCopyScript** -egenskapen i Kopiera aktivitet mottagare. I det här fallet för varje kopieringsaktiviteten som kör kör Azure Data Factory skriptet först. Sedan körs kopia om du vill infoga data. Exempel: Om du vill skriva över hela tabellen med den senaste informationen, anger du ett skript för att först ta bort alla poster innan du massimportera läsa in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i den [Upsert data](#upsert-data) avsnittet. När du ska använda extra bearbetning innan sista inmatningen av källdata i måltabellen för storskalig, du kan göra något av följande: 

- Läsa in till en tillfällig tabell och sedan anropa en lagrad procedur. 
- Anropa en lagrad procedur vid kopiering.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till en SQL Server-databas du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

> [!TIP]
> Anropa en lagrad procedur bearbetar data rad för rad i stället för med hjälp av en bulkåtgärd som vi inte rekommenderar för storskaliga kopia. Läs mer i [bästa praxis för inläsning av data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer inte fungerar. Ett exempel är när du vill tillämpa ytterligare bearbetning innan sista inmatningen av källdata i måltabellen. Några extra bearbetningen exempel är när du vill slå ihop kolumner, leta upp ytterligare värden och infoga data i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i SQL Server-databasen. Anta att indata och mottagaren **marknadsföring** varje tabell har tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn, och gäller endast för en specifik kategori.

**Datauppsättningen för utdata:** ”TableName” är samma typ parametern tabellnamnet i den lagrade proceduren som visas i följande lagrade proceduren skript:

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definiera den **SQL mottagare** avsnittet i kopieringsaktiviteten på följande sätt:

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

I databasen, definierar du den lagrade proceduren med samma namn som **SqlWriterStoredProcedureName**. Den hanterar indata från angivna källan och sammanfogar i utdatatabellen. Parameternamnet av tabelltypen i den lagrade proceduren är samma som **tableName** definierats i datauppsättningen.

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

I databasen, definierar du tabelltyp med samma namn som **sqlWriterTableType**. Schemat för tabelltypen är samma som det schema som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Den lagrade proceduren funktionen utnyttjar [tabellvärdeparametrar](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Datatypsmappningen för SQL Server

När du kopierar data från och till SQL Server, används följande mappningar från SQL Server-datatyper till Azure Data Factory tillfälliga-datatyper. Läs hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| SQL Server-datatypen | Azure Data Factory tillfälliga datatyp |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> För information om vilka datatyper som mappar till decimaltyp tillfällig stöder för närvarande Azure Data Factory precision upp till 28. Överväg att konvertera till en sträng i en SQL-fråga om du har data som kräver precision som är större än 28.

## <a name="troubleshoot-connection-issues"></a>Felsök anslutningsproblem

1. Konfigurera SQL Server-instansen för att acceptera fjärranslutningar. Starta **SQL Server Management Studio**, högerklicka på **server**, och välj **egenskaper**. Välj **anslutningar** i listan och välj den **Tillåt fjärranslutningar till den här servern** markerar du kryssrutan.

    ![Aktivera fjärranslutningar](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Detaljerade anvisningar finns i [konfigurera serverkonfigurationsalternativet för fjärråtkomst](https://msdn.microsoft.com/library/ms191464.aspx).

2. Starta **SQL Server Configuration Manager**. Expandera **nätverkskonfiguration för SQL Server** för den instans du vill och väljer **protokoll för MSSQLSERVER**. Protokoll visas i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och välja **aktivera**.

    ![Aktivera TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Mer information och alternativa sätt för att aktivera TCP/IP-protokollet finns i [aktivera eller inaktivera ett server-nätverksprotokoll](https://msdn.microsoft.com/library/ms191294.aspx).

3. I samma fönster dubbelklickar du på **TCP/IP** att starta den **TCP/IP-egenskaper** fönster.
4. Växla till den **IP-adresser** fliken. Rulla ned för att se den **IPAll** avsnittet. Anteckna den **TCP-Port**. Standardvärdet är **1433**.
5. Skapa en **regeln för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten. 
6. **Verifiera anslutningen**: Anslut till SQL Server med hjälp av ett fullständigt kvalificerat namn genom att använda SQL Server Management Studio från en annan dator. Ett exempel är `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
