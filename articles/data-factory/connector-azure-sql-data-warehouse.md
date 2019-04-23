---
title: Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds butiker till Azure SQL Data Warehouse eller SQL Data Warehouse till mottagarens datalager med Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: jingwang
ms.openlocfilehash: b97d21503e8dcd75906581faf1851533bcd69fa6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009352"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuell version](connector-azure-sql-data-warehouse.md)

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure SQL Data Warehouse. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure SQL Data Warehouse till alla datalager för mottagare som stöds. Och du kan kopiera data från alla dataarkiv till Azure SQL Data Warehouse. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure SQL Data Warehouse-anslutningsappen dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och autentisering med Azure Active Directory (Azure AD) Application-token med ett tjänstens huvudnamn eller hanterade identiteter för Azure-resurser.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Läs in data med hjälp av PolyBase eller en grupp Infoga som en mottagare. Vi rekommenderar PolyBase för ger kopieringen bättre prestanda.

> [!IMPORTANT]
> Observera att PolyBase stöder endast SQL-autentisering men inte Azure AD-autentisering.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime kan du konfigurera en [Azure SQL-serverbrandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en lokal integration runtime kan du konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Det här intervallet inkluderar den datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

> [!TIP]
> För att uppnå bästa prestanda kan du använda PolyBase för att läsa in data i Azure SQL Data Warehouse. Den [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) -avsnittet innehåller information om. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory-entiteter som är specifika för en Azure SQL Data Warehouse-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för en länkad Azure SQL Data Warehouse-tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureSqlDW**. | Ja |
| connectionString | Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instansen för den **connectionString** egenskapen. <br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också publicera nyckel för lösenord/tjänstens huvudnamn i Azure Key Vault, och om den är SQL-autentisering pull den `password` konfiguration av anslutningssträngen. Se JSON-exemplet nedan i tabellen och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. | Nej |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel, respektive:

- [SQL-autentisering](#sql-authentication)
- Azure AD-token-autentisering: [Tjänstens huvud](#service-principal-authentication)
- Azure AD-token-autentisering: [Hanterade identiteter för Azure-resurser](#managed-identity)

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
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Följ dessa steg om du vill använda tokenautentisering för service principal-baserade Azure AD-program:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen med hanterad identitet en administratörsroll kan du hoppa över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja behörigheter krävs för tjänstens huvudnamn** som du brukar för SQL-användare eller andra. Kör följande kod eller hänvisa till fler alternativ [här](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurera en länkad Azure SQL Data Warehouse-tjänsten** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Länkad tjänst-exempel som använder autentisering av tjänstens huvudnamn

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

### <a name="managed-identity"></a> Hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika fabriken. Du kan använda den här hanterad identitet för Azure SQL Data Warehouse-autentisering. Den angivna datafabriken kan komma åt och kopieringsdata från eller till dina data warehouse med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitet-autentisering:

1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen med hanterad identitet en administratörsroll kan du hoppa över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

2. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för Data Factory hanterad identitet. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL. 
    
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Bevilja behörigheter krävs för Data Factory hanterade identiteter** som du brukar för SQL-användare och andra. Kör följande kod eller hänvisa till fler alternativ [här](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

5. **Konfigurera en länkad Azure SQL Data Warehouse-tjänsten** i Azure Data Factory.

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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse-datauppsättningen.

För att kopiera data från eller till Azure SQL Data Warehouse, ange den **typ** egenskapen på datauppsättningen till **AzureSqlDWTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskap måste anges till **AzureSqlDWTable**. | Ja |
| tableName | Namnet på tabellen eller vyn i Azure SQL Data Warehouse-instans som den länkade tjänsten refererar till. | Nej för källa, Ja för mottagare |

#### <a name="dataset-properties-example"></a>Exempel med datauppsättningen egenskaper

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse källa och mottagare.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse som källa

Om du vill kopiera data från Azure SQL Data Warehouse, ange den **typ** -egenskapen i Kopieringsaktiviteten källan till **SqlDWSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **SqlDWSource**. | Ja |
| sqlReaderQuery | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |

### <a name="points-to-note"></a>Saker att Observera

- Om den **sqlReaderQuery** har angetts för den **SqlSource**, Kopieringsaktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data. Du kan också ange en lagrad procedur. Ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar parametrar för den lagrade proceduren.
- Om du inte anger något **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, kolumnerna som definieras i den **struktur** delen av datauppsättnings-JSON är van vid Skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL Data Warehouse. Om definitionen för datauppsättningen inte har den **struktur**, alla kolumner har valts från tabellen.

#### <a name="sql-query-example"></a>Exempel för SQL-fråga

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

#### <a name="stored-procedure-example"></a>Lagrad procedur-exempel

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

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse som mottagare

Om du vill kopiera data till Azure SQL Data Warehouse, ange Mottagartyp i Kopieringsaktiviteten till **SqlDWSink**. Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **SqlDWSink**. | Ja |
| allowPolyBase | Anger om du vill använda PolyBase, när så är tillämpligt, i stället för BULKINSERT mekanism. <br/><br/> Vi rekommenderar att du läser in data i SQL Data Warehouse med PolyBase. Se den [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet begränsningar och information.<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard).  | Nej |
| polyBaseSettings | En grupp egenskaper som kan anges när den **allowPolybase** är inställd på **SANT**. | Nej |
| rejectValue | Anger det tal eller procentandelen rader som kan avvisas innan frågan inte kunde köras.<br/><br/>Läs mer om Polybases avvisningsalternativen i avsnittet argument i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Tillåtna värden är 0 (standard), 1, 2, osv. |Nej |
| rejectType | Anger om den **rejectValue** alternativet är ett exakt värde eller en procentandel.<br/><br/>Tillåtna värden är **värdet** (standard) och **procent**. | Nej |
| rejectSampleValue | Anger antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade raden.<br/><br/>Tillåtna värden är 1, 2, osv. | Ja, om den **rejectType** är **procent**. |
| useTypeDefault | Anger hur du hanterar värden som saknas i avgränsade textfiler när PolyBase hämtar data från textfilen.<br/><br/>Mer information om den här egenskapen från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard). | Nej |
| writeBatchSize | Infogar data i SQL-tabell när buffertstorleken når **writeBatchSize**. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **heltal** (antal rader). | Nej. Standardvärdet är 10 000. |
| writeBatchTimeout | Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **timespan**. Exempel: ”00: 30:00” (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan du skriver data till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen för att rensa förinstallerade data. | Nej |

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

Läs mer om hur du använder PolyBase för att effektivt läsa in SQL Data Warehouse i nästa avsnitt.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använda PolyBase för att läsa in data i Azure SQL Data Warehouse

Med hjälp av [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in en stor mängd data till Azure SQL Data Warehouse med högt dataflöde. Du ser en stor vinst i dataflödet med PolyBase i stället för BULKINSERT standardmekanismen. Se [Prestandareferens](copy-activity-performance.md#performance-reference) en detaljerad jämförelse. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Om dina källdata finns i **Azure Blob, Azure Data Lake Storage Gen1 eller Azure Data Lake Storage Gen2**, och **format är PolyBase kompatibel**, du kan använda Kopieringsaktivitet för att anropa direkt PolyBase för att låta Azure SQL Data Warehouse kan du hämta data från källan. Mer information finns i  **[dirigera kopiera genom att använda PolyBase](#direct-copy-by-using-polybase)**.
* Om dina källdatalagret och format ursprungligen inte stöds av PolyBase, använder du den **[mellanlagrad kopiering genom att använda PolyBase](#staged-copy-by-using-polybase)** funktionen i stället. Funktionen mellanlagrad kopiering ger dig också bättre genomströmning. Den konverterar automatiskt data till PolyBase-kompatibelt format. Och den lagrar data i Azure Blob storage. Det hämtar sedan data till SQL Data Warehouse.

### <a name="direct-copy-by-using-polybase"></a>Direct kopiera genom att använda PolyBase

SQL Data Warehouse PolyBase stöder direkt Azure Blob, Azure Data Lake Storage Gen1 och Gen2 för Azure Data Lake Storage. Om dina källdata uppfyller kriterierna som beskrivs i det här avsnittet, kan du använda PolyBase för att kopiera direkt från källans datalager till Azure SQL Data Warehouse. Annars kan du använda [mellanlagrad kopiering genom att använda PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> För att kopiera data effektivt till SQL Data Warehouse, Läs mer i [Azure Data Factory gör det ännu enklare och praktiskt att få fram insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, Azure Data Factory kontrollerar du inställningarna och faller automatiskt tillbaka till BULKINSERT mekanism för dataförflyttning.

1. Den **källa länkad tjänst** är med följande typer och autentiseringsmetoder:

    | Stöds datalagertyp | Typ av autentisering som stöds |
    |:--- |:--- |
    | [Azure Blob](connector-azure-blob-storage.md) | Konto-nyckelautentisering |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autentisering av tjänstens huvudnamn |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Konto-nyckelautentisering |

2. Den **datauppsättning källformatet** är av **ParquetFormat**, **OrcFormat**, eller **TextFormat**, med följande konfigurationer:

   1. `folderPath` och `fileName` inte innehåller jokertecken-filtret.
   2. `rowDelimiter` måste vara **\n**.
   3. `nullValue` anges antingen till **tom sträng** (””) eller till vänster som standard och `treatEmptyAsNull` är lämnas som standard eller angetts till true.
   4. `encodingName` anges till **utf-8**, vilket är standardvärdet.
   5. `escapeChar`, `quoteChar` och `skipLineCount` inte anges. Stöd för PolyBase hoppa över rubrikraden, vilket kan konfigureras som `firstRowAsHeader` i ADF.
   6. `compression` kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

      ```json
      "typeProperties": {
        "folderPath": "<path>",
        "format": {
            "type": "TextFormat",
            "columnDelimiter": "<any delimiter>",
            "rowDelimiter": "\n",
            "nullValue": "",
            "encodingName": "utf-8",
            "firstRowAsHeader": <any>
        }
      },
      ```

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
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
                "type": "BlobSource",
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

När dina källdata inte uppfyller villkoret i föregående avsnitt, aktivera kopiering via en mellanliggande mellanlagring Azure Blob storage-instans. Den kan inte Azure Premium Storage. Azure Data Factory körs i det här fallet automatiskt transformationer på data för att uppfylla formatkraven data med PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse. Slutligen rensar den tillfälliga data från blob-lagringen. Se [mellanlagrad kopiering](copy-activity-performance.md#staged-copy) mer information om hur du kopierar data via en mellanlagrings Azure Blob storage-instans.

Om du vill använda denna funktion kan du skapa en [länkad Azure Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure storage-konto med tillfälliga blob-lagringen. Ange sedan den `enableStaging` och `stagingSettings` egenskaper för Kopieringsaktiviteten som du ser i följande kod:

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

## <a name="best-practices-for-using-polybase"></a>Metodtips för att använda PolyBase

Följande avsnitt innehåller bästa praxis utöver de som nämns i [Metodtips för Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Behörighet krävs

Användaren som läser in data i SQL Data Warehouse måste ha för att använda PolyBase [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) i måldatabasen. Ett sätt att uppnå som är att lägga till användaren som en medlem i den **db_owner** roll. Lär dig hur du gör den [översikt över SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Ange gränser Radstorleken och data

PolyBase-inläsningar är begränsade till rader som är mindre än 1 MB. De kan inte läsa in VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Mer information finns i [kapacitetsbegränsningar i SQL Data Warehouse-tjänsten](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När dina källdata innehåller rader som är större än 1 MB, kanske du vill dela lodrätt källtabellerna i flera små. Kontrollera att den största storleken på varje rad inte överskrider gränsen. Mindre tabeller kan sedan lästs in med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklass

Tilldela en större resursklass till användaren som läser in data i SQL Data Warehouse via PolyBase för att uppnå bästa möjliga genomflöde.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** i Azure SQL Data Warehouse

I följande tabell innehåller exempel på hur du anger den **tableName** egenskap i JSON-datauppsättning. Den visar flera kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamn | **tableName** JSON-egenskap |
| --- | --- | --- |
| dbo | MyTable | MyTable eller dbo. MyTable eller [dbo]. [Tabell] |
| dbo1 | MyTable | dbo1. MyTable eller [dbo1]. [Tabell] |
| dbo | My.Table | [My.Table] eller [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Om du ser följande fel kan problemet vara värdet du angav för den **tableName** egenskapen. Se tabellen ovan för det korrekta sättet att ange värden för den **tableName** JSON-egenskap.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden

PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Ett exempel är en tabell med fyra kolumner där en av dem har definierats med ett standardvärde. Indata måste fortfarande ha fyra kolumner. En indatauppsättning med tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en särskild form av standardvärdet. Om kolumnen är nullbar vara inkommande data i blob för kolumnen tom. Men den kan inte vara saknas från datauppsättningen för indata. PolyBase infogas NULL för värden som saknas i Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datatypsmappningen för Azure SQL Data Warehouse

När du kopierar data från eller till Azure SQL Data Warehouse, används följande mappningar från Azure SQL Data Warehouse-datatyper till Azure Data Factory tillfälliga-datatyper. Se [schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Azure SQL Data Warehouse-datatyp | Data Factory tillfälliga datatyp |
|:--- |:--- |
| bigint | Int64 |
| binary | Byte[] |
| bit | Boolean |
| char | String, Char[] |
| date | Datetime |
| Datetime | Datetime |
| datetime2 | Datetime |
| Datetimeoffset | Datetimeoffset |
| Decimal | Decimal |
| FILESTREAM-attributet (varbinary(max)) | Byte[] |
| Float | Double |
| image | Byte[] |
| int | Int32 |
| money | Decimal |
| nchar | String, Char[] |
| ntext | String, Char[] |
| numeriskt | Decimal |
| nvarchar | String, Char[] |
| real | Single |
| ROWVERSION | Byte[] |
| smalldatetime | Datetime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql_variant | Object |
| text | String, Char[] |
| time | TimeSpan |
| tidsstämpel | Byte[] |
| tinyint | Byte |
| uniqueidentifier | Guid |
| Varbinary | Byte[] |
| varchar | String, Char[] |
| xml | Xml |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
