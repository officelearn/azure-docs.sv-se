---
title: Kopiera data från en SAP-tabell med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en SAP-tabell till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
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
ms.openlocfilehash: 8d98405cfbabdff25c40d41b209d79761e699396
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996597"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopiera data från en SAP-tabell med hjälp av Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en SAP-tabell. Mer information finns i [Översikt över kopierings aktivitet](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en SAP-tabell till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder SAP Table Connector:

- Kopiera data från en SAP-tabell i:

  - SAP ERP Central Component (SAP ECC) version 7,01 eller senare (i en senaste SAP-support paket stack som publicerats efter 2015).
  - SAP Business Warehouse (SAP BW) version 7,01 eller senare.
  - SAP S/4HANA.
  - Andra produkter i SAP Business Suite version 7,01 eller senare.

- Kopiera data från både en transparent SAP-tabell, en sammanställd tabell, en klustrad tabell och en vy.
- Kopiera data med hjälp av grundläggande autentisering eller säker nätverkskommunikation (SNC), om SNC har kon figurer ATS.
- Ansluta till en SAP-program Server eller SAP Message Server.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här SAP Table Connector måste du:

- Konfigurera en integration runtime med egen värd (version 3,17 eller senare). Mer information finns i [skapa och konfigurera en integration runtime med egen värd](create-self-hosted-integration-runtime.md).

