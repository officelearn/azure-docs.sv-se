---
title: Kopiera data från en REST-källa med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en moln-eller lokal REST-källa till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 8c7c8faad70022ba985a4041fd578becbaf70078
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966868"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiera data från en REST-slutpunkt genom att använda Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från en REST-slutpunkt. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

Skillnaden mellan den här REST-anslutningen, [http-kopplingen](connector-http.md) och [webb tabell anslutningen](connector-web-table.md) är:

- **Rest Connector** har stöd för att kopiera data från RESTful-API: er; 
- **Http-anslutningen** är generisk för att hämta data från alla http-slutpunkter, t. ex. för att hämta filen. Innan den här REST-anslutningen blir tillgänglig kan du välja att använda HTTP-anslutning för att kopiera data från RESTful-API, som stöds men mindre funktions jämförelser till REST Connector.
- **Webb tabells koppling** extraherar tabell innehåll från en HTML-webbsida.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en REST-källa till alla mottagar data lager som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder den här generiska REST-anslutningen:

- Hämta data från en REST-slutpunkt med metoderna **Get** eller **post** .
- Hämtar data genom att använda någon av följande autentiseringar: **Anonym**, **grundläggande**, **AAD-tjänstens huvud namn**och **hanterade identiteter för Azure-resurser**.
- **[Sid brytning](#pagination-support)** i REST-API: erna.
- Kopiera REST JSON-svaret [som det är](#export-json-response-as-is) eller parsa det med hjälp av [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping). Endast svars nytto Last i **JSON** stöds.

> [!TIP]
> Om du vill testa en begäran om data hämtning innan du konfigurerar REST-anslutningen i Data Factory kan du läsa om API-specifikationen för sidhuvuds-och text krav. Du kan använda verktyg som Postman eller webbläsare för att validera.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för REST-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den REST-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen **Type** måste anges till **RestService**. | Ja |
| url | Bas-URL: en för REST-tjänsten. | Ja |
| enableServerCertificateValidation | Om SSL-certifikatet på Server sidan ska verifieras vid anslutning till slut punkten. | Nej<br /> (Standardvärdet är **Sant**) |
| authenticationType | Typ av autentisering som används för att ansluta till REST-tjänsten. Tillåtna värden är **Anonymous**, **Basic**, **AadServicePrincipal** och **ManagedServiceIdentity**. Se motsvarande avsnitt nedan om du vill ha fler egenskaper respektive exempel. | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om detta inte anges använder den här egenskapen standard Azure Integration Runtime. |Nej |

### <a name="use-basic-authentication"></a>Använd grundläggande autentisering

Ange egenskapen **authenticationType** som **Basic**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| userName | Användar namnet som används för att få åtkomst till REST-slutpunkten. | Ja |
| password | Lösenordet för användaren (den **userName** värde). Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Använd AAD-tjänstens huvud namns autentisering

Ange egenskapen **authenticationType** till **AadServicePrincipal**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange det Azure Active Directory programmets klient-ID. | Ja |
| servicePrincipalKey | Ange Azure Active Directory programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering, t `https://management.core.windows.net`. ex.| Ja |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Använda hanterade identiteter för Azure-resurser-autentisering

Ange egenskapen **authenticationType** till **ManagedServiceIdentity**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering, t `https://management.core.windows.net`. ex.| Ja |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av REST-datauppsättningen. 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Följande egenskaper stöds för att kopiera data från REST:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens **typ** -egenskap måste anges till **RestResource**. | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen inte anges används endast den URL som anges i den länkade tjänst definitionen. | Nej |
| requestMethod | HTTP-metoden. Tillåtna värden är **Get** (standard) och **post**. | Nej |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Nej |
| requestBody | Bröd texten för HTTP-begäran. | Nej |
| paginationRules | Sid brytnings regler för att skapa nästa sida begär Anden. Mer information finns i avsnittet om [sid brytnings stöd](#pagination-support) . | Nej |

**Exempel 1: Använda Get-metoden med sid brytning**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Exempel 2: Använda post-metoden**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som REST-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST som källa

Följande egenskaper stöds i kopieringsaktiviteten **source** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | **Typ** egenskapen för kopierings aktivitets källan måste anges till **RestSource**. | Ja |
| httpRequestTimeout | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det här värdet är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. Standardvärdet är **00:01:40**.  | Nej |
| requestInterval | Vänte tiden innan begäran skickas för nästa sida. Standardvärdet är **00:00:01** |  Nej |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Stöd för sid brytning

Normalt REST API begränsa dess svars nytto Last storlek för en enskild begäran under ett rimligt antal. När stora mängder data skulle returneras delas resultatet in på flera sidor och det krävs att anropare skickar efterföljande begär Anden för att hämta nästa sida i resultatet. Vanligt vis är begäran för en sida dynamisk och består av den information som returneras från svars sidan föregående.

Denna generiska REST-anslutning har stöd för följande sid brytnings mönster: 

* Nästa begär ande absoluta eller relativa URL = egenskap svärdet i den aktuella svars texten
* Nästa begär ande absoluta eller relativa URL = huvud värde i aktuella svarshuvuden
* Nästa förfrågans frågeparameter = egenskaps värde i den aktuella svars texten
* Nästa förfrågans frågeparameter = header-värde i aktuella svarshuvuden
* Nästa begär ande rubrik = egenskaps värde i den aktuella svars texten
* Nästa begär ande rubrik = huvud värde i aktuella svarshuvuden

**Sid brytnings regler** definieras som en ord lista i data uppsättningen som innehåller ett eller flera Skift läges känsliga nyckel/värde-par. Konfigurationen kommer att användas för att generera begäran från den andra sidan. Kopplingen slutar att gå igenom när den får HTTP-statuskod 204 (inget innehåll) eller något av JSONPath-uttrycket i "paginationRules" returnerar null.

**Nycklar som stöds** i sid brytnings regler:

| Nyckel | Beskrivning |
|:--- |:--- |
| AbsoluteUrl | Anger den URL som utfärdar nästa begäran. Det kan **antingen vara en absolut URL eller en relativ URL**. |
| QueryParameters. *request_query_parameter* ELLER QueryParameters [' request_query_parameter '] | "request_query_parameter" är användardefinierad som refererar till ett parameter namn för en fråga i nästa HTTP-begärande-URL. |
| Sidhuvud. *request_header* ELLER rubriker [' request_header '] | "request_header" är användardefinierad som refererar till ett rubrik namn i nästa HTTP-begäran. |

**Värden som stöds** i sid brytnings regler:

| Value | Beskrivning |
|:--- |:--- |
| Sidhuvud. *response_header* ELLER rubriker [' response_header '] | "response_header" är användardefinierad som refererar till ett rubrik namn i det aktuella HTTP-svaret och värdet som ska användas för nästa begäran. |
| Ett JSONPath-uttryck som börjar med "$" (som representerar roten i svars texten) | Svars texten får bara innehålla ett JSON-objekt. JSONPath-uttrycket ska returnera ett enda primitivt värde som ska användas för nästa begäran. |

**Exempel:**

Facebook Graph API returnerar svar i följande struktur, i vilket fall visas nästa sidas URL i ***sid indelning. nästa***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Motsvarande konfiguration av rest-dataset särskilt `paginationRules` är följande:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Exportera JSON-svar som-är

Du kan använda den här REST-anslutningen för att exportera REST API JSON-svaret som är till olika filbaserade butiker. För att få en sådan oberoende kopia kan du hoppa över avsnittet "struktur" (kallas även *schema*) i data uppsättning och schema mappning i kopierings aktiviteten.

## <a name="schema-mapping"></a>Schema mappning

Information om hur du kopierar data från REST-slutpunkt till tabell mottagare finns i [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
