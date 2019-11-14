---
title: Kopiera data till och från Azure SQL Database Hanterad instans med Azure Data Factory
description: Lär dig hur du flyttar data till och från Azure SQL Database Hanterad instans med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 9a70ecacdf10c985cabca8fa3ddf4314bf266afe
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075613"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database Hanterad instans med Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till och från Azure SQL Database Hanterad instans. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure SQL Database hanterade instans anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Du kan kopiera data från Azure SQL Database Hanterad instans till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till den hanterade instansen. En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här Azure SQL Database hanterade instans Connector:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token-autentisering med tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar data med hjälp av en SQL-fråga eller en lagrad procedur.
- Som mottagare lägger du till data i en mål tabell eller anropar en lagrad procedur med anpassad logik under kopieringen.

>[!NOTE]
>Azure SQL Database Hanterad instans [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) stöds inte av den här anslutningen nu. För att lösa problemet kan du använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin via en lokal integration Runtime. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) för hämtning av ODBC-drivrutiner och konfiguration av anslutnings strängar.

>[!NOTE]
>Tjänstens huvud namn och hanterade identitets autentiseringar stöds för närvarande inte av den här anslutningen. Undvik genom att välja en Azure SQL Database anslutning och ange servern manuellt för den hanterade instansen.

## <a name="prerequisites"></a>Krav

