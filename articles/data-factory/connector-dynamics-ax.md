---
title: Kopiera data från Dynamics AX med Azure Data Factory (för hands version) | Microsoft Docs
description: Lär dig hur du kopierar data från Dynamics AX till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 39c03058970a412a9bc312f99c239377898e5073
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092106"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Kopiera data från Dynamics AX med Azure Data Factory (för hands version)

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från Dynamics AX-källan. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Dynamics AX-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Dynamics AX till alla mottagar data lager som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder den här Dynamics AX-anslutaren kopiering av data från Dynamics AX med hjälp av **OData-protokollet** med **autentisering av tjänstens huvud namn**.

>[!TIP]
>Du kan också använda den här anslutningen för att kopiera data från **ekonomi och åtgärder i Dynamics 365**. Se Dynamics 365: s [OData-support](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) och [autentiseringsmetod](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Dynamics AX-anslutning.

## <a name="prerequisites"></a>Förutsättningar

Följ dessa steg om du vill använda autentisering av tjänstens huvudnamn:

1. Registrera en entitet för program i Azure Active Directory (Azure AD) genom att följa [registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Gå till Dynamics AX och ge den här tjänstens huvud behörighet rätt behörighet för att komma åt Dynamics AX.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Dynamics AX-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen **Type** måste anges till **DynamicsAx**. |Ja |
| url | OData-slutpunkten för Dynamics AX (eller Dynamics 365 finans och Operations). |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering. Om din Dynamics URL till exempel är `https://sampledynamics.sandbox.operations.dynamics.com/data/`, är motsvarande AAD-resurs vanligt vis. `https://sampledynamics.sandbox.operations.dynamics.com` | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan välja Azure Integration Runtime eller en egen värd Integration Runtime (om ditt data lager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics AX-datauppsättningen.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från Dynamics AX anger du egenskapen **Type** för data uppsättningen till **DynamicsAXResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens **typ** -egenskap måste anges till **DynamicsAXResource**. | Ja |
| path | Sökvägen till Dynamics AX OData-entiteten. | Ja |

**Exempel**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som Dynamics AX-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX som källa

Om du vill kopiera data från Dynamics AX anger du **käll** typen i kopierings aktivitet till **DynamicsAXSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | **Typ** egenskapen för kopierings aktivitets källan måste anges till **DynamicsAXSource**. | Ja |
| query | OData-frågealternativ för att filtrera data. Exempel: `"?$select=Name,Description&$top=5"`.<br/><br/>**Obs!** Kopplingen kopierar data från den kombinerade URL: en `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`:. Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
