---
title: Kopiera data från SAP-tabellen med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP-tabell till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2018
ms.author: jingwang
ms.openlocfilehash: 49f07b4aaadfd45e9743bde58dc715230e5bc983
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074051"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Kopiera data från SAP-tabellen med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en SAP-tabell. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP-tabell till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här tabellen för SAP-anslutningen:

- Kopiera data från SAP-tabellen i:

    - **SAP ECC** med version 7.01 eller senare (i en senaste SAP Support paketet Stack lanseras efter år 2015)
    - **SAP BW** med version 7.01 eller senare
    - **SAP S/4HANA**
    - **Andra produkter i SAP Business Suite** med version 7.01 eller senare 

- Kopiera data från både **SAP Transparent tabell** och **visa**.
- Kopiera data med hjälp av **grundläggande autentisering** eller **SNC** (skydda nätverkskommunikation) om SNC har konfigurerats.
- Ansluta till **programserver** eller **Message Server**.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda den här tabellen för SAP-anslutningsappen, måste du:

- Konfigurera en lokal Integration Runtime med version 3.17 eller senare. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.

- Ladda ned den **64-bitars [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  från SAP: s webbplats och installera den på den lokala IR-datorn. När installation i fönstret inställningar för valfria steg gör att du väljer den **installera sammansättningar GAC** alternativet. 

    ![Installera SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-användare som används i Data Factory SAP tabell connector måste ha följande behörigheter: 

    - Auktorisering för RFC. 
    - Behörigheter för att aktiviteten ”kör” för auktorisering objektet ”S_SDSAUTH”.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för anslutningstjänsten SAP-tabellen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP Business Warehouse öppna hubben som är länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **SapTable** | Ja |
| server | Namnet på den server som den SAP-instansen finns.<br/>Om du vill ansluta till **SAP-programservern**. | Nej |
| systemNumber | Systemnummer för SAP-system.<br/>Om du vill ansluta till **SAP-programservern**.<br/>Tillåtna värdet: tvåsiffrig decimaltal representeras som en sträng. | Nej |
| messageServer | Värdnamnet för SAP Message Server.<br/>Om du vill ansluta till **SAP Message Server**. | Nej |
| messageServerService | Tjänstnamnet eller port antal Message Server.<br/>Om du vill ansluta till **SAP Message Server**. | Nej |
| systemId | System-ID för SAP-system där tabellen finns.<br/>Om du vill ansluta till **SAP Message Server**. | Nej |
| logonGroup | Gruppen inloggning för SAP-System.<br/>Om du vill ansluta till **SAP Message Server**. | Nej |
| clientId | Klient-ID för klienten i SAP-system.<br/>Tillåtna värdet: tresiffrig decimaltal representeras som en sträng. | Ja |
| language | Språk som använder SAP-system. | Nej (standardvärdet är **EN**)|
| userName | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| password | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| sncMode | SNC-aktivering indikator för åtkomst till SAP-servern där tabellen finns.<br/>Gäller om du vill använda SNC för att ansluta till SAP-server.<br/>Tillåtna värden är: **0** (inaktiverat, standard) eller **1** (på). | Nej |
| sncMyName | Initierarens SNC-namnet för att ansluta till SAP-servern där tabellen finns.<br/>Tillämpliga när `sncMode` är på. | Nej |
| sncPartnerName | Kommunikation partnerns SNC-namn för att ansluta till SAP-servern där tabellen finns.<br/>Tillämpliga när `sncMode` är på. | Nej |
| sncLibraryPath | Externa security produktens biblioteket att ansluta till SAP-servern där tabellen finns.<br/>Tillämpliga när `sncMode` är på. | Nej |
| sncQop | SNC kvaliteten på skydd.<br/>Tillämpliga när `sncMode` är på. <br/>Tillåtna värden är: **1** (autentisering), **2** (integritet) **3** (sekretess), **8** (standard), **9** (max). | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel 1: ansluta till SAP-programservern**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**Exempel 2: ansluta till SAP Message Server**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**Exempel 3: ansluta med SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP-tabelldatauppsättning.

För att kopiera data från och till SAP BW Open Hub, stöds följande egenskaper.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **SapTableResource**. | Ja |
| tableName | Namnet på tabellen SAP för att kopiera data från. | Ja |

**Exempel:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP tabellkälla.

### <a name="sap-table-as-source"></a>SAP-tabell som källa

För att kopiera data från SAP-tabell, stöds följande egenskaper.

| Egenskap                         | Beskrivning                                                  | Krävs |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | Type-egenskapen måste anges till **SapTableSource**.       | Ja      |
| Radantal                         | Antal rader som ska hämtas.                              | Nej       |
| rfcTableFields                   | Fält att kopiera från SAP-tabellen. Till exempel `column0, column1`. | Nej       |
| rfcTableOptions                  | Alternativ för att filtrera raderna i SAP-tabellen. Till exempel `COLUMN0 EQ 'SOMEVALUE'`. Se mer beskrivning under den här tabellen. | Nej       |
| customRfcReadTableFunctionModule | Anpassade RFC funktionsmodul som kan användas för att läsa data från SAP-tabellen. | Nej       |
| partitionOption                  | Mekanismen partition för att läsa från SAP-tabellen. Alternativ som stöds är: <br/>- **Ingen**<br/>- **PartitionOnInt** (normal heltal eller heltalsvärden med noll utfyllnad till vänster, till exempel 0000012345)<br/>- **PartitionOnCalendarYear** (4 siffror i formatet ”åååå”)<br/>- **PartitionOnCalendarMonth** (6 siffror i formatet ”YYYYMM”)<br/>- **PartitionOnCalendarDate** (8 siffror i formatet ”ååååmmdd”) | Nej       |
| partitionColumnName              | Namnet på kolumnen att partitionera data. | Nej       |
| partitionUpperBound              | Det maximala värdet för den angivna i kolumnen `partitionColumnName` som ska användas för att partitionera om du fortsätter. | Nej       |
| partitionLowerBound              | Det minsta värdet för den angivna i kolumnen `partitionColumnName` som ska användas för att partitionera om du fortsätter. | Nej       |
| maxPartitionsNumber              | Det maximala antalet partitioner för att dela upp data till. | Nej       |

>[!TIP]
>- Om din SAP-tabell har stora mängder data, till exempel flera miljarder rader använder `partitionOption` och `partitionSetting` för att dela upp data i små partitioner, då data läses av partitioner och varje datapartition hämtas från SAP-servern via en enda RFC-anrop.<br/>
>- Tar `partitionOption` som `partitionOnInt` exempelvis beräknas antalet rader i varje partition genom (totalt antal rader som ligger mellan *partitionUpperBound* och *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Om du vill köra ytterligare partitioner parallellt för att påskynda kopia, vi rekommenderar starkt att göra `maxPartitionsNumber` som en multipel av värdet för `parallelCopies` (Läs mer i [parallella kopia](copy-activity-performance.md#parallel-copy)).

I `rfcTableOptions`, du kan använda t.ex. följande vanliga SAP fråga operatorer för att filtrera rader: 

| Operator | Beskrivning |
| :------- | :------- |
| EQ | Lika med |
| NE | Inte lika med |
| LT | Mindre än |
| LE | Mindre än eller lika med |
| GT | Större än |
| GE | Större än eller lika med |
| T.EX. | I som (Emma %) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>Datatypsmappningen för SAP-tabell

När du kopierar data från SAP-tabellen används följande mappningar från SAP tabelldatatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SAP ABAP-typ | Data factory tillfälliga datatyp |
|:--- |:--- |
| C (sträng) | String |
| Jag (heltal) | Int32 |
| F (Float) | Double |
| D (datum) | String |
| T (tid) | String |
| P (BCD späckad, valuta, Decimal, kvantitet) | Decimal |
| N (numeriska) | String |
| X (binär och Raw) | String |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
