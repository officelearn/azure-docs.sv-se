---
title: Kopiera data till och från Oracle med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds butiker till en Oracle-databas eller från Oracle till mottagarens datalager med Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: jingwang
ms.openlocfilehash: 35c0d9190a11ad76ef44b43ef5160d2b39bee1fc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016922"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiera data från och till Oracle med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuell version](connector-oracle.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till en Oracle-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en Oracle-databas till alla datalager för mottagare som stöds. Du kan också kopiera data från alla dataarkiv till en Oracle-databas. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder Oracle-anslutningsapp följande versioner av en Oracle-databas. Det stöder också grundläggande eller OID autentiseringar:

- Oracle 12c R1 (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10,2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Oracle-proxyserver stöds inte.

## <a name="prerequisites"></a>Förutsättningar

För att kopiera data från och till en Oracle-databas som inte är offentligt tillgänglig, måste du konfigurera en lokal Integration Runtime. Mer information om integration runtime finns i [lokal Integration Runtime](create-self-hosted-integration-runtime.md). Integration runtime får en inbyggd drivrutin för Oracle. Därför behöver du inte installera en drivrutin manuellt när du kopierar data från och till Oracle.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Oracle-anslutningsapp.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Oracle-länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **Oracle**. | Ja |
| connectionString | Anger information som behövs för att ansluta till Oracle Database-instans. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).<br><br>**Stöd för anslutningstypen**: Du kan använda **Oracle-SID** eller **Oracle-tjänstnamn** att identifiera din databas:<br>– Om du använder SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Om du använder tjänstens namn: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!TIP]
>Om du stöter på fel som säger ”ORA-01025: UPI parametern är utanför intervallet ”och din Oracle är av version 8i, lägga till `WireProtocolMode=1` till din anslutningssträng och försök igen.

**Aktivera kryptering på Oracle anslutning**, har du två alternativ:

1.  Att använda **kryptering trippel-DES (3DES) och Advanced Encryption Standard (AES)** på Oracle serversidan, går du till Oracle avancerad säkerhet (OAS) och konfigurera krypteringsinställningar för, finns [här](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). ADF Oracle-anslutningsapp förhandlar automatiskt krypteringsmetod för att använda den som du konfigurerar i OAS när anslutning till Oracle.

2.  Att använda **SSL**, följer du dessa steg:

    1.  Få information om SSL-certifikat. Hämta information om DER-kodat certifikat för SSL-certifikat och spara utdata (---börjar certifikat... Sluta Certificate---) som en textfil.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exempel:** extrahera cert information från DERcert.cer; spara utdata till cert.txt

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
    
    2.  Skapa keystore eller truststore. Följande kommando skapar truststore-filen med eller utan lösenord i PKCS-12-format.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exempel:** skapar en PKCS12 trustsotre-fil med namnet MyTrustStoreFile med ett lösenord

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Placera filen truststore på lokal IR-dator, t.ex. vid C:\MyTrustStoreFile.
    4.  I ADF, konfigurerar du anslutningssträngen för Oracle med `EncryptionMethod=1` och motsvarande `TrustStore` / `TrustStorePassword`värde, t.ex. `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-datauppsättningen.

För att kopiera data från och till Oracle, ange typegenskapen på datauppsättningen till **OracleTable**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **OracleTable**. | Ja |
| tableName |Namnet på tabellen i Oracle-databas som den länkade tjänsten refererar till. | Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-källa och mottagare.

### <a name="oracle-as-a-source-type"></a>Oracle som en typ av datakälla

För att kopiera data från Oracle, ange typ av datakälla i kopieringsaktiviteten till **OracleSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **OracleSource**. | Ja |
| oracleReaderQuery | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej |

Om du inte anger ”oracleReaderQuery”, de kolumner som definierats i avsnittet ”struktur” av datauppsättningen som används för att skapa en fråga (`select column1, column2 from mytable`) ska köras på Oracle-databasen. Om definitionen för datauppsättningen inte har ”struktur”, markeras alla kolumner från tabellen.

**Exempel:**

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

### <a name="oracle-as-a-sink-type"></a>Oracle som en Mottagartyp

Om du vill kopiera data till Oracle, ange Mottagartyp i kopieringsaktiviteten till **OracleSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **OracleSink**. | Ja |
| WriteBatchSize | Infogar data i SQL-tabell när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är heltal (antal rader). |Nej (standardvärdet är 10 000) |
| writeBatchTimeout | Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är tidsintervallet. Ett exempel är 00:30:00 (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för kopieringsaktiviteten ska köras innan du skriver data till Oracle i varje körning. Du kan använda den här egenskapen för att rensa upp de förinstallerade data. | Nej |

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

## <a name="data-type-mapping-for-oracle"></a>Datatypen mappning för Oracle

När du kopierar data från och till Oracle, används följande mappningar från Oracle-datatyper till Data Factory tillfälliga-datatyper. Mer information om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren, se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| Oracle-datatypen | Data Factory tillfälliga datatyp |
|:--- |:--- |
| BFILE |Byte] |
| BLOB |Byte]<br/>(endast kan användas på Oracle 10g och senare) |
| CHAR |Sträng |
| CLOB |Sträng |
| DATE |DateTime |
| FLYTTAL |Decimal, sträng (om precision > 28) |
| HELTAL |Decimal, sträng (om precision > 28) |
| LÅNG |Sträng |
| LÄNGE RÅDATA |Byte] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| ANTAL |Decimal, sträng (om precision > 28) |
| NVARCHAR2 |Sträng |
| RÅDATA |Byte] |
| RAD-ID |Sträng |
| TIDSSTÄMPEL |DateTime |
| TIDSSTÄMPEL MED LOKALA TIDSZON |Sträng |
| TIDSSTÄMPEL MED TIDSZON |Sträng |
| HELTALET |Tal |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Datatyperna intervall år till månad och INTERVALLET dag till andra stöds inte.


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
