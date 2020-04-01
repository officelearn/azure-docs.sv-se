---
title: Kopiera och omvandla data i Azure Synapse Analytics
description: Lär dig hur du kopierar data till och från Azure Synapse Analytics och omvandlar data i Azure Synapse Analytics med hjälp av Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 822a981b84919670aa476567625cdf914206eaa8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422186"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera och omvandla data i Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) med hjälp av Azure Data Factory 

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuell version](connector-azure-sql-data-warehouse.md)

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från och till Azure Synapse Analytics och använda Dataflöde för att omvandla data i Azure Data Lake Storage Gen2. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Synapse Analytics-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matristabell som stöds](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

För kopieringsaktivitet stöder den här Azure Synapse Analytics-kopplingen dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token autentisering med ett tjänsthuvudnamn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar du data med hjälp av en SQL-fråga eller en lagrad procedur.
- Som en mottagare läser du in data med hjälp av [PolyBase-](#use-polybase-to-load-data-into-azure-sql-data-warehouse) eller [COPY-sats](#use-copy-statement) (förhandsgranskning) eller massinfogning. Vi rekommenderar PolyBase eller COPY-sats (förhandsversion) för bättre kopieringsprestanda.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime konfigurerar du en [Azure SQL-serverbrandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster kan komma åt servern.
> Om du kopierar data med hjälp av en självvärd integreringskörning konfigurerar du Azure SQL-serverbrandväggen så att det tillåter lämpligt IP-intervall. Det här intervallet innehåller datorns IP som används för att ansluta till Azure Synapse Analytics.

## <a name="get-started"></a>Kom igång

> [!TIP]
> För att uppnå bästa prestanda kan du använda PolyBase för att läsa in data i Azure Synapse Analytics. Avsnittet [Använd PolyBase för att läsa in data i Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) innehåller information. En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure Synapse Analytics under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory-entiteter som är specifika för en Azure Synapse Analytics-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för en Azure Synapse Analytics-länkad tjänst:

| Egenskap            | Beskrivning                                                  | Krävs                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| typ                | Typegenskapen måste anges till **AzureSqlDW**.             | Ja                                                          |
| Connectionstring    | Ange den information som behövs för att ansluta till Azure Synapse Analytics-instansen för egenskapen **connectionString.** <br/>Markera det här fältet som en SecureString för att lagra det säkert i Data Factory. Du kan också placera lösenords-/tjänsthuvudnyckeln i Azure Key `password` Vault och om det är SQL-autentisering dra konfigurationen ur anslutningssträngen. Se JSON-exemplet nedanför tabellen och [Store-autentiseringsuppgifterna i](store-credentials-in-key-vault.md) Azure Key Vault-artikeln med mer information. | Ja                                                          |
| servicePrincipalId  | Ange programmets klient-ID.                         | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn. |
| tenant              | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja, när du använder Azure AD-autentisering med ett tjänsthuvudnamn. |
| connectVia (på)          | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en självvärd integreringskörning (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. | Inga                                                           |

För olika autentiseringstyper finns i följande avsnitt om förutsättningar respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- Azure AD-programtokenautentisering: [Tjänsthuvudnamn](#service-principal-authentication)
- Azure AD-programtokenautentisering: [Hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du stöter på fel med felkod som "UserErrorFailedToConnectToSqlServer" och meddelande som "Sessionsgränsen för databasen är XXX och har nåtts.", lägger du `Pooling=false` till anslutningssträngen och försöker igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exempel på länkad tjänst som använder SQL-autentisering

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

**Lösenord i Azure Key Vault:**

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

Så här använder du tjänsthuvudbaserat Azure AD-programtokenautentisering:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** från Azure-portalen. Anteckna programnamnet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** för din Azure SQL-server på Azure-portalen om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen en hanterad identitet en administratörsroll hoppar du över steg 3 och 4. Administratören kommer att ha fullständig åtkomst till databasen.

3. **[Skapa inneslutna databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** för tjänstens huvudnamn. Anslut till informationslagret från eller till vilket du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ändra användarbehörighet. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja tjänstens huvudnamn nödvändiga behörigheter** som vanligt för SQL-användare eller andra. Kör följande kod eller se fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [databasbehörighet som krävs](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurera en Azure Synapse Analytics-länkad tjänst** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exempel på länkad tjänst som använder autentisering av tjänstens huvudnamn

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Hanterade identiteter för Azure-resursautentisering

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika fabriken. Du kan använda den här hanterade identiteten för Azure Synapse Analytics-autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till ditt informationslager med hjälp av den här identiteten.

Så här använder du autentisering av hanterade identiteter:

1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** för din Azure SQL-server på Azure-portalen om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen en hanterad identitet en administratörsroll hoppar du över steg 3 och 4. Administratören kommer att ha fullständig åtkomst till databasen.

2. **[Skapa inneslutna databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** för datafabrikens hanterade identitet. Anslut till informationslagret från eller till vilket du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ändra användarbehörighet. Kör följande T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Bevilja datafabrikens hanterade identitet nödvändiga behörigheter** som vanligt för SQL-användare och andra. Kör följande kod eller se fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [databasbehörighet som krävs](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfigurera en Azure Synapse Analytics-länkad tjänst** i Azure Data Factory.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) 

Följande egenskaper stöds för Azure Synapse Analytics-datauppsättning:

| Egenskap  | Beskrivning                                                  | Krävs                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | **Egenskapen Type** property för datauppsättningen måste anges till **AzureSqlDWTable**. | Ja                         |
| Schemat | Namnet på schemat. |Nej för källa, Ja för diskho  |
| tabell | Namn på tabellen/vyn. |Nej för källa, Ja för diskho  |
| tableName | Namn på tabellen/vyn med schema. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table`. | Nej för källa, Ja för diskho |

#### <a name="dataset-properties-example"></a>Exempel på egenskaper för Datauppsättning

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Synapse Analytics-källan och diskhon.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics som källa

Om du vill kopiera data från Azure **type** Synapse Analytics anger du typegenskapen i källan Kopiera aktivitet till **SqlDWSource**. Följande egenskaper stöds i **källavsnittet** Kopiera aktivitet:

| Egenskap                     | Beskrivning                                                  | Krävs |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | **Egenskapen Type** property för källan Kopiera aktivitet måste anges till **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Använd den anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Inga       |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. | Inga       |
| storedProcedureParameters    | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- eller värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. | Inga       |
| isoleringNivå | Anger transaktionslåsningsbeteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted** (standard), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Se [det här dokumentet](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) för mer information. | Inga |

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

**Prov lagrad procedur:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics som mottagare

Azure Data Factory stöder tre sätt att läsa in data i SQL Data Warehouse.

![Alternativ för SQL DW-diskhoskopia](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Använd PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Använda COPY-sats (förhandsgranskning)](#use-copy-statement)
- Använd bulkskär

Det snabbaste och mest skalbara sättet att läsa in data är via [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) eller [COPY-satsen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (förhandsversion).

Om du vill kopiera data till Azure SQL Data Warehouse anger du sink-typen i Kopiera aktivitet till **SqlDWSink**. Följande egenskaper stöds i avsnittet Kopiera **aktivitetsmottagare:**

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | **Egenskapen Type** property för copy activity sink måste anges till **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Anger om PolyBase ska användas för att läsa in data i SQL Data Warehouse. `allowCopyCommand`och `allowPolyBase` kan inte vara både sant. <br/><br/>Se [Använda PolyBase för att läsa in data i avsnittet Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) för begränsningar och information.<br/><br/>Tillåtna värden är **Sant** och **Falskt** (standard). | Nej.<br/>Ansök när du använder PolyBase.     |
| polyBaseSettings polyBaseSettings polyBaseSettings polyBase  | En grupp egenskaper som kan anges `allowPolybase` när egenskapen är inställd på **true**. | Nej.<br/>Ansök när du använder PolyBase. |
| allowCopyCommand | Anger om [COPY-uttryck](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (förhandsgranskning) ska användas för att läsa in data i SQL Data Warehouse. `allowCopyCommand`och `allowPolyBase` kan inte vara både sant. <br/><br/>Se [Använda COPY-uttryck för att läsa in data i avsnittet Azure SQL Data Warehouse](#use-copy-statement) för begränsningar och information.<br/><br/>Tillåtna värden är **Sant** och **Falskt** (standard). | Nej.<br>Ansök när du använder COPY. |
| kopieraCommandSettings | En grupp egenskaper som kan `allowCopyCommand` anges när egenskapen är inställd på SANT. | Nej.<br/>Ansök när du använder COPY. |
| skriverBatchSize    | Antal rader som ska infogas i SQL-tabellen **per batch**.<br/><br/>Det tillåtna värdet är **heltal** (antal rader). Som standard bestämmer Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken. | Nej.<br/>Använd när du använder bulkinsats.     |
| skriverBatchTimeout | Vänta på att batchinsatsen ska slutföras innan den har klarats upp.<br/><br/>Det tillåtna värdet är **tidsspann**. Exempel: "00:30:00" (30 minuter). | Nej.<br/>Använd när du använder bulkinsats.        |
| preCopyScript     | Ange en SQL-fråga för kopieringsaktivitet som ska köras innan du skriver data till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen för att rensa de förinstallerade data. | Inga                                            |
| tabellOption | Anger om sink-tabellen ska skapas automatiskt om det inte finns baserat på källschemat. Automatisk skapande av tabeller stöds inte när den stegvisa kopian konfigureras i kopieringsaktivitet. Tillåtna värden `none` är: `autoCreate`(standard), . |Inga |
| inaktiveraMetricsCollection | Data Factory samlar in mått som SQL Data Warehouse DWUs för kopiering prestandaoptimering och rekommendationer. Om du har något att `true` göra med det här beteendet anger du att den ska stängas av. | Nej (standard `false`är) |

#### <a name="sql-data-warehouse-sink-example"></a>Exempel på SQL Data Warehouse-mottagare

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data i Azure SQL Data Warehouse

Att använda [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in en stor mängd data i Azure Synapse Analytics med högt dataflöde. Du ser en stor vinst i dataflödet med hjälp av PolyBase i stället för standard BULKINSERT-mekanismen. En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Om källdata finns i **Azure Blob, Azure Data Lake Storage Gen1 eller Azure Data Lake Storage Gen2**och **formatet är PolyBase-kompatibelt**kan du använda kopieringsaktivitet för att direkt anropa PolyBase för att låta Azure SQL Data Warehouse hämta data från källan. Mer information finns i **[Direktkopia med PolyBase](#direct-copy-by-using-polybase)**.
* Om källdatalagringen och formatet inte stöds ursprungligen av PolyBase använder du **[kopian Stegvis med hjälp av PolyBase](#staged-copy-by-using-polybase)** i stället. Den iscensatta kopieringsfunktionen ger dig också bättre dataflöde. Den konverterar automatiskt data till PolyBase-kompatibelt format, lagrar data i Azure Blob storage., sedan anropar PolyBase för att läsa in data i SQL Data Warehouse.

>[!TIP]
>Läs mer om [metodtips för att använda PolyBase](#best-practices-for-using-polybase).

Följande PolyBase-inställningar stöds `polyBaseSettings` under i kopieringsaktivitet:

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| avvisaVärde       | Anger antalet eller procentandelen rader som kan avvisas innan frågan misslyckas.<br/><br/>Läs mer om PolyBases avvisningsalternativ i avsnittet Argument i [SKAPA EXTERN TABELL (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) <br/><br/>Tillåtna värden är 0 (standard), 1, 2, etc. | Inga                                            |
| rejectType (avskände)        | Anger om alternativet **rejectValue** är ett litteralt värde eller en procentsats.<br/><br/>Tillåtna värden är **Värde** (standard) och **Procent**. | Inga                                            |
| avvisaPlevalue | Bestämmer antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade rader.<br/><br/>Tillåtna värden är 1, 2, etc. | Ja, om **rejectType** är **procent**. |
| useTypeDefault    | Anger hur värden som saknas i avgränsade textfiler ska hanteras när PolyBase hämtar data från textfilen.<br/><br/>Läs mer om den här egenskapen från avsnittet Argument i [SKAPA EXTERNT FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **Sant** och **Falskt** (standard).<br><br> | Inga                                            |

### <a name="direct-copy-by-using-polybase"></a>Direktkopia med PolyBase

SQL Data Warehouse PolyBase har direkt stöd för Azure Blob, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Om källdata uppfyller de kriterier som beskrivs i det här avsnittet använder du PolyBase för att kopiera direkt från källdataarkivet till Azure Synapse Analytics. Annars kan du använda [Stegvis kopia med PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Om du vill kopiera data effektivt till SQL Data Warehouse gör du mer från [Azure Data Factory ännu enklare och bekvämare att upptäcka insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls kontrollerar Azure Data Factory inställningarna och faller automatiskt tillbaka till BULKINSERT-mekanismen för dataflyttning.

1. Den **källlänkade tjänsten** är med följande typer och autentiseringsmetoder:

    | Informationslagringstyp som stöds                             | Autentiseringstyp för källa som stöds                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure-blobb](connector-azure-blob-storage.md)                | Autentisering av kontonyckel, autentisering av hanterade identiteter |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autentisering av tjänstens huvudnamn                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autentisering av kontonyckel, autentisering av hanterade identiteter |

    >[!IMPORTANT]
    >Om din Azure Storage har konfigurerats med VNet-tjänstslutpunkt måste du använda autentisering av hanterad identitet – se Effekten av att [använda VNet-tjänstslutpunkter med Azure-lagring](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig de konfigurationer som krävs i Data Factory från [Azure Blob - autentisering av hanterade identiteter](connector-azure-blob-storage.md#managed-identity) och Azure Data Lake Storage Gen2 - autentiseringsavsnittet för [hanterad identitet.](connector-azure-data-lake-storage.md#managed-identity)

2. **Källdataformatet** är av **parett**, **ORC**eller **avgränsad text**, med följande konfigurationer:

   1. Mappsökvägen innehåller inte jokerteckenfilter.
   2. Filnamnet är tomt eller pekar på en enda fil. Om du anger jokerteckenfilnamn i kopieringsaktivitet kan det bara vara `*` eller `*.*`.
   3. `rowDelimiter`är **standard**, **\n**, **\r\n**eller **\r**.
   4. `nullValue`lämnas som standard eller är inställd `treatEmptyAsNull` på tom **sträng** (""), och lämnas som standard eller är inställd på true.
   5. `encodingName`lämnas som standard eller är inställd på **utf-8**.
   6. `quoteChar`, `escapeChar`och `skipLineCount` har inte angetts. PolyBase-stöd hoppar över rubrikraden, `firstRowAsHeader` som kan konfigureras som i ADF.
   7. `compression`kan inte vara **någon komprimering,** **GZip**eller **Deflate**.

3. Om källan är en `recursive` mapp måste kopieringsaktiviteten vara true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`och `modifiedDateTimeEnd` `additionalColumns` har inte angetts.

>[!NOTE]
>Om källan är en mapp hämtar du PolyBase filer från mappen och alla dess undermappar, och den hämtar inte data från filer för vilka filnamnet börjar med en understrykning (_) eller en punkt (.), som dokumenteras [här - LOCATION-argumentet](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

### <a name="staged-copy-by-using-polybase"></a>Iscensatt kopia med hjälp av PolyBase

När källdata inte är kompatibla med PolyBase aktiverar du datakopiering via en tillfällig mellanlagring av Azure Blob-lagringsinstans (det kan inte vara Azure Premium Storage). I det här fallet konverterar Azure Data Factory automatiskt data för att uppfylla kraven för dataformat i PolyBase. Sedan anropas PolyBase för att läsa in data i SQL Data Warehouse. Slutligen rensas dina tillfälliga data från blob-lagringen. Se [Stegvis kopia](copy-activity-performance-features.md#staged-copy) för information om hur du kopierar data via en mellanlagring av Azure Blob-lagringsinstans.

Om du vill använda den här funktionen skapar du en [Azure Blob Storage-länkad tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure-lagringskontot med interim blob-lagring. Ange sedan `enableStaging` `stagingSettings` egenskaperna och för kopieringsaktiviteten enligt följande kod.

>[!IMPORTANT]
>Om din mellanlagring Azure Storage har konfigurerats med VNet-tjänstslutpunkt måste du använda autentisering av hanterad identitet – se Effekten av att [använda VNet-tjänstslutpunkter med Azure-lagring](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig de konfigurationer som krävs i Data Factory från [Azure Blob - autentisering av hanterad identitet](connector-azure-blob-storage.md#managed-identity).

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

Följande avsnitt innehåller metodtips utöver de som nämns i [Metodtips för Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Obligatorisk databasbehörighet

Om du vill använda PolyBase måste användaren som läser in data i SQL Data Warehouse ha [behörigheten "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) i måldatabasen. Ett sätt att uppnå detta är att lägga till användaren som medlem i **db_owner** roll. Lär dig hur du gör det i [ÖVERSIKTEN FÖR SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Radstorlek och datatypsgränser

PolyBase-belastningar är begränsade till rader som är mindre än 1 MB. Den kan inte användas för att läsa in till VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Mer information finns i [sql data warehouse-tjänstkapacitetsbegränsningar](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När källdata har rader som är större än 1 MB kanske du vill dela upp källtabellerna lodrätt i flera små. Kontrollera att den största storleken på varje rad inte överskrider gränsen. De mindre tabellerna kan sedan läsas in med Hjälp av PolyBase och slås samman i Azure Synapse Analytics.

Alternativt, för data med så breda kolumner, kan du använda icke-PolyBase för att läsa in data med hjälp av ADF, genom att stänga av inställningen "tillåt PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Resursklass för SQL Data Warehouse

För att uppnå bästa möjliga dataflöde, tilldela en större resursklass till användaren som läser in data i SQL Data Warehouse via PolyBase.

#### <a name="polybase-troubleshooting"></a>Felsökning av PolyBase

**Läsa in i decimalkolumnen**

Om källdata är i textformat eller andra icke-PolyBase-kompatibla butiker (med mellanslagna kopior och PolyBase) och den innehåller tomt värde som ska läsas in i kolumnen Decimal i SQL-datalager, kan du få följande fel:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Lösningen är att avmarkera alternativet "**Använd typtyp**" (som falskt) i kopieringsaktivitetsmottagaren -> PolyBase-inställningarna. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" är en inbyggd PolyBase-konfiguration som anger hur värden som saknas i avgränsade textfiler ska hanteras när PolyBase hämtar data från textfilen. 

**`tableName`i Azure Synapse Analytics**

I följande tabell finns exempel på hur du anger egenskapen **tableName** i JSON-datauppsättningen. Den visar flera kombinationer av schema och tabellnamn.

| DB-schema | Tabellnamn | **tableName (tableName)** JSON, egenskapen               |
| --------- | ---------- | ----------------------------------------- |
| Dbo       | MyTable (MyTable)    | MyTable eller dbo. MyTable eller [dbo]. [MyTable] |
| dbo1 (på andra)      | MyTable (MyTable)    | dbo1. MyTable eller [dbo1]. [MyTable]          |
| Dbo       | My.Table (bord)   | [My.Table] eller [dbo]. [Mitt.Bord]            |
| dbo1 (på andra)      | My.Table (bord)   | [dbo1]. [Mitt.Bord]                         |

Om följande fel visas kan problemet bero på det värde som du angav för egenskapen **tableName.** Se den föregående tabellen för rätt sätt att ange värden för **egenskapen tableName** JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumner med standardvärden**

För närvarande accepterar PolyBase-funktionen i Data Factory endast samma antal kolumner som i måltabellen. Ett exempel är en tabell med fyra kolumner där en av dem definieras med ett standardvärde. Indata måste fortfarande ha fyra kolumner. En indatauppsättning med tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en särskild form av standardvärdet. Om kolumnen är nullable kan indata i blobben för den kolumnen vara tomma. Men det kan inte saknas från indatauppsättningen. PolyBase infogar NULL för saknade värden i Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Använd COPY-uttryck för att läsa in data i Azure SQL Data Warehouse (förhandsversion)

SQL Data Warehouse [COPY-uttryck](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (förhandsversion) stöder direkt inläsning av data från **Azure Blob och Azure Data Lake Storage Gen2**. Om källdata uppfyller de kriterier som beskrivs i det här avsnittet kan du välja att använda COPY-uttryck i ADF för att läsa in data i Azure SQL Data Warehouse. Azure Data Factory kontrollerar inställningarna och misslyckas kopieringsaktiviteten köra om villkoren inte uppfylls.

>[!NOTE]
>För närvarande Stöder Data Factory endast kopia från COPY-satsens kompatibla källor som nämns nedan.

Att använda COPY-sats stöder följande konfiguration:

1. Den **källlänkade tjänsten och formatet** är med följande typer och autentiseringsmetoder:

    | Informationslagringstyp som stöds                             | Format som stöds           | Autentiseringstyp för källa som stöds                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure-blobb](connector-azure-blob-storage.md)                | [Avgränsad text](format-delimited-text.md)             | Autentisering av kontonyckel, autentisering av signatur för delad åtkomst, autentisering av tjänstens huvudnamn, autentisering av hanterad identitet |
    | &nbsp;                                                       | [Parkett](format-parquet.md)                    | Autentisering av kontonyckel, autentisering av signatur för delad åtkomst |
    | &nbsp;                                                       | [Orc](format-orc.md)                        | Autentisering av kontonyckel, autentisering av signatur för delad åtkomst |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Avgränsad text](format-delimited-text.md)<br/>[Parkett](format-parquet.md)<br/>[Orc](format-orc.md) | Autentisering av kontonyckel, autentisering av tjänstens huvudnamn, autentisering av hanterad identitet |

    >[!IMPORTANT]
    >Om din Azure Storage har konfigurerats med VNet-tjänstslutpunkt måste du använda autentisering av hanterad identitet – se Effekten av att [använda VNet-tjänstslutpunkter med Azure-lagring](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig de konfigurationer som krävs i Data Factory från [Azure Blob - autentisering av hanterade identiteter](connector-azure-blob-storage.md#managed-identity) och Azure Data Lake Storage Gen2 - autentiseringsavsnittet för [hanterad identitet.](connector-azure-data-lake-storage.md#managed-identity)

2. Formatinställningarna är med följande:

   1. För **Parkett:** `compression` kan inte vara **någon komprimering,** **Snappy**eller **GZip**.
   2. För **ORC:** `compression` kan inte **```zlib```** vara någon **komprimering**, eller **Snappy**.
   3. För **avgränsad text:**
      1. `rowDelimiter`uttryckligen anges som **ett tecken** eller "**\r\n**", stöds inte standardvärdet.
      2. `nullValue`lämnas som standard eller är inställd på **tom sträng** ("").
      3. `encodingName`lämnas som standard eller är inställd på **utf-8 eller utf-16**.
      4. `escapeChar`måste vara `quoteChar`samma som och inte vara tom.
      5. `skipLineCount`lämnas som standard eller är inställd på 0.
      6. `compression`kan inte vara **någon komprimering** eller **GZip**.

3. Om källan är en `recursive` mapp måste kopieringsaktiviteten vara true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`och `modifiedDateTimeEnd` `additionalColumns` har inte angetts.

Följande copy-programsatsinställningar `allowCopyCommand` stöds under i kopieringsaktivitet:

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Anger standardvärdena för varje målkolumn i SQL DW.  Standardvärdena i egenskapen skriver över standardvillkorsuppsättningen i informationslagret och identitetskolumnen kan inte ha ett standardvärde. | Inga |
| ytterligareAlternativ | Ytterligare alternativ som skickas till SQL DW COPY-uttryck direkt i "Med"-satsen i [COPY-satsen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Citera värdet efter behov för att justera med COPY-utdragskraven. | Inga |

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


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Aktivitetsegenskaper för GetMetadata

Om du vill veta mer om egenskaperna kontrollerar du [GetMetadata-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datatypsmappning för Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du omvandlar data i mappning av dataflöde kan du läsa och skriva till tabeller från Azure Synapse Analytics. Mer information finns i [källomvandlingen](data-flow-source.md) och [sink-omvandlingen](data-flow-sink.md) vid mappning av dataflöden.

### <a name="source-transformation"></a>Omvandling av källa

Inställningar som är specifika för Azure Synapse Analytics är tillgängliga på fliken **Källalternativ** i källomvandlingen. 

**Ingång:** Välj om du pekar källan mot ```Select * from <table-name>```en tabell (motsvarande) eller anger en anpassad SQL-fråga.

**Fråga**: Om du väljer Fråga i indatafältet anger du en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i datauppsättningen. **Order** by-satser stöds inte här, men du kan ange en fullständig SELECT FROM-sats. Du kan också använda användardefinierade tabellfunktioner. **välj * från udfGetData()** är en UDF i SQL som returnerar en tabell. Den här frågan skapar en källtabell som du kan använda i dataflödet. Att använda frågor är också ett bra sätt att minska rader för testning eller för uppslag. 

* SQL-exempel:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchstorlek:** Ange en batchstorlek för att segmenta stora data i läsningar. I dataflöden använder ADF den här inställningen för att ställa in Spark columnar-cachelagring. Det här är ett alternativfält som använder Spark som standard om det lämnas tomt.

**Isoleringsnivå**: Standardvärdet för SQL-källor vid mappning av dataflöde läss inte. Du kan ändra isoleringsnivån här till ett av dessa värden:
* Läs bekräftad
* Läs oengagerad
* Repeterbar läsning
* Serialiserbar
* Ingen (ignorera isoleringsnivå)

![Isoleringsnivå](media/data-flow/isolationlevel.png "Isoleringsnivå")

### <a name="sink-transformation"></a>Sink omvandling

Inställningar som är specifika för Azure Synapse Analytics är tillgängliga på fliken **Inställningar** i sink-omvandlingen.

**Uppdateringsmetod:** Bestämmer vilka åtgärder som är tillåtna på databasens mål. Standard är att endast tillåta infogningar. För att uppdatera, upsert eller ta bort rader krävs en omvandling på en rad för att tagga rader för dessa åtgärder. För uppdateringar, upserts och borttagningar måste en nyckelkolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

**Tabellåtgärd:** Bestämmer om alla rader ska återskapas eller tas bort från måltabellen innan du skriver.
* Ingen: Ingen åtgärd kommer att göras i tabellen.
* Återskapa: Tabellen kommer att släppas och återskapas. Krävs om du skapar en ny tabell dynamiskt.
* Trunkera: Alla rader från måltabellen tas bort.

**Aktivera mellanlagring:** Avgör om [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) ska användas eller inte när du skriver till Azure Synapse Analytics

**Batchstorlek:** Styr hur många rader som skrivs i varje bucket. Större batchstorlekar förbättrar komprimerings- och minnesoptimeringen, men risken är på minnesundantag när data cachelagrar.

**Före- och post-SQL-skript:** Ange flerradiga SQL-skript som ska köras före (förbearbetning) och efter (efterbearbetning) data skrivs till din Sink-databas

![för- och efter-SQL-bearbetningsskript](media/data-flow/prepost1.png "SQL-bearbetningsskript")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Datatypsmappning för Azure Synapse Analytics

När du kopierar data från eller till Azure Synapse Analytics används följande mappningar från Azure Synapse Analytics-datatyper till Azure Data Factory interimsdatatyper. Se [schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta hur Kopiera aktivitet mappar källschemat och datatypen till diskhon.

>[!TIP]
>Se [Tabelldatatyper i Azure Synapse Analytics-artikeln](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) om SQL DW-stödda datatyper och lösningarna för data som inte stöds.

| Azure Synapse Analytics-datatyp    | Data Factory interimdatatyp |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | Sträng, Röding[]                 |
| date                                  | DateTime                       |
| Datumtid                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datumtidsdatum                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM-attribut (varbinary(max)) | Byte[]                         |
| Float (Flyttal)                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | Sträng, Röding[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | Sträng, Röding[]                 |
| real                                  | Enkel                         |
| Rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16 (int16)                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | Sträng, Röding[]                 |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor efter kopiera aktivitet i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
