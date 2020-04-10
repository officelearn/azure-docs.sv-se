---
title: Kopiera data från en SAP-tabell
description: Lär dig hur du kopierar data från en SAP-tabell till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 44efd0d402d6cb9f1ee44c583c88140121ca3001
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011624"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopiera data från en SAP-tabell med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en SAP-tabell. Mer information finns i [Kopiera aktivitetsöversikt](copy-activity-overview.md).

>[!TIP]
>Mer information om ADF:s övergripande support i SCENARIOt för SAP-dataintegrering finns i [SAP-dataintegration med hjälp av Azure Data Factory-faktablad](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, jämförelse och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP-tabellkopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en SAP-tabell till alla sink-datalager som stöds. En lista över de datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här SAP-tabellkopplingen stöder:

- Kopiera data från en SAP-tabell i:

  - SAP ERP Central Component (SAP ECC) version 7.01 eller senare (i en nyligen SAP-supportpaketstack som släpptes efter 2015).
  - SAP Business Warehouse (SAP BW) version 7.01 eller senare (i en nyligen SAP-supportpaketstack som släpptes efter 2015).
  - SAP S/4HANA.
  - Andra produkter i SAP Business Suite version 7.01 eller senare (i en nyligen SAP-supportpaketstack som släpptes efter 2015).

- Kopiera data från både en SAP-genomskinlig tabell, en poolad tabell, en grupperad tabell och en vy.
- Kopiera data med hjälp av grundläggande autentisering eller säker nätverkskommunikation (SNC), om SNC är konfigurerat.
- Ansluter till en SAP-programserver eller SAP-meddelandeserver.

## <a name="prerequisites"></a>Krav

Om du vill använda den här SAP-tabellkopplingen måste du:

- Konfigurera en självvärd integrationskörning (version 3.17 eller senare). Mer information finns i [Skapa och konfigurera en självvärd för integrationskörning](create-self-hosted-integration-runtime.md).

- Hämta 64-bitars [SAP Connector för Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) från SAP:s webbplats och installera den på den självvärderade integrationskörningsdatorn. Under installationen kontrollerar du att du väljer alternativet **Installera sammansättningar till GAC** i fönstret **Valfria installationssteg.**

  ![Installera SAP Connector för .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- DEN SAP-användare som används i data factory SAP-tabellkopplingen måste ha följande behörigheter:

  - Auktorisering för användning av RFC-mål (Remote Function Call).
  - Behörigheter till körningsaktiviteten för S_SDSAUTH auktoriseringsobjektet.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera de datafabrikentiteter som är specifika för SAP-tabellkopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den kopplade tjänsten SAP BW Open Hub:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Egenskapen `type` måste anges `SapTable`till . | Ja |
| `server` | Namnet på den server där SAP-instansen finns.<br/>Används för att ansluta till en SAP-programserver. | Inga |
| `systemNumber` | Sap-systemets systemnummer.<br/>Används för att ansluta till en SAP-programserver.<br/>Tillåtet värde: Ett tvåsiffrigt decimaltal som representeras som en sträng. | Inga |
| `messageServer` | Värdnamnet på SAP-meddelandeservern.<br/>Används för att ansluta till en SAP-meddelandeserver. | Inga |
| `messageServerService` | Meddelandeserverns tjänstnamn eller portnummer.<br/>Används för att ansluta till en SAP-meddelandeserver. | Inga |
| `systemId` | ID:t för SAP-systemet där tabellen finns.<br/>Används för att ansluta till en SAP-meddelandeserver. | Inga |
| `logonGroup` | Inloggningsgruppen för SAP-systemet.<br/>Används för att ansluta till en SAP-meddelandeserver. | Inga |
| `clientId` | ID för klienten i SAP-systemet.<br/>Tillåtet värde: Ett tresiffrigt decimaltal representerat som en sträng. | Ja |
| `language` | Det språk som SAP-systemet använder.<br/>Standardvärdet `EN`är .| Inga |
| `userName` | Namnet på den användare som har åtkomst till SAP-servern. | Ja |
| `password` | Ange lösenordet för användaren. Markera det här `SecureString` fältet med typen för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| `sncMode` | SNC-aktiveringsindikatorn för att komma åt SAP-servern där tabellen finns.<br/>Använd om du vill använda SNC för att ansluta till SAP-servern.<br/>Tillåtna `0` värden är (av, `1` standard) eller (på). | Inga |
| `sncMyName` | Initierarens SNC-namn för att komma åt SAP-servern där tabellen finns.<br/>Gäller `sncMode` när den är på. | Inga |
| `sncPartnerName` | Kommunikationspartnerns SNC-namn för att komma åt SAP-servern där tabellen finns.<br/>Gäller `sncMode` när den är på. | Inga |
| `sncLibraryPath` | Den externa säkerhetsproduktens bibliotek för att komma åt SAP-servern där tabellen finns.<br/>Gäller `sncMode` när den är på. | Inga |
| `sncQop` | SNC-skyddsnivån för skydd gäller.<br/>Gäller `sncMode` när är På. <br/>Tillåtna `1` värden är `2` (autentisering), (integritet), `3` (sekretess), `8` (standard), `9` (Maximum). | Inga |
| `connectVia` | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärd för integrationskörning krävs, som tidigare [nämnts](#prerequisites)i Förutsättningar . |Ja |

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Exempel 2: Ansluta till en SAP-meddelandeserver

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

### <a name="example-3-connect-by-using-snc"></a>Exempel 3: Anslut med SNC

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

En fullständig lista över avsnitt och egenskaper för att definiera datauppsättningar finns i [Datauppsättningar](concepts-datasets-linked-services.md). I följande avsnitt finns en lista över de egenskaper som stöds av SAP-tabelldatauppsättningen.

Så här kopierar du data från och till den sap BW Open Hub-länkade tjänsten stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Egenskapen `type` måste anges `SapTableResource`till . | Ja |
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

En fullständig lista över avsnitt och egenskaper för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). I följande avsnitt finns en lista över de egenskaper som stöds av SAP-tabellkällan.

### <a name="sap-table-as-source"></a>SAP-tabell som källa

Så här kopierar du data från en SAP-tabell:

| Egenskap                         | Beskrivning                                                  | Krävs |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Egenskapen `type` måste anges `SapTableSource`till .         | Ja      |
| `rowCount`                         | Antalet rader som ska hämtas.                              | Inga       |
| `rfcTableFields`                   | De fält (kolumner) som ska kopieras från SAP-tabellen. Till exempel `column0, column1`. | Inga       |
| `rfcTableOptions`                  | Alternativen för att filtrera raderna i en SAP-tabell. Till exempel `COLUMN0 EQ 'SOMEVALUE'`. Se även postoperatortabellen för SAP-frågor senare i den här artikeln. | Inga       |
| `customRfcReadTableFunctionModule` | En anpassad RFC-funktionsmodul som kan användas för att läsa data från en SAP-tabell.<br>Du kan använda en anpassad RFC-funktionsmodul för att definiera hur data hämtas från DITT SAP-system och returneras till Data Factory. Den anpassade funktionsmodulen måste ha ett gränssnitt implementerat (import, export, tabeller) som liknar `/SAPDS/RFC_READ_TABLE2`, vilket är standardgränssnittet som används av Data Factory. | Inga       |
| `partitionOption`                  | Partitionsmekanismen som ska läsas från en SAP-tabell. Alternativ som stöds är: <ul><li>`None`</li><li>`PartitionOnInt`(normala heltals- eller heltalsvärden med noll `0000012345`utfyllnad till vänster, till exempel )</li><li>`PartitionOnCalendarYear`(4 siffror i formatet "YYYY")</li><li>`PartitionOnCalendarMonth`(6 siffror i formatet "YYYYMM")</li><li>`PartitionOnCalendarDate`(8 siffror i formatet "YYYYMMDD")</li></ul> | Inga       |
| `partitionColumnName`              | Namnet på den kolumn som används för att partitionera data.                | Inga       |
| `partitionUpperBound`              | Det maximala värdet för kolumnen `partitionColumnName` som anges i den som ska användas för att fortsätta med partitionering. | Inga       |
| `partitionLowerBound`              | Det minsta värdet för kolumnen `partitionColumnName` som anges i den som ska användas för att fortsätta med partitionering. (Obs: `partitionLowerBound` kan inte vara "0" när partitionsalternativet är `PartitionOnInt`) | Inga       |
| `maxPartitionsNumber`              | Det maximala antalet partitioner att dela upp data i.     | Inga       |

>[!TIP]
>Om SAP-tabellen har en stor mängd data, till `partitionOption` exempel `partitionSetting` flera miljarder rader, använder och delar du upp data i mindre partitioner. I det här fallet läss data per partition och varje datapartition hämtas från DIN SAP-server via ett enda RFC-anrop.<br/>
<br/>
>`partitionOnInt` Som `partitionOption` exempel beräknas antalet rader i varje partition med den här formeln: `partitionUpperBound` (totala rader som faller mellan och `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Om du vill läsa in datapartitioner parallellt för [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) att påskynda kopieringen styrs den parallella graden av inställningen på kopieringsaktiviteten. Om du till `parallelCopies` exempel anger fyra genererar och kör Data Factory samtidigt fyra frågor baserat på det angivna partitionsalternativet och inställningarna, och varje fråga hämtar en del data från SAP-tabellen. Vi rekommenderar `maxPartitionsNumber` starkt att göra en `parallelCopies` multipel av värdet på fastigheten. När du kopierar data till filbaserat datalager, är det också recommanded att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestanda är bättre än att skriva till en enda fil.

I `rfcTableOptions`kan du använda följande vanliga SAP-frågeoperatorer för att filtrera raderna:

| Operator | Beskrivning |
| :------- | :------- |
| `EQ` | Lika med |
| `NE` | Inte lika med |
| `LT` | Mindre än |
| `LE` | Mindre än eller lika med |
| `GT` | Större än |
| `GE` | Större än eller lika med |
| `IN` | Liksom i`TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Liksom i`LIKE 'Emma%'` |

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

## <a name="data-type-mappings-for-an-sap-table"></a>Datatypsmappningar för en SAP-tabell

När du kopierar data från en SAP-tabell används följande mappningar från SAP-tabelldatatyperna till interimsdatatyperna i Azure Data Factory. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| SAP ABAP-typ | Data Factory interimdatatyp |
|:--- |:--- |
| `C`- Jag vet inte vad du ska gå till. | `String` |
| `I`(Heltal) | `Int32` |
| `F`- Jag är inte så bra. | `Double` |
| `D`(Datum) | `String` |
| `T`- Jag vet inte vad du ska ska gå till. | `String` |
| `P`(BCD-packad, valuta, decimal, ant) | `Decimal` |
| `N`(Numeriskt) | `String` |
| `X`(Binär och rå) | `String` |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg

En lista över de datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [i Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
