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
ms.openlocfilehash: 9208ceeb760bba97c12b23a1b6e5bdff7efc9020
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274832"
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
>Azure SQL Database Managed Instance **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)** stöds inte nu av den här anslutningen. Du kan använda för att arbeta runt [allmän ODBC-anslutningsprogram](connector-odbc.md) och ODBC-drivrutinen via lokal Integration Runtime. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) med ODBC-drivrutinen nedladdning och anslutningen sträng konfigurationer.

>[!NOTE]
>Tjänstens huvudnamn och hanterade identitetsautentiseringar stöds för närvarande inte av denna koppling och för att aktivera strax efter. För tillfället till lösning kan välja du Azure SQL Database-anslutningen och manuellt ange server för din hanterade instans.

## <a name="prerequisites"></a>Nödvändiga komponenter

Åtkomst till Azure SQL Database Managed Instance  **[offentlig slutpunkt](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , du kan använda ADF hanterade Azure IR. Kontrollera att du inte bara aktivera den offentliga slutpunkten, men även tillåta trafik för offentlig slutpunkt på nätverkssäkerhetsgrupp med Se ADF som går att ansluta till din databas, genom att följa [den här vägledningen](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Åtkomst till Azure SQL Database Managed Instance **privat slutpunkt**, Ställ in en [integration runtime med egen värd](create-self-hosted-integration-runtime.md) som har åtkomst till databasen. Om du etablerar den lokala integreringskörningen i samma virtuella nätverk som din hanterade instans kan du kontrollera att din integration runtime-datorn är i ett annat undernät än din hanterade instans. Om du etablerar din lokal integration runtime i ett annat virtuellt nätverk än din hanterade instans kan du använda en virtuell nätverkspeering eller i virtuella nätverk virtuell nätverksanslutning. Mer information finns i [Anslut ditt program till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure SQL Database Managed Instance-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure SQL Database Managed Instance länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **SqlServer**. | Ja. |
| connectionString |Den här egenskapen anger connectionString information som behövs för att ansluta till den hanterade instansen med SQL-autentisering. Mer information finns i följande exempel. <br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också publicera lösenord i Azure Key Vault, och om den är SQL-autentisering pull den `password` konfiguration av anslutningssträngen. Se JSON-exemplet nedan i tabellen och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja. |
| connectVia | Detta [integreringskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om din hanterade instans har offentlig slutpunkt och Tillåt ADF om du vill komma åt). Om den inte anges används standard Azure Integration Runtime. |Ja. |

**Exempel 1: Använd SQL-autentisering** standardporten är 1433. Om du använder SQL Managed Instance med offentlig slutpunkt kan du uttryckligen ange port 3342.

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

**Exempel 2: Använd SQL-autentisering med lösenord i Azure Key Vault** standardporten är 1433. Om du använder SQL Managed Instance med offentlig slutpunkt kan du uttryckligen ange port 3342.

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
| type | Type-egenskapen för datauppsättningen måste anges till **SqlServerTable**. | Ja. |
| tableName |Den här egenskapen är namnet på tabellen eller vyn i den databasinstans som den länkade tjänsten refererar till. | Nej för källa. Ja för mottagaren. |

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
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till **SqlSource**. | Ja. |
| sqlReaderQuery |Den här egenskapen använder anpassade SQL-fråga för att läsa data. Ett exempel är `select * from MyTable`. |Nej. |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej. |
| storedProcedureParameters |Det är dessa parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrarna måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej. |

Observera följande punkter:

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
> Läs mer om stöds skrivning beteenden, konfigurationer och bästa praxis från [bästa praxis för inläsning av data i Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Om du vill kopiera data till Azure SQL Database Managed Instance, ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitet mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **SqlSink**. | Ja. |
| writeBatchSize |Antalet rader som tillägg i SQL-tabell **per batch**.<br/>Tillåtna värden är heltal för hur många rader. Som standard Data Factory dynamiskt kan bestämma lämpliga batchstorleken baserat på radstorleken.  |Nej |
| writeBatchTimeout |Denna egenskap anger väntetiden batch Infoga åtgärd ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är för tidsintervallet. Ett exempel är ”00: 30:00”, som är 30 minuter. |Nej. |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopieringsaktiviteten ska köras innan du skriver data till den hantera instansen. Den anropas bara en gång per kopia som kör. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej. |
| sqlWriterStoredProcedureName |Det här namnet är för den lagrade proceduren som definierar hur du använder källdata i måltabellen. <br/>Den här lagrade proceduren är *anropas per batch*. För att göra en åtgärd som körs bara en gång och har inget att göra med källdata, till exempel ta bort eller trunkera använder den `preCopyScript` egenskapen. |Nej. |
| storedProcedureParameters |Dessa parametrar används för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrarna måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej. |
| sqlWriterTableType |Den här egenskapen anger ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga de data som ska kopieras med befintliga data. |Nej. |

**Exempel 1: Lägg till data**

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

**Exempel 2: anropa en lagrad procedur vid kopiering**

Lär dig mer från [anropa en lagrad procedur från en SQL-mottagare](#invoking-stored-procedure-for-sql-sink).

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

- **[Lägg till](#append-data)** : min källdata har bara nya poster;
- **[Upsert](#upsert-data)** : min källdata har både infogningar och uppdateringar.
- **[Skriv över](#overwrite-entire-table)** : Jag vill fylla på hela dimensionstabellen varje gång;
- **[Skriva med anpassad logik](#write-data-with-custom-logic)** : Jag behöver ytterligare bearbetning innan den slutliga infogningen i måltabellen.

Finns i respektive avsnitt om hur du konfigurerar i ADF och bästa praxis.

### <a name="append-data"></a>Lägga till data

Det här är standardinställningen för den här anslutningen för Azure SQL Database Managed Instance mottagare och ADF gör **massinfogning** att skriva till din tabell effektivt. Du kan helt enkelt konfigurera källa och mottagare i enlighet med detta i kopieringsaktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativet jag** (föreslås särskilt när du har stora mängder data att kopiera): den **metod för de flesta högpresterande** göra upsert är följande: 

- Det första utnyttja en [temporära tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) till massinläsning alla poster med hjälp av Kopieringsaktivitet. Eftersom åtgärder mot tillfälliga tabeller inte loggas, du kan läsa in miljontals poster i sekunder.
- Kör en lagringsprocedur-aktivitet i ADF att tillämpa en [sammanfoga](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (eller infoga/uppdatera)-instruktionen och Använd temporära tabellen som källa för att utföra alla uppdaterar eller infogar som en enda transaktion vilket minskar mängden görs och logga åtgärder. Temporär tabell kan trunkeras för att vara redo för nästa cykel för upsert i slutet av aktiviteten lagringsprocedur. 

I Azure Data Factory, till exempel skapa en pipeline med en **Kopieringsaktivitet** sammankedjade med en **lagringsprocedur-aktivitet** om åtgärden lyckades. Tidigare kopierar data från din källagringen till en tillfällig tabell, exempelvis ” **##UpsertTempTable**” som tabellnamnet i datauppsättningen, sedan det senare anropar en lagringsprocedur för att sammanfoga information från den temporära tabellen i måltabellen och rensa temporär tabell.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definiera en lagringsprocedur med MERGE logik som liknar följande, som refereras till från aktiviteten ovan lagringsprocedur i din databas. Förutsatt att målet **marknadsföring** tabell med tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**, och gör upsert baserat på den **profil-ID** kolumn.

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

**Alternativ II:** du kan också välja att [anropa lagrade proceduren i kopieringsaktiviteten](#invoking-stored-procedure-for-sql-sink), vid Obs den här metoden körs för varje rad i källtabellen i stället för att utnyttja bulk insert som standard-metoden i kopieringsaktiviteten, alltså plats inte för storskaliga upsert.

### <a name="overwrite-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera **preCopyScript** -egenskapen i kopieringsaktiviteten mottagare, i vilket fall för varje körningen av kopieringsaktiviteten, ADF kör skriptet först kör kopia om du vill infoga data. Om du vill skriva över hela tabellen med den senaste informationen, kan du till exempel ange ett skript för att först ta bort alla poster innan massinläsning nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Liknande enligt beskrivningen i [Upsert data](#upsert-data) avsnittet när du ska använda ytterligare bearbetning innan sista inmatningen av källdata i tabellen, kan du en) för stor skala in till en tillfällig tabell och sedan anropa en lagrad proceduren eller b) anropar en lagrad procedur vid kopiering.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till Azure SQL Database Managed Instance kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

> [!TIP]
> Anropa lagrade proceduren bearbetar den data rad för rad i stället för massredigering, vilket inte rekommenderas för storskaliga kopia. Läs mer i [bästa praxis för inläsning av data i Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer som inte servar syfte, t.ex. tillämpa ytterligare bearbetning innan sista inmatningen av källdata i måltabellen. Några extra bearbetningen exempel är merge kolumner, leta upp ytterligare värden och infogning i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i SQL Server-databasen. Anta som indata och mottagaren **marknadsföring** varje tabell har tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn, och gäller endast för en specifik kategori.

**Datauppsättningen för utdata:** ”tableName” ska vara samma typ parametern tabellnamnet i den lagrade proceduren (se lagrad procedur-skriptet nedan).

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

Definiera den **SQL mottagare** avsnittet i kopieringsaktiviteten på följande sätt.

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

I databasen, definierar du den lagrade proceduren med samma namn som den **SqlWriterStoredProcedureName**. Den hanterar indata från angivna källan och sammanfogar i utdatatabellen. Parameternamnet av tabelltypen i den lagrade proceduren bör vara samma som den **tableName** definierats i datauppsättningen.

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

Definiera tabelltyp med samma namn som sqlWriterTableType i din databas. Schemat för tabelltypen är samma som det schema som returnerades av dina indata.

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
