---
title: Kopiera data från Hive med Azure Data Factory
description: Lär dig hur du kopierar data från Hive till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 1854465c463d78999674080048207cfa0916da3b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992172"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopiera data från Hive med Azure Data Factory 

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Hive. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Hive-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Hive till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Hive-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Hive-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **Hive** | Ja |
| värd | IP-adress eller värdnamn för Hive-servern, avgränsat med ';' för flera värdar (endast när serviceDiscoveryMode är aktiverat).  | Ja |
| port | TCP-porten som Hive-servern använder för att lyssna efter klientanslutningar. Om du ansluter till Azure HDInsights anger du porten som 443. | Ja |
| serverTyp | Typ av Hive-server. <br/>Tillåtna värden är: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Inga |
| sparsamhetTransportProtocol | Transportprotokollet som ska användas i sparsamhetslagret. <br/>Tillåtna värden är: **Binär**, **SASL**, **HTTP** | Inga |
| authenticationType | Autentiseringsmetoden som används för att komma åt Hive-servern. <br/>Tillåtna värden är: **Anonym**, **Användarnamn**, **AnvändarnamnOchPassword**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | sant att ange med hjälp av ZooKeeper tjänsten, falskt inte.  | Inga |
| zooKeeperNameSpace | Namnområdet på ZooKeeper som Hive Server 2-noder läggs till under.  | Inga |
| användaNativeQuery | Anger om drivrutinen använder inbyggda HiveQL-frågor eller konverterar dem till ett motsvarande formulär i HiveQL.  | Inga |
| användarnamn | Det användarnamn som du använder för att komma åt Hive Server.  | Inga |
| password | Lösenordet som motsvarar användaren. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| httpPath (på ett sätt) | Den partiella URL som motsvarar Hive-servern.  | Inga |
| enableSsl enableSsl enableSsl enableS | Anger om anslutningarna till servern är krypterade med TLS. Standardvärdet är false.  | Inga |
| betroddaCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare för att verifiera servern när du ansluter via TLS. Den här egenskapen kan bara ställas in när du använder TLS på självvärdbaserad IR. The default value is the cacerts.pem file installed with the IR.  | Inga |
| användaSystemTrustStore | Anger om ett CERTIFIKAT FRÅN systemförtroendearkivet eller från en angiven PEM-fil ska användas. Standardvärdet är false.  | Inga |
| allowHostNameCNMismatch | Anger om ett certifikatutfärdat TLS/SSL-certifikatnamn ska krävas för att matcha serverns värdnamn när du ansluter via TLS. Standardvärdet är false.  | Inga |
| tillåtVälsignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

**Exempel:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Hive-datauppsättningen.

Om du vill kopiera data från Hive anger du egenskapen typ för datauppsättningen till **HiveObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **HiveObject** | Ja |
| Schemat | Namnet på schemat. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Namn på tabellen inklusive schemadel. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table`. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Hive-källan.

### <a name="hivesource-as-source"></a>HiveSource som källa

Om du vill kopiera data från Hive anger du källtypen i kopieringsaktiviteten till **HiveSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **HiveSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
