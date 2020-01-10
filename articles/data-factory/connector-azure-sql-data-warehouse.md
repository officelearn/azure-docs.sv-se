---
title: Kopiera och transformera data i Azure Synapse Analytics
description: Lär dig hur du kopierar data till och från Azure Synapse Analytics och transformerar data i Azure Synapse Analytics med hjälp av Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: c23bba777a7a29f572764914cfa505d2b668683d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830419"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) med hjälp av Azure Data Factory 

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuell version](connector-azure-sql-data-warehouse.md)

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Synapse Analytics och använda data flöden för att transformera data i Azure Data Lake Storage Gen2. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Synapse Analytics-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

För kopierings aktivitet stöder den här Azure Synapse Analytics-anslutaren dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och autentisering med Azure Active Directory (Azure AD) Application-token med ett tjänstens huvudnamn eller hanterade identiteter för Azure-resurser.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Läs in data med hjälp av [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) -eller [copy-instruktionen](#use-copy-statement) (för hands version) eller Mass infogning i som mottagare. Vi rekommenderar PolyBase-eller COPY-instruktion (för hands version) för bättre kopierings prestanda.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime kan du konfigurera en [Azure SQL-serverbrandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en lokal integration runtime kan du konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Detta intervall inkluderar datorns IP-adress som används för att ansluta till Azure Synapse Analytics.

## <a name="get-started"></a>Kom i gång

> [!TIP]
> Använd PolyBase för att läsa in data i Azure Synapse Analytics för att uppnå bästa prestanda. Mer information finns i avsnittet [använda PolyBase för att läsa in data i avsnittet om Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory entiteter som är speciella för en Azure Synapse Analytics-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för en länkad Azure Synapse Analytics-tjänst:

| Egenskap            | Beskrivning                                                  | Krävs                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| typ                | Type-egenskapen måste anges till **AzureSqlDW**.             | Ja                                                          |
| connectionString    | Ange den information som krävs för att ansluta till Azure Synapse Analytics-instansen för egenskapen **ConnectionString** . <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ange lösen ord/tjänstens huvud namn i Azure Key Vault, och om det är SQL-autentisering hämtar du `password`-konfigurationen från anslutnings strängen. Se JSON-exemplet under tabellen och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja                                                          |
| servicePrincipalId  | Ange programmets klient-ID.                         | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| tenant              | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia          | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. | Inga                                                           |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel, respektive:

- [SQL-autentisering](#sql-authentication)
- Azure AD-token-autentisering: [tjänstens huvudnamn](#service-principal-authentication)
- Azure AD-token-autentisering: [hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du når fel med felkod som ”UserErrorFailedToConnectToSqlServer” och visas som ”sessionens tidsgräns för databasen är XXX och har uppnåtts”., lägga till `Pooling=false` till din anslutningssträng och försök igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Länkad tjänst-exempel som använder SQL-autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Lösen ord i Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Följ dessa steg om du vill använda tokenautentisering för service principal-baserade Azure AD-program:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören har fullständig åtkomst till databasen.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja behörigheter krävs för tjänstens huvudnamn** som du brukar för SQL-användare eller andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurera en länkad Azure Synapse Analytics-tjänst** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Länkad tjänst-exempel som använder autentisering av tjänstens huvudnamn

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identity"></a> Hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika fabriken. Du kan använda den här hanterade identiteten för Azure Synapse Analytics-autentisering. Den angivna datafabriken kan komma åt och kopieringsdata från eller till dina data warehouse med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering:

1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören har fullständig åtkomst till databasen.

2. **[Skapa inneslutna databas användare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** för den Data Factory hanterade identiteten. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Ge den Data Factory hanterade identiteten nödvändiga behörigheter** som du vanligt vis använder för SQL-användare och andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfigurera en länkad Azure Synapse Analytics-tjänst** i Azure Data Factory.

**Exempel:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. 

Följande egenskaper stöds för data uppsättningen för Azure Synapse Analytics:

| Egenskap  | Beskrivning                                                  | Krävs                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Den **typ** egenskap måste anges till **AzureSqlDWTable**. | Ja                         |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| table | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table`för nya arbets belastningar. | Nej för källa, Ja för mottagare |

#### <a name="dataset-properties-example"></a>Exempel med datauppsättningen egenskaper

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Synapse Analytics-källan och mottagare.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics som källa

Om du vill kopiera data från Azure Synapse Analytics ställer du in egenskapen **Type** i källan för kopierings aktiviteten till **SqlDWSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap                     | Beskrivning                                                  | Krävs |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Inga       |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Inga       |
| storedProcedureParameters    | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Inga       |

**Exempel: använda SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**Exempel på lagrad procedur:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics som mottagare

Azure Data Factory stöder tre sätt att läsa in data i SQL Data Warehouse.

![Kopierings alternativ för SQL DW-mottagare](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Använd PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Använd COPY-instruktion (förhands granskning)](#use-copy-statement)
- Använd Mass infogning

Det snabbaste och mest skalbara sättet att läsa in data är genom [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) eller [kopierings instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version).

Om du vill kopiera data till Azure SQL Data Warehouse, ange Mottagartyp i Kopieringsaktiviteten till **SqlDWSink**. Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Anger om PolyBase ska användas för att läsa in data i SQL Data Warehouse. `allowCopyCommand` och `allowPolyBase` kan inte båda vara sanna. <br/><br/>Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnitt för begränsningar och information.<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard). | Nej.<br/>Använd när du använder PolyBase.     |
| polyBaseSettings  | En grupp egenskaper som kan anges när egenskapen `allowPolybase` har värdet **True**. | Nej.<br/>Använd när du använder PolyBase. |
| allowCopyCommand | Anger om [kopierings instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version) ska användas för att läsa in data i SQL Data Warehouse. `allowCopyCommand` och `allowPolyBase` kan inte båda vara sanna. <br/><br/>Se [använda Copy-instruktionen för att läsa in data i Azure SQL Data Warehouse](#use-copy-statement) avsnittet för begränsningar och information.<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard). | Nej.<br>Använd när du använder COPY. |
| copyCommandSettings | En grupp egenskaper som kan anges när `allowCopyCommand`-egenskapen har angetts till TRUE. | Nej.<br/>Använd när du använder COPY. |
| writeBatchSize    | Antal rader som ska infogas i SQL-tabellen **per batch**.<br/><br/>Det tillåtna värdet är **heltal** (antal rader). Som standard bestämmer Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. | Nej.<br/>Använd när du använder Mass infogning.     |
| writeBatchTimeout | Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/><br/>Det tillåtna värdet är **timespan**. Exempel ”: 00: 30:00” (30 minuter). | Nej.<br/>Använd när du använder Mass infogning.        |
| preCopyScript     | Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan du skriver data till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen för att rensa förinstallerade data. | Inga                                            |
| tableOption | Anger om mottagar tabellen ska skapas automatiskt om den inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när mellanlagrad kopiering har kon figurer ATS i kopierings aktiviteten. Tillåtna värden är: `none` (standard) `autoCreate`. |Inga |
| disableMetricsCollection | Data Factory samlar in mått som SQL Data Warehouse DWU: er för att kopiera prestanda optimering och rekommendationer. Om du är orolig för det här beteendet anger du `true` att inaktivera det. | Nej (standard är `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse sink exempel

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använda PolyBase för att läsa in data i Azure SQL Data Warehouse

Att använda [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in stora mängder data i Azure Synapse Analytics med högt data flöde. Du ser en stor vinst i dataflödet med PolyBase i stället för BULKINSERT standardmekanismen. En genom gång med ett användnings fall finns i [läsa in 1 TB i Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Om dina källdata finns i **Azure Blob, Azure Data Lake Storage gen1 eller Azure Data Lake Storage Gen2**och **formatet är PolyBase-kompatibelt**, kan du använda kopierings aktivitet för att direkt anropa PolyBase för att låta Azure SQL Data Warehouse hämta data från källan. Mer information finns i  **[dirigera kopiera genom att använda PolyBase](#direct-copy-by-using-polybase)** .
* Om dina källdatalagret och format ursprungligen inte stöds av PolyBase, använder du den **[mellanlagrad kopiering genom att använda PolyBase](#staged-copy-by-using-polybase)** funktionen i stället. Funktionen mellanlagrad kopiering ger dig också bättre genomströmning. Den konverterar automatiskt data till PolyBase-kompatibelt format, lagrar data i Azure Blob Storage. sedan anropar PolyBase för att läsa in data i SQL Data Warehouse.

>[!TIP]
>Lär dig mer om [metod tips för att använda PolyBase](#best-practices-for-using-polybase).

Följande PolyBase-inställningar stöds under `polyBaseSettings` i kopierings aktiviteten:

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Anger det tal eller procentandelen rader som kan avvisas innan frågan inte kunde köras.<br/><br/>Läs mer om Polybases avvisningsalternativen i avsnittet argument i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Tillåtna värden är 0 (standard), 1, 2, osv. | Inga                                            |
| rejectType        | Anger om den **rejectValue** alternativet är ett exakt värde eller en procentandel.<br/><br/>Tillåtna värden är **värdet** (standard) och **procent**. | Inga                                            |
| rejectSampleValue | Anger antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade raden.<br/><br/>Tillåtna värden är 1, 2, osv. | Ja, om den **rejectType** är **procent**. |
| useTypeDefault    | Anger hur du hanterar värden som saknas i avgränsade textfiler när PolyBase hämtar data från textfilen.<br/><br/>Mer information om den här egenskapen från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard).<br><br> | Inga                                            |

### <a name="direct-copy-by-using-polybase"></a>Direct kopiera genom att använda PolyBase

SQL Data Warehouse PolyBase stöder direkt Azure Blob, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet använder du PolyBase för att kopiera direkt från käll data lagret till Azure Synapse Analytics. Annars kan du använda [mellanlagrad kopiering genom att använda PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Om du vill kopiera data på ett effektivt sätt till SQL Data Warehouse kan du läsa mer från [Azure Data Factory gör det ännu enklare och bekvämt att få insikter från data när du använder data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, Azure Data Factory kontrollerar du inställningarna och faller automatiskt tillbaka till BULKINSERT mekanism för dataförflyttning.

1. Den **länkade käll tjänsten** har följande typer och autentiseringsmetoder:

    | Typ av käll data lager som stöds                             | Typ av källtyp som stöds                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Autentisering av konto nyckel, hanterad identitets autentisering |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autentisering av tjänstens huvudnamn                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autentisering av konto nyckel, hanterad identitets autentisering |

    >[!IMPORTANT]
    >Om din Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig mer om de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitets verifiering](connector-azure-blob-storage.md#managed-identity) och [Azure Data Lake Storage Gen2-hanterad identitets verifiering](connector-azure-data-lake-storage.md#managed-identity) .

2. **Käll data formatet** är av **Parquet**, **Orc**eller **avgränsad text**, med följande konfigurationer:

   1. Mappsökvägen innehåller inte något filter för jokertecken.
   2. Fil namnet är tomt eller pekar på en enskild fil. Om du anger jokertecken i kopierings aktiviteten kan du bara `*` eller `*.*`.
   3. `rowDelimiter` är **standard**, **\n**, **\r\n**eller **\r**.
   4. `nullValue` lämnas som standard eller inställd på **tom sträng** (""), och `treatEmptyAsNull` lämnas som standard eller anges till sant.
   5. `encodingName` är kvar som standard eller anges till **UTF-8**.
   6. `quoteChar`, `escapeChar`och `skipLineCount` har inte angetts. Huvud stöd för att hoppa över rubrik rad, som kan konfigureras som `firstRowAsHeader` i ADF.
   7. `compression` kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

3. Om din källa är en mapp måste `recursive` i kopierings aktiviteten vara inställd på sant.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`och `modifiedDateTimeEnd` inte har angetts.

>[!NOTE]
>Om din källa är en mapp hämtas filer från mappen och alla dess undermappar, och data hämtas inte från filer vars fil namn börjar med en understrykning (_) eller en punkt (.), enligt beskrivningen [här – plats argument](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Mellanlagrad kopiering genom att använda PolyBase

När dina källdata inte är internt kompatibla med PolyBase kan du aktivera data kopiering via en tillfällig mellanlagrings instans av Azure Blob Storage (den kan inte vara Azure Premium Storage). I det här fallet konverterar Azure Data Factory automatiskt data så att de uppfyller data format kraven för PolyBase. Sedan anropar den PolyBase för att läsa in data i SQL Data Warehouse. Slutligen rensar den tillfälliga data från blob-lagringen. Se [mellanlagrad kopiering](copy-activity-performance.md#staged-copy) mer information om hur du kopierar data via en mellanlagrings Azure Blob storage-instans.

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure Storage-kontot med Interim Blob Storage. Ange sedan `enableStaging` och `stagingSettings` egenskaper för kopierings aktiviteten som du ser i följande kod.

>[!IMPORTANT]
>Om din mellanlagrings Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitetsautentisering](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Metodtips för att använda PolyBase

Följande avsnitt innehåller metod tips utöver de som nämns i [metod tips för Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Behörighet krävs

Användaren som läser in data i SQL Data Warehouse måste ha för att använda PolyBase [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) i måldatabasen. Ett sätt att uppnå som är att lägga till användaren som en medlem i den **db_owner** roll. Lär dig hur du gör den [översikt över SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Ange gränser Radstorleken och data

PolyBase-inläsningar är begränsade till rader som är mindre än 1 MB. Den kan inte användas för att läsa in till VARCHR (MAX), NVARCHAR (MAX) eller VARBINARY (MAX). Mer information finns i [kapacitetsbegränsningar i SQL Data Warehouse-tjänsten](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När dina källdata innehåller rader som är större än 1 MB, kanske du vill dela lodrätt källtabellerna i flera små. Kontrollera att den största storleken på varje rad inte överskrider gränsen. De mindre tabellerna kan sedan läsas in med PolyBase och sammanfogas tillsammans i Azure Synapse Analytics.

Du kan också använda icke-PolyBase för att läsa in data med hjälp av ADF genom att inaktivera inställningen Tillåt PolyBase för data med sådana breda kolumner.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklass

Tilldela en större resursklass till användaren som läser in data i SQL Data Warehouse via PolyBase för att uppnå bästa möjliga genomflöde.

#### <a name="polybase-troubleshooting"></a>PolyBase-fel sökning

**Läser in till decimal kolumn**

Om dina källdata är i text format eller andra icke-PolyBase-kompatibla lager (med mellanlagrad kopia och PolyBase) och den innehåller ett tomt värde som ska läsas in i SQL Data Warehouse decimal kolumn, kan du trycka på följande fel:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Lösningen är att avmarkera alternativet**Använd standard**alternativet (som falskt) i kopierings aktiviteten handfat-> PolyBase-inställningar. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" är en PolyBase-inbyggd konfiguration som anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen. 

**`tableName` i Azure Synapse Analytics**

I följande tabell innehåller exempel på hur du anger den **tableName** egenskap i JSON-datauppsättning. Den visar flera kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamn | **tableName** JSON-egenskap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable eller dbo.MyTable eller [dbo].[Tabell] |
| dbo1      | MyTable    | dbo1.MyTable eller [dbo1].[Tabell]          |
| dbo       | My.Table   | [My.Table] eller [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Om du ser följande fel kan problemet vara värdet du angav för den **tableName** egenskapen. Se tabellen ovan för det korrekta sättet att ange värden för den **tableName** JSON-egenskap.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumner med standardvärden**

PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Ett exempel är en tabell med fyra kolumner där en av dem har definierats med ett standardvärde. Indata måste fortfarande ha fyra kolumner. En indatauppsättning med tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en särskild form av standardvärdet. Om kolumnen är nullbar vara inkommande data i blob för kolumnen tom. Men den kan inte vara saknas från datauppsättningen för indata. PolyBase infogar NULL för saknade värden i Azure Synapse Analytics.

## <a name="use-copy-statement"></a>Använda COPY-instruktionen för att läsa in data i Azure SQL Data Warehouse (för hands version)

SQL Data Warehouse [copy-instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version) stöder direkt inläsning av data från **Azure blob och Azure Data Lake Storage Gen2**. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet, kan du välja att använda COPY-instruktionen i ADF för att läsa in data i Azure SQL Data Warehouse. Azure Data Factory kontrollerar inställningarna och det går inte att köra kopierings aktiviteten om villkoren inte är uppfyllda.

>[!NOTE]
>För närvarande finns Data Factory bara stöd för kopiering från COPY Statement-kompatibla källor som anges nedan.

Att använda COPY-instruktionen har stöd för följande konfiguration:

1. Den **länkade käll tjänsten och formatet** har följande typer och autentiseringsmetoder:

    | Typ av käll data lager som stöds                             | Format som stöds           | Typ av källtyp som stöds                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Avgränsad text](format-delimited-text.md)             | Autentisering av konto nyckel, autentisering av signatur för delad åtkomst, autentisering av tjänstens huvud konto, hanterad identitetsautentisering |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autentisering av konto nyckel, autentisering av delad åtkomst för signaturer |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autentisering av konto nyckel, autentisering av delad åtkomst för signaturer |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Avgränsad text](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autentisering av konto nyckel, autentisering av tjänstens huvud konto, hanterad identitets autentisering |

    >[!IMPORTANT]
    >Om din Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig mer om de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitets verifiering](connector-azure-blob-storage.md#managed-identity) och [Azure Data Lake Storage Gen2-hanterad identitets verifiering](connector-azure-data-lake-storage.md#managed-identity) .

2. Format inställningarna är med följande:

   1. För **Parquet**: `compression` kan vara **Ingen komprimering**, **fästfunktionen**eller **gzip**.
   2. För **Orc**: `compression` kan vara **Ingen komprimering**, **zlib**eller **fästning**.
   3. För **avgränsad text**:
      1. `rowDelimiter` anges explicit som **enstaka** eller " **\r\n**", så stöds inte standardvärdet.
      2. `nullValue` är kvar som standard eller inställt på **tom sträng** ("").
      3. `encodingName` lämnas som standard eller anges till **UTF-8 eller UTF-16**.
      4. `escapeChar` måste vara samma som `quoteChar`och är inte tom.
      5. `skipLineCount` är kvar som standard eller anges till 0.
      6. `compression` kan vara **Ingen komprimering** eller **gzip**.

3. Om din källa är en mapp måste `recursive` i kopierings aktiviteten vara inställd på sant.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`och `modifiedDateTimeEnd` inte har angetts.

Följande KOPIERINGs instruktions inställningar stöds under `allowCopyCommand` i kopierings aktiviteten:

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Anger standardvärden för varje mål kolumn i SQL DW.  Standardvärdena i egenskapen skriver över standard begränsnings uppsättningen i data lagret och identitets kolumnen kan inte ha ett standardvärde. | Inga |
| additionalOptions | Ytterligare alternativ som skickas till SQL DW COPY-instruktionen direkt i With-satsen i [copy-instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Citera värdet efter behov för att passa med KOPIERINGs instruktionens krav. | Inga |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datatypsmappningen för Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till tabeller från Azure Synapse Analytics. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden.

### <a name="source-transformation"></a>Käll omvandling

Inställningar som är särskilt tillgängliga för Azure Synapse Analytics finns på fliken **käll alternativ** i käll omvandlingen. 

**Inmatade:** Välj om du vill peka din källa i en tabell (motsvarande ```Select * from <table-name>```) eller ange en anpassad SQL-fråga.

**Fråga**: om du väljer fråga i fältet inmatat anger du en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i data uppsättningen. **Order by** -satser stöds inte här, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell. Med den här frågan skapas en käll tabell som du kan använda i ditt data flöde. Att använda frågor är också ett bra sätt att minska antalet rader för testning eller sökning. 

* SQL-exempel: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchstorlek**: Ange en batchstorlek för att segmentera stora data till läsningar.

**Isolerings nivå**: standardvärdet för SQL-källor i mappnings data flödet är Read uncommitted. Du kan ändra isolerings nivån här till något av följande värden:
* Läs bekräftad
* Läs ej allokerad
* Upprepnings bar läsning
* Serialiserbar
* Ingen (ignorera isolerings nivå)

![Isolerings nivå](media/data-flow/isolationlevel.png "Isoleringsnivå")

### <a name="sink-transformation"></a>Omvandling av mottagare

Inställningar som är tillgängliga för Azure Synapse Analytics finns på fliken **Inställningar** i omvandlingen för mottagare.

**Uppdaterings metod:** Avgör vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar. Om du vill uppdatera, upsert eller ta bort rader krävs en Alter-Row-omvandling för att tagga rader för dessa åtgärder. För uppdateringar, upsertar och borttagningar måste en nyckel kolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

**Tabell åtgärd:** Bestämmer om du vill återskapa eller ta bort alla rader från mål tabellen innan du skriver.
* Ingen: ingen åtgärd utförs i tabellen.
* Återskapa: tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.
* Trunkera: alla rader från mål tabellen tas bort.

**Aktivera mellanlagring:** Bestämmer om [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) ska användas vid skrivning till Azure Synapse Analytics

**Batchstorlek**: styr hur många rader som skrivs i varje Bucket. Större batch-storlekar förbättrar komprimeringen och minnes optimeringen, men riskerar att ta bort minnes undantag när data cachelagras.

**För-och post-SQL-skript**: Ange SQL-skript med flera rader som ska köras före (för bearbetning) och efter (efter bearbetning)-data skrivs till din mottagar databas

![skript för SQL-bearbetning före och efter bearbetning](media/data-flow/prepost1.png "Skript för SQL-bearbetning")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Data typs mappning för Azure Synapse Analytics

När du kopierar data från eller till Azure Synapse Analytics används följande mappningar från Azure Synapse Analytics-datatyper för att Azure Data Factory interimistiska data typer. Se [schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

>[!TIP]
>Se [tabell data typer i Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) -artikeln om data typer som stöds av SQL DW och lösningar för de som inte stöds.

| Data typen Azure Synapse Analytics    | Data Factory tillfälliga datatyp |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Flyttal                                 | Double                         |
| mallar                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Enkel                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Mottagna byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
