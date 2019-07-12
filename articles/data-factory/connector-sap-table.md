---
title: Kopiera data från en SAP-tabell med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en SAP-tabell till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827757"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopiera data från en SAP-tabell med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en SAP-tabell. Mer information finns i [översikt över Kopieringsaktivitet](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en SAP-tabell till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här anslutningen för SAP-tabellen:

- Kopiera data från en SAP-tabell i:

  - SAP ERP-centrala komponenten (SAP ECC) version 7.01 eller senare (i en senaste SAP Support paketet Stack lanseras efter 2015).
  - SAP Business Warehouse (SAP BW) version 7.01 eller senare.
  - SAP S/4HANA.
  - Andra produkter i SAP Business Suite 7.01 eller senare.

- Kopiera data från både en transparent SAP-tabell, en pool tabell, en klustrad tabell och en vy.
- Kopiera data med hjälp av grundläggande autentisering eller Secure Network Communications (SNC), om SNC har konfigurerats.
- Ansluter till en SAP-programservern eller SAP-meddelandeservern.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda denna anslutning för SAP-tabellen måste du:

- Konfigurera en lokal integration runtime (version 3.17 eller senare). Mer information finns i [skapa och konfigurera en lokal integration runtime](create-self-hosted-integration-runtime.md).

- Hämta 64-bitars [SAP-Anslutningsappen för Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) från SAP: s webbplats och installera den på den lokala installation av integration runtime-datorn. Under installationen, se till att välja den **installera sammansättningar GAC** alternativet i den **installationsanvisningarna** fönster.

  ![Installera SAP-Anslutningsappen för .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-användare som används i Data Factory SAP tabell connector måste ha följande behörigheter:

  - Auktorisering för att använda Remote funktionen anropa (RFC) mål.
  - Behörigheter för att köra aktiviteten objektets S_SDSAUTH auktorisering.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera de Data Factory-entiteterna som är specifika för tabell SAP-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP BW Open Hub länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Den `type` egenskapen måste anges till `SapTable`. | Ja |
| `server` | Namnet på den server där SAP-instansen finns.<br/>Använd för att ansluta till en SAP-programserver. | Nej |
| `systemNumber` | Systemnummer SAP-system.<br/>Använd för att ansluta till en SAP-programserver.<br/>Tillåtna värde: Ett reellt tal på två siffror som representeras som en sträng. | Nej |
| `messageServer` | Värdnamnet för SAP-meddelandeservern.<br/>Använd för att ansluta till en server för SAP-meddelande. | Nej |
| `messageServerService` | Tjänstnamnet eller port nummer för meddelandeservern.<br/>Använd för att ansluta till en server för SAP-meddelande. | Nej |
| `systemId` | ID för SAP-system där tabellen finns.<br/>Använd för att ansluta till en server för SAP-meddelande. | Nej |
| `logonGroup` | Inloggningsgruppen för SAP-system.<br/>Använd för att ansluta till en server för SAP-meddelande. | Nej |
| `clientId` | ID för klienten i SAP-system.<br/>Tillåtna värde: Ett reellt tal på tre siffror som representeras som en sträng. | Ja |
| `language` | Det språk som använder SAP-system.<br/>Standardvärdet är `EN`.| Nej |
| `userName` | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| `password` | Ange lösenordet för användaren. Markera det här fältet med den `SecureString` Skriv för att lagra den på ett säkert sätt i Data Factory eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| `sncMode` | Indikatorn SNC-aktivering för åtkomst till SAP-servern där tabellen finns.<br/>Använd om du vill använda SNC för att ansluta till SAP-server.<br/>Tillåtna värden är `0` (av, standard) eller `1` (på). | Nej |
| `sncMyName` | Namn på den initieraren SNC åtkomst till SAP-servern där tabellen finns.<br/>Gäller när `sncMode` är på. | Nej |
| `sncPartnerName` | Kommunikation partnerns SNC-namnet för åtkomst till SAP-servern där tabellen finns.<br/>Gäller när `sncMode` är på. | Nej |
| `sncLibraryPath` | Externa security produktens biblioteket att ansluta till SAP-servern där tabellen finns.<br/>Gäller när `sncMode` är på. | Nej |
| `sncQop` | SNC kvaliteten av skyddsnivån tillämpas.<br/>Gäller när `sncMode` är på. <br/>Tillåtna värden är `1` (autentisering), `2` (integritet) `3` (sekretess), `8` (standard), `9` (max). | Nej |
| `connectVia` | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal integration runtime krävs, som tidigare nämnts tidigare i [krav](#prerequisites). |Ja |

**Exempel 1: Ansluta till en SAP-programserver**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Exempel 2: Ansluta till en server för SAP-meddelande

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exempel 3: Ansluta med hjälp av SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper för att definiera datauppsättningar finns i [datauppsättningar](concepts-datasets-linked-services.md). Följande avsnitt innehåller en lista över egenskaper som stöds av SAP-tabelldatauppsättning.

För att kopiera data från och till tjänsten SAP BW Open Hub länkad, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Den `type` egenskapen måste anges till `SapTableResource`. | Ja |
| `tableName` | Namnet på tabellen SAP för att kopiera data från. | Ja |

### <a name="example"></a>Exempel

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). Följande avsnitt innehåller en lista över egenskaper som stöds av SAP-tabellkälla.

### <a name="sap-table-as-a-source"></a>SAP-tabell som en källa

För att kopiera data från en SAP-tabell, stöds följande egenskaper:

| Egenskap                         | Beskrivning                                                  | Krävs |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Den `type` egenskapen måste anges till `SapTableSource`.         | Ja      |
| `rowCount`                         | Antal rader som ska hämtas.                              | Nej       |
| `rfcTableFields`                   | Fält (kolumner) att kopiera från SAP-tabellen. Till exempel `column0, column1`. | Nej       |
| `rfcTableOptions`                  | Alternativ för att filtrera rader i en SAP-tabell. Till exempel `COLUMN0 EQ 'SOMEVALUE'`. Se även SAP operatorn frågetabellen senare i den här artikeln. | Nej       |
| `customRfcReadTableFunctionModule` | En anpassad RFC funktionsmodul som kan användas för att läsa data från en SAP-tabell.<br>Du kan använda en anpassad RFC-modul för funktionen för att definiera hur data hämtas från SAP-system och tillbaka till Data Factory. Anpassad funktionsmodul måste ha ett gränssnitt (import, export, tabeller) som liknar `/SAPDS/RFC_READ_TABLE2`, vilket är standardgränssnittet används i Data Factory. | Nej       |
| `partitionOption`                  | Mekanismen partition för att läsa från en SAP-tabell. Alternativ som stöds är: <ul><li>`None`</li><li>`PartitionOnInt` (normalt heltal eller heltalsvärden med noll utfyllnad till vänster, till exempel `0000012345`)</li><li>`PartitionOnCalendarYear` (4 siffror i formatet ”åååå”)</li><li>`PartitionOnCalendarMonth` (6 siffror i formatet ”YYYYMM”)</li><li>`PartitionOnCalendarDate` (8 siffror i formatet ”ååååmmdd”)</li></ul> | Nej       |
| `partitionColumnName`              | Namnet på den kolumn som används för att partitionera data.                | Nej       |
| `partitionUpperBound`              | Det maximala värdet för den angivna i kolumnen `partitionColumnName` som används för att fortsätta med partitionering. | Nej       |
| `partitionLowerBound`              | Det minsta värdet för den angivna i kolumnen `partitionColumnName` som används för att fortsätta med partitionering. | Nej       |
| `maxPartitionsNumber`              | Det maximala antalet partitioner för att dela upp data till.     | Nej       |

>[!TIP]
>Om din SAP-tabellen har en stor mängd data, till exempel flera miljarder rader, använda `partitionOption` och `partitionSetting` att dela upp data i mindre partitioner. I det här fallet data läses per partition, och varje datapartition hämtas från SAP-server via ett enda RFC-anrop.<br/>
<br/>
>Tar `partitionOption` som `partitionOnInt` kan t.ex, antalet rader i varje partition beräknas med den här formeln: (totalt antal rader som ligger mellan `partitionUpperBound` och `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Om du vill köra partitioner parallellt för att påskynda kopiering, rekommenderar vi starkt att `maxPartitionsNumber` en multipel av värdet för den `parallelCopies` egenskapen. Mer information finns i [parallell kopiera](copy-activity-performance.md#parallel-copy).

I `rfcTableOptions`, du kan använda följande vanliga SAP-frågeoperatorer för att filtrera rader:

| Operator | Beskrivning |
| :------- | :------- |
| `EQ` | Lika med |
| `NE` | Inte lika med |
| `LT` | Mindre än |
| `LE` | Mindre än eller lika med |
| `GT` | Större än |
| `GE` | Större än eller lika med |
| `LIKE` | Som i `LIKE 'Emma%'` |

### <a name="example"></a>Exempel

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mappningar av datatyper för en SAP-tabell

När du kopierar data från en SAP-tabell, används följande mappningar från SAP-tabelldatatyper till Azure Data Factory tillfälliga-datatyper. Läs hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| SAP ABAP-typ | Data Factory tillfälliga datatyp |
|:--- |:--- |
| `C` (Sträng) | `String` |
| `I` (Heltal) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Datum) | `String` |
| `T` (Tid) | `String` |
| `P` (BCD späckad, valuta, Decimal, kvantitet) | `Decimal` |
| `N` (Numeriska) | `String` |
| `X` (Binär och Raw) | `String` |

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
