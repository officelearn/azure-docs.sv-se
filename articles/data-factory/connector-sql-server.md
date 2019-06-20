---
title: Kopiera data till och från SQL Server med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data till/från SQL Server-databas som finns lokalt eller i en Azure-dator med Azure Data Factory.
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
ms.openlocfilehash: 62845557f33fd9c4f3c2ec4e239213c75101955d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275976"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopiera data till och från SQL Server med Azure Data Factory
> [!div class="op_single_selector" title1="Välj versionen av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuell version](connector-sql-server.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till en SQL Server-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från/till SQL Server-databas till alla mottagare som stöds eller kopiera data från alla dataarkiv till SQL Server-databas. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SQL Server-anslutningen:

- SQL Server version 2016, 2014, 2012, 2008 R2, 2008 och 2005
- Kopiera data med hjälp av **SQL** eller **Windows** autentisering.
- Hämta data med SQL-fråga eller lagrad procedur som källan.
- Som mottagare, data läggs till måltabell eller anropa en lagrad procedur med anpassad logik vid kopiering.

>[!NOTE]
>SQL Server **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)** stöds inte nu av den här anslutningen. Du kan använda för att arbeta runt [allmän ODBC-anslutningsprogram](connector-odbc.md) och ODBC-drivrutinen. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) med ODBC-drivrutinen nedladdning och anslutningen sträng konfigurationer.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda data från en SQL Server-databas som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information. Integreringskörningen innehåller en inbyggd drivrutin för SQL Server-databas, måste du därför inte installera några drivrutinen manuellt när du kopierar data från/till SQL Server-databas.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SQL Server database-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SQL Server-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **SqlServer** | Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. Se följande exempel.<br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också publicera lösenord i Azure Key Vault, och om den är SQL-autentisering pull den `password` konfiguration av anslutningssträngen. Se JSON-exemplet nedan i tabellen och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| userName |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!TIP]
>Om du når fel med felkod som ”UserErrorFailedToConnectToSqlServer” och visas som ”sessionens tidsgräns för databasen är XXX och har uppnåtts”., lägga till `Pooling=false` till din anslutningssträng och försök igen.

**Exempel 1: med SQL-autentisering**

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

**Exempel 2: med SQL-autentisering med lösenord i Azure Key Vault**

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

**Exempel 3: med hjälp av Windows-autentisering**

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

För att kopiera data från/till SQL Server-databas, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **SqlServerTable** | Ja |
| tableName |Namnet på tabellen eller vyn i SQL Server-databasinstansen som den länkade tjänsten refererar till. | Nej för källa, Ja för mottagare |

**Exempel:**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server-datakälla och mottagare.

### <a name="sql-server-as-source"></a>SQL Server som källan

För att kopiera data från SQL Server, ange typ av datakälla i kopieringsaktiviteten till **SqlSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **SqlSource** | Ja |
| sqlReaderQuery |Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

**Saker att Observera:**

- Om den **sqlReaderQuery** har angetts för SqlSource, Kopieringsaktivitet körs den här frågan mot SQL Server-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).
- Om du inte anger ”sqlReaderQuery” eller ”sqlReaderStoredProcedureName” de kolumner som definierats i avsnittet ”struktur” i datauppsättningen JSON används för att skapa en fråga (`select column1, column2 from mytable`) ska köras mot SQL Server. Om definitionen för datauppsättningen inte har ”strukturen”, markeras alla kolumner från tabellen.

**Exempel: med SQL-fråga**

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

**Exempel: använda lagrad procedur**

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

**Definitionen av lagrade proceduren:**

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

### <a name="sql-server-as-sink"></a>SQL Server som mottagare

