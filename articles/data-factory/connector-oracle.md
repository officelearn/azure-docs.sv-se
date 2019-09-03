---
title: Kopiera data till och från Oracle med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från käll arkiv som stöds till en Oracle-databas, eller från Oracle till Sink-lager som stöds, genom att använda Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: jingwang
ms.openlocfilehash: 90adacffd947be38b447117bfe64242bed3a90af
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231353"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiera data från och till Oracle med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuell version](connector-oracle.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till en Oracle-databas. Den bygger på [Översikt över kopierings aktiviteten](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en Oracle-databas till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till en Oracle-databas. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Oracle-anslutningen:

- Följande versioner av en Oracle-databas:
    - Oracle 18c R1 (18,1) och högre
    - Oracle 12C R1 (12,1) och högre
    - Oracle 11g R1 (11,1) och högre
    - Oracle 10g R1 (10,1) och högre
    - Oracle 9i R2 (9,2) och högre
    - Oracle 8i R3 (8.1.7) och högre
    - Oracle Database Cloud Exadata service
- Kopiera data med Basic-eller OID-autentiseringar.
- Parallell kopiering från en Oracle-källa. Mer information finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Oracle-proxyservern stöds inte.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integrerings körningen innehåller en inbyggd Oracle-drivrutin. Därför behöver du inte installera en driv rutin manuellt när du kopierar data från och till Oracle.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Oracle-anslutaren.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Den länkade Oracle-tjänsten stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till **Oracle**. | Ja |
| connectionString | Anger den information som krävs för att ansluta till Oracle Database-instansen. <br/>Markera det här fältet som `SecureString` en för att lagra det på ett säkert sätt i Data Factory. Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Läs följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information. <br><br>**Anslutnings typ som stöds**: Du kan använda **Oracle sid** eller **Oracle-tjänstens namn** för att identifiera databasen:<br>-Om du använder SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Om du använder tjänst namn:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!TIP]
>Om du får ett fel meddelande "ORA-01025: UPI-parametern är utanför intervallet ", och din Oracle-version är 8i `WireProtocolMode=1` , Lägg till i anslutnings strängen. Försök sedan igen.

Om du vill aktivera kryptering på Oracle-anslutning har du två alternativ:

