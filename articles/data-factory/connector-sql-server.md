---
title: Kopiera data till/från SQL Server med Azure Data Factory | Microsoft Docs
description: Lär dig mer om hur du flyttar data till/från SQL Server-databas som är lokalt eller i en virtuell Azure-dator med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 7fc4fc42893ec839f3ffbe667e9fcfad944115f5
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053565"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopiera data till och från SQL Server med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuell version](connector-sql-server.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till en SQL Server-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från/till SQL Server-databas till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till SQL Server-databas. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SQL Server-anslutningen:

- SQL Server version 2016, 2014, 2012, 2008 R2, 2008 och 2005
- Kopiera data med hjälp av **SQL** eller **Windows** autentisering.
- Hämtar data med hjälp av SQL-fråga eller en lagrad procedur som källa.
- Som mottagare, bifoga data måltabellen eller anropa en lagrad procedur med egen kod vid kopiering.

## <a name="prerequisites"></a>Förutsättningar

Du måste ställa in en Self-hosted integrering Runtime om du vill använda data från en SQL Server-databas som inte är allmänt tillgänglig. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information. Integration Runtime innehåller en inbyggd drivrutin för SQL Server-databasen, därför behöver du inte installera en drivrutin manuellt när du kopierar data från/till SQL Server-databas.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter för anslutning av SQL Server-databas.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för SQL Server länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SqlServer** | Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. Se följande exempel och du lägger till utökas för att inkludera fler egenskaper, t.ex. AlwaysOn. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| Användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel 1: använder SQL-autentisering**

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

**Exempel 2: med Windows-autentisering**

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

Ange typegenskapen för dataset för att kopiera data från/till SQL Server-databas, **SqlServerTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **SqlServerTable** | Ja |
| tableName |Namnet på den tabell eller vy i SQL Server-databasinstansen som den länkade tjänsten refererar till. | Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server-källa och mottagare.

### <a name="sql-server-as-source"></a>SQL Server som källa

Om du vill kopiera data från SQL Server anger du datakällan i kopieringsaktiviteten till **SqlSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SqlSource** | Ja |
| sqlReaderQuery |Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

**Pekar på Observera:**

- Om den **sqlReaderQuery** har angetts för SqlSource, kopiera-aktivitet körs den här frågan mot SQL Server-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).
- Om du inte anger ”sqlReaderQuery” eller ”sqlReaderStoredProcedureName” kolumner som har definierats i avsnittet ”struktur” i datauppsättningen JSON används för att skapa en fråga (`select column1, column2 from mytable`) ska köras mot SQL Server. Om datauppsättningsdefinitionen inte har ”struktur”, markeras alla kolumner från tabellen.
- När du använder **sqlReaderStoredProcedureName**, du måste ange en **tableName** egenskap i datauppsättningen JSON.

**Exempel: använda SQL-fråga**

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

### <a name="sql-server-as-sink"></a>SQL Server som mottagare

Om du vill kopiera data till SQL Server anger du sink i kopieringsaktiviteten till **SqlSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **SqlSink** | Ja |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antalet rader). |Nej (standard: 10000) |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: timespan. Exempel ”: 00: 30:00” (30 minuter). |Nej |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan skrivningen av data i SQL Server. Det ska bara anropas en gång per kopia kör. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur ska gälla källdata i måltabellen, t.ex. vill upserts eller transformera med hjälp av egna affärslogik. <br/><br/>Observera den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärden som endast körs en gång och har ingenting att göra med källdata som t.ex. Ta bort/trunkera använder `preCopyScript` egenskapen. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är: namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabell-typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en temporär tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. |Nej |

> [!TIP]
> När du kopierar data till SQL Server, lägger kopieringsaktiviteten data till tabellen sink som standard. Använd den lagrade proceduren för att utföra en UPSERT eller ytterligare affärslogik i SqlSink. Lär dig mer information från [anropar lagrade proceduren för SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

Lär dig mer information från [anropar lagrade proceduren för SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

Det här avsnittet innehåller ett exempel som kopierar data från en källtabell med någon identity-kolumn till en måltabell med en identitetskolumn.

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

**Mål JSON-definitionen för datauppsättning**

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

Observera att som käll- och tabellen har olika schema (målet har en ytterligare kolumn med identity). I det här scenariot måste du ange **struktur** egenskap i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa lagrade procedur från SQL-mottagare

När du kopierar data till SQL Server-databas, ange en användare lagrade proceduren kan konfigureras och anropas med ytterligare parametrar.

En lagrad procedur kan användas när inbyggda kopiera mekanismer inte fyller syftet. Den används vanligtvis när upsert (insert + uppdatering) eller extra bearbetning (sammanslagning kolumner, söka efter ytterligare värden infogning i flera tabeller, etc.) måste göras innan sista infogningen av källdata i måltabellen.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i SQL Server-databasen. Under förutsättning att indata och tabellen ”marknadsföringsmaterial” mottagare har tre kolumner: profil-ID, tillstånd och kategori. Utföra upsert baserat på kolumnen ”ProfileID” och gäller endast för en specifik kategori.

**Datamängd för utdata**

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

Definiera avsnittet SqlSink i kopieringsaktiviteten på följande sätt.

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

Definiera den lagrade proceduren med samma namn som SqlWriterStoredProcedureName i din databas. Den hanterar indata från angiven källa och kopplad till utdatatabellen. Observera att parameternamnet på den lagrade proceduren bör vara detsamma som ”tabellnamn”-definieras i dataset.

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

## <a name="data-type-mapping-for-sql-server"></a>Datatypen mappning för SQLServer

När du kopierar data från/till SQL Server, används följande mappning från SQL Server-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| SQL Server-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |
| bigint |Int64 |
| Binär |Byte] |
| bitar |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attributet (varbinary(max)) |Byte] |
| Flyttal |Dubbel |
| image |Byte] |
| int |Int32 |
| Money |Decimal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |Decimal |
| nvarchar |Sträng, Char] |
| Verklig |Enkel |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objektet * |
| text |Sträng, Char] |
| time |TimeSpan |
| tidsstämpel |Byte] |
| tinyint |Int16 |
| Unik identifierare |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="troubleshooting-connection-issues"></a>Felsökning av anslutningsproblem med

1. Konfigurera SQL Server för att acceptera fjärranslutningar. Starta **SQL Server Management Studio**, högerklicka på **server**, och klicka på **egenskaper**. Välj **anslutningar** i listan och kontrollera **tillåta fjärranslutningar till servern**.

    ![Aktivera fjärranslutningar](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Se [konfigurerar fjärråtkomst Server Configuration Option](https://msdn.microsoft.com/library/ms191464.aspx) detaljerade anvisningar.

2. Starta **SQL Server Configuration Manager**. Expandera **SQL Server-nätverkskonfigurationen** för instansen och väljer **protokoll för MSSQLSERVER**. Du bör se protokoll i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och klicka på **aktivera**.

    ![Aktivera TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Se [aktivera eller inaktivera nätverksprotokoll Server](https://msdn.microsoft.com/library/ms191294.aspx) information och alternativa metoder för att aktivera TCP/IP-protokollet.

3. Dubbelklicka i samma fönster **TCP/IP** att starta **TCP/IP-egenskaper** fönster.
4. Växla till den **IP-adresser** fliken. Rulla Se **IPAll** avsnitt. Notera den ** TCP-Port ** (standard är **1433**).
5. Skapa en **regeln för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten.  
6. **Verifiera anslutning**: använda SQL Server Management Studio för att ansluta till SQL Server med hjälp av fullständigt kvalificerat namn, från en annan dator. Till exempel: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).
