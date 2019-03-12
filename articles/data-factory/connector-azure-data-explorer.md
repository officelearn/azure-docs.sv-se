---
title: Kopiera data till och från Azure Data Explorer med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data till eller från Azure Data Explorer genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: orspod
ms.openlocfilehash: 4e2448b3043c194bda884963975d85536c329baf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531648"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopiera data till och från Azure Data Explorer med Azure Data Factory

Den här artikeln beskriver hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till eller från [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla dataarkiv till Datautforskaren i Azure. Du kan också kopiera data från Azure Data Explorer till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md) tabell.

>[!NOTE]
>Kopiera data till och från Azure Data Explorer från/till på lokala data store med hjälp av lokal Integration Runtime stöds sedan version 3,14.

Datautforskaren i Azure-anslutningen kan du göra följande:

* Kopiera data med hjälp av Azure Active Directory (Azure AD) application tokenautentisering med en **tjänstens huvudnamn**.
* Hämta data med hjälp av en KQL (Kusto)-fråga som en källa.
* Lägga till data till en måltabell som en mottagare.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Data Explorer-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Datautforskaren i Azure-anslutningsappen använder autentisering av tjänstens huvudnamn. Följ dessa steg för att få ett huvudnamn för tjänsten och bevilja behörigheter:

1. Registrera en entitet för program i Azure Active Directory (Azure AD) genom att följa [registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt behörighet i Datautforskaren i Azure. Referera till [hantera Azure Data Explorer databasbehörigheter](../data-explorer/manage-database-permissions.md) med detaljerad information om roller och behörigheter samt genomgång om hur du hanterar behörigheter. I allmänhet måste du

    - **Som källa**, ge minst **databasen viewer** rollen till din databas.
    - **Som mottagare**, ge minst **Database lösning** rollen till din databas.

>[!NOTE]
>När du använder ADF UI för att skapa kan åtgärder för att visa en lista över databaser på den länkade tjänsten eller visa en lista över tabeller i datauppsättningen kräva högre Privilegierade beviljats behörigheten för tjänstens huvudnamn. Du kan också välja att manuellt ange databasnamnet och tabellnamn. Kopiera aktivitet körning fungerar så länge tjänstens huvudnamn har beviljats med rätt behörighet att läsa/skriva data.

Följande egenskaper har stöd för Azure Data Explorer länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **AzureDataExplorer** | Ja |
| slutpunkt | Slutpunkts-URL för Azure Data Explorer-klustret med formatet som `https://<clusterName>.<regionName>.kusto.windows.net `. | Ja |
| databas | Namnet på databasen. | Ja |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Det här är det du normalt kan som ”**utfärdare ID**” i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Hämta det genom att hovra med musen i det övre högra hörnet i Azure Portal. | Ja |
| servicePrincipalId | Ange programmets klient-ID. Det här är det du normalt kan som ”**klient-ID för AAD-programmet**” i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Ange programmets nyckel. Det här är det du normalt kan som ”**AAD programnyckel**” i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel på länkade egenskaper:**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Explorer-datauppsättningen.

Om du vill kopiera data till Azure Data Explorer, ange typegenskapen på datauppsättningen till **AzureDataExplorerTable**.

Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **AzureDataExplorerTable** | Ja |
| tabell | Namnet på den tabell som den länkade tjänsten refererar till. | Ja för mottagaren; Nej för källa |

**Exempel med datauppsättningen egenskaper**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Explorer källa och mottagare.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer som källa

Om du vill kopiera data från Azure Data Explorer, ange den **typ** -egenskapen i kopieringskälla för aktiviteten till **AzureDataExplorerSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för aktiviteten kopieringskälla måste anges till: **AzureDataExplorerSource** | Ja |
| DocumentDB | En skrivskyddad begäran som anges i en [KQL format](/azure/kusto/query/). Använd anpassade KQL fråga som referens. | Ja |
| queryTimeout | Väntetid innan frågebegäran når sin tidsgräns. Standardvärdet är 10: e minut (00: 10:00); tillåtna maxvärdet är 1 timme (01: 00:00). | Nej |

>[!NOTE]
>Azure Data Explorer källan som standard har en storleksgräns på 500 000 poster eller 64 MB. Du kan ange om du vill hämta alla poster utan trunkering `set notruncation;` i början av din fråga. Referera till [fråga gränser](https://docs.microsoft.com/azure/kusto/concepts/querylimits) på mer information.

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

För att kopiera data till Azure Data Explorer, ange egenskapen type i Kopiera aktivitet mottagare till **AzureDataExplorerSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen kopiera aktivitet komprimeringstyp måste anges som: **AzureDataExplorerSink** | Ja |
| ingestionMappingName | Namnet på en förskapad **[mappning](/azure/kusto/management/mappings#csv-mapping)** för en Kusto-tabell. Mappa kolumner från källan till Datautforskaren i Azure - som gäller för **[alla stöds källans datalager/format](copy-activity-overview.md#supported-data-stores-and-formats)** inklusive CSV/JSON/Avro formaterar osv, kan du använda kopieringsaktiviteten [kolumn mappning av](copy-activity-schema-and-type-mapping.md) (implicit efter namn eller uttryckligen som konfigurerats) och/eller Azure Data Explorer mappningar. | Nej |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).