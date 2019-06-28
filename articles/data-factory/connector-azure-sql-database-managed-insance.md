---
title: Kopiera data till och från Azure SQL Database Managed Instance med Azure Data Factory | Microsoft Docs
description: Lär dig mer om att flytta data till och från Azure SQL Database Managed Instance med Azure Data Factory.
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
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338607"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database Managed Instance med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure SQL Database Managed Instance. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure SQL Database Managed Instance till alla datalager för mottagare som stöds. Du kan också kopiera data från alla dataarkiv till den hanterade instansen. En lista över datalager som stöds av kopieringsaktiviteten som källor och mottagare finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här anslutningen för Azure SQL Database Managed Instance:

- Kopiera data med hjälp av SQL-autentisering.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik vid kopiering.

>[!NOTE]
>Azure SQL Database Managed Instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) stöds inte av denna koppling nu. Du kan använda för att arbeta runt en [allmän ODBC-anslutningsprogram](connector-odbc.md) och en ODBC-drivrutinen via en lokal integration runtime. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) med ODBC-drivrutinen nedladdning och anslutningen sträng konfigurationer.

>[!NOTE]
>Tjänstens huvudnamn och hanterade identitetsautentiseringar stöds för närvarande inte av den här anslutningen. Undvik, Välj en Azure SQL Database-anslutning och manuellt ange server för din hanterade instans.

## <a name="prerequisites"></a>Nödvändiga komponenter

