---
title: Kopiera data till och från Azure SQL Database med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds källa datalager till Azure SQL Database eller SQL-databas till stöds sink datalager med hjälp av Data Factory.
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
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 9b2acf622f33f5d1748c503ab4765b72c3d921e2
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751586"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Version 1, GA](v1/data-factory-azure-sql-connector.md)
> * [Version 2, förhandsgranskning](connector-azure-sql-database.md)

Den här artikeln förklarar hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data från eller till Azure SQL Database. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över Kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory för närvarande under förhandsgranskning. Om du använder version 1 av tjänsten Data Factory allmänt tillgänglig (GA), se [Azure SQL Database-anslutningen i V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från eller till Azure SQL Database till alla stöds sink-datalagret. Och du kan kopiera data från alla datalager stöds källa till Azure SQL Database. En lista över datalager som stöds som datakällor eller sänkor av Kopieringsaktiviteten, finns det [datalager och format stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure SQL Database-anslutningen dessa funktioner:

- Kopiera data genom att använda SQL-autentisering och autentisering för Azure Active Directory (AD Azure) token med ett huvudnamn för tjänsten eller hanterade tjänsten identitet (MSI).
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som källa.
- Lägg till data i en måltabell eller anropa en lagrad procedur med egen kod under kopieringen som en mottagare.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime, konfigurera en [Azure SQL server-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en automatisk värdbaserade integration runtime, konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Det här intervallet inkluderar den datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till en Azure SQL Database-koppling.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Dessa egenskaper stöds för en Azure SQL Database länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** egenskapen måste anges till **AzureSqlDatabase**. | Ja |
| connectionString | Ange information som behövs för att ansluta till Azure SQL Database-instansen för den **connectionString** egenskapen. Markera det här fältet som en **SecureString** att lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckeln. Markera det här fältet som en **SecureString** att lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| klient | Ange information om klient (domain name eller klient ID) under där programmet finns. Hämta den håller musen i övre högra hörnet i Azure-portalen. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en egen värdbaserade integration runtime om datalager finns i ett privat nätverk. Om inget anges används standard-Azure Integration Runtime. | Nej |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel respektive:

- [SQL-autentisering](#sql-authentication)
- [Azure AD application tokenautentisering: tjänstens huvudnamn](#service-principal-authentication)
- [Azure AD application tokenautentisering: hanterade tjänstidentiteten](#managed-service-identity-authentication)

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

Följ dessa steg om du vill använda tokenautentisering service principal-baserad Azure AD-program:

1. **[Skapa ett program i Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Nyckeln för programmet
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för din Azure SQL server på Azure portal om du inte redan gjort det. Azure AD-administratör måste vara en Azure AD-användare eller Azure AD-grupp, men den kan inte vara ett huvudnamn för tjänsten. Det här steget gör du så att i nästa steg ska du använda en Azure AD-identitet för att skapa en innesluten databasanvändare för tjänsten huvudnamn.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till databasen från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ALTER för alla användare. Kör följande T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja behörigheter som krävs för tjänstens huvudnamn** som vanligt för SQL-användare eller andra. Kör följande kod:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurera en Azure SQL Database länkad tjänst** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Länkad tjänst-exempel som använder service principal autentisering

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

### <a name="managed-service-identity-authentication"></a>Den hanterade tjänstidentiteten autentisering

En datafabrik kan associeras med en [hanterade tjänstidentiteten](data-factory-service-identity.md) som representerar specifika data factory. Du kan använda den här tjänstidentiteten för Azure SQL Database-autentisering. Avsedda fabriken har åtkomst till och kopiera data från eller till din databas med hjälp av den här identiteten.

Följ dessa steg om du vill använda autentisering för MSI-baserad Azure AD-token:

1. **Skapa en grupp i Azure AD.** Se fabriken MSI medlem i gruppen.

    a. Hitta data factory tjänstidentiteten från Azure-portalen. Gå till din data factory **egenskaper**. Kopiera ID. tjänsten identitet

    b. Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Logga in med hjälp av den `Connect-AzureAD` kommando. Kör följande kommandon för att skapa en grupp och Lägg till data factory MSI som en medlem.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för din Azure SQL server på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen MSI en administratörsroll kan du hoppa över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för Azure AD-grupp. Ansluta till databasen från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ALTER för alla användare. Kör följande T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Ge Azure AD-grupp nödvändiga behörigheter** som vanligt för SQL-användare och andra. Till exempel köra följande kod:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. **Konfigurera en Azure SQL Database länkad tjänst** i Azure Data Factory.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database-datauppsättningen.

Om du vill kopiera data från eller till Azure SQL Database, ange den **typen** -egenskapen för datamängden som **AzureSqlTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** -egenskapen för dataset måste anges till **AzureSqlTable**. | Ja |
| tableName | Namnet på den tabell eller vy i Azure SQL Database-instans som den länkade tjänsten refererar till. | Ja |

#### <a name="dataset-properties-example"></a>DataSet egenskaper exempel

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database källa och mottagare.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database som källa

Om du vill kopiera data från Azure SQL Database, ange den **typen** egenskap i Kopieringsaktiviteten källan till **SqlSource**. Följande egenskaper stöds i aktiviteten kopiera **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** -egenskapen för aktiviteten kopiera källan måste anges till **SqlSource**. | Ja |
| sqlReaderQuery | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |

### <a name="points-to-note"></a>Information

- Om den **sqlReaderQuery** har angetts för den **SqlSource**, Kopieringsaktiviteten körs den här frågan mot Azure SQL Database-källa för att hämta data. Eller så kan du ange en lagrad procedur. Ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar den lagrade proceduren parametrar.
- Om du inte anger någon **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, kolumnerna som definieras i den **struktur** avsnitt i datauppsättningen JSON används för att Skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL-databasen. Om datauppsättningsdefinitionen inte har den **struktur**, markeras alla kolumner från tabellen.
- När du använder **sqlReaderStoredProcedureName**, du måste ange en **tableName** egenskap i datauppsättningen JSON.

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

#### <a name="stored-procedure-example"></a>Exempel för lagrad procedur

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

### <a name="stored-procedure-definition"></a>Definition av lagrad procedur

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database som sink

Om du vill kopiera data till Azure SQL Database, ange den **typen** egenskapen i aktiviteten kopiera sink till **SqlSink**. Följande egenskaper stöds i aktiviteten kopiera **sink** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** -egenskapen för Kopieringsaktiviteten sink måste anges till **SqlSink**. | Ja |
| writeBatchSize | Infogar data i SQL-tabellen när buffertstorleken når **writeBatchSize**.<br/> Det tillåtna värdet är **heltal** (antalet rader). | Nej. Standardvärdet är 10 000. |
| writeBatchTimeout | Väntetiden för batch Infoga åtgärden slutförs innan tidsgränsen uppnås.<br/> Det tillåtna värdet är **timespan**. Exempel ”: 00: 30:00” (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för aktiviteten kopiera köras innan skrivningen av data till Azure SQL Database. Metoden bara anropas en gång per kopia kör. Använd den här egenskapen Rensa förinstallerade data. | Nej |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur du använder källdata till en måltabell. Ett exempel är att göra upserts eller transformera genom att använda din egen affärslogik. <br/><br/>Den här lagrade proceduren är **anropas per batch**. Åtgärder som endast körs en gång och har ingenting att göra med källdata använder den `preCopyScript` egenskapen. Exempel åtgärder är delete och trunkera. | Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn och värdepar. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |
| sqlWriterTableType | Ange ett tabell-typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en tillfällig tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. | Nej |

> [!TIP]
> När du kopierar data till Azure SQL Database läggs Kopieringsaktiviteten data till tabellen mottagare som standard. Om du vill göra en upsert eller ytterligare affärslogik, Använd den lagrade proceduren i **SqlSink**. Lär dig mer information från [anropar lagrade procedur från SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Anropa en lagrad procedur vid kopiering till exempel upsert

Lär dig mer information från [anropar lagrade procedur från SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

Det här avsnittet visar hur du kopierar data från en källtabell utan en identity-kolumn till en måltabell med en identitetskolumn.

#### <a name="source-table"></a>Källtabellen

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Måltabellen

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

#### <a name="source-dataset-json-definition"></a>Källa JSON-definitionen för datauppsättning

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

#### <a name="destination-dataset-json-definition"></a>Mål JSON-definitionen för datauppsättning

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

Målet har en ytterligare kolumn med en identitet. I det här scenariot måste du ange den **struktur** egenskap i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa lagrade procedur från SQL-mottagare

När du kopierar data till Azure SQL Database, kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

Du kan använda en lagrad procedur när inbyggda kopiera mekanismer inte fungerar. De vanligtvis används när en upsert, insert och update eller extra bearbetning måste göras innan källdata slutliga infogas i måltabellen. Exempel extra bearbetning är merge kolumner, leta upp ytterligare värden och infogning i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i Azure SQL Database. Anta som inkommande data och sink **marknadsföring** tabell varje ha tre kolumner: **ProfileID**, **tillstånd**, och **kategori**. Gör upsert baserat på de **ProfileID** kolumn, och gäller endast för en specifik kategori.

#### <a name="output-dataset"></a>Datamängd för utdata

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

Definiera den **SqlSink** avsnitt i en Kopieringsaktivitet:

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

I databasen, definierar du den lagrade proceduren med samma namn som den **SqlWriterStoredProcedureName**. Den hanterar inkommande data från den angivna källan och sammanfogar till utdatatabellen. På den lagrade proceduren parameternamn måste vara samma som den **tableName** definieras i dataset.

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

I databasen, definierar den tabell med samma namn som den **sqlWriterTableType**. Schemat för tabelltypen ska vara densamma som schemat som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

Den lagrade proceduren funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Datatypsmappningen för Azure SQL Database

När du kopierar data från eller till Azure SQL Database, används följande mappningar från Azure SQL Database-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till sink.

| Azure SQL Database-datatyp | Data Factory tillfälliga datatyp |
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
| tinyint |Byte |
| Unik identifierare |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av Kopieringsaktiviteten i Azure Data Factory finns [datalager och format stöds](copy-activity-overview.md##supported-data-stores-and-formats).
