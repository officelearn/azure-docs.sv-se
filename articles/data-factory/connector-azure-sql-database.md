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
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 90126a607065bdb10e2ff81ce6ab52809ecc3f36
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273757"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj versionen av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuell version](connector-azure-sql-database.md)

Den här artikeln beskrivs hur du kopierar data till och från Azure SQL Database. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen för Azure SQL Database stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [stöd matrix för källa/mottagare](copy-activity-overview.md) tabell
- [Mappning av dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Mer specifikt stöder den här anslutningen för Azure SQL Database dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och autentisering med Azure Active Directory (Azure AD) Application-token med ett tjänstens huvudnamn eller hanterade identiteter för Azure-resurser.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Som en mottagare, lägga till data till en måltabell eller anropa en lagrad procedur med anpassad logik under kopieringen.

>[!NOTE]
>Azure SQL Database **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)** stöds inte nu av den här anslutningen. Du kan använda för att arbeta runt [allmän ODBC-anslutningsprogram](connector-odbc.md) och ODBC-drivrutinen via lokal Integration Runtime. Följ [den här vägledningen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) med ODBC-drivrutinen nedladdning och anslutningen sträng konfigurationer.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime kan du konfigurera en [Azure SQL-serverbrandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en lokal integration runtime kan du konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Det här intervallet inkluderar den datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för en Azure SQL Database-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

De här egenskaperna har stöd för en länkad Azure SQL Database-tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen måste anges till **AzureSqlDatabase**. | Ja |
| connectionString | Ange information som behövs för att ansluta till Azure SQL Database-instansen för den **connectionString** egenskapen. <br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också publicera nyckel för lösenord/tjänstens huvudnamn i Azure Key Vault, och om den är SQL-autentisering pull den `password` konfiguration av anslutningssträngen. Se JSON-exemplet nedan i tabellen och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime om ditt datalager finns i ett privat nätverk. Om den inte anges används standard Azure Integration Runtime. | Nej |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel, respektive:

- [SQL-autentisering](#sql-authentication)
- [Azure AD-token-autentisering: Tjänstens huvudnamn](#service-principal-authentication)
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

**Lösenord i Azure Key Vault:** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

Följ dessa steg om du vill använda autentisering med enhetstoken en service principal-baserade Azure AD-program:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör måste vara en Azure AD-användare eller Azure AD-grupp, men den kan inte vara ett huvudnamn för tjänsten. Det här steget gör du så att det i nästa steg ska du använda en Azure AD-identitet för att skapa en oberoende databasanvändare för tjänsten huvudnamn.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till databasen från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja behörigheter krävs för tjänstens huvudnamn** som du brukar för SQL-användare eller andra. Kör följande kod eller hänvisa till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurera en länkad Azure SQL Database-tjänsten** i Azure Data Factory.


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

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar specifika data factory. Du kan använda den här hanterad identitet för Azure SQL Database-autentisering. Den angivna datafabriken kan komma åt och kopiera data från eller till din databas med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitet-autentisering:

1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen med hanterad identitet en administratörsroll kan du hoppa över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

2. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för Data Factory hanterad identitet. Ansluta till databasen från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Bevilja behörigheter krävs för Data Factory hanterade identiteter** som du brukar för SQL-användare och andra. Kör följande kod eller hänvisa till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. **Konfigurera en länkad Azure SQL Database-tjänsten** i Azure Data Factory.

**Exempel:**

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

För att kopiera data från eller till Azure SQL Database, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskap måste anges till **AzureSqlTable**. | Ja |
| tableName | Namnet på tabellen eller vyn i Azure SQL Database-instans som den länkade tjänsten refererar till. | Nej för källa, Ja för mottagare |

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
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

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **SqlSource**. | Ja |
| sqlReaderQuery | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |

### <a name="points-to-note"></a>Saker att Observera

- Om den **sqlReaderQuery** har angetts för den **SqlSource**, Kopieringsaktivitet körs den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur. Ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar parametrar för den lagrade proceduren.
- Om du inte anger något **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, kolumnerna som definieras i den **struktur** delen av datauppsättnings-JSON är van vid Skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL-databas. Om definitionen för datauppsättningen inte har den **struktur**, alla kolumner har valts från tabellen.

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

> [!TIP]
> Läs mer om stöds skrivning beteenden, konfigurationer och bästa praxis från [bästa praxis för inläsning av data till Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

För att kopiera data till Azure SQL Database, ange den **typ** -egenskapen i aktiviteten kopiera mottagare till **SqlSink**. Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **SqlSink**. | Ja |
| writeBatchSize | Antalet rader som tillägg i SQL-tabell **per batch**.<br/> Det tillåtna värdet är **heltal** (antal rader). Som standard Data Factory dynamiskt kan bestämma lämpliga batchstorleken baserat på radstorleken. | Nej |
| writeBatchTimeout | Väntetid för batch Infoga åtgärden slutförs innan tidsgränsen uppnås.<br/> Det tillåtna värdet är **timespan**. Exempel: ”00: 30:00” (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan du skriver data till Azure SQL Database. Det är bara anropas en gång per kopia som kör. Använd den här egenskapen för att rensa förinstallerade data. | Nej |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur du använder källdata till en måltabell. <br/>Den här lagrade proceduren är **anropas per batch**. Åtgärder som endast kör en gång och har inget att göra med källdata, till exempel ta bort eller trunkera, använda den `preCopyScript` egenskapen. | Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn och värdepar. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |
| sqlWriterTableType | Ange ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en tillfällig tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. | Nej |

**Exempel 1: Lägg till data**

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

**Exempel 2: anropa en lagrad procedur vid kopiering**

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Bästa praxis för inläsning av data till Azure SQL Database

När du kopierar data till Azure SQL Database kan du kräva olika skrivning beteende:

- **[Lägg till](#append-data)** : min källdata har bara nya poster;
- **[Upsert](#upsert-data)** : min källdata har både infogningar och uppdateringar.
- **[Skriv över](#overwrite-entire-table)** : Jag vill fylla på hela dimensionstabellen varje gång;
- **[Skriva med anpassad logik](#write-data-with-custom-logic)** : Jag behöver ytterligare bearbetning innan den slutliga infogningen i måltabellen.

Finns i respektive avsnitt om hur du konfigurerar i ADF och bästa praxis.

### <a name="append-data"></a>Lägga till data

Det här är standardinställningen för den här anslutningen för Azure SQL Database-mottagare och ADF gör **massinfogning** att skriva till din tabell effektivt. Du kan helt enkelt konfigurera källa och mottagare i enlighet med detta i kopieringsaktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativet jag** (föreslås särskilt när du har stora mängder data att kopiera): den **metod för de flesta högpresterande** göra upsert är följande: 

- Det första utnyttja en [databasbegränsade tillfälliga tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) till massinläsning alla poster med hjälp av Kopieringsaktivitet. Åtgärder mot databasen begränsade temporära tabeller loggas inte, du kan läsa in flera miljoner poster i sekunder.
- Kör en lagringsprocedur-aktivitet i ADF att tillämpa en [sammanfoga](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (eller infoga/uppdatera)-instruktionen och Använd temporära tabellen som källa för att utföra alla uppdaterar eller infogar som en enda transaktion vilket minskar mängden görs och logga åtgärder. Temporär tabell kan trunkeras för att vara redo för nästa cykel för upsert i slutet av aktiviteten lagringsprocedur. 

I Azure Data Factory, till exempel skapa en pipeline med en **Kopieringsaktivitet** sammankedjade med en **lagringsprocedur-aktivitet** om åtgärden lyckades. Tidigare kopierar data från din källagringen till en tillfällig tabell i Azure SQL Database säg ” **##UpsertTempTable**” som tabellnamnet i datauppsättningen, sedan det senare anropar en lagringsprocedur för att slå samman information från den temporära tabellen till mål tabellen och rensa temporära tabellen.

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

Liknande enligt beskrivningen i [Upsert data](#upsert-data) avsnittet när du ska använda ytterligare bearbetning innan sista inmatningen av källdata i tabellen, kan du en) för stor skala in till en databas begränsade tillfällig tabell och sedan anropa lagrade procedurer och b) anropar en lagrad procedur vid kopiering.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Anropa lagrade procedur från SQL-mottagare

När du kopierar data till Azure SQL Database kan du konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar.

> [!TIP]
> Anropa lagrade proceduren bearbetar den data rad för rad i stället för massredigering, vilket inte rekommenderas för storskaliga kopia. Läs mer i [bästa praxis för inläsning av data till Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Du kan använda en lagrad procedur när inbyggd kopiera mekanismer som inte servar syfte, t.ex. tillämpa ytterligare bearbetning innan sista inmatningen av källdata i måltabellen. Några extra bearbetningen exempel är merge kolumner, leta upp ytterligare värden och infogning i mer än en tabell.

I följande exempel visas hur du använder en lagrad procedur för att göra en upsert i en tabell i Azure SQL Database. Anta som indata och mottagaren **marknadsföring** varje tabell har tre kolumner: **Profil-ID**, **tillstånd**, och **kategori**. Gör upsert baserat på den **profil-ID** kolumn, och gäller endast för en specifik kategori.

**Datauppsättningen för utdata:** ”tableName” ska vara samma typ parametern tabellnamnet i den lagrade proceduren (se lagrad procedur-skriptet nedan).

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

I databasen, definierar du tabelltyp med samma namn som den **sqlWriterTableType**. Schemat för tabelltypen bör vara detsamma som det schema som returnerades av dina indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

Den lagrade proceduren funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Egenskaper för mappning av dataflöde

Få mer detaljerad information från [source omvandling](data-flow-source.md) och [mottagare omvandling](data-flow-sink.md) i mappning dataflöde.

## <a name="data-type-mapping-for-azure-sql-database"></a>Datatypsmappningen för Azure SQL Database

När du kopierar data från eller till Azure SQL Database, används följande mappningar från Azure SQL Database-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Azure SQL-databas-datatyp | Data Factory tillfälliga datatyp |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> För typer mappas till decimaltyp tillfällig stöder för närvarande ADF precision upp till 28. Om du har data med precision som är större än 28, Överväg för att konvertera till en sträng i SQL-frågan.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
