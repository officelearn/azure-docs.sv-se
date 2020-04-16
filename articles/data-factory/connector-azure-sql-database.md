---
title: Kopiera och omvandla data i Azure SQL Database
description: Lär dig hur du kopierar data till och från Azure SQL Database och omvandlar data i Azure SQL Database med hjälp av Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: db55e685fb50c89eb850e1b9ee9dcf13d20fb614
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417546"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopiera och omvandla data i Azure SQL Database med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuell version](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från och till Azure SQL Database och använda dataflöde för att omvandla data i Azure SQL Database. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure SQL Database-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matristabell som stöds](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

För kopieringsaktivitet stöder den här Azure SQL Database-anslutningen dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token autentisering med ett tjänsthuvudnamn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar du data med hjälp av en SQL-fråga eller en lagrad procedur.
- Som en diskbänk, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik under kopian.

>[!NOTE]
>Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) stöds inte av den här anslutningen nu. Du kan lösa dig genom att använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin via en självvärd integreringskörning. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) med konfigurationer för hämtning av ODBC-drivrutiner och anslutningssträngar.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory-integreringskörningen konfigurerar du en [Azure SQL Server-brandvägg](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) så att Azure-tjänster kan komma åt servern.
> Om du kopierar data med hjälp av en självvärd integreringskörning konfigurerar du Azure SQL Server-brandväggen så att det tillåter lämpligt IP-intervall. Det här intervallet innehåller datorns IP som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Azure Data Factory-entiteter som är specifika för en Azure SQL Database-anslutningsapp.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Dessa egenskaper stöds för en Azure SQL Database-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typegenskapen** måste anges till **AzureSqlDatabase**. | Ja |
| Connectionstring | Ange information som behövs för att ansluta till Azure SQL Database-instansen för **egenskapen connectionString.** <br/>Du kan också placera en lösenords- eller tjänstnyckel i Azure Key Vault. Om det är SQL-autentisering drar du ut konfigurationen `password` ur anslutningssträngen. Mer information finns i JSON-exemplet som följer tabellen och [Store-autentiseringsuppgifterna i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det säkert i Azure Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn |
| tenant | Ange klientinformation, till exempel domännamnet eller klient-ID: n, under vilken programmet finns. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn |
| connectVia (på) | Den här [integrationskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Du kan använda Azure-integreringskörningen eller en självvärderad integrationskörning om ditt datalager finns i ett privat nätverk. Om inget anges används standardkörningen för Azure-integrering. | Inga |

För olika autentiseringstyper finns i följande avsnitt om förutsättningar respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- [Azure AD-programtokenautentisering: Tjänsthuvudnamn](#service-principal-authentication)
- [Azure AD-programtokenautentisering: Hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du har träffat ett fel med felkoden "UserErrorFailedToConnectToSqlServer" och ett meddelande som "Sessionsgränsen för databasen är XXX och har nåtts", lägger du `Pooling=false` till anslutningssträngen och försöker igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exempel på länkad tjänst som använder SQL-autentisering

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Lösenord i Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Så här använder du en principbaserad Azure AD-programtokenautentisering:

1. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) från Azure-portalen. Anteckna programnamnet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. [Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) för din Azure SQL Server på Azure-portalen om du inte redan har gjort det. Azure AD-administratören måste vara en Azure AD-användare eller Azure AD-grupp, men det kan inte vara ett tjänsthuvudnamn. Det här steget görs så att du i nästa steg kan använda en Azure AD-identitet för att skapa en innesluten databasanvändare för tjänstens huvudnamn.

3. [Skapa inneslutna databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) för tjänstens huvudnamn. Anslut till databasen från eller till vilken du vill kopiera data med hjälp av verktyg som SQL Server Management Studio, med en Azure AD-identitet som har minst ändra användarbehörighet. Kör följande T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Bevilja tjänstens huvudnamn nödvändiga behörigheter som vanligt för SQL-användare eller andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurera en Azure SQL Database-länkad tjänst i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exempel på länkad tjänst som använder autentisering av tjänstens huvudnamn

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Hanterade identiteter för Azure-resursautentisering

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika datafabriken. Du kan använda den här hanterade identiteten för Azure SQL Database-autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till databasen med hjälp av den här identiteten.

Så här använder du autentisering av hanterad identitet.

1. [Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) för din Azure SQL Server på Azure-portalen om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. Om du ger gruppen en hanterad identitet en administratörsroll hoppar du över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

2. [Skapa inneslutna databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) för den hanterade identiteten för Azure Data Factory. Anslut till databasen från eller till vilken du vill kopiera data med hjälp av verktyg som SQL Server Management Studio, med en Azure AD-identitet som har minst ändra användarbehörighet. Kör följande T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Bevilja datafabrikens hanterade identitet som behövs som du normalt gör för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfigurera en Azure SQL Database-länkad tjänst i Azure Data Factory.

**Exempel**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

Följande egenskaper stöds för Azure SQL Database-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för datauppsättningen måste anges till **AzureSqlTable**. | Ja |
| Schemat | Namnet på schemat. |Nej för källa, Ja för diskho  |
| tabell | Namn på tabellen/vyn. |Nej för källa, Ja för diskho  |
| tableName | Namn på tabellen/vyn med schema. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table`. | Nej för källa, Ja för diskho |

#### <a name="dataset-properties-example"></a>Exempel på egenskaper för Datauppsättning

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database-källan och diskhon.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database som källa

Om du vill kopiera data från Azure SQL Database stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till **AzureSqlSource**. "SqlSource"-typ stöds fortfarande för bakåtkompatibilitet. | Ja |
| sqlReaderQuery | Den här egenskapen använder den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. | Inga |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. | Inga |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- eller värdepar. Parametrarnas namn och hölje skall stämma överens med namn och hölje för parametrarna för den lagrade proceduren. | Inga |
| isoleringNivå | Anger transaktionslåsningsbeteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted** (standard), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Se [det här dokumentet](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) för mer information. | Inga |

**Punkter att notera:**

- Om **sqlReaderQuery** har angetts för **AzureSqlSource**körs den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger **antingen sqlReaderQuery** eller **sqlReaderStoredProcedureName**används kolumnerna som definieras i avsnittet "struktur" i datauppsättningen JSON för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot Azure SQL Database. Om datauppsättningsdefinitionen inte har "struktur" markeras alla kolumner i tabellen.

#### <a name="sql-query-example"></a>Exempel på SQL-fråga

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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Exempel på lagrad procedur

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
                "type": "AzureSqlSource",
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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL-databas som diskbänk

> [!TIP]
> Läs mer om skrivbeteenden, konfigurationer och metodtips som stöds från [Bästa praxis för inläsning av data i Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Om du vill kopiera data till Azure SQL Database stöds följande egenskaper i avsnittet kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för copy activity sink måste anges till **AzureSqlSink**. "SqlSink"-typ stöds fortfarande för bakåtkompatibilitet. | Ja |
| skriverBatchSize | Antal rader som ska infogas i SQL-tabellen *per batch*.<br/> Det tillåtna värdet är **heltal** (antal rader). Som standard bestämmer Azure Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken. | Inga |
| skriverBatchTimeout | Väntetiden för batchinsatsen ska slutföras innan den time out.<br/> Det tillåtna värdet är **tidsspann**. Ett exempel är "00:30:00" (30 minuter). | Inga |
| preCopyScript | Ange en SQL-fråga för kopieringsaktiviteten som ska köras innan du skriver data till Azure SQL Database. Det anropas bara en gång per kopieringskörning. Använd den här egenskapen för att rensa de förinstallerade data. | Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en måltabell. <br/>Den här *lagrade*proceduren anropas per batch . För åtgärder som körs bara en gång och inte har något att göra `preCopyScript` med källdata, till exempel ta bort eller trunkera, använder du egenskapen. | Inga |
| storedProcedureTableTypeParameterName |Parameternamnet för den tabelltyp som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Tabelltypens namn som ska användas i den lagrade proceduren. Kopieringsaktiviteten gör de data som flyttas tillgängliga i en temporär tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- och värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. | Inga |
| tabellOption | Anger om sink-tabellen ska skapas automatiskt om det inte finns baserat på källschemat. Skapandet av automatisk tabell stöds inte när diskho anger lagrad procedur eller stegvis kopia konfigureras i kopieringsaktivitet. Tillåtna värden `none` är: `autoCreate`(standard), . |Inga |
| inaktiveraMetricsCollection | Data Factory samlar in mått som Azure SQL Database DTUs för kopiering prestandaoptimering och rekommendationer. Om du har något att `true` göra med det här beteendet anger du att den ska stängas av. | Nej (standard `false`är) |

**Exempel 1: Lägga till data**

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
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exempel 2: Anropa en lagrad procedur under kopiering**

Läs mer från [Anropa en lagrad procedur från en SQL-diskho](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Bästa praxis för att läsa in data i Azure SQL Database

När du kopierar data till Azure SQL Database kan du behöva olika skrivbeteende:

- [Lägg till](#append-data): Källdata har bara nya poster.
- [Upsert](#upsert-data): Källdata har både infogningar och uppdateringar.
- [Skriv över:](#overwrite-the-entire-table)Jag vill ladda om en hel dimensionstabell varje gång.
- [Skriv med anpassad logik:](#write-data-with-custom-logic)Jag behöver extra bearbetning innan den slutliga insättningen i måltabellen.

Se respektive avsnitt om hur du konfigurerar i Azure Data Factory och metodtips.

### <a name="append-data"></a>Lägga till data

Lägga till data är standardbeteendet för den här Azure SQL Database sink-anslutningen. Azure Data Factory gör en massinfogning för att skriva till din tabell effektivt. Du kan konfigurera källan och handfatet i kopian.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data att kopiera använder du följande metod för att göra en upsert: 

- Använd först en [tillfällig tabell för att massinhysa](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) alla poster med hjälp av kopieringsaktiviteten. Eftersom åtgärder mot tillfälliga tabeller i databasen inte loggas kan du läsa in miljontals poster på några sekunder.
- Kör en lagrad proceduraktivitet i Azure Data Factory för att tillämpa en [MERGE-](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) eller INSERT/UPDATE-sats. Använd temp-tabellen som källa för att utföra alla uppdateringar eller infogningar som en enda transaktion. På så sätt minskar antalet tur- och retur- och loggoperationer. I slutet av den lagrade proceduraktiviteten kan temporära tabellen trunkeras för att vara redo för nästa upsert-cykel.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopiera aktivitet** kedjad med en aktivitet **för lagrad procedur**. Den förstnämnda kopierar data från källarkivet till en tillfällig tabell i Azure SQL Database, till exempel **##UpsertTempTable**, som tabellnamn i datauppsättningen. Sedan anropar den senare en lagrad procedur för att sammanfoga källdata från temporärtabellen till måltabellen och rensa temporärtabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med MERGE-logik, till exempel följande exempel, som pekas på från föregående lagrad proceduraktivitet. Anta att målet är tabellen **Marknadsföring** med tre kolumner: **ProfileID**, **State**och **Category**. Gör upsert baserat på **profileid-kolumnen.**

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

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i kopieringsaktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden körs varje `writeBatchSize` batch (som styrs av egenskapen) i källtabellen i stället för att använda massinfogning som standardmetod i kopieringsaktiviteten.

### <a name="overwrite-the-entire-table"></a>Skriva över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i kopiaaktivitetsmottagaren. I det här fallet, för varje kopieringsaktivitet som körs, azure data factory kör skriptet först. Sedan körs kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med de senaste data anger du ett skript för att först ta bort alla poster innan du massinläsning av nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar dem som beskrivs i avsnittet [Upsert-data.](#upsert-data) När du behöver använda extra bearbetning innan den slutliga insättningen av källdata i måltabellen, för stor skala, kan du göra en av två saker:

- Läs in till en tillfällig tabell för databasscope och anropa sedan en lagrad procedur. 
- Anropa en lagrad procedur under kopieringen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Anropa en lagrad procedur från en SQL-diskho

När du kopierar data till Azure SQL Database kan du också konfigurera och anropa en användarspecificerad lagrad procedur med ytterligare parametrar i varje batch i källtabellen. Funktionen lagrad procedur utnyttjar [tabellvärdade parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

Du kan använda en lagrad procedur när inbyggda kopieringsmekanismer inte tjänar syftet. Ett exempel är när du vill använda extra bearbetning innan den slutliga insättningen av källdata i måltabellen. Några extra bearbetningsexempel är när du vill sammanfoga kolumner, slå upp ytterligare värden och infoga i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i Azure SQL Database. Anta att indata och tabellen **för användningsrukonamnning** har tre kolumner vardera: **ProfileID**, **State**och **Category**. Gör upsert baserat på **ProfileID-kolumnen** och tillämpa den endast för en viss kategori som kallas "ProductA".

1. I databasen definierar du tabelltypen med samma namn som **sqlWriterTableType**. Tabelltypens schema är detsamma som schemat som returneras av indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från den angivna källan och sammanfogas till utdatatabellen. Parameternamnet för tabelltypen i den lagrade proceduren är detsamma som **tabellnamn** som definierats i datauppsättningen.

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

3. I Azure Data Factory definierar du **avsnittet SQL sink** i kopieringsaktiviteten enligt följande:

    ```json
    "sink": {
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du omvandlar data i mappning av dataflöde kan du läsa och skriva till tabeller från Azure SQL Database. Mer information finns i [källomvandlingen](data-flow-source.md) och [sink-omvandlingen](data-flow-sink.md) vid mappning av dataflöden.

### <a name="source-transformation"></a>Omvandling av källa

Inställningar som är specifika för Azure SQL Database är tillgängliga på fliken **Källalternativ** i källomvandlingen. 

**Ingång:** Välj om du pekar källan mot ```Select * from <table-name>```en tabell (motsvarande) eller anger en anpassad SQL-fråga.

**Fråga**: Om du väljer Fråga i indatafältet anger du en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i datauppsättningen. **Order** by-satser stöds inte här, men du kan ange en fullständig SELECT FROM-sats. Du kan också använda användardefinierade tabellfunktioner. **välj * från udfGetData()** är en UDF i SQL som returnerar en tabell. Den här frågan skapar en källtabell som du kan använda i dataflödet. Att använda frågor är också ett bra sätt att minska rader för testning eller för uppslag. 

* SQL-exempel:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchstorlek:** Ange en batchstorlek för att segmenta stora data i läsningar.

**Isoleringsnivå**: Standardvärdet för SQL-källor vid mappning av dataflöde läss inte. Du kan ändra isoleringsnivån här till ett av dessa värden:
* Läs bekräftad
* Läs oengagerad
* Repeterbar läsning
* Serialiserbar
* Ingen (ignorera isoleringsnivå)

![Isoleringsnivå](media/data-flow/isolationlevel.png "Isoleringsnivå")

### <a name="sink-transformation"></a>Sink omvandling

Inställningar som är specifika för Azure SQL Database är tillgängliga på fliken **Inställningar** i sink-omvandlingen.

**Uppdateringsmetod:** Bestämmer vilka åtgärder som är tillåtna på databasens mål. Standard är att endast tillåta infogningar. För att uppdatera, upsert eller ta bort rader krävs en omvandling på en rad för att tagga rader för dessa åtgärder. För uppdateringar, upserts och borttagningar måste en nyckelkolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

![Viktiga kolumner](media/data-flow/keycolumn.png "Viktiga kolumner")

Kolumnnamnet som du väljer som nyckel här kommer att användas av ADF som en del av den efterföljande uppdateringen, upsert, delete. Därför måste du välja en kolumn som finns i sink-mappningen. Om du inte vill skriva värdet till den här nyckelkolumnen klickar du på "Hoppa över att skriva nyckelkolumner".

**Tabellåtgärd:** Bestämmer om alla rader ska återskapas eller tas bort från måltabellen innan du skriver.
* Ingen: Ingen åtgärd kommer att göras i tabellen.
* Återskapa: Tabellen kommer att släppas och återskapas. Krävs om du skapar en ny tabell dynamiskt.
* Trunkera: Alla rader från måltabellen tas bort.

**Batchstorlek:** Styr hur många rader som skrivs i varje bucket. Större batchstorlekar förbättrar komprimerings- och minnesoptimeringen, men risken är på minnesundantag när data cachelagrar.

**Före- och post-SQL-skript:** Ange flerradiga SQL-skript som ska köras före (förbearbetning) och efter (efterbearbetning) data skrivs till din Sink-databas

![för- och efter-SQL-bearbetningsskript](media/data-flow/prepost1.png "SQL-bearbetningsskript")

## <a name="data-type-mapping-for-azure-sql-database"></a>Datatypsmappning för Azure SQL Database

När data kopieras från eller till Azure SQL Database används följande mappningar från Azure SQL Database-datatyper till Azure Data Factory interimsdatatyper. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Datatyp för Azure SQL Database | Interimdatatyp för Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolesk |
| char |Sträng, Röding[] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| Datumtidsdatum |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary(max)) |Byte[] |
| Float (Flyttal) |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, Röding[] |
| ntext |Sträng, Röding[] |
| numeric |Decimal |
| nvarchar |Sträng, Röding[] |
| real |Enkel |
| Rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 (int16) |
| smallmoney |Decimal |
| Sql_variant |Objekt |
| text |Sträng, Röding[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Röding[] |
| xml |Xml |

>[!NOTE]
> För datatyper som mappas till decimal interimstypen stöder Azure Data Factory precision upp till 28. Om du har data med precision som är större än 28 kan du överväga att konvertera till en sträng i SQL-frågan.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Aktivitetsegenskaper för GetMetadata

Om du vill veta mer om egenskaperna kontrollerar du [GetMetadata-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [i Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
