---
title: Kopiera data till och från Azure SQL Database med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till Azure SQL Database eller SQL-databas till mottagarens datalager med Data Factory.
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
ms.date: 09/12/2018
ms.author: jingwang
ms.openlocfilehash: a4de054926339985b77f110bd00f77c5c8f7d705
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957997"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuell version](connector-azure-sql-database.md)

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från eller till Azure SQL Database. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som anger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från eller till Azure SQL Database till alla datalager för mottagare som stöds. Och du kan kopiera data från alla dataarkiv till Azure SQL Database. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här anslutningen för Azure SQL Database dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och autentisering med Azure Active Directory (Azure AD) Application-token med ett tjänstens huvudnamn eller hanterade identiteter för Azure-resurser.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik under kopieringen.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime kan du konfigurera en [Azure SQL-serverbrandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en lokal integration runtime kan du konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Det här intervallet inkluderar den datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för en Azure SQL Database-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

De här egenskaperna har stöd för en länkad Azure SQL Database-tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **AzureSqlDatabase**. | Ja |
| connectionString | Ange information som behövs för att ansluta till Azure SQL Database-instansen för den **connectionString** egenskapen. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime om ditt datalager finns i ett privat nätverk. Om den inte anges används standard Azure Integration Runtime. | Nej |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel, respektive:

- [SQL-autentisering](#sql-authentication)
- [Azure AD-token-autentisering: tjänstens huvudnamn](#service-principal-authentication)
- [Azure AD-token-autentisering: hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du når fel med felkod som ”UserErrorFailedToConnectToSqlServer” och visas som ”sessionens tidsgräns för databasen är XXX och har uppnåtts”., lägga till `Pooling=false` till din anslutningssträng och försök igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Länkad tjänst-exempel som använder SQL-autentisering

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda autentisering med enhetstoken en service principal-baserade Azure AD-program:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör måste vara en Azure AD-användare eller Azure AD-grupp, men den kan inte vara ett huvudnamn för tjänsten. Det här steget gör du så att det i nästa steg ska du använda en Azure AD-identitet för att skapa en oberoende databasanvändare för tjänsten huvudnamn.

1. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till databasen från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Bevilja behörigheter krävs för tjänstens huvudnamn** som du brukar för SQL-användare eller andra. Kör följande kod:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Konfigurera en länkad Azure SQL Database-tjänsten** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Länkad tjänst-exempel som använder autentisering av tjänstens huvudnamn

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar specifika data factory. Du kan använda den här tjänstidentitet för Azure SQL Database-autentisering. Den angivna datafabriken kan komma åt och kopiera data från eller till din databas med hjälp av den här identiteten.

Följ dessa steg om du vill använda MSI-baserad Azure AD-token autentisering:

1. **Skapa en grupp i Azure AD.** Göra factory MSI medlem i gruppen.
    
    1. Hitta tjänstidentiteten för data factory från Azure-portalen. Gå till din datafabrik **egenskaper**. Kopiera SERVICE IDENTITY-ID.
    
    1. Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulen. Logga in med den `Connect-AzureAD` kommando. Kör följande kommandon för att skapa en grupp och Lägg till data factory MSI som medlem.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```
    
1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen med MSI en administratörsroll kan du hoppa över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

1. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för Azure AD-gruppen. Ansluta till databasen från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Bevilja behörigheter krävs för Azure AD-gruppen** som du brukar för SQL-användare och andra. Exempelvis kör följande kod:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. **Konfigurera en länkad Azure SQL Database-tjänsten** i Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Länkad tjänst-exempel som använder MSI-autentisering

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database-datauppsättning.

För att kopiera data från eller till Azure SQL Database, ange den **typ** egenskapen på datauppsättningen till **AzureSqlTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskap måste anges till **AzureSqlTable**. | Ja |
| tableName | Namnet på tabellen eller vyn i Azure SQL Database-instans som den länkade tjänsten refererar till. | Ja |

#### <a name="dataset-properties-example"></a>Exempel med datauppsättningen egenskaper

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database-källa och mottagare.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL-databas som källa

För att kopiera data från Azure SQL Database, ange den **typ** -egenskapen i Kopieringsaktiviteten källan till **SqlSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **SqlSource**. | Ja |
| sqlReaderQuery | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |

### <a name="points-to-note"></a>Saker att Observera

- Om den **sqlReaderQuery** har angetts för den **SqlSource**, Kopieringsaktivitet körs den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur. Ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar parametrar för den lagrade proceduren.
- Om du inte anger något **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, kolumnerna som definieras i den **struktur** delen av datauppsättnings-JSON är van vid Skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL-databas. Om definitionen för datauppsättningen inte har den **struktur**, alla kolumner har valts från tabellen.
- När du använder **sqlReaderStoredProcedureName**, behöver du fortfarande ange en **tableName** egenskap i JSON-datauppsättningen.

#### <a name="sql-query-example"></a>Exempel för SQL-fråga

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>Lagrad procedur-exempel

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>Lagrad Procedurdefinition

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL-databas som mottagare

För att kopiera data till Azure SQL Database, ange den **typ** -egenskapen i aktiviteten kopiera mottagare till **SqlSink**. Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **SqlSink**. | Ja |
| WriteBatchSize | Infogar data i SQL-tabell när buffertstorleken når **writeBatchSize**.<br/> Det tillåtna värdet är **heltal** (antal rader). | Nej. Standardvärdet är 10 000. |
| writeBatchTimeout | Väntetid för batch Infoga åtgärden slutförs innan tidsgränsen uppnås.<br/> Det tillåtna värdet är **timespan**. Exempel ”: 00: 30:00” (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan du skriver data till Azure SQL Database. Det är bara anropas en gång per kopia som kör. Använd den här egenskapen för att rensa förinstallerade data. | Nej |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur du använder källdata till en måltabell. Ett exempel är att göra upsertar eller omvandla med egen affärslogik. <br/><br/>Den här lagrade proceduren är **anropas per batch**. Åtgärder som endast kör en gång och har inget samband med källdata, använda den `preCopyScript` egenskapen. Exempel åtgärder är delete och trunkera. | Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn och värdepar. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |
| sqlWriterTableType | Ange ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en tillfällig tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. | Nej |

> [!TIP]
> När du kopierar data till Azure SQL Database, Kopieringsaktivitet lägger till data tabellen mottagare som standard. Om du vill göra en upsert eller ytterligare affärslogik, använder du den lagrade proceduren i **SqlSink**. Lär dig mer från [anropa lagrade procedur från SQL Sink](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Lägg till data-exempel

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Anropa en lagrad procedur vid kopiering till upsert-exempel

Lär dig mer från [anropa lagrade procedur från SQL Sink](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

Det här avsnittet visar hur du kopierar data från en källa tabell utan en identity-kolumn till en måltabell med en identity-kolumn.

#### <a name="source-table"></a>Källtabellen

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Måltabell

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> Måltabellen har en identitetskolumn.

#### <a name="source-dataset-json-definition"></a>Källa dataset JSON-definition

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
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

#### <a name="destination-dataset-json-definition"></a>Mål dataset JSON-definition

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
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

> [!NOTE]
> Käll- och tabellen har olika schema. 

Målet har ytterligare en kolumn med en identitet. I det här scenariot måste du ange den **struktur** -egenskapen i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa lagrade procedur från SQL-mottagare

När du kopierar data till Azure SQL Database kan du konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer inte fungerar. De vanligtvis används när en upsert, insert och update eller extra bearbetningen måste göras innan sista inmatningen av källdata i måltabellen. Några extra bearbetningen exempel är merge kolumner, leta upp ytterligare värden och infogning i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i Azure SQL Database. Anta som indata och mottagaren **marknadsföring** varje tabell har tre kolumner: **profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn, och gäller endast för en specifik kategori.

#### <a name="output-dataset"></a>Datauppsättningen för utdata

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definiera den **SqlSink** avsnittet i Kopieringsaktiviteten:

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

I databasen, definierar du tabelltyp med samma namn som den **sqlWriterTableType**. Schemat för tabelltypen bör vara detsamma som det schema som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

Den lagrade proceduren funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Om du skriver till pengar/Smallmoney-datatypen som anropar lagrade proceduren, är det möjligt att värden ska avrundas. Ange den motsvarande datatypen i TVP som decimaltal i stället för pengar/Smallmoney för att minska. 

## <a name="data-type-mapping-for-azure-sql-database"></a>Datatypsmappningen för Azure SQL Database

När du kopierar data från eller till Azure SQL Database, används följande mappningar från Azure SQL Database-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Azure SQL-databas-datatyp | Data Factory tillfälliga datatyp |
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
| flyttal |Double-värde |
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
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
