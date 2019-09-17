---
title: Kopiera data till eller från Azure Datautforskaren med Azure Data Factory
description: Lär dig hur du kopierar data till eller från Azure Datautforskaren med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
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
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010256"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopiera data till eller från Azure Datautforskaren med Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till eller från [Azure datautforskaren](../data-explorer/data-explorer-overview.md). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Datautforskaren-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från alla käll data lager som stöds till Azure Datautforskaren. Du kan också kopiera data från Azure Datautforskaren till alla mottagar data lager som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md) tabell.

>[!NOTE]
>Det finns stöd för att kopiera data till/från Azure Datautforskaren från/till lokalt data lager med hjälp av egen värd Integration Runtime sedan version 3,14.

Med Azure Datautforskaren-anslutningen kan du göra följande:

* Kopiera data med hjälp av Azure Active Directory (Azure AD) Application token-autentisering med ett **huvud namn för tjänsten**.
* Som källa hämtar du data med hjälp av en KQL-fråga (Kusto).
* Lägg till data i en mål tabell som mottagare.

## <a name="getting-started"></a>Komma igång

>[!TIP]
>En genom gång av hur du använder Azure Datautforskaren Connector finns i [Kopiera data till/från Azure datautforskaren med Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Datautforskaren Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Datautforskaren-anslutningen använder autentisering av tjänstens huvud namn. Följ de här stegen för att hämta ett huvud namn för tjänsten och bevilja behörigheter:

1. Registrera en entitet för program i Azure Active Directory (Azure AD) genom att följa [registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvud behörighet rätt behörighet i Azure Datautforskaren. Se [Hantera behörigheter för Azure datautforskaren Database](../data-explorer/manage-database-permissions.md) med detaljerad information om roller och behörigheter, samt genom att gå vidare med att hantera behörigheter. I allmänhet måste du

    - **Som källa**ger du minst rollen **databas hanterare** till din databas.
    - **Som mottagare**ger du minst rollen **databas** inmatnings roll till din databas.

>[!NOTE]
>När du använder ADF-gränssnittet till författare, används ditt inloggnings användar konto för att Visa Azure Datautforskaren-kluster, databaser och tabeller. Ange namnet manuellt om du inte har behörighet för den här åtgärden.

Följande egenskaper stöds för den länkade tjänsten Azure Datautforskaren:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen **Type** måste anges till **AzureDataExplorer** | Ja |
| endpoint | Slut punkts-URL för Azure Datautforskaren-klustret, med `https://<clusterName>.<regionName>.kusto.windows.net`formatet. | Ja |
| database | Namn på databasen. | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Detta är vad du normalt vet som "**auktoritets-ID**" i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Hämta det genom att hovra med musen i det övre högra hörnet av Azure Portal. | Ja |
| servicePrincipalId | Ange programmets klient-ID. Detta är vad du normalt vet som "**AAD-programklient-ID**" i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Ange programmets nyckel. Detta är vad du normalt vet som "**AAD-programnyckel**" i [Kusto-anslutningssträngen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel på länkade tjänst egenskaper:**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Datautforskaren-datauppsättningen.

Om du vill kopiera data till Azure Datautforskaren anger du egenskapen type för data uppsättningen till **AzureDataExplorerTable**.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen **Type** måste anges till **AzureDataExplorerTable** | Ja |
| table | Namnet på den tabell som den länkade tjänsten refererar till. | Ja för mottagare; Nej för källa |

**Exempel på data uppsättnings egenskaper**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Datautforskaren källa och mottagare.

### <a name="azure-data-explorer-as-source"></a>Azure Datautforskaren som källa

Om du vill kopiera data från Azure Datautforskaren anger du egenskapen **Type** i kopierings aktivitetens källa till **AzureDataExplorerSource**. Följande egenskaper stöds i kopieringsaktiviteten **source** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | **Typ** egenskapen för kopierings aktivitets källan måste anges till: **AzureDataExplorerSource** | Ja |
| query | En skrivskyddad begäran har angetts i ett [KQL-format](/azure/kusto/query/). Använd den anpassade KQL-frågan som referens. | Ja |
| queryTimeout | Vänte tiden innan fråge förfrågningen nådde tids gränsen. Standardvärdet är 10 min (00:10:00); det högsta tillåtna värdet är 1 timme (01:00:00). | Nej |
| notrunkering | Anger om den returnerade resultat uppsättningen ska trunkeras. Som standard trunkeras resultatet efter 500 000 poster eller 64 MB. Trunkering rekommenderas starkt för att aktiviteten ska fungera korrekt. |Nej |

>[!NOTE]
>Azure Datautforskaren-källan som standard har en storleks gräns på 500 000 poster eller 64 MB. Om du vill hämta alla poster utan trunkering kan du ange `set notruncation;` i början av frågan. Mer information finns i [fråga om begränsningar](https://docs.microsoft.com/azure/kusto/concepts/querylimits) .

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

### <a name="azure-data-explorer-as-sink"></a>Azure Datautforskaren som mottagare

Om du vill kopiera data till Azure Datautforskaren anger du egenskapen type i kopierings aktiviteten Sink till **AzureDataExplorerSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till: **AzureDataExplorerSink** | Ja |
| ingestionMappingName | Namnet på en i förväg skapad **[mappning](/azure/kusto/management/mappings#csv-mapping)** i en Kusto-tabell. För att mappa kolumnerna från källa till Azure Datautforskaren – som gäller för **[Alla käll Arkiv/format som stöds](copy-activity-overview.md#supported-data-stores-and-formats)** , inklusive CSV/JSON/Avro-format osv., kan du använda [kolumn mappningen](copy-activity-schema-and-type-mapping.md) kopiera aktivitet (implicit efter namn eller uttryckligen enligt konfigurationen) och /or Azure Datautforskaren-mappningar. | Nej |

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

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

* En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

* Läs mer om [att kopiera data från Azure Data Factory till Azure datautforskaren](/azure/data-explorer/data-factory-load-data).