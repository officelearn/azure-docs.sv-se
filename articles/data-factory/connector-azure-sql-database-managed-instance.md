---
title: Kopiera data till och från Azure SQL Database Managed Instance
description: Lär dig hur du flyttar data till och från Azure SQL Database Managed Instance med hjälp av Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238745"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database Managed Instance med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data till och från Azure SQL Database Managed Instance. Den bygger på artikeln [Kopiera aktivitetsöversikt](copy-activity-overview.md) som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure SQL Database Managed Instance-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Du kan kopiera data från Azure SQL Database Managed Instance till alla sink-datalager som stöds. Du kan också kopiera data från alla källdatalager som stöds till den hanterade instansen. En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här Azure SQL Database-hanterad instans-anslutningsappen stöder:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token autentisering med ett tjänsthuvudnamn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar du data med hjälp av en SQL-fråga eller en lagrad procedur.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik under kopiering.

>[!NOTE]
>Azure SQL Database Managed Instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) stöds inte av den här anslutningen nu. Du kan lösa dig genom att använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin via en självvärd integreringskörning. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) med konfigurationer för hämtning av ODBC-drivrutiner och anslutningssträngar.

## <a name="prerequisites"></a>Krav

Om du vill komma åt [den offentliga slutpunkten](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)för Azure SQL Database Managed Instance kan du använda en Azure Data Factory-hanterad Azure-integreringskörning. Se till att du aktiverar den offentliga slutpunkten och även tillåter offentlig slutpunktstrafik i nätverkssäkerhetsgruppen så att Azure Data Factory kan ansluta till databasen. Mer information finns i [den här vägledningen](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Om du vill komma åt den privata slutpunkten för Azure SQL Database Managed Instance konfigurerar du en [självvärd integreringskörning](create-self-hosted-integration-runtime.md) som kan komma åt databasen. Om du etablerar den självvärderade integrationskörningen i samma virtuella nätverk som den hanterade instansen kontrollerar du att programkörningsdatorn finns i ett annat undernät än den hanterade instansen. Om du etablerar din självvärderade integrationskörning i ett annat virtuellt nätverk än den hanterade instansen kan du använda antingen en virtuell nätverks peering eller ett virtuellt nätverk till virtuell nätverksanslutning. Mer information finns i [Ansluta ditt program till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Azure Data Factory-entiteter som är specifika för Azure SQL Database Managed Instance-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten för hanterad Azure SQL-databasderad instans:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureSqlMI**. | Ja |
| Connectionstring |Den här egenskapen anger den **connectionString-information** som behövs för att ansluta till den hanterade instansen med hjälp av SQL-autentisering. Mer information finns i följande exempel. <br/>Standardporten är 1433. Om du använder Azure SQL Database Managed Instance med en offentlig slutpunkt anger du uttryckligen port 3342.<br> Du kan också placera ett lösenord i Azure Key Vault. Om det är SQL-autentisering drar du ut konfigurationen `password` ur anslutningssträngen. Mer information finns i JSON-exemplet som följer tabellen och [Store-autentiseringsuppgifterna i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det säkert i Azure Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn |
| tenant | Ange klientinformation, till exempel domännamnet eller klient-ID: n, under vilken programmet finns. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn |
| connectVia (på) | Den här [integrationskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Du kan använda en självvärderad integrationskörning eller en Azure-integreringskörning om din hanterade instans har en offentlig slutpunkt och tillåter Azure Data Factory att komma åt den. Om inget anges används standardkörningen för Azure-integrering. |Ja |

För olika autentiseringstyper finns i följande avsnitt om förutsättningar respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- [Azure AD-programtokenautentisering: Tjänsthuvudnamn](#service-principal-authentication)
- [Azure AD-programtokenautentisering: Hanterade identiteter för Azure-resurser](#managed-identity)

### <a name="sql-authentication"></a>SQL-autentisering

**Exempel 1: använda SQL-autentisering**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: använda SQL-autentisering med ett lösenord i Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. Följ stegen för att [etablera en Azure Active Directory-administratör för din hanterade instans](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) från Azure-portalen. Anteckna programnamnet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

3. [Skapa inloggningar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) för den hanterade azure data factory-identiteten. I SQL Server Management Studio (SSMS) ansluter du till din hanterade instans med ett SQL Server-konto som är ett **sysadmin**. Kör **master** följande T-SQL i huvuddatabasen:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Skapa inneslutna databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) för den hanterade identiteten för Azure Data Factory. Kör följande T-SQL till databasen från eller till vilken du vill kopiera data: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Bevilja datafabrikens hanterade identitet som behövs som du normalt gör för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Konfigurera en azure SQL Database-länkad instanslänkade tjänst i Azure Data Factory.

**Exempel: använd autentisering av tjänstens huvudnamn**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika datafabriken. Du kan använda den här hanterade identiteten för Azure SQL Database Managed Instance-autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till databasen med hjälp av den här identiteten.

Så här använder du autentisering av hanterad identitet.

1. Följ stegen för att [etablera en Azure Active Directory-administratör för din hanterade instans](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Skapa inloggningar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) för den hanterade azure data factory-identiteten. I SQL Server Management Studio (SSMS) ansluter du till din hanterade instans med ett SQL Server-konto som är ett **sysadmin**. Kör **master** följande T-SQL i huvuddatabasen:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Skapa inneslutna databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) för den hanterade identiteten för Azure Data Factory. Kör följande T-SQL till databasen från eller till vilken du vill kopiera data: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Bevilja datafabrikens hanterade identitet som behövs som du normalt gör för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Konfigurera en azure SQL Database-länkad instanslänkade tjänst i Azure Data Factory.

**Exempel: använder autentisering av hanterad identitet**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i datauppsättningsartikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database Managed Instance-datauppsättningen.

Så här kopierar du data till och från Azure SQL Database Managed Instance:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **AzureSqlMITable**. | Ja |
| Schemat | Namnet på schemat. |Nej för källa, Ja för diskho  |
| tabell | Namn på tabellen/vyn. |Nej för källa, Ja för diskho  |
| tableName | Namn på tabellen/vyn med schema. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table`. | Nej för källa, Ja för diskho |

**Exempel**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database Managed Instance källa och mottagare.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Hanterad instans för Azure SQL Database som källa

Om du vill kopiera data från Azure SQL Database Managed Instance stöds följande egenskaper i avsnittet kopiera aktivitetskälla:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **SqlMISource**. | Ja |
| sqlReaderQuery |Den här egenskapen använder den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade proceduren som läser data från källtabellen. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. |Inga |
| storedProcedureParameters |Dessa parametrar är för den lagrade proceduren.<br/>Tillåtna värden är namn- eller värdepar. Parametrarnas namn och hölje skall stämma överens med namn och hölje för parametrarna för den lagrade proceduren. |Inga |
| isoleringNivå | Anger transaktionslåsningsbeteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted** (standard), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Se [det här dokumentet](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) för mer information. | Inga |

**Observera följande punkter:**

- Om **sqlReaderQuery** har angetts för **SqlMISource**körs den här frågan mot den hanterade instanskällan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger egenskapen **sqlReaderQuery** eller **sqlReaderStoredProcedureName** används kolumnerna som definieras i avsnittet "struktur" i datauppsättningen JSON för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot den hanterade instansen. Om datauppsättningsdefinitionen inte har "struktur" markeras alla kolumner i tabellen.

**Exempel: Använda en SQL-fråga**

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
                "type": "SqlMISource",
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
                "type": "SqlMISource",
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

**Den lagrade procedurens definition**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Hanterad instans för Azure SQL-databas som en mottagare

> [!TIP]
> Läs mer om skrivbeteenden, konfigurationer och metodtips som stöds från [Bästa praxis för inläsning av data i Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Om du vill kopiera data till Azure SQL Database Managed Instance stöds följande egenskaper i avsnittet kopiera aktivitetsmottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **SqlMISink**. | Ja |
| skriverBatchSize |Antal rader som ska infogas i SQL-tabellen *per batch*.<br/>Tillåtna värden är heltal för antalet rader. Som standard bestämmer Azure Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken.  |Inga |
| skriverBatchTimeout |Den här egenskapen anger väntetiden för batchinfogningen för att slutföras innan den time out.<br/>Tillåtna värden är för tidsspannet. Ett exempel är "00:30:00", vilket är 30 minuter. |Inga |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopieringsaktiviteten som ska köras innan data skrivs till den hanterade instansen. Det anropas bara en gång per kopieringskörning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en måltabell. <br/>Den här *lagrade*proceduren anropas per batch . För åtgärder som körs bara en gång och inte har något att göra `preCopyScript` med källdata, till exempel ta bort eller trunkera, använder du egenskapen. | Inga |
| storedProcedureTableTypeParameterName |Parameternamnet för den tabelltyp som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Tabelltypens namn som ska användas i den lagrade proceduren. Kopieringsaktiviteten gör de data som flyttas tillgängliga i en temporär tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- och värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. | Inga |
| tabellOption | Anger om sink-tabellen ska skapas automatiskt om det inte finns baserat på källschemat. Skapandet av automatisk tabell stöds inte när diskho anger lagrad procedur eller stegvis kopia konfigureras i kopieringsaktivitet. Tillåtna värden `none` är: `autoCreate`(standard), . |Inga |

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
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exempel 2: Anropa en lagrad procedur under kopiering**

Läs mer från [Anropa en lagrad procedur från en SQL MI-diskho](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Bästa praxis för att läsa in data i Azure SQL Database Managed Instance

När du kopierar data till Azure SQL Database Managed Instance kan du kräva olika skrivbeteende:

- [Lägg till](#append-data): Källdata har bara nya poster.
- [Upsert](#upsert-data): Källdata har både infogningar och uppdateringar.
- [Skriv över:](#overwrite-the-entire-table)Jag vill ladda om hela dimensionstabellen varje gång.
- [Skriv med anpassad logik:](#write-data-with-custom-logic)Jag behöver extra bearbetning innan den slutliga insättningen i måltabellen. 

Se respektive avsnitt för hur du konfigurerar i Azure Data Factory och metodtips.

### <a name="append-data"></a>Lägga till data

Lägga till data är standardbeteendet för den här Azure SQL Database Managed Instance sink-kopplingen. Azure Data Factory gör en massinfogning för att skriva till din tabell effektivt. Du kan konfigurera källan och handfatet i kopian.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data att kopiera använder du följande metod för att göra en upsert: 

- Använd först en [tillfällig tabell](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) för att massladda alla poster med hjälp av kopieringsaktiviteten. Eftersom åtgärder mot temporära tabeller inte loggas kan du läsa in miljontals poster på några sekunder.
- Kör en lagrad proceduraktivitet i Azure Data Factory för att tillämpa en [MERGE-](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) eller INSERT/UPDATE-sats. Använd temp-tabellen som källa för att utföra alla uppdateringar eller infogningar som en enda transaktion. På så sätt minskar antalet tur- och retur- och loggoperationer. I slutet av den lagrade proceduraktiviteten kan temporära tabellen trunkeras för att vara redo för nästa upsert-cykel.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopiera aktivitet** kedjad med en aktivitet **för lagrad procedur**. Den förstnämnda kopierar data från källarkivet till en tillfällig tabell, till exempel **##UpsertTempTable**, som tabellnamn i datauppsättningen. Sedan anropar den senare en lagrad procedur för att sammanfoga källdata från temporärtabellen till måltabellen och rensa temporärtabellen.

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

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i en kopieringsaktivitet](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden körs varje rad i källtabellen i stället för att använda massinfogning som standardmetod i kopieringsaktiviteten, vilket inte är lämpligt för storskalig upsert.

### <a name="overwrite-the-entire-table"></a>Skriva över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i en kopia aktivitetsmottagare. I det här fallet, för varje kopieringsaktivitet som körs, azure data factory kör skriptet först. Sedan körs kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med de senaste data anger du ett skript för att först ta bort alla poster innan du massinläsning av nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar dem som beskrivs i avsnittet [Upsert-data.](#upsert-data) När du behöver använda extra bearbetning innan den slutliga insättningen av källdata i måltabellen, för stor skala, kan du göra en av två saker: 

- Läs in till en tillfällig tabell och anropa sedan en lagrad procedur.
- Anropa en lagrad procedur under kopieringen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Anropa en lagrad procedur från en SQL-diskho

När du kopierar data till Azure SQL Database Managed Instance kan du också konfigurera och anropa en användarspecificerad lagrad procedur med ytterligare parametrar. Funktionen lagrad procedur utnyttjar [tabellvärdade parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> När du anropar en lagrad procedur bearbetas dataraden för rad i stället för med hjälp av en massåtgärd, som vi inte rekommenderar för storskalig kopia. Läs mer från [Bästa praxis för att läsa in data i Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Du kan använda en lagrad procedur när inbyggda kopieringsmekanismer inte tjänar syftet. Ett exempel är när du vill använda extra bearbetning innan den slutliga insättningen av källdata i måltabellen. Några extra bearbetningsexempel är när du vill sammanfoga kolumner, slå upp ytterligare värden och infoga data i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i SQL Server-databasen. Anta att indata och tabellen **för användningsrukonamnning** har tre kolumner vardera: **ProfileID**, **State**och **Category**. Gör upsert baserat på **ProfileID-kolumnen** och tillämpa den endast för en viss kategori som kallas "ProductA".

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

3. I Azure Data Factory definierar du avsnittet **SQL MI-mottagare** i kopieringsaktiviteten enligt följande:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Datatypsmappning för Hanterad Azure SQL-databas-instans

När data kopieras till och från Azure SQL Database Managed Instance används följande mappningar från Azure SQL Database Managed Instance-datatyper till Azure Data Factory interimsdatatyper. Mer information om hur kopieringsaktiviteten mappar från källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Datatyp för hanterad instans i Azure SQL Database | Interimdatatyp för Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
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
| tinyint |Int16 (int16) |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Röding[] |
| xml |Xml |

>[!NOTE]
> För datatyper som mappas till decimal interimstypen stöder Azure Data Factory precision upp till 28. Om du har data som kräver precision som är större än 28 kan du överväga att konvertera till en sträng i en SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Aktivitetsegenskaper för GetMetadata

Om du vill veta mer om egenskaperna kontrollerar du [GetMetadata-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [i Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
