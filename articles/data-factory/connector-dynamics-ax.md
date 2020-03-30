---
title: Kopiera data från Dynamics AX
description: Lär dig hur du kopierar data från Dynamics AX till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4acad5e2de55211b6c4492513f331c36286ed852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892785"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopiera data från Dynamics AX med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från Dynamics AX-källa. Artikeln bygger på [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md), som presenterar en allmän översikt över kopiera aktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Dynamics AX-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Dynamics AX till alla sink-datalager som stöds. En lista över datalager som Kopierar aktivitet stöder som källor och sänkor finns i [Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Den här Dynamics AX-anslutningen stöder kopiering av data från Dynamics AX med **OData-protokollet** med **autentisering av tjänsthuvudnamn**.

>[!TIP]
>Du kan också använda den här kopplingen för att kopiera data från **Dynamics 365 Finance and Operations**. Se Dynamics 365:s [OData-support](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) [och autentiseringsmetod](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som du kan använda för att definiera datacenter som är specifika för Dynamics AX-koppling.

## <a name="prerequisites"></a>Krav

Så här använder du autentisering av tjänstens huvudnamn:

1. Registrera en programentitet i Azure Active Directory (Azure AD) genom att följa [Registrera ditt program med en Azure AD-klientorganisation](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Gå till Dynamics AX och ge den här tjänsten huvudnamn rätt behörighet att komma åt din Dynamics AX.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Dynamics AX-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typegenskapen** måste anges till **DynamicsAX**. |Ja |
| url | Dynamics AX-instansen (eller Dynamics 365 Finance and Operations) OData-slutpunkten. |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering. Om din Dynamics-URL `https://sampledynamics.sandbox.operations.dynamics.com/data/`till exempel är , `https://sampledynamics.sandbox.operations.dynamics.com`är motsvarande AAD-resurs vanligtvis . | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan välja Azure Integration Runtime eller en självvärd integrationskörning (om ditt datalager finns i ett privat nätverk). Om inget anges används standardkörningen för Azure Integration Runtime. |Inga |

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

Det här avsnittet innehåller en lista över egenskaper som Dynamics AX-datauppsättningen stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från Dynamics AX anger du **egenskapen type** för datauppsättningen till **DynamicsAXResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för datauppsättningen måste anges till **DynamicsAXResource**. | Ja |
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

Om du vill kopiera data från Dynamics AX anger du **källtypen** i Kopiera aktivitet till **DynamicsAXSource**. Följande egenskaper stöds i **källavsnittet** Kopiera aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för källan Kopiera aktivitet måste anges till **DynamicsAXSource**. | Ja |
| DocumentDB | OData-frågealternativ för filtrering av data. Exempel: `"?$select=Name,Description&$top=5"`.<br/><br/>**Obs:** Kopplingen kopierar data från `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`den kombinerade webbadressen: . Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Inga |

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


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns [i Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
