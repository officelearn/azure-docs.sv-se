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
ms.date: 11/08/2018
ms.author: jingwang
ms.openlocfilehash: 1c58c77cb20f269c3a11615ccebf0bdc9cce0d86
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230822"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopiera data till och från SQL Server med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
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

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda data från en SQL Server-databas som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information. Integreringskörningen innehåller en inbyggd drivrutin för SQL Server-databas, måste du därför inte installera några drivrutinen manuellt när du kopierar data från/till SQL Server-databas.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SQL Server database-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SQL Server-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **SqlServer** | Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. Se följande exempel. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| Användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Nej |
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

**Exempel 2: använda Windows-autentisering**

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

För att kopiera data från/till SQL Server-databas, ange typegenskapen på datauppsättningen till **SqlServerTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **SqlServerTable** | Ja |
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

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **SqlSource** | Ja |
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

För att kopiera data till SQL Server, ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till: **SqlSink** | Ja |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antal rader). |Nej (standard: 10000) |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: timespan. Exempel: ”00: 30:00” (30 minuter). |Nej |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten till att köra innan du skriver data till SQL Server. Det ska bara anropas en gång per kopia som kör. Du kan använda den här egenskapen för att rensa tidigare inlästa data. |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur du använder källdata i måltabellen, t.ex. att göra upsertar eller transformering med egen affärslogik. <br/><br/>Observera att den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärd som endast körs en gång och har inget att göra med källdata, t.ex. Ta bort/trunkera, Använd `preCopyScript` egenskapen. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. |Nej |

> [!TIP]
> När du kopierar data till SQL Server, läggs kopieringsaktiviteten data till tabellen mottagare som standard. Använd den lagrade proceduren i SqlSink för att utföra en UPSERT eller ytterligare affärslogik. Lär dig mer från [när lagrade proceduren for SQL Sink](#invoking-stored-procedure-for-sql-sink).

**Exempel 1: lägga till data**

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

**Exempel 2: anropar en lagrad procedur vid kopiering för upsert**

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

## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen

Det här avsnittet innehåller ett exempel som kopierar data från en källtabellen med någon identity-kolumn till en måltabell med en identity-kolumn.

**Källtabellen:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Måltabell:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observera att måltabellen har en identitetskolumn.

**Källa dataset JSON-definition**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
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

**Mål dataset JSON-definition**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
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

Observera att som din käll- och tabell har olika schema (målet har ytterligare en kolumn med identiteten). I det här scenariot måste du ange **struktur** -egenskapen i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa lagrade procedur från SQL-mottagare

När du kopierar data till SQL Server-databas, användarspecificerade en lagrad procedur kan konfigureras och anropas med ytterligare parametrar.

En lagrad procedur kan användas när inbyggd kopiera mekanismer inte skickar syftet. Den används vanligtvis när upsert (insert + uppdatering) eller extra bearbetning (sammanslagning kolumner, leta upp ytterligare värden, infogning i flera tabeller, etc.) måste göras innan sista inmatningen av källdata i tabellen.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i SQL Server-databasen. Förutsatt att indata och tabellen ”marknadsföring” mottagare har tre kolumner: Profil-ID, tillstånd och kategori. Utför upsert baserat på kolumnen ”profil-ID” och gäller endast för en specifik kategori.

**Datauppsättningen för utdata**

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

Definiera SqlSink-avsnitt i kopieringsaktiviteten på följande sätt.

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

Definiera den lagrade proceduren med samma namn som SqlWriterStoredProcedureName i din databas. Den hanterar indata från angiven källa och sammanfoga till utdata i tabellen. Parameternamnet av tabelltypen i den lagrade proceduren bör vara samma som den ”tableName” som definierats i datauppsättningen.

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

Definiera tabelltyp med samma namn som sqlWriterTableType i din databas. Observera att schemat för tabelltypen ska vara samma som det schema som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Den lagrade proceduren funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Om du skriver till pengar/Smallmoney-datatypen som anropar lagrade proceduren, är det möjligt att värden ska avrundas. Ange den motsvarande datatypen i TVP som decimaltal i stället för pengar/Smallmoney för att minska. 

## <a name="data-type-mapping-for-sql-server"></a>Datatypen mappning för SQLServer

När du kopierar data från/till SQL Server, används följande mappningar från SQL Server-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SQL Server-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |
| bigint |Int64 |
| binär |Byte] |
| bitars |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| decimaltal |decimaltal |
| FILESTREAM-attributet (varbinary(max)) |Byte] |
| Flyttal |Double-värde |
| image |Byte] |
| int |Int32 |
| pengar |decimaltal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |decimaltal |
| nvarchar |Sträng, Char] |
| verkliga |Enkel |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimaltal |
| sql_variant |Objektet * |
| text |Sträng, Char] |
| time |Tidsintervall |
| tidsstämpel |Byte] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

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
