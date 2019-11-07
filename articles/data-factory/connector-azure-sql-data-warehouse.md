---
title: Kopiera data till och från Azure SQL Data Warehouse med Azure Data Factory
description: Lär dig hur du kopierar data från käll arkiv som stöds till Azure SQL Data Warehouse eller från SQL Data Warehouse till Sink-butiker som stöds med hjälp av Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: b64bfd046a42a630e7913c45213053e84377a037
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681155"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera data till eller från Azure SQL Data Warehouse med Azure Data Factory 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuell version](connector-azure-sql-data-warehouse.md)

Den här artikeln beskriver hur du kopierar data till och från Azure SQL Data Warehouse. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure SQL Data Warehouse anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Mer specifikt har den här Azure SQL Data Warehouse-anslutningen stöd för dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token-autentisering med tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar du data med hjälp av en SQL-fråga eller lagrad procedur.
- Läs in data som mottagare genom att använda PolyBase eller en Mass infogning. Vi rekommenderar PolyBase för bättre kopierings prestanda.

> [!IMPORTANT]
> Om du kopierar data genom att använda Azure Data Factory Integration Runtime konfigurerar du en [Azure SQL Server-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänsterna kan komma åt servern.
> Om du kopierar data med hjälp av en lokal integration runtime konfigurerar du Azure SQL Server-brandväggen så att rätt IP-intervall tillåts. Detta intervall inkluderar datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

> [!TIP]
> Använd PolyBase för att läsa in data i Azure SQL Data Warehouse för att uppnå bästa prestanda. [Använd PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet innehåller information. För en genom gång med ett användnings fall, se [load 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som definierar Data Factory entiteter som är speciella för en Azure SQL Data Warehouse anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en Azure SQL Data Warehouse länkad tjänst:

| Egenskap            | Beskrivning                                                  | Krävs                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| typ                | Egenskapen Type måste anges till **AzureSqlDW**.             | Ja                                                          |
| Begär    | Ange den information som krävs för att ansluta till Azure SQL Data Warehouse-instansen för egenskapen **ConnectionString** . <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ange lösen ord/tjänstens huvud namn i Azure Key Vault, och om det är SQL-autentisering hämtar du `password`-konfigurationen från anslutnings strängen. Se JSON-exemplet under tabellen och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja                                                          |
| servicePrincipalId  | Ange programmets klient-ID.                         | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten. |
| innehav              | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten. |
| connectVia          | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller en egen värd för integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. | Nej                                                           |

För olika typer av autentiseringar, se följande avsnitt om krav respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- Azure AD Application token-autentisering: [tjänstens huvud namn](#service-principal-authentication)
- Azure AD Application token-autentisering: [hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du får ett fel meddelande med felkoden "UserErrorFailedToConnectToSqlServer" och "meddelande" som "databasens sessionsgräns är XXX och har nåtts." lägger du till `Pooling=false` i anslutnings strängen och försöker igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exempel på länkade tjänster som använder SQL-autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Lösen ord i Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

Följ dessa steg om du vill använda tjänstens huvud-baserade Azure AD Application token-autentisering:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** från Azure Portal. Anteckna program namnet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Program nyckel
    - Klient-ID:t

2. **[Etablera en Azure Active Directory administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** för din Azure SQL-server på Azure Portal om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören får fullständig åtkomst till databasen.

3. **[Skapa inneslutna databas användare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** för tjänstens huvud namn. Anslut till det data lager från eller till vilket du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ändra användar behörighet. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Ge tjänstens huvud namn nödvändiga behörigheter** som du vanligt vis använder för SQL-användare eller andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurera en Azure SQL Data Warehouse länkad tjänst** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exempel på länkad tjänst som använder autentisering av tjänstens huvud namn

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identity"></a>Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den aktuella fabriken. Du kan använda den här hanterade identiteten för Azure SQL Data Warehouse autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till ditt data lager med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering:

1. **[Etablera en Azure Active Directory administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** för din Azure SQL-server på Azure Portal om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören får fullständig åtkomst till databasen.

2. **[Skapa inneslutna databas användare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** för den Data Factory hanterade identiteten. Anslut till det data lager från eller till vilket du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ändra användar behörighet. Kör följande T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Ge den Data Factory hanterade identiteten nödvändiga behörigheter** som du vanligt vis använder för SQL-användare och andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfigurera en Azure SQL Data Warehouse länkad tjänst** i Azure Data Factory.

**Exempel:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

## <a name="dataset-properties"></a>Egenskaper för data mängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse data uppsättningen.

Följande egenskaper stöds för att kopiera data från eller till Azure SQL Data Warehouse:

| Egenskap  | Beskrivning                                                  | Krävs                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Data uppsättningens **typ** -egenskap måste anges till **AzureSqlDWTable**. | Ja                         |
| Schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| partitionstabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för ny arbets belastning. | Nej för källa, Ja för mottagare |

#### <a name="dataset-properties-example"></a>Exempel på data uppsättnings egenskaper

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
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

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse källa och mottagare.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse som källa

Om du vill kopiera data från Azure SQL Data Warehouse anger du egenskapen **Type** i kopierings aktivitets källan till **SqlDWSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap                     | Beskrivning                                                  | Krävs |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | **Typ** egenskapen för kopierings aktivitets källan måste anges till **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Använd den anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej       |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från käll tabellen. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej       |
| storedProcedureParameters    | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-eller värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. | Nej       |

### <a name="points-to-note"></a>Poäng notering

- Om **sqlReaderQuery** har angetts för **SqlSource**kör kopierings aktiviteten den här frågan mot Azure SQL Data Warehouse källan för att hämta data. Du kan också ange en lagrad procedur. Ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger antingen **sqlReaderQuery** eller **sqlReaderStoredProcedureName**används kolumnerna som definieras i avsnittet **struktur** i data uppsättnings-JSON för att skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL Data Warehouse. Om data uppsättnings definitionen saknar **strukturen**väljs alla kolumner från tabellen.

#### <a name="sql-query-example"></a>Exempel på SQL-fråga

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

#### <a name="stored-procedure-example"></a>Exempel på lagrad procedur

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse som mottagare

Om du vill kopiera data till Azure SQL Data Warehouse anger du mottagar typen i kopierings aktivitet till **SqlDWSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Anger om PolyBase ska användas, när det är tillämpligt, i stället för BULKINSERT-mekanismen. <br/><br/> Vi rekommenderar att du läser in data i SQL Data Warehouse med PolyBase. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet.<br/><br/>Tillåtna värden är **True** och **false** (standard). | Nej                                            |
| polyBaseSettings  | En grupp egenskaper som kan anges när **allowPolybase** -egenskapen har angetts till **True**. | Nej                                            |
| rejectValue       | Anger antalet rader eller procent av rader som kan avvisas innan frågan Miss lyckas.<br/><br/>Läs mer om polybases avvisnings alternativ i avsnittet arguments i [skapa en extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Tillåtna värden är 0 (standard), 1, 2 osv. | Nej                                            |
| rejectType        | Anger om alternativet **rejectValue** är ett litteralt värde eller en procents ATS.<br/><br/>Tillåtna värden är **värde** (standard) och **procent**. | Nej                                            |
| rejectSampleValue | Anger det antal rader som ska hämtas innan PolyBase beräknar om procent andelen avvisade rader.<br/><br/>Tillåtna värden är 1, 2 osv. | Ja, om **rejectType** är **procent andelen**. |
| useTypeDefault    | Anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen.<br/><br/>Lär dig mer om den här egenskapen från avsnittet argument i [Skapa externt fil format (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **True** och **false** (standard).<br><br> | Nej                                            |
| writeBatchSize    | Antal rader som ska infogas i SQL-tabellen **per batch**. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **Integer** (antal rader). Som standard bestämmer Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. | Nej                                            |
| writeBatchTimeout | Vänte tid för att slutföra batch-infogningen innan tids gränsen uppnåddes. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **TimeSpan**. Exempel: "00:30:00" (30 minuter). | Nej                                            |
| preCopyScript     | Ange en SQL-fråga för kopierings aktivitet som ska köras innan data skrivs till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen för att rensa de förinstallerade data. | Nej                                            |
| tableOption | Anger om mottagar tabellen ska skapas automatiskt om den inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när mellanlagrad kopiering har kon figurer ATS i kopierings aktiviteten. Tillåtna värden är: `none` (standard) `autoCreate`. |Nej |
| disableMetricsCollection | Data Factory samlar in mått som SQL Data Warehouse DWU: er för att kopiera prestanda optimering och rekommendationer. Om du är orolig för det här beteendet anger du `true` att inaktivera det. | Nej (standard är `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Exempel på SQL Data Warehouse mottagare

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

Läs mer om hur du använder PolyBase för att effektivt läsa in SQL Data Warehouse i nästa avsnitt.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data i Azure SQL Data Warehouse

Att använda [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in stora mängder data i Azure SQL Data Warehouse med högt data flöde. Du ser en stor ökning i data flödet med hjälp av PolyBase i stället för standard mekanismen för BULKINSERT. För en genom gång med ett användnings fall, se [load 1 TB till Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Om dina källdata finns i **Azure Blob, Azure Data Lake Storage gen1 eller Azure Data Lake Storage Gen2**och **formatet är PolyBase-kompatibelt**, kan du använda kopierings aktivitet för att direkt anropa PolyBase för att låta Azure SQL Data Warehouse hämta data från källan. Mer information finns i **[direkt kopiering med hjälp av PolyBase](#direct-copy-by-using-polybase)** .
* Om käll data lagret och formatet inte ursprungligen stöds av PolyBase använder du den **[mellanlagrade kopian med PolyBase](#staged-copy-by-using-polybase)** -funktionen i stället. Funktionen för mellanlagrad kopiering ger också bättre data flöde. Data konverteras automatiskt till PolyBase-kompatibelt format. Och lagrar data i Azure Blob Storage. Den läser sedan in data i SQL Data Warehouse.

>[!TIP]
>Lär dig mer om [metod tips för att använda PolyBase](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Direkt kopia med hjälp av PolyBase

SQL Data Warehouse PolyBase stöder direkt Azure Blob, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet använder du PolyBase för att kopiera direkt från käll data lagret till Azure SQL Data Warehouse. Annars använder du [mellanlagrad kopiering med PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Om du vill kopiera data på ett effektivt sätt till SQL Data Warehouse kan du läsa mer från [Azure Data Factory gör det ännu enklare och bekvämt att få insikter från data när du använder data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls kontrollerar Azure Data Factory inställningarna och återgår automatiskt till BULKINSERT-mekanismen för data förflyttning.

1. Den **länkade käll tjänsten** har följande typer och autentiseringsmetoder:

    | Typ av käll data lager som stöds                             | Typ av källtyp som stöds                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure-Blob](connector-azure-blob-storage.md)                | Autentisering av konto nyckel, hanterad identitets autentisering |
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
   7. `compression` kan inte vara **komprimering**, **gzip**eller **DEFLATE**.

3. Om din källa är en mapp måste `recursive` i kopierings aktiviteten vara inställd på sant.

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
                    "type": "AzureBlobStorageReadSetting",
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

### <a name="staged-copy-by-using-polybase"></a>Mellanlagrad kopia med PolyBase

När dina källdata inte uppfyller kriterierna i föregående avsnitt aktiverar du data kopiering via en tillfällig mellanlagringsplats av Azure Blob Storage-instansen. Det får inte vara Azure-Premium Storage. I det här fallet kör Azure Data Factory automatiskt omvandlingar av data för att uppfylla data format kraven för PolyBase. Sedan använder den PolyBase för att läsa in data i SQL Data Warehouse. Slutligen rensar den temporära data från blob-lagringen. Se [mellanlagrad kopia](copy-activity-performance.md#staged-copy) för information om hur du kopierar data via en mellanlagringsplats av Azure Blob Storage-instansen.

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

## <a name="best-practices-for-using-polybase"></a>Metod tips för att använda PolyBase

Följande avsnitt innehåller metod tips utöver de som nämns i [metod tips för Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Nödvändig databas behörighet

Om du vill använda PolyBase måste användaren som läser in data i SQL Data Warehouse ha [behörigheten "kontroll"](https://msdn.microsoft.com/library/ms191291.aspx) för mål databasen. Ett sätt att åstadkomma detta är att lägga till användaren som medlem i rollen **db_owner** . Lär dig hur du gör i [SQL Data Warehouse översikt](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Rad storlek och data typs gränser

PolyBase-belastningarna är begränsade till rader som är mindre än 1 MB. Den kan inte användas för att läsa in till VARCHR (MAX), NVARCHAR (MAX) eller VARBINARY (MAX). Mer information finns i [SQL Data Warehouse tjänst kapacitets gränser](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När dina källdata har rader som är större än 1 MB, kanske du vill dela käll tabellerna lodrätt i flera små. Kontrol lera att den största storleken på varje rad inte överskrider gränsen. De mindre tabellerna kan sedan läsas in med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

Du kan också använda icke-PolyBase för att läsa in data med hjälp av ADF genom att inaktivera inställningen Tillåt PolyBase för data med sådana breda kolumner.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resurs klass

För att uppnå bästa möjliga data flöde tilldelar du en större resurs klass till användaren som läser in data i SQL Data Warehouse via PolyBase.

### <a name="polybase-troubleshooting"></a>PolyBase-fel sökning

**Läser in till decimal kolumn**

Om dina källdata är i text format eller andra icke-PolyBase-kompatibla lager (med mellanlagrad kopia och PolyBase) och den innehåller ett tomt värde som ska läsas in i SQL Data Warehouse decimal kolumn, kan du trycka på följande fel:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Lösningen är att avmarkera alternativet**Använd standard**alternativet (som falskt) i kopierings aktiviteten handfat-> PolyBase-inställningar. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" är en PolyBase-inbyggd konfiguration som anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen. 

**`tableName` i Azure SQL Data Warehouse**

Följande tabell innehåller exempel på hur du anger egenskapen **TableName** i JSON-datauppsättningen. Det visar flera kombinationer av schema-och tabell namn.

| DB-schema | Tabellnamn | **TableName** JSON-egenskap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | Min tabell eller dbo. Min tabell eller [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. Min tabell eller [dbo1]. MyTable          |
| dbo       | My. table   | [My. table] eller [dbo]. [My. table]            |
| dbo1      | My. table   | [dbo1]. [My. table]                         |

Om du ser följande fel kan problemet vara det värde som du har angett för egenskapen **TableName** . I tabellen ovan finns det korrekta sättet att ange värden för JSON-egenskapen **TableName** .

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumner med standardvärden**

För närvarande accepterar PolyBase-funktionen i Data Factory bara samma antal kolumner som i mål tabellen. Ett exempel är en tabell med fyra kolumner där en av dem definieras med ett standardvärde. Indata behöver fortfarande ha fyra kolumner. En data uppsättning med tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en särskild form av standardvärdet. Om kolumnen kan vara null kan indata i bloben för den kolumnen vara tomma. Men det kan inte saknas i indata-datauppsättningen. PolyBase infogar NULL för saknade värden i Azure SQL Data Warehouse.

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flödet.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Data typs mappning för Azure SQL Data Warehouse

När du kopierar data från eller till Azure SQL Data Warehouse, används följande mappningar från Azure SQL Data Warehouse data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig hur kopierings aktivitet mappar käll schema och datatyp till mottagaren.

>[!TIP]
>Se [tabell data typer i Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) artikel om data typer som stöds av SQL DW och de lösningar som inte stöds.

| Azure SQL Data Warehouse data typ    | Data Factory data typen Interim |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binär                                | Byte []                         |
| bitmask                                   | Boolesk                        |
| hängande                                  | Sträng, char []                 |
| datum                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM-attribut (varbinary (max)) | Byte []                         |
| Flyta                                 | Dubbelklicka                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| mynt                                 | Decimal                        |
| nchar                                 | Sträng, char []                 |
| nummer                               | Decimal                        |
| nvarchar                              | Sträng, char []                 |
| verkligen                                  | Enkel                         |
| rowversion                            | Byte []                         |
| datatyp                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | Intervall                       |
| tinyint                               | Stor                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte []                         |
| varchar                               | Sträng, char []                 |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare efter kopierings aktivitet i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
