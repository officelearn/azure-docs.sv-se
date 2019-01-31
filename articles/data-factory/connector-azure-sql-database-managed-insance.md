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
ms.date: 01/23/2019
ms.author: jingwang
ms.openlocfilehash: 9cd2eaefb845b6ce9ca2f1cfcaf1234f8f96615c
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300344"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database Managed Instance med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure SQL Database Managed Instance. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure SQL Database Managed Instance till alla datalager för mottagare som stöds. Du kan också kopiera data från alla dataarkiv till den hanterade instansen. En lista över datalager som stöds av kopieringsaktiviteten som källor och mottagare finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här anslutningen för Azure SQL Database Managed Instance:

- Kopiera data med hjälp av SQL- eller Windows-autentisering.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik vid kopiering.

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) stöds inte nu. 

## <a name="prerequisites"></a>Förutsättningar

För att använda data från en Azure SQL Database Managed Instance som befinner sig i ett virtuellt nätverk måste du konfigurera en lokal integration runtime som har åtkomst till databasen. Mer information finns i [integration runtime med egen värd](create-self-hosted-integration-runtime.md).

Om du etablerar din lokal integration runtime i samma virtuella nätverk som din hanterade instans kan du se till att din datorn är i ett annat undernät än din hanterade instans. Om du etablerar din lokal integration runtime i ett annat virtuellt nätverk än din hanterade instans kan du använda en virtuell nätverkspeering eller i virtuella nätverk virtuell nätverksanslutning. Mer information finns i [Anslut ditt program till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure SQL Database Managed Instance-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure SQL Database Managed Instance länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **SqlServer**. | Ja. |
| connectionString |Den här egenskapen anger connectionString information som behövs för att ansluta till den hanterade instansen med hjälp av SQL-autentisering eller Windows-autentisering. Mer information finns i följande exempel. Välj **SecureString** att lagra connectionString-information på ett säkert sätt i Data Factory eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja. |
| Användarnamn |Den här egenskapen anger ett användarnamn om du använder Windows-autentisering. Ett exempel är **domainname\\användarnamn**. |Nej. |
| lösenord |Den här egenskapen anger ett lösenord för det användarkonto som du angav för användarnamnet. Välj **SecureString** att lagra connectionString-information på ett säkert sätt i Data Factory eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Nej. |
| connectVia | Detta [integreringskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Etablera den lokala integreringskörningen i samma virtuella nätverk som din hanterade instans. |Ja. |

>[!TIP]
>Du kan se felkoden ”UserErrorFailedToConnectToSqlServer” med ett meddelande som ”sessionens tidsgräns för databasen är XXX och har uppnåtts”. Om det här felet uppstår, lägger du till `Pooling=false` till din anslutningssträng och försök igen.

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

**Exempel 2: Använd Windows-autentisering**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för användning att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database Managed Instance-datauppsättningen.

För att kopiera data till och från Azure SQL Database Managed Instance, ange typegenskapen på datauppsättningen till **SqlServerTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **SqlServerTable**. | Ja. |
| tableName |Den här egenskapen är namnet på tabellen eller vyn i den databasinstans som den länkade tjänsten refererar till. | Nej för källa. Ja för mottagaren. |

**Exempel**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
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

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **SqlSource**. | Ja. |
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
        "name": "CopyFromSQLServer",
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
        "name": "CopyFromSQLServer",
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

Om du vill kopiera data till Azure SQL Database Managed Instance, ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitet mottagare:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **SqlSink**. | Ja. |
| WriteBatchSize |Den här egenskapen infogar data i SQL-tabell när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är heltal för hur många rader. |Nej (standard: 10,000). |
| writeBatchTimeout |Denna egenskap anger väntetiden batch Infoga åtgärd ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är för tidsintervallet. Ett exempel är ”00: 30:00”, som är 30 minuter. |Nej. |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopieringsaktiviteten ska köras innan du skriver data till den hantera instansen. Den anropas bara en gång per kopia som kör. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej. |
| sqlWriterStoredProcedureName |Det här namnet är för den lagrade proceduren som definierar hur du använder källdata i måltabellen. Exempel på procedurer är att göra upsertar eller transformeringar med egen affärslogik. <br/><br/>Den här lagrade proceduren är *anropas per batch*. För att göra en åtgärd som körs bara en gång och har inget att göra med källdata, till exempel ta bort eller trunkera använder den `preCopyScript` egenskapen. |Nej. |
| storedProcedureParameters |Dessa parametrar används för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrarna måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej. |
| sqlWriterTableType |Den här egenskapen anger ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga de data som ska kopieras med befintliga data. |Nej. |

> [!TIP]
> När data har kopierats till Azure SQL Database Managed Instance, läggs kopieringsaktiviteten data till tabellen mottagare som standard. Använd den lagrade proceduren i SqlSink för att utföra en upsert eller ytterligare affärslogik. Mer information finns i [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink).

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

**Exempel 2: Anropa en lagrad procedur vid kopiering för upsert**

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

## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen

I följande exempel kopierar data från en källtabellen med någon identity-kolumn till en måltabell med en identity-kolumn.

**Källtabellen**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Måltabell**

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

Observera att din käll- och tabell har olika schema. Måltabellen har en identitetskolumn. Ange egenskapen ”struktur” i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn i det här scenariot.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När data har kopierats till Azure SQL Database Managed Instance, kan en lagrad procedur konfigureras och anropas med ytterligare parametrar som du anger.

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer inte fungerar. Den används vanligtvis när en upsert (uppdatering + insert) eller extra bearbetning måste göras innan sista inmatningen av källdata i tabellen. Extra bearbetningen kan innehålla uppgifter såsom sammanfoga kolumner, leta upp ytterligare värden och infogning i flera tabeller.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i den hanterade instansen. Exemplet förutsätter att indata och tabellen mottagare ”marknadsföring” har tre kolumner: Profil-ID, tillstånd och kategori. Utför upsert baserat på kolumnen profil-ID och ansöka om endast en specifik kategori.

**Datauppsättningen för utdata**

```json
{
    "name": "SQLServerDataset",
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

Definiera avsnittet SqlSink i en Kopieringsaktivitet enligt följande:

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

Definiera den lagrade proceduren med samma namn som SqlWriterStoredProcedureName i din databas. Den hanterar indata från angivna källan och sammanfogar den i utdata i tabellen. Parameternamnet av tabelltypen i den lagrade proceduren är samma som ”tableName” som har definierats i datauppsättningen.

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

Definiera tabelltyp med samma namn som sqlWriterTableType i din databas. Schemat för tabelltypen är samma som det schema som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Den lagrade proceduren funktionen utnyttjar [tabellvärdeparametrar](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Om du skriver till den **pengar/Smallmoney** datatypen genom att anropa en lagrad procedur, värden kan ska avrundas. Ange den motsvarande datatypen i tabellvärdeparametrar som **Decimal** i stället för **pengar/Smallmoney** att åtgärda problemet. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Datatypsmappningen för Azure SQL Database Managed Instance

När data kopieras till och från Azure SQL Database Managed Instance, används följande mappningar från Azure SQL Database Managed Instance-datatyper till Azure Data Factory tillfälliga-datatyper. Läs hur kopieringsaktiviteten mappar från källtyp schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database Managed Instance-datatyp | Azure Data Factory tillfälliga datatyp |
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
| sql_variant |Objekt |
| text |Sträng, Char] |
| time |Tidsintervall |
| tidsstämpel |Byte] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

>[!NOTE]
> För information om vilka datatyper som mappar till decimaltyp tillfällig stöder för närvarande Azure Data Factory precision upp till 28. Överväg att konvertera till en sträng i en SQL-fråga om du har data som kräver precision som är större än 28.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
