---
title: Kopiera data till och från Oracle med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från källarkiv som stöds till en Oracle-databas eller från Oracle till sink-butiker som stöds med hjälp av Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 874c685491774e2a318ae0a8b7394945a51b2f7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244516"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiera data från och till Oracle med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuell version](connector-oracle.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från och till en Oracle-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Oracle-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en Oracle-databas till alla sink-datalager som stöds. Du kan också kopiera data från alla källdatalager som stöds till en Oracle-databas. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Specifikt stöder den här Oracle-anslutningen:

- Följande versioner av en Oracle-databas:
    - Oracle 18c R1 (18.1) och högre
    - Oracle 12c R1 (12.1) och högre
    - Oracle 11g R1 (11,1) och högre
    - Oracle 10g R1 (10,1) och högre
    - Oracle 9i R2 (9,2) och högre
    - Oracle 8i R3 (8.1.7) och högre
    - Oracle-databas moln Exadata-tjänst
- Parallellkopiering från en Oracle-källa. Mer information finns i avsnittet [Parallellkopia från Oracle.](#parallel-copy-from-oracle)

> [!Note]
> Oracle-proxyserver stöds inte.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integrationskörningen ger en inbyggd Oracle-drivrutin. Därför behöver du inte installera en drivrutin manuellt när du kopierar data från och till Oracle.

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Oracle-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Den Oracle-länkade tjänsten stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Oracle**. | Ja |
| Connectionstring | Anger den information som behövs för att ansluta till Oracle Database-instansen. <br/>Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Se följande exempel och [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information. <br><br>**Anslutningstyp som stöds**: Du kan använda **Oracle SID** eller **Oracles tjänstnamn** för att identifiera databasen:<br>- Om du använder SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Om du använder Tjänstnamn:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>För avancerade Oracle-inbyggda anslutningsalternativ kan du välja att lägga till en post i [TNSNAMES. ORA-filen](http://www.orafaq.com/wiki/Tnsnames.ora) på Oracle-servern och i ADF Oracles länkade tjänst väljer du att använda anslutningstypen Oracle-tjänstnamn och konfigurera motsvarande tjänstnamn. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om inget anges används standardkörningen för Azure Integration Runtime. |Inga |

>[!TIP]
>Om du får ett felmeddelande, "ORA-01025: UPI parameter out of range" `WireProtocolMode=1` och din Oracle-version är 8i, lägger du till din anslutningssträng. Försök sedan igen.

Fler anslutningsegenskaper som du kan ange i anslutningssträngen per ärende:

| Egenskap | Beskrivning | Tillåtna värden |
|:--- |:--- |:--- |
| Matrisstorlek |Antalet byte som anslutningen kan hämta i en enda nätverksresa. T.ex. `ArraySize=‭10485760‬`<br/><br/>Större värden ökar dataflödet genom att minska antalet gånger för att hämta data över nätverket. Mindre värden ökar svarstiden, eftersom det är mindre fördröjning som väntar på att servern ska överföra data. | Ett heltal från 1 till 4294967296 (4 GB). Standardvärdet `60000`är . Värdet 1 definierar inte antalet byte, men anger allokeringsutrymme för exakt en rad data. |

Om du vill aktivera kryptering på Oracle-anslutning har du två alternativ:

-   Om du vill använda **Triple-DES Encryption (3DES) och Advanced Encryption Standard (AES)** går du till Oracle Advanced Security (OAS) på Oracle-serversidan och konfigurerar krypteringsinställningarna. Mer information finns i den här [Oracle-dokumentationen](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Oracle Application Development Framework (ADF) -anslutningen förhandlar automatiskt om krypteringsmetoden för att använda den du konfigurerar i OAS när du upprättar en anslutning till Oracle.

-   Så här använder du **SSL:**

    1.  Hämta SSL-certifikatinformationen. Hämta der-kodade certifikatinformation (Distinguished Encoding Rules) för ditt SSL-certifikat och spara utdata (----- Begin-certifikat ... Avsluta certifikat -----) som en textfil.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exempel:** Extrahera cert info från DERcert.cer och spara sedan utdata till cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Bygg `keystore` eller `truststore`. Följande kommando skapar `truststore` filen, med eller utan lösenord, i PKCS-12-format.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exempel:** Skapa en PKCS12-fil `truststore` med namnet MyTrustStoreFile med ett lösenord.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Placera `truststore` filen på den självvärderade IR-datorn. Placera till exempel filen på C:\MyTrustStoreFile.
    4.  Konfigurera Oracle-anslutningssträngen med `EncryptionMethod=1` och motsvarande `TrustStore` / `TrustStorePassword`värde i Azure Data Factory. Till exempel `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exempel:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösenord i Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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
## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar](concepts-datasets-linked-services.md). 

Om du vill kopiera data från och till Oracle `OracleTable`anger du egenskapen typ för datauppsättningen till . Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste `OracleTable`anges till . | Ja |
| Schemat | Namnet på schemat. |Nej för källa, Ja för diskho  |
| tabell | Namn på tabellen/vyn. |Nej för källa, Ja för diskho  |
| tableName | Namn på tabellen/vyn med schema. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table`. | Nej för källa, Ja för diskho |

**Exempel:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-källan och diskhon. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle som källa

>[!TIP]
>Om du vill läsa in data från Oracle effektivt med hjälp av datapartitionering kan du läsa mer från [Parallellkopia från Oracle](#parallel-copy-from-oracle).

Om du vill kopiera data från Oracle anger `OracleSource`du källtypen i kopieringsaktiviteten till . Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopians `OracleSource`aktivitet måste anges till . | Ja |
| orakelLäsareQuery | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla motsvarande inbyggda partitionsparametrar i frågan. Exempel på exempel finns i avsnittet [Parallellkopia från Oracle.](#parallel-copy-from-oracle) | Inga |
| partitionOptions | Anger de datapartitionsalternativ som används för att läsa in data från Oracle. <br>Tillåtna värden är: **Inga** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitionsalternativ är aktiverat (det vill ha), `None`styrs graden av parallellitet till samtidig inläsning av data från en Oracle-databas av [`parallelCopies`](copy-activity-performance.md#parallel-copy) inställningen på kopieringsaktiviteten. | Inga |
| partitionSätta | Ange gruppen för inställningarna för datapartitionering. <br>Använd när partitionsalternativet `None`inte är . | Inga |
| partitionNames (partitionNames) | Listan över fysiska partitioner som måste kopieras. <br>Använd när partitionsalternativet är `PhysicalPartitionsOfTable`. Om du använder en fråga för `?AdfTabularPartitionName` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Oracle.](#parallel-copy-from-oracle) | Inga |
| partitionColumnName | Ange namnet på källkolumnen **i heltalstyp** som ska användas av områdespartitionering för parallellkopia. Om inget anges identifieras den primära nyckeln i tabellen automatiskt och används som partitionskolumn. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder en fråga för `?AdfRangePartitionColumnName` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Oracle.](#parallel-copy-from-oracle) | Inga |
| partitionUpperBound | Det maximala värdet för partitionskolumnen för att kopiera data ut. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder en fråga för `?AdfRangePartitionUpbound` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Oracle.](#parallel-copy-from-oracle) | Inga |
| partitionLowerBound | Det minsta värdet för partitionskolumnen för att kopiera data ut. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder en fråga för `?AdfRangePartitionLowbound` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Oracle.](#parallel-copy-from-oracle) | Inga |

**Exempel: kopiera data med hjälp av en grundläggande fråga utan partition**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle som diskbänk

Om du vill kopiera data till Oracle anger `OracleSink`du handfänktypen i kopieringsaktiviteten till . Följande egenskaper stöds i avsnittet kopiera **aktivitetsmottagare.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för kopiaaktivitetsmottagaren måste anges till `OracleSink`. | Ja |
| skriverBatchSize | Infogar data i SQL-tabellen när `writeBatchSize`buffertstorleken når .<br/>Tillåtna värden är Heltal (antal rader). |Nej (standard är 10 000) |
| skriverBatchTimeout | Väntetiden för batchinsatsen att slutföras innan den time out.<br/>Tillåtna värden är Timespan. Ett exempel är 00:30:00 (30 minuter). | Inga |
| preCopyScript | Ange en SQL-fråga för kopieringsaktiviteten som ska köras innan du skriver data till Oracle i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Parallell kopia från Oracle

Data Factory Oracle-anslutningen tillhandahåller inbyggd datapartitionering för att kopiera data från Oracle parallellt. Du hittar alternativ för datapartitionering på fliken **Källa** för kopieringsaktiviteten.

![Skärmbild av partitionsalternativ](./media/connector-oracle/connector-oracle-partition-options.png)

När du aktiverar partitionerad kopia kör Data Factory parallella frågor mot Oracle-källan för att läsa in data efter partitioner. Den parallella graden [`parallelCopies`](copy-activity-performance.md#parallel-copy) styrs av inställningen på kopieringsaktiviteten. Om du till `parallelCopies` exempel anger fyra genererar och kör Data Factory samtidigt fyra frågor baserat på det angivna partitionsalternativet och inställningarna, och varje fråga hämtar en del data från Oracle-databasen.

Du föreslås aktivera parallellkopiering med datapartitionering, särskilt när du läser in stora mängder data från Oracle-databasen. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat datalager rekommenderas att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Full belastning från stor tabell, med fysiska partitioner.          | **Partition alternativ**: Fysiska partitioner av tabellen. <br><br/>Under körningen identifierar Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. |
| Fullständig belastning från stor tabell, utan fysiska partitioner, medan med en heltalskolumn för datapartitionering. | **Partitionsalternativ**: Partitionering med dynamiskt omfång.<br>**Partitionskolumnen**: Ange den kolumn som används för att partitionera data. Om inget anges används kolumnen primärnyckel. |
| Läs in en stor mängd data med hjälp av en anpassad fråga med fysiska partitioner. | **Partition alternativ**: Fysiska partitioner av tabellen.<br>**Fråga** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**Partitionsnamn**: Ange de partitionsnamn som data ska kopieras från. Om inget anges identifierar Data Factory automatiskt de fysiska partitionerna i tabellen som du angav i Oracle-datauppsättningen.<br><br>Under körningen ersätter `?AdfTabularPartitionName` Data Factory med det faktiska partitionsnamnet och skickar till Oracle. |
| Läs in en stor mängd data med hjälp av en anpassad fråga, utan fysiska partitioner, medan du har en heltalskolumn för datapartitionering. | **Partitionsalternativ**: Partitionering med dynamiskt omfång.<br>**Fråga** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partitionskolumnen**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med heltalsdatatyp.<br>**Partitionens övre gräns** och **partitionens nedre gräns:** Ange om du vill filtrera mot partitionskolumnen för att hämta data endast mellan det nedre och det övre intervallet.<br><br>Under körningen ersätter `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory , och med det faktiska kolumnnamn och värdeintervall för varje partition och skickas till Oracle. <br>Om partitionskolumnen "ID" till exempel är inställd med den nedre gränsen som 1 och den övre gränsen som 80, med parallellkopieringsuppsättning som 4, hämtar Data Factory data med 4 partitioner. Deras ID är mellan [1,20], [21, 40], [41, 60] respektive [61, 80]. |

**Exempel: fråga med fysisk partition**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Exempel: fråga med partition för dynamiskt omfång**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mappning av datatyp för Oracle

När du kopierar data från och till Oracle gäller följande mappningar. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Oracle-datatyp | Data Factory interimdatatyp |
|:--- |:--- |
| BFILE (BFILE) |Byte[] |
| Blob |Byte[]<br/>(endast stöds på Oracle 10g och högre) |
| CHAR |String |
| Clob |String |
| DATE |DateTime |
| Flyta |Decimal, Sträng (om precision > 28) |
| INTEGER |Decimal, Sträng (om precision > 28) |
| Lång |String |
| LÅNG RÅ |Byte[] |
| Nchar |String |
| NCLOB (på andra sätt) |String |
| Nummer |Decimal, Sträng (om precision > 28) |
| NVARCHAR2 (olika) |String |
| Raw |Byte[] |
| ROWID (RADID) |String |
| TIMESTAMP |DateTime |
| TIDSSTÄMPEL MED LOKAL TIDSZON |String |
| TIDSSTÄMPEL MED TIDSZON |String |
| OSIGNERAT HELTAL |Tal |
| VARCHAR2 (OLIKA) |String |
| XML |String |

> [!NOTE]
> Datatyperna INTERVALL ÅR TILL MÅNAD OCH INTERVALL DAG TILL SEKUND stöds inte.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