- Hämta 64-bitars [SAP-anslutaren för Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) från SAP: s webbplats och installera den på den lokala datorn för integration Runtime. Under installationen ser du till att du väljer alternativet **Installera sammansättningar till GAC** i fönstret **valfria installations steg** .

  ![Installera SAP Connector för .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Den SAP-användare som används i Data Factory SAP Table Connector måste ha följande behörigheter:

  - Auktorisering för att använda RFC-mål (Remote Function Call).
  - Behörigheter till EXECUTE-aktiviteten för S_SDSAUTH-Authorization-objektet.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SAP Table Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten SAP BW Open Hub:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| `type` | Egenskapen måste anges till `SapTable`. `type` | Ja |
| `server` | Namnet på den server där SAP-instansen finns.<br/>Använd för att ansluta till en SAP-Programserver. | Nej |
| `systemNumber` | System numret för SAP-systemet.<br/>Använd för att ansluta till en SAP-Programserver.<br/>Tillåtet värde: Ett tvåsiffrigt decimal tal som visas som en sträng. | Nej |
| `messageServer` | Värd namnet för SAP Message Server.<br/>Använd för att ansluta till en SAP-meddelande Server. | Nej |
| `messageServerService` | Tjänst namnet eller port numret för meddelande servern.<br/>Använd för att ansluta till en SAP-meddelande Server. | Nej |
| `systemId` | ID: t för det SAP-system där tabellen finns.<br/>Använd för att ansluta till en SAP-meddelande Server. | Nej |
| `logonGroup` | Inloggnings gruppen för SAP-systemet.<br/>Använd för att ansluta till en SAP-meddelande Server. | Nej |
| `clientId` | ID för klienten i SAP-systemet.<br/>Tillåtet värde: Ett fyrsiffrigt decimal tal som visas som en sträng. | Ja |
| `language` | Det språk som används i SAP-systemet.<br/>Standardvärdet `EN`är.| Nej |
| `userName` | Namnet på den användare som har åtkomst till SAP-servern. | Ja |
| `password` | Ange lösenordet för användaren. Markera det här fältet med `SecureString` typen för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| `sncMode` | Aktiverings indikatorn för SNC för att få åtkomst till SAP-servern där tabellen finns.<br/>Använd om du vill använda SNC för att ansluta till SAP-servern.<br/>Tillåtna värden är `0` (av, standard) eller `1` (på). | Nej |
| `sncMyName` | Initierarens SNC-namn för att få åtkomst till SAP-servern där tabellen finns.<br/>Gäller när `sncMode` är på. | Nej |
| `sncPartnerName` | Kommunikations partnerns SNC-namn för att få åtkomst till SAP-servern där tabellen finns.<br/>Gäller när `sncMode` är på. | Nej |
| `sncLibraryPath` | Den externa säkerhets produktens bibliotek för att få åtkomst till SAP-servern där tabellen finns.<br/>Gäller när `sncMode` är på. | Nej |
| `sncQop` | SNC kvalitet för skydds nivå som ska tillämpas.<br/>Gäller när `sncMode` är på. <br/>Tillåtna värden är `1` (autentisering), `2` (integritet) `8` , `3` (sekretess), (standard), `9` (max). | Nej |
| `connectVia` | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. En integration runtime med egen värd krävs, som nämnts tidigare i [krav](#prerequisites). |Ja |

**Exempel 1: Ansluta till en SAP-Programserver**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Exempel 2: Ansluta till en SAP-meddelande Server

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

### <a name="example-3-connect-by-using-snc"></a>Exempel 3: Anslut med hjälp av SNC

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

En fullständig lista över avsnitten och egenskaperna för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP Table-datauppsättningen.

Följande egenskaper stöds för att kopiera data från och till den länkade tjänsten SAP BW Open Hub:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| `type` | Egenskapen måste anges till `SapTableResource`. `type` | Ja |
| `tableName` | Namnet på SAP-tabellen som data ska kopieras från. | Ja |

### <a name="example"></a>Exempel

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitten och egenskaperna för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP-tabellens källa.

### <a name="sap-table-as-source"></a>SAP-tabell som källa

Följande egenskaper stöds för att kopiera data från en SAP-tabell:

| Egenskap                         | Beskrivning                                                  | Obligatorisk |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Egenskapen måste anges till `SapTableSource`. `type`         | Ja      |
| `rowCount`                         | Antalet rader som ska hämtas.                              | Nej       |
| `rfcTableFields`                   | De fält (kolumner) som ska kopieras från SAP-tabellen. Till exempel `column0, column1`. | Nej       |
| `rfcTableOptions`                  | Alternativen för att filtrera raderna i en SAP-tabell. Till exempel `COLUMN0 EQ 'SOMEVALUE'`. Se även tabellen SAP Query-operator längre fram i den här artikeln. | Nej       |
| `customRfcReadTableFunctionModule` | En anpassad RFC Function-modul som kan användas för att läsa data från en SAP-tabell.<br>Du kan använda en anpassad RFC Function-modul för att definiera hur data hämtas från SAP-systemet och returneras till Data Factory. Modulen för anpassad funktion måste ha ett implementerat gränssnitt (importera, exportera, tabeller) som liknar `/SAPDS/RFC_READ_TABLE2`, vilket är det standard gränssnitt som används av Data Factory. | Nej       |
| `partitionOption`                  | Den partition mekanism som ska läsas från en SAP-tabell. De alternativ som stöds är: <ul><li>`None`</li><li>`PartitionOnInt`(vanliga heltals-eller heltals värden med utfyllnaden noll till vänster `0000012345`, till exempel)</li><li>`PartitionOnCalendarYear`(4 siffror i formatet "åååå")</li><li>`PartitionOnCalendarMonth`(6 siffror i formatet "YYYYMM")</li><li>`PartitionOnCalendarDate`(8 siffror i formatet "ÅÅÅÅMMDD")</li></ul> | Nej       |
| `partitionColumnName`              | Namnet på den kolumn som används för att partitionera data.                | Nej       |
| `partitionUpperBound`              | Det maximala värdet för kolumnen som anges i `partitionColumnName` som ska användas för att fortsätta med partitionering. | Nej       |
| `partitionLowerBound`              | Det minsta värdet för kolumnen som anges i `partitionColumnName` som ska användas för att fortsätta med partitionering. | Nej       |
| `maxPartitionsNumber`              | Det maximala antalet partitioner att dela data i.     | Nej       |

>[!TIP]
>Om SAP-tabellen har en stor mängd data, t. ex. flera miljarder rader, `partitionOption` använder `partitionSetting` du och för att dela upp data i mindre partitioner. I det här fallet läses data per partition och varje datapartition hämtas från SAP-servern via ett enda RFC-anrop.<br/>
<br/>
>`maxPartitionsNumber` `partitionLowerBound` `partitionUpperBound` Med följande formel beräknas antalet rader i varje partition: (totalt antal rader som faller mellan och)/. `partitionOption` `partitionOnInt`<br/>
<br/>
>Om du vill läsa in datapartitioner parallellt för att påskynda kopieringen styrs parallell [`parallelCopies`](copy-activity-performance.md#parallel-copy) graden av inställningen för kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från SAP-tabellen. Vi rekommenderar starkt att `maxPartitionsNumber` du gör en multipel av `parallelCopies` egenskapens värde. När du kopierar data till ett filbaserat data lager, skrivs det också om att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

I `rfcTableOptions`kan du använda följande vanliga SAP-fråge operatorer för att filtrera raderna:

| Operator | Beskrivning |
| :------- | :------- |
| `EQ` | Lika med |
| `NE` | Inte lika med |
| `LT` | Mindre än |
| `LE` | Mindre än eller lika med |
| `GT` | Större än |
| `GE` | Större än eller lika med |
| `LIKE` | Som i`LIKE 'Emma%'` |

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
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Data typs mappningar för en SAP-tabell

När du kopierar data från en SAP-tabell används följande mappningar från data typerna SAP Table till Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Typ av SAP-ABAP | Data Factory tillfälliga datatyp |
|:--- |:--- |
| `C`Nollängd | `String` |
| `I`Talet | `Int32` |
| `F`Flyta | `Double` |
| `D`Ikraftträdande | `String` |
| `T`Tid | `String` |
| `P`(BCD-packad, valuta, decimal, KVT) | `Decimal` |
| `N`Nummer | `String` |
| `X`(Binary och RAW) | `String` |

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