Åtkomst till Azure SQL Database Managed Instance [offentlig slutpunkt](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), du kan använda en Azure Data Factory hanterade Azure integration runtime. Se till att du aktiverar den offentliga slutpunkten och även tillåta offentliga slutpunkten trafik i nätverkssäkerhetsgruppen så att Azure Data Factory kan ansluta till databasen. Mer information finns i [den här vägledningen](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Om du vill få åtkomst till Azure SQL Database Managed Instance privat slutpunkt måste du ställa in en [integration runtime med egen värd](create-self-hosted-integration-runtime.md) som har åtkomst till databasen. Om du etablerar den lokala integreringskörningen i samma virtuella nätverk som din hanterade instans kan du kontrollera att din integration runtime-datorn är i ett annat undernät än din hanterade instans. Du kan använda en virtuell nätverkspeering eller ett virtuellt nätverk till virtuellt nätverk om du etablerar din lokal integration runtime i ett annat virtuellt nätverk än din hanterade instans. Mer information finns i [Anslut ditt program till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera entiteter i Azure Data Factory till Azure SQL Database Managed Instance-anslutningstjänsten.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure SQL Database Managed Instance länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **SqlServer**. | Ja |
| connectionString |Den här egenskapen anger den **connectionString** information som behövs för att ansluta till den hanterade instansen med SQL-autentisering. Mer information finns i följande exempel. <br/>Markera det här fältet som **SecureString** ska lagras på ett säkert sätt i Azure Data Factory. Du kan också ange ett lösenord i Azure Key Vault. Om den är SQL-autentisering kan du hämta den `password` konfiguration av anslutningssträngen. Mer information finns i JSON-exemplet som följer tabellen och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Detta [integreringskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Du kan använda en lokal integration runtime eller en Azure integration runtime om din hanterade instans har en offentlig slutpunkt och gör att Azure Data Factory för att komma åt den. Om den inte anges används standard Azure integration runtime. |Ja |

**Exempel 1: Använda SQL-autentisering**

Standardporten är 1433. Om du använder Azure SQL Database Managed Instance med en offentlig slutpunkt kan du uttryckligen ange port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

Standardporten är 1433. Om du använder Azure SQL Database Managed Instance med en offentlig slutpunkt kan du uttryckligen ange port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för användning att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database Managed Instance-datauppsättningen.

För att kopiera data till och från Azure SQL Database Managed Instance, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till **SqlServerTable**. | Ja |
| tableName |Den här egenskapen är namnet på tabellen eller vyn i den databasinstans som den länkade tjänsten refererar till. | Nej för källa, Ja för mottagare |

**Exempel**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för användning att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database Managed Instance källa och mottagare.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Managed Instance som en källa

För att kopiera data från Azure SQL Database Managed Instance kan du ange typ av datakälla i kopieringsaktiviteten till **SqlSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitet källa:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till **SqlSource**. | Ja |
| sqlReaderQuery |Den här egenskapen använder anpassade SQL-fråga för att läsa data. Ett exempel är `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Det är dessa parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrarna måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

**Observera följande:**

- Om **sqlReaderQuery** har angetts för **SqlSource**, Kopieringsaktivitet körs den här frågan mot hanterad instans-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar parametrar för den lagrade proceduren.
- Om du inte anger antingen den **sqlReaderQuery** eller **sqlReaderStoredProcedureName** egenskap, kolumner som definierats i avsnittet ”struktur” i datauppsättningen JSON som används för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot den hanterade instansen. Om definitionen för datauppsättningen inte har ”struktur”, markeras alla kolumner från tabellen.

**Exempel: Använd en SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Managed Instance som mottagare

> [!TIP]
> Mer information om den stöds skrivning beteenden, konfigurationer och bästa praxis från [bästa praxis för inläsning av data i Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Om du vill kopiera data till Azure SQL Database Managed Instance, ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitet mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **SqlSink**. | Ja |
| writeBatchSize |Antalet rader att infoga i SQL-tabell *per batch*.<br/>Tillåtna värden är heltal för hur många rader. Som standard anger Azure Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken.  |Nej |
| writeBatchTimeout |Denna egenskap anger väntetiden batch Infoga åtgärd ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är för tidsangivelse. Ett exempel är ”00: 30:00”, som är 30 minuter. |Nej |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopieringsaktiviteten ska köras innan du skriver data till den hantera instansen. Den anropas bara en gång per kopia som kör. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |
| sqlWriterStoredProcedureName |Det här namnet är för den lagrade proceduren som definierar hur du använder källdata i måltabellen. <br/>Den här lagrade proceduren är *anropas per batch*. För att göra en åtgärd som körs bara en gång och har inget att göra med källdata, till exempel ta bort eller trunkera använder den `preCopyScript` egenskapen. |Nej |
| storedProcedureParameters |Dessa parametrar används för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrarna måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Den här egenskapen anger ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga de data som ska kopieras med befintliga data. |Nej |

**Exempel 1: Lägga till data**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Bästa praxis för inläsning av data i Azure SQL Database Managed Instance

När du kopierar data till Azure SQL Database Managed Instance kan du kräva olika skrivning beteende:

- [Lägg till](#append-data): Mina källdata har endast nya poster.
- [Upsert](#upsert-data): Mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill fylla på hela dimensionstabellen varje gång.
- [Skriva med anpassad logik](#write-data-with-custom-logic): Jag behöver ytterligare bearbetning innan den slutliga infogningen i måltabellen. 

Finns i respektive avsnitt att konfigurera i Azure Data Factory och bästa praxis.

### <a name="append-data"></a>Lägga till data

Data läggs till är standardbeteendet för den här anslutningen för Azure SQL Database Managed Instance mottagare. Azure Data Factory har en massinfogning att skriva till din tabell effektivt. Du kan konfigurera källa och mottagare i enlighet med detta i kopieringsaktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data för att kopiera, Använd följande metod för att göra en upsert: 

- Använd först en [temporära tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) till massinläsning alla poster med hjälp av kopieringsaktiviteten. Eftersom åtgärder mot tillfälliga tabeller inte är inloggad, du kan läsa in flera miljoner poster i sekunder.
- Kör en lagrad procedur-aktivitet i Azure Data Factory för att tillämpa en [sammanfoga](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) eller INSERT/UPDATE-instruktion. Använda den temporära tabellen som källa för att utföra alla uppdaterar eller infogar som en enda transaktion. På så vis minskar antalet sändningar och log operations. Den temporära tabellen kan trunkeras om du vill vara redo för nästa cykel för upsert i slutet av aktivitet för lagrad procedur.

I Azure Data Factory, till exempel skapa en pipeline med en **Kopieringsaktivitet** sammankedjade med en **lagringsprocedur-aktivitet**. Tidigare kopierar data från din källagringen till en tillfällig tabell, till exempel **##UpsertTempTable**, som tabellnamn i datauppsättningen. Det senare sedan anropar en lagrad procedur för att sammanfoga information från den temporära tabellen i måltabellen och rensa den temporära tabellen.

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

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i en Kopieringsaktivitet](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden körs varje rad i källtabellen istället för att använda massinfogning som standard-metod i kopieringsaktiviteten, vilket inte är lämplig för storskalig upsert.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera den **preCopyScript** -egenskapen i Kopiera aktivitet mottagare. I det här fallet för varje kopieringsaktiviteten som kör kör Azure Data Factory skriptet först. Sedan körs kopia om du vill infoga data. Exempel: Om du vill skriva över hela tabellen med den senaste informationen, anger du ett skript för att först ta bort alla poster innan du massimportera läsa in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i den [Upsert data](#upsert-data) avsnittet. När du ska använda extra bearbetning innan sista inmatningen av källdata i måltabellen för storskalig, du kan göra något av följande: 

- Läsa in till en tillfällig tabell och sedan anropa en lagrad procedur.
- Anropa en lagrad procedur vid kopiering.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till Azure SQL Database Managed Instance kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

> [!TIP]
> Anropa en lagrad procedur bearbetar data rad för rad i stället för med hjälp av en bulkåtgärd som vi inte rekommenderar för storskaliga kopia. Läs mer i [bästa praxis för inläsning av data i Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer inte fungerar. Ett exempel är när du vill tillämpa ytterligare bearbetning innan sista inmatningen av källdata i måltabellen. Några extra bearbetningen exempel är när du vill slå ihop kolumner, leta upp ytterligare värden och infoga data i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i SQL Server-databasen. Anta att indata och mottagaren **marknadsföring** varje tabell har tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn, och gäller endast för en specifik kategori.

**Datauppsättningen för utdata:** ”TableName” är samma typ parametern tabellnamnet i den lagrade proceduren som visas i följande lagrade proceduren skript:

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Datatypsmappningen för Azure SQL Database Managed Instance

När data kopieras till och från Azure SQL Database Managed Instance, används följande mappningar från Azure SQL Database Managed Instance-datatyper till Azure Data Factory tillfälliga-datatyper. Läs hur kopieringsaktiviteten mappar från källtyp schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database Managed Instance-datatyp | Azure Data Factory tillfälliga datatyp |
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

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
