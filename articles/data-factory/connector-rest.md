---
title: Kopiera data från en REST-källa med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en källa för molnet eller lokalt REST till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 31f523dc8d171f62e814cd1a4225d2b25a972f36
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970329"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiera data från en REST-slutpunkt med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en REST-slutpunkt. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

Skillnaden mellan den här REST-anslutningsapp [HTTP-anslutningsappen](connector-http.md) och [tabell Webbanslutning](connector-web-table.md) är:

- **REST-anslutningsapp** mer specifikt stöd som kopierar data från RESTful API: er; 
- **HTTP-anslutningsappen** är generisk att hämta data från alla HTTP-slutpunkt, t.ex. att hämta filen. Innan det här REST-anslutningsapp blir tillgänglig, kan du råkar använda HTTP-anslutningsappen för att kopiera data från RESTful-API, vilket är stöds men mindre funktionella jämföra för REST-anslutningsapp.
- **Tabellen Webbanslutning** extraherar tabellen innehåll från en HTML-webbsidan.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en REST-källa till alla datalager för mottagare som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder den här allmän REST-anslutningen:

- Hämta data från en REST-slutpunkt med hjälp av den **hämta** eller **POST** metoder.
- Hämtning av data på något av följande autentiseringar: **Anonym**, **grundläggande**, **AAD tjänstens huvudnamn**, och **hanterade identiteter för Azure-resurser**.
- **[Sidbrytning](#pagination-support)**  i REST-API: er.
- Kopiera REST JSON-svar [som – är](#export-json-response-as-is) eller parsa den med hjälp av [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping). Svarsnyttolasten i **JSON** stöds.

> [!TIP]
> Läs mer om API-specifikationen för rubriken och brödtexten krav för att testa en begäran om hämtning av data innan du konfigurerar REST-anslutningen i Data Factory. Du kan använda verktyg som Postman eller en webbläsare för att verifiera.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för REST-anslutningsapp.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för REST-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **HTTP-servern**. | Ja |
| url | Bas-URL REST-tjänst. | Ja |
| enableServerCertificateValidation | Om du vill verifiera SSL-certifikat för server-sida när du ansluter till slutpunkten. | Nej<br /> (standardvärdet är **SANT**) |
| authenticationType | Typ av autentisering som används för att ansluta till REST-tjänst. Tillåtna värden är **anonym**, **grundläggande**, **AadServicePrincipal** och **ManagedServiceIdentity**. Se motsvarande avsnitt nedan på flera egenskaper och exempel respektive. | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används den här egenskapen standard Azure Integration Runtime. |Nej |

### <a name="use-basic-authentication"></a>Använd grundläggande autentisering

Ange den **authenticationType** egenskap **grundläggande**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| Användarnamn | Användarnamnet du använder för att få åtkomst till REST-slutpunkten. | Ja |
| lösenord | Lösenordet för användaren (den **användarnamn** värde). Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "HttpServer",
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

### <a name="use-aad-service-principal-authentication"></a>Använd autentisering av AAD tjänstens huvudnamn

Ange den **authenticationType** egenskap **AadServicePrincipal**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange Azure Active Directory-programmets klient-ID. | Ja |
| servicePrincipalKey | Ange nyckel för Azure Active Directory-programmet. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
| aadResourceId | Ange AAD-resurs som du begär om tillstånd, t.ex. `https://management.core.windows.net`.| Ja |

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

### <a name="managed-identity"></a> Använda hanterade identiteter för autentisering för Azure-resurser

Ange den **authenticationType** egenskap **ManagedServiceIdentity**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| aadResourceId | Ange AAD-resurs som du begär om tillstånd, t.ex. `https://management.core.windows.net`.| Ja |

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

Det här avsnittet innehåller en lista över egenskaper som har stöd för REST-datauppsättningen. 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från REST, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskap måste anges till **RestResource**. | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen har inte angetts används bara den URL som anges i länkade tjänstedefinition. | Nej |
| requestMethod | HTTP-metoden. Tillåtna värden är **hämta** (standard) och **Post**. | Nej |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Nej |
| RequestBody | Brödtexten för HTTP-begäran. | Nej |
| paginationRules | Sidbrytning regler till att skapa nästa sidförfrågningar. Referera till [stöd för sidbrytning](#pagination-support) avsnittet med information. | Nej |

**Exempel 1: Med Get-metoden med sidbrytning**

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

**Exempel 2: Med hjälp av metoden Post**

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

Det här avsnittet innehåller en lista över egenskaper som har stöd för REST-källan.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST som källa

Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för aktiviteten kopieringskälla måste anges till **RestSource**. | Ja |
| httpRequestTimeout | Timeout (i **TimeSpan** värde) för HTTP-begäran att få svar. Det här värdet är tidsgränsen för att få svar timeout inte att läsa svarsdata. Standardvärdet är **00:01:40**.  | Nej |
| requestInterval | Tiden att vänta innan du skickar en begäran för nästa sida. Standardvärdet är **00:00:01** |  Nej |

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

## <a name="pagination-support"></a>Stöd för sidbrytning

Normalt sett begränsa REST API dess svarsstorlek nyttolasten för en enskild förfrågan under ett rimligt antal; tag att returnera stora mängder data, det delar upp resultatet i flera sidor och kräver anropare ska kunna skicka efterföljande begäranden att hämta nästa sida i resultatet. Begäran för en sida är vanligtvis dynamiska och består av den information som returneras från svaret på föregående sida.

Den här allmän REST-anslutningsapp stöder följande sidbrytning mönster: 

* Nästa förfrågan absolut URL = egenskapsvärdet i aktuella svarstext
* Nästa förfrågan absolut URL = huvudvärde i aktuella svarshuvuden
* Nästa förfrågan Frågeparametern = egenskapsvärdet i aktuella svarstext
* Nästa förfrågan Frågeparametern = huvudvärde i aktuella svarshuvuden
* Nästa begärandehuvudet = egenskapsvärdet i aktuella svarstext
* Nästa begärandehuvudet = huvudvärde i aktuella svarshuvuden

**Regler för sidbrytning** definieras som en ordlista med datauppsättning som innehåller en eller flera skiftlägeskänsliga nyckel / värde-par. Konfigurationen används för att generera begäran från den andra sidan. Anslutningen slutar att styra när den får en HTTP-statuskod 204 (inget innehåll) eller någon av JSONPath-uttryck i ”paginationRules” returnerar null.

**Stöd för nycklar** i sidbrytning regler:

| Nyckel | Beskrivning |
|:--- |:--- |
| AbsoluteUrl | Anger URL: en för att skicka nästa begäran. |
| QueryParameters. *request_query_parameter* eller QueryParameters [request_query_parameter] | ”request_query_parameter” är en användardefinierad som hänvisar till en fråga parameternamn i nästa HTTP-begärans-URL. |
| Rubriker. *request_header* eller rubriker [request_header] | ”request_header” är en användardefinierad som hänvisar till en rubriknamn i nästa HTTP-begäran. |

**Värden som stöds** i sidbrytning regler:

| Värde | Beskrivning |
|:--- |:--- |
| Rubriker. *response_header* eller rubriker [response_header] | ”response_header” är en användardefinierad som hänvisar till en rubriknamn i det aktuella HTTP-svaret, där värdet kommer användas för att skicka nästa begäran. |
| Ett JSONPath-uttryck som börjar med ”$” (som representerar roten för svarstexten) | Svarstexten får innehålla endast en JSON-objekt. JSONPath-uttrycket ska returnera ett enstaka primitiva värde som ska användas för att skicka nästa begäran. |

**Exempel:**

Facebook Graph API returnerar svaret i följande struktur, där fallet nästa sidans URL representeras i ***paging.next***:

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

Motsvarande REST datauppsättning konfigurationen synnerhet sektionen `paginationRules` är följande:

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

## <a name="export-json-response-as-is"></a>Exportera JSON-svar som – är

Du kan använda den här REST-anslutningen för att exportera REST API-JSON-svar som – är att olika filbaserade lager. Hoppa över ”strukturen” för att uppnå kopian schemaoberoende (kallas även *schemat*) i avsnittet datauppsättning och schemamappning i kopieringsaktiviteten.

## <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från REST-slutpunkt till tabular mottagare, referera till [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
