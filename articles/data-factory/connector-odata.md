---
title: Kopiera data från OData-källor med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från OData-källor till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.openlocfilehash: 17b78e03e330e342e9d558dd3ca5d9071bcd3c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163938"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiera data från en OData-källa med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [Aktuell version](connector-odata.md)

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från en OData-källa. Artikeln bygger på [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md), som presenterar en allmän översikt över kopiera aktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här OData-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en OData-källa till alla sink-datalager som stöds. En lista över datalager som Kopierar aktivitet stöder som källor och sänkor finns i [Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Specifikt stöder den här OData-anslutningen:

- OData version 3.0 och 4.0.
- Kopiera data med någon av följande autentiseringar: **Anonym**, **Basic**, **Windows**och **AAD-tjänstens huvudnamn**.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som du kan använda för att definiera datafabrikentiteter som är specifika för en OData-anslutningsapp.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för en OData-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen type** måste anges till **OData**. |Ja |
| url | Rot-URL:en för OData-tjänsten. |Ja |
| authenticationType | Den typ av autentisering som används för att ansluta till OData-källan. Tillåtna värden är **Anonym**, **Basic**, **Windows**och **AadServicePrincipal**. Användarbaserad OAuth stöds inte. | Ja |
| userName | Ange **användarnamn** om du använder Grundläggande autentisering eller Windows-autentisering. | Inga |
| password | Ange **lösenord** för användarkontot som du angav för **userName**. Markera det här fältet som en **SecureString-typ** för att lagra det säkert i Data Factory. Du kan också [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| servicePrincipalId | Ange Azure Active Directory-programmets klient-ID. | Inga |
| aadServicePrincipalCredentialType | Ange vilken autentiseringstyp som ska användas för huvudautentisering av tjänsten. Tillåtna värden `ServicePrincipalKey` `ServicePrincipalCert`är: eller . | Inga |
| servicePrincipalKey | Ange nyckeln till Azure Active Directory-programmet. Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| servicePrincipalEmbeddedCert | Ange det base64-kodade certifikatet för ditt program som är registrerat i Azure Active Directory. Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| servicePrincipalEmbeddedCertPassword | Ange lösenordet för certifikatet om certifikatet är skyddat med ett lösenord. Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).  | Inga|
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Inga |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering.| Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om inget anges används standardkörningen för Azure Integration Runtime. |Inga |

**Exempel 1: Använda anonym autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: Använda grundläggande autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 3: Använda Windows-autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 4: Använda autentisering av tjänstens huvudnyckel**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Exempel 5: Använda autentisering av tjänstens huvudcertifikat**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som OData-datauppsättningen stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från OData anger du **egenskapen type** för datauppsättningen till **ODataResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens **typegenskap** måste anges till **ODataResource**. | Ja |
| path | Sökvägen till OData-resursen. | Ja |

**Exempel**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som OData-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData som källa

Om du vill kopiera data från OData stöds följande egenskaper i avsnittet Kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för källan Kopiera aktivitet måste anges till **ODataSource**. | Ja |
| DocumentDB | OData-frågealternativ för filtrering av data. Exempel: `"$select=Name,Description&$top=5"`.<br/><br/>**Obs:** OData-anslutningen kopierar data `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`från den kombinerade WEBBADRESSEN: . Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Inga |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du `RelationalSource` använde den maskinskrivna källan stöds den fortfarande som den är, medan du föreslås använda den nya framåt.

## <a name="data-type-mapping-for-odata"></a>Mappning av datatyp för OData

När du kopierar data från OData används följande mappningar mellan OData-datatyper och Azure Data Factory interimsdatatyper. Mer information om hur Kopiera aktivitet mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Datatyp för OData | Data Factory interimdatatyp |
|:--- |:--- |
| Edm.Binary (Edm.Binary) | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Singel | Enkel |
| Edm.Guid (edm.guid) | GUID |
| Edm.Int16 (på andra) | Int16 (int16) |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte (edm.sbyte) | Int16 (int16) |
| Edm.String | String |
| Edm.Time (på Edm.Time) | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData-komplexa datatyper (till exempel **Objekt)** stöds inte.


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns [i Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).