-   Om du vill använda **3DES (triple-des Encryption) och Advanced Encryption Standard (AES)** på Oracle-server sidan går du till Oracle Advanced Security (oas) och konfigurerar krypterings inställningarna. Mer information finns i den här [Oracle-dokumentationen](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Program varan för Oracle Application Development Framework (ADF) förhandlar automatiskt om krypterings metoden för att använda den som du konfigurerar i OAS när du upprättar en anslutning till Oracle.

-   Så här använder du **SSL**:

    1.  Hämta information om SSL-certifikatet. Hämta den Distinguished Encoding Rules (DER)-kodade certifikat informationen för ditt SSL-certifikat och spara utdata (-----starta certifikat... Avsluta certifikat-----) som en textfil.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exempel:** Extrahera certifikat information från DERcert. cer och spara sedan utdata till cert. txt.

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
    
    2.  `keystore` Bygg eller .`truststore` Följande kommando skapar `truststore` filen, med eller utan lösen ord, i PKCS-12-format.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exempel:** Skapa en PKCS12 `truststore` -fil med namnet MyTrustStoreFile med ett lösen ord.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  `truststore` Placera filen på IR-datorn med egen värd. Placera till exempel filen på C:\MyTrustStoreFile.
    4.  I Azure Data Factory konfigurerar du Oracle-anslutningssträngen med `EncryptionMethod=1` och motsvarande `TrustStore` / `TrustStorePassword`värde. Till exempel `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exempel:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösen ord i Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
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
## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md). 

Om du vill kopiera data från och till Oracle anger du egenskapen type för data uppsättningen `OracleTable`till. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type för data mängden måste anges till `OracleTable`. | Ja |
| tableName |Namnet på tabellen i Oracle-databasen som den länkade tjänsten refererar till. | Ja |

**Exempel:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-källan och mottagare. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter [](concepts-pipelines-activities.md)finns i pipelines. 

### <a name="oracle-as-source"></a>Oracle som källa

>[!TIP]
>Om du vill läsa in data från Oracle effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från Oracle](#parallel-copy-from-oracle).

Om du vill kopiera data från Oracle anger du käll typen i kopierings aktiviteten `OracleSource`till. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste vara inställd på `OracleSource`. | Ja |
| oracleReaderQuery | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla alla motsvarande inbyggda partitionsalternativ i frågan. Exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Nej |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Oracle. <br>Tillåtna värden är: **Ingen** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None`), kontrol leras graden av parallellitet för att samtidigt läsa in data från en Oracle-databas [`parallelCopies`](copy-activity-performance.md#parallel-copy) med inställningen på kopierings aktiviteten. | Nej |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när alternativet partition inte är `None`det. | Nej |
| partitionNames | Listan över fysiska partitioner som behöver kopieras. <br>Använd när alternativet partition är `PhysicalPartitionsOfTable`. Om du använder en fråga för att hämta källdata, Hook `?AdfTabularPartitionName` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Nej |
| partitionColumnName | Ange namnet på den käll kolumn **i Integer-typ** som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras primär nyckeln för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionColumnName` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Nej |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Nej |
| partitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Nej |

**Exempel: kopiera data med en grundläggande fråga utan partition**

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

### <a name="oracle-as-sink"></a>Oracle som mottagare

Om du vill kopiera data till Oracle ställer du in mottagar typen i kopierings aktiviteten till `OracleSink`. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type för kopierings aktivitetens Sink måste anges till `OracleSink`. | Ja |
| writeBatchSize | Infogar data i SQL-tabellen när buffertstorleken når `writeBatchSize`sig.<br/>Tillåtna värden är heltal (antal rader). |Nej (standard är 10 000) |
| writeBatchTimeout | Vänte tiden för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br/>Tillåtna värden är TimeSpan. Ett exempel är 00:30:00 (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till Oracle i varje körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. | Nej |

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

## <a name="parallel-copy-from-oracle"></a>Parallell kopiering från Oracle

Data Factory Oracle-anslutaren tillhandahåller inbyggd data partitionering för att kopiera data från Oracle parallellt. Du kan hitta alternativ för data partitionering på fliken **källa** i kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-oracle/connector-oracle-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Oracle-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från Oracle-databasen.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från Oracle-databasen. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager, skrivs de om för att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Föreslagna inställningar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell med fysiska partitioner.          | **Partitions alternativ**: Fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. |
| Fullständig belastning från stor tabell, utan fysiska partitioner, med en heltals kolumn för data partitionering. | **Partitionsalternativ**: Partition med dynamiskt intervall.<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Om inget anges används primär nyckel kolumn. |
| Läs in en stor mängd data med hjälp av en anpassad fråga med fysiska partitioner. | **Partitions alternativ**: Fysiska partitioner i tabell.<br>**Fråga**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Partitionsnamn**: Ange namnen på partitionerna som data ska kopieras från. Om detta inte anges identifierar Data Factory automatiskt de fysiska partitionerna i tabellen som du angav i Oracle-datauppsättningen.<br><br>Under körningen ersätts `?AdfTabularPartitionName` Data Factory med det faktiska partitionsnamnet och skickas till Oracle. |
| Läs in en stor mängd data med hjälp av en anpassad fråga, utan fysiska partitioner, med en heltals kolumn för data partitionering. | **Partitionsalternativ**: Partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partitionens nedre gränser**: Ange om du vill filtrera mot partition-kolumnen för att endast hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätts `?AdfRangePartitionColumnName`Data Factory `?AdfRangePartitionUpbound`, och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Oracle. <br>Om till exempel din partitionstabell "ID" är inställt på den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med fyra partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

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

**Exempel: fråga med Dynamic Range-partition**

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

## <a name="data-type-mapping-for-oracle"></a>Data typs mappning för Oracle

När du kopierar data från och till Oracle gäller följande mappningar. Information om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Oracle-datatyp | Data Factory tillfälliga datatyp |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(stöds endast på Oracle 10g och högre) |
| CHAR |Sträng |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (om precision > 28) |
| INTEGER |Decimal, String (om precision > 28) |
| SOM |Sträng |
| LONG RAW |Byte[] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| MÅNGA |Decimal, String (om precision > 28) |
| NVARCHAR2 |Sträng |
| RAW |Byte[] |
| ROWID |Sträng |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |Sträng |
| TIMESTAMP WITH TIME ZONE |Sträng |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Data typerna INTERVAL år till månad och intervall dag till sekund stöds inte.


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
