---
title: Kopiera data till eller från Azure Data Explorer
description: Lär dig hur du kopierar data till eller från Azure Data Explorer med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 4c265cb0cdc665ef52f4dc6e69440e83c22db449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460985"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Data Explorer med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data till eller från [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln, som erbjuder en allmän översikt över kopieringsaktivitet.

>[!TIP]
>För Azure Data Factory- och Azure Data Explorer-integrering i allmänhet kan du läsa mer från [Integrate Azure Data Explorer med Azure Data Factory](../data-explorer/data-factory-integration.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Explorer-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från alla källdatalager som stöds till Azure Data Explorer. Du kan också kopiera data från Azure Data Explorer till alla sink-datalager som stöds. En lista över datalager som kopieringsaktiviteten stöder som källor eller sänkor finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Kopiering av data till eller från Azure Data Explorer via ett lokalt datalager med hjälp av självvärdbaserad integreringskörning stöds i version 3.14 och senare.

Med Azure Data Explorer-kopplingen kan du göra följande:

* Kopiera data med hjälp av Azure Active Directory (Azure AD) programtokenautentisering med ett **tjänsthuvudnamn**.
* Som källa hämtar du data med hjälp av en KQL -fråga (Kusto).
* Som en diskbänk lägger du till data i en måltabell.

## <a name="getting-started"></a>Komma igång

>[!TIP]
>En genomgång av Azure Data Explorer-anslutningsappen finns i [Kopiera data till/från Azure Data Explorer med Azure Data Factory](../data-explorer/data-factory-load-data.md) och [Mass-kopia från en databas till Azure Data Explorer](../data-explorer/data-factory-template.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Data Explorer-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Data Explorer-anslutningsappen använder autentisering av tjänstens huvudnamn. Följ dessa steg för att hämta ett huvudnamn för tjänsten och bevilja behörigheter:

1. Registrera en programentitet i Azure Active Directory genom att följa stegen i [Registrera ditt program med en Azure AD-klientorganisation](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt behörigheter i Azure Data Explorer. Mer information om roller och behörigheter för hantera behörigheter finns i [Hantera Azure Data Explorer-databasbehörigheter.](../data-explorer/manage-database-permissions.md) I allmänhet måste du:

    - **Som källa**ger du databasen minst **databasvisningsrollen**
    - **Som mottagare**, ge åtminstone **rollen Databas ingestor** till databasen

>[!NOTE]
>När du använder datafabriksgränssnittet för att skapa används ditt inloggningsanvändarkonto för att lista Azure Data Explorer-kluster, databaser och tabeller. Ange namnet manuellt om du inte har behörighet för dessa åtgärder.

Följande egenskaper stöds för den länkade Azure Data Explorer-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typegenskapen** måste anges till **AzureDataExplorer**. | Ja |
| slutpunkt | Slutpunkts-URL för Azure Data Explorer-klustret, med formatet som `https://<clusterName>.<regionName>.kusto.windows.net`. | Ja |
| databas | Databasens namn. | Ja |
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Detta kallas "Myndighets-ID" i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Hämta den genom att hålla muspekaren i det övre högra hörnet av Azure-portalen. | Ja |
| servicePrincipalId | Ange programmets klient-ID. Detta kallas "AAD-programklient-ID" i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Ange programmets nyckel. Detta kallas "AAD-programnyckel" i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory eller [referera till säkra data som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel på länkade tjänstegenskaper:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns [i Datauppsättningar i Azure Data Factory](concepts-datasets-linked-services.md). I det här avsnittet visas egenskaper som Azure Data Explorer-datauppsättningen stöder.

Om du vill kopiera data till Azure Data Explorer anger du egenskapen type för datauppsättningen till **AzureDataExplorerTable**.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typegenskapen** måste anges till **AzureDataExplorerTable**. | Ja |
| tabell | Namnet på den tabell som den länkade tjänsten refererar till. | Ja för diskbänk; Nej för källa |

**Exempel på datauppsättningsegenskaper:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns [i Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som Azure Data Explorer-källor och sänkor stöder.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer som källa

Om du vill kopiera data **type** från Azure Data Explorer anger du typegenskapen i aktivitetskällan Kopiera till **AzureDataExplorerSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till: **AzureDataExplorerSource** | Ja |
| DocumentDB | En skrivskyddad begäran som ges i [KQL-format](/azure/kusto/query/). Använd den anpassade KQL-frågan som referens. | Ja |
| frågaTimeout | Väntetiden innan frågebegäran time out. Standardvärdet är 10 min (00:10:00); högsta tillåtna värdet är 1 timme (01:00:00). | Inga |
| noTruncation | Anger om den returnerade resultatuppsättningen ska trunkeras. Som standard trunkeras resultatet efter 500 000 poster eller 64 MB.The Default, result is truncated after 500,000 records or 64 megabytes (MB). Trunkering rekommenderas starkt för att säkerställa korrekt beteende av aktiviteten. |Inga |

>[!NOTE]
>Som standard har Azure Data Explorer-källan en storleksgräns på 500 000 poster eller 64 MB. Om du vill hämta alla poster utan `set notruncation;` trunkering kan du ange i början av frågan. Mer information finns i [Frågegränser](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer som mottagare

Om du vill kopiera data till Azure Data Explorer anger du typegenskapen i kopieraaktivitetsmottagaren till **AzureDataExplorerSink**. Följande egenskaper stöds i avsnittet kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för copy activity sink måste anges till: **AzureDataExplorerSink**. | Ja |
| intagMappingName | Namn på en förskapad [mappning](/azure/kusto/management/mappings#csv-mapping) i en Kusto-tabell. Om du vill mappa kolumnerna från källa till Azure Data Explorer (som gäller för [alla källarkiv och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats), inklusive CSV/JSON/Avro-format), kan du använda [kolumnmappningen](copy-activity-schema-and-type-mapping.md) för kopieringsaktivitet (implicit efter namn eller explicit som konfigurerat) och/eller Azure Data Explorer-mappningar. | Inga |
| ytterligareegenskaper | En egenskapspåse som kan användas för att ange någon av de inmatningsegenskaper som inte redan anges av Azure Data Explorer Sink. Specifikt kan det vara användbart för att ange inmatningstaggar. Läs mer från [Azure Data Explore datainmatning doc](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html). | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Mer information om egenskaperna finns i [Uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

* En lista över datalager som kopieringsaktiviteten i Azure Data Factory stöder som källor och sänkor finns [i datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

* Läs mer om hur du [kopierar data från Azure Data Factory till Azure Data Explorer](/azure/data-explorer/data-factory-load-data).