För att få åtkomst till den [offentliga slut punkten](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)för Azure SQL Database Hanterad instans kan du använda en Azure Data Factory hanterad Azure integration Runtime. Se till att aktivera den offentliga slut punkten och Tillåt även offentlig slut punkts trafik på nätverks säkerhets gruppen så att Azure Data Factory kan ansluta till databasen. Mer information finns i [den här vägledningen](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Om du vill komma åt den privata slut punkten för Azure SQL Database hanterade instanser konfigurerar du en lokal [integration runtime](create-self-hosted-integration-runtime.md) som har åtkomst till databasen. Om du etablerar integration runtime med egen värd i samma virtuella nätverk som den hanterade instansen kontrollerar du att integration runtime-datorn finns i ett annat undernät än din hanterade instans. Om du etablerar en integration runtime med egen värd i ett annat virtuellt nätverk än din hanterade instans kan du antingen använda en virtuell nätverks-peering eller ett virtuellt nätverk till en virtuell nätverks anslutning. Mer information finns i [ansluta ditt program till Azure SQL Database Hanterad instans](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Azure Data Factory entiteter som är speciella för den Azure SQL Database hanterade instans anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Azure SQL Database-hanterade instans:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureSqlMI**. | Ja |
| connectionString |Den här egenskapen anger **ConnectionString** -informationen som behövs för att ansluta till den hanterade instansen med hjälp av SQL-autentisering. Mer information finns i följande exempel. <br/>Standardporten är 1433. Om du använder Azure SQL Database Hanterad instans med en offentlig slut punkt anger du uttryckligen port 3342.<br>Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Azure Data Factory. Du kan också ange ett lösen ord i Azure Key Vault. Om det är SQL-autentisering, hämtar du `password`-konfigurationen från anslutnings strängen. Mer information finns i JSON-exemplet som följer tabellen och [lagrar autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Azure Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| tenant | Ange klient information, t. ex. domän namnet eller klient-ID: t, som ditt program finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) används för att ansluta till data lagret. Du kan använda en lokal integration runtime eller en Azure integration Runtime om din hanterade instans har en offentlig slut punkt och ger Azure Data Factory åtkomst till den. Om inget värde anges används standard Azure integration Runtime. |Ja |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel, respektive:

- [SQL-autentisering](#sql-authentication)
- [Azure AD Application token-autentisering: tjänstens huvud namn](#service-principal-authentication)
- [Azure AD Application token-autentisering: hanterade identiteter för Azure-resurser](#managed-identity)

### <a name="sql-authentication"></a>SQL-autentisering

**Exempel 1: Använd SQL-autentisering**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
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

**Exempel 2: Använd SQL-autentisering med ett lösen ord i Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
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

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda en tjänst objekts Azure AD-baserad autentisering av Azure AD-programtoken:

1. Följ stegen för att [etablera en Azure Active Directory administratör för din hanterade instans](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) från Azure Portal. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

3. [Skapa inloggningar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) för den Azure Data Factory hanterade identiteten. I SQL Server Management Studio (SSMS) ansluter du till din hanterade instans med ett SQL Server konto som är en **sysadmin**. I **Master** -databasen kör du följande T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Skapa inneslutna databas användare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) för den Azure Data Factory hanterade identiteten. Anslut till den databas som du vill kopiera data från eller kör följande T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Ge den Data Factory hanterade identiteten nödvändiga behörigheter som du vanligt vis använder för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Konfigurera en länkad Azure SQL Database Hanterad instans-tjänst i Azure Data Factory.

**Exempel: Använd tjänstens huvud namns autentisering**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten för Azure SQL Database Hanterad instans-autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till databasen med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering.

1. Följ stegen för att [etablera en Azure Active Directory administratör för din hanterade instans](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Skapa inloggningar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) för den Azure Data Factory hanterade identiteten. I SQL Server Management Studio (SSMS) ansluter du till din hanterade instans med ett SQL Server konto som är en **sysadmin**. I **Master** -databasen kör du följande T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Skapa inneslutna databas användare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) för den Azure Data Factory hanterade identiteten. Anslut till den databas som du vill kopiera data från eller kör följande T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Ge den Data Factory hanterade identiteten nödvändiga behörigheter som du vanligt vis använder för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Konfigurera en länkad Azure SQL Database Hanterad instans-tjänst i Azure Data Factory.

**Exempel: använder hanterad identitets autentisering**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

En fullständig lista över avsnitt och egenskaper som kan användas för att definiera data uppsättningar finns i artikeln data uppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av den Azure SQL Database hanterade instans data uppsättningen.

Följande egenskaper stöds för att kopiera data till och från Azure SQL Database Hanterad instans:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzureSqlMITable**. | Ja |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| table | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table`för nya arbets belastningar. | Nej för källa, Ja för mottagare |

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

En fullständig lista över avsnitt och egenskaper som kan användas för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av den Azure SQL Database hanterade instans källan och mottagaren.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Hanterad instans som källa

Om du vill kopiera data från Azure SQL Database Hanterad instans, stöds följande egenskaper i avsnittet Kopiera aktivitets Källa:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **SqlMISource**. | Ja |
| sqlReaderQuery |Den här egenskapen använder den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade procedur som läser data från käll tabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |De här parametrarna är för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och Skift läge för parametrarna måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Nej |

**Observera följande:**

- Om **sqlReaderQuery** har angetts för **SqlMISource**kör kopierings aktiviteten den här frågan mot den hanterade instans källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger någon av **sqlReaderQuery** -eller **sqlReaderStoredProcedureName** -egenskapen används kolumnerna som definierats i avsnittet "struktur" i JSON-datauppsättnings-JSON för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot den hanterade instansen. Om definitionen för data uppsättningen inte har "struktur" är alla kolumner markerade från tabellen.

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

**Exempel: använda en lagrad procedur**

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

**Definitionen av den lagrade proceduren**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Hanterad instans som en mottagare

> [!TIP]
> Lär dig mer om de Skriv beteenden, konfigurationer och bästa metoder som stöds av [bästa praxis för att läsa in data i Azure SQL Database Hanterad instans](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Om du vill kopiera data till Azure SQL Database Hanterad instans, stöds följande egenskaper i avsnittet Kopiera aktivitets mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **SqlMISink**. | Ja |
| writeBatchSize |Antal rader som ska infogas i SQL-tabellen *per batch*.<br/>Tillåtna värden är heltal för antalet rader. Som standard bestämmer Azure Data Factory dynamiskt rätt batchstorlek baserat på rad storleken.  |Nej |
| writeBatchTimeout |Den här egenskapen anger vänte tiden för åtgärden Infoga som ska slutföras innan tids gränsen uppnås.<br/>Tillåtna värden är för TimeSpan. Ett exempel är "00:30:00", som är 30 minuter. |Nej |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till den hanterade instansen. Den anropas bara en gång per kopierings körning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en mål tabell. <br/>Den här lagrade proceduren *anropas per batch*. För åtgärder som bara körs en gång och som inte har något att göra med källdata, till exempel ta bort eller trunkera, använder du egenskapen `preCopyScript`. | Nej |
| storedProcedureTableTypeParameterName |Parameter namnet för den tabell typ som anges i den lagrade proceduren.  |Nej |
| sqlWriterTableType |Det tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktiviteten gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga de data som kopieras med befintliga data. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-och värdepar. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |
| tableOption | Anger om mottagar tabellen ska skapas automatiskt om den inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när Sink anger lagrad procedur eller mellanlagrad kopia har kon figurer ATS i kopierings aktiviteten. Tillåtna värden är: `none` (standard) `autoCreate`. |Nej |

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
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exempel 2: anropa en lagrad procedur under kopiering**

Läs mer om att [anropa en lagrad procedur från en SQL mi-mottagare](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Bästa praxis för att läsa in data i Azure SQL Database Hanterad instans

När du kopierar data till Azure SQL Database Hanterad instans kan du behöva ett annat Skriv beteende:

- [Lägg till](#append-data): mina källdata har endast nya poster.
- [Upsert](#upsert-data): mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill läsa in hela dimensions tabellen på nytt varje tillfälle.
- [Skriv med anpassad logik](#write-data-with-custom-logic): Jag behöver extra bearbetning före den slutliga infogningen i mål tabellen. 

Se respektive avsnitt för hur du konfigurerar i Azure Data Factory och metod tips.

### <a name="append-data"></a>Lägg till data

Att lägga till data är standard beteendet för den här Azure SQL Database hanterade instans mottagarens koppling. Azure Data Factory skapar en Mass infogning för att skriva till tabellen effektivt. Du kan konfigurera källan och mottagaren efter detta i kopierings aktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data som ska kopieras använder du följande metod för att göra en upsert: 

- Använd först en [tillfällig tabell](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) för att överföra alla poster med hjälp av kopierings aktiviteten. Eftersom åtgärder mot temporära tabeller inte loggas kan du läsa in miljon tals poster på några sekunder.
- Kör en lagrad procedur aktivitet i Azure Data Factory om du vill tillämpa en [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) -eller INSERT-/Update-instruktion. Använd Temp-tabellen som källa för att utföra alla uppdateringar eller infogningar som en enskild transaktion. På det här sättet minskar antalet tur och utloggning-åtgärder. I slutet av den lagrade procedur aktiviteten kan den temporära tabellen trunkeras så att den är redo för nästa upsert-cykel.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopierings aktivitet** som är länkad till en **lagrad procedur aktivitet**. Den tidigare kopierar data från käll arkivet till en tillfällig tabell, till exempel **# #UpsertTempTable**som tabell namnet i data uppsättningen. Sedan anropar den senare en lagrad procedur för att koppla källdata från Temp-tabellen till mål tabellen och rensa Temp-tabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med SAMMANSLAGNINGs logik, som i följande exempel, som pekas från föregående lagrade procedur aktivitet. Anta att målet är **marknadsförings** tabellen med tre kolumner: **profil**, **State**och **Category**. Gör upsert baserat på kolumnen **profil** .

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

**Alternativ 2:** Du kan också välja att [anropa en lagrad procedur i en kopierings aktivitet](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden kör varje rad i käll tabellen i stället för att använda Mass infogning som standard metod i kopierings aktiviteten, vilket inte är lämpligt för storskaliga upsert.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i en kopierings aktivitets mottagare. I det här fallet kör Azure Data Factory skriptet först för varje kopierings aktivitet som körs. Sedan kör den kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med den senaste informationen anger du ett skript för att först ta bort alla poster innan du läser in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i avsnittet [upsert data](#upsert-data) . När du behöver använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen, för stor skala, kan du göra något av följande: 

- Läs in till en temporär tabell och anropa sedan en lagrad procedur.
- Anropa en lagrad procedur under kopieringen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till Azure SQL Database Hanterad instans kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar. Funktionen för lagrade procedurer utnyttjar [tabell värdes parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> När en lagrad procedur anropas bearbetas data raden efter rad i stället för med en Mass åtgärd som vi inte rekommenderar för storskalig kopiering. Lär dig mer från [bästa praxis för att läsa in data i Azure SQL Database Hanterad instans](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Du kan använda en lagrad procedur när inbyggda kopierings metoder inte fungerar. Ett exempel är när du vill använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen. Några extra bearbetnings exempel är när du vill slå samman kolumner, leta upp ytterligare värden och infoga data i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i den SQL Server databasen. Anta att indata och **marknadsförings** tabellen för mottagare har tre kolumner: **profil**, **State**och **Category**. Gör upsert baserat på kolumnen **profil** och Använd den bara för en viss kategori som kallas "ProduktA".

1. I databasen definierar du tabell typen med samma namn som **sqlWriterTableType**. Schemat för tabell typen är detsamma som det schema som returnerades av indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från din angivna källa och sammanfogar dem i utdatatabellen. Parameter namnet för tabell typen i den lagrade proceduren är samma som **TableName** som definierats i data uppsättningen.

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

3. I Azure Data Factory definierar du avsnittet **SQL mi Sink** i kopierings aktiviteten enligt följande:

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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Data typs mappning för Azure SQL Database Hanterad instans

När data kopieras till och från Azure SQL Database Hanterad instans används följande mappningar från Azure SQL Database hanterade instans data typer för att Azure Data Factory tillfälliga data typer. Information om hur kopierings aktiviteten mappar från käll schema och data typ till mottagaren finns i [mappningar för scheman och data typer](copy-activity-schema-and-type-mapping.md).

| Data typen Azure SQL Database Hanterad instans | Azure Data Factory data typen Interim |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolesk |
| char |String, Char[] |
| datum |DateTime |
| Datum/tid |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| decimaltal |decimaltal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |decimaltal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |decimaltal |
| nvarchar |String, Char[] |
| real |Enkel |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimaltal |
| sql_variant |Objekt |
| text |String, Char[] |
| time |TimeSpan |
| tidsstämpel |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> För data typer som mappas till en decimal-interimistisk-typ, stöds för närvarande Azure Data Factory precision upp till 28. Om du har data som kräver precision som är större än 28, bör du överväga att konvertera till en sträng i en SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