> [!TIP]
> Läs mer om stöds skrivning beteenden, konfigurationer och bästa praxis från [bästa praxis för inläsning av data i SQL Server](#best-practice-for-loading-data-into-sql-server).

För att kopiera data till SQL Server, ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till: **SqlSink** | Ja |
| writeBatchSize |Antalet rader som tillägg i SQL-tabell **per batch**.<br/>Tillåtna värden är: heltal (antal rader). Som standard Data Factory dynamiskt kan bestämma lämpliga batchstorleken baserat på radstorleken. |Nej |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: timespan. Exempel: ”00: 30:00” (30 minuter). |Nej |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten till att köra innan du skriver data till SQL Server. Det ska bara anropas en gång per kopia som kör. Du kan använda den här egenskapen för att rensa tidigare inlästa data. |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur du använder källdata i måltabellen.<br/>Observera att den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärd som endast körs en gång och har inget att göra med källdata, t.ex. Ta bort/trunkera, Använd `preCopyScript` egenskapen. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. |Nej |

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exempel 2: anropa en lagrad procedur vid kopiering**

Lär dig mer från [när lagrade proceduren for SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

När du kopierar data till SQL Server kan kräva olika skrivning beteende:

- **[Lägg till](#append-data)** : min källdata har bara nya poster;
- **[Upsert](#upsert-data)** : min källdata har både infogningar och uppdateringar.
- **[Skriv över](#overwrite-entire-table)** : Jag vill fylla på hela dimensionstabellen varje gång;
- **[Skriva med anpassad logik](#write-data-with-custom-logic)** : Jag behöver ytterligare bearbetning innan den slutliga infogningen i måltabellen.

Finns i respektive avsnitt om hur du konfigurerar i ADF och bästa praxis.

### <a name="append-data"></a>Lägga till data

Det här är standardinställningen för den här SQL Server-anslutningen för mottagare och ADF gör **massinfogning** att skriva till din tabell effektivt. Du kan helt enkelt konfigurera källa och mottagare i enlighet med detta i kopieringsaktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativet jag** (föreslås särskilt när du har stora mängder data att kopiera): den **metod för de flesta högpresterande** göra upsert är följande: 

- Det första utnyttja en [temporära tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) till massinläsning alla poster med hjälp av Kopieringsaktivitet. Eftersom åtgärder mot tillfälliga tabeller inte loggas, du kan läsa in miljontals poster i sekunder.
- Kör en lagringsprocedur-aktivitet i ADF att tillämpa en [sammanfoga](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (eller infoga/uppdatera)-instruktionen och Använd temporära tabellen som källa för att utföra alla uppdaterar eller infogar som en enda transaktion vilket minskar mängden görs och logga åtgärder. Temporär tabell kan trunkeras för att vara redo för nästa cykel för upsert i slutet av aktiviteten lagringsprocedur. 

I Azure Data Factory, till exempel skapa en pipeline med en **Kopieringsaktivitet** sammankedjade med en **lagringsprocedur-aktivitet** om åtgärden lyckades. Tidigare kopierar data från din källagringen till en tillfällig tabell i databasen säg ” **##UpsertTempTable**” som tabellnamnet i datauppsättningen, sedan det senare anropar en lagringsprocedur för att sammanfoga information från den temporära tabellen i måltabellen, och rensa temporära tabellen.

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

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa lagrade procedur från SQL-mottagare

När du kopierar data till SQL Server-databas, kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

> [!TIP]
> Anropa lagrade proceduren bearbetar den data rad för rad i stället för massredigering, vilket inte rekommenderas för storskaliga kopia. Läs mer i [bästa praxis för inläsning av data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer som inte servar syfte, t.ex. tillämpa ytterligare bearbetning innan sista inmatningen av källdata i måltabellen. Några extra bearbetningen exempel är merge kolumner, leta upp ytterligare värden och infogning i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i SQL Server-databasen. Anta som indata och mottagaren **marknadsföring** varje tabell har tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn, och gäller endast för en specifik kategori.

**Datauppsättningen för utdata:** ”tableName” ska vara samma typ parametern tabellnamnet i den lagrade proceduren (se lagrad procedur-skriptet nedan).

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

Definiera tabelltyp med samma namn som sqlWriterTableType i din databas. Observera att schemat för tabelltypen ska vara samma som det schema som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Den lagrade proceduren funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Datatypen mappning för SQLServer

När du kopierar data från/till SQL Server, används följande mappningar från SQL Server-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SQL Server-datatypen | Data factory tillfälliga datatyp |
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
> För typer mappas till decimaltyp tillfällig stöder för närvarande ADF precision upp till 28. Om du har data med precision som är större än 28, Överväg för att konvertera till en sträng i SQL-frågan.

## <a name="troubleshooting-connection-issues"></a>Felsöka anslutningsproblem

1. Konfigurera din SQL Server för att acceptera fjärranslutningar. Starta **SQL Server Management Studio**, högerklicka på **server**, och klicka på **egenskaper**. Välj **anslutningar** i listan och kontrollera **Tillåt fjärranslutningar till servern**.

    ![Aktivera fjärranslutningar](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Se [konfigurerar fjärråtkomst Serverkonfigurationsalternativet](https://msdn.microsoft.com/library/ms191464.aspx) detaljerade anvisningar.

2. Starta **SQL Server Configuration Manager**. Expandera **nätverkskonfiguration för SQL Server** för den instans du vill och väljer **protokoll för MSSQLSERVER**. Du bör se protokoll i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och klicka på **aktivera**.

    ![Aktivera TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Se [aktivera eller inaktivera ett Server-nätverksprotokoll](https://msdn.microsoft.com/library/ms191294.aspx) information och alternativa sätt för att aktivera TCP/IP-protokollet.

3. I samma fönster dubbelklickar du på **TCP/IP** att starta **TCP/IP-egenskaper** fönster.
4. Växla till den **IP-adresser** fliken. Rulla ned för att se **IPAll** avsnittet. Anteckna den **TCP-Port** (standardvärdet är **1433**).
5. Skapa en **regeln för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten.  
6. **Verifiera anslutningen**: Anslut till SQL-servern med fullständigt kvalificerade namnet genom att använda SQL Server Management Studio från en annan dator. Till exempel: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
