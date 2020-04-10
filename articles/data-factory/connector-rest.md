---
title: Kopiera data från en REST-källa med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från en moln- eller lokal REST-källa till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: ca913a4c197e04e20c962c4a4a7a1e479a3cdf92
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990896"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiera data från en REST-slutpunkt med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från en REST-slutpunkt. Artikeln bygger på [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md), som presenterar en allmän översikt över kopiera aktivitet.

Skillnaden mellan den här REST-kopplingen, [HTTP-kopplingen](connector-http.md) och [webbtabellkopplingen](connector-web-table.md) är:

- **REST-anslutning** stöder specifikt kopiering av data från RESTful API:er. 
- **HTTP-anslutningsappen** är allmän för att hämta data från valfri HTTP-slutpunkt, t.ex. Innan den här REST-anslutningen blir tillgänglig kan det hända att http-anslutningsappen används för att kopiera data från RESTful API, som stöds men mindre funktionellt jämfört med REST-anslutningsappen.
- **Webbbordskoppling** extraherar tabellinnehåll från en HTML-webbsida.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en REST-källa till alla sink-datalager som stöds. En lista över datalager som Kopierar aktivitet stöder som källor och sänkor finns i [Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Denna generiska REST-kontakt stöder:

- Hämtar data från en REST-slutpunkt med hjälp av **get-** eller **postmetoderna.**
- Hämtar data med hjälp av någon av följande autentiseringar: **Anonym**, **Basic**, **AAD-tjänsthuvudnamn**och **hanterade identiteter för Azure-resurser**.
- **[Sidnumrering](#pagination-support)** i REST-API:erna.
- Kopiera REST [JSON-svaret som det är](#export-json-response-as-is) eller tolka det med hjälp av [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping). Endast svarsnyttolast i **JSON** stöds.

> [!TIP]
> Om du vill testa en begäran om datahämtning innan du konfigurerar REST-anslutningen i Data Factory kan du läsa om API-specifikationen för huvud- och kroppskrav. Du kan använda verktyg som Brevbärare eller en webbläsare för att validera.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som du kan använda för att definiera datafabrikentiteter som är specifika för REST-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den REST-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** måste vara **restservice**. | Ja |
| url | Rest-tjänstens bas-URL. | Ja |
| aktiveraServerCertificateValidation | Om TLS/SSL-certifikat på serversidan ska valideras vid anslutning till slutpunkten. | Inga<br /> (standardvärdet är **sant)** |
| authenticationType | Typ av autentisering som används för att ansluta till REST-tjänsten. Tillåtna värden är **Anonym**, **Basic**, **AadServicePrincipal** och **ManagedServiceIdentity**. Se motsvarande avsnitt nedan om fler egenskaper respektive exempel. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges använder den här egenskapen standardkörningen för Azure Integration. |Inga |

### <a name="use-basic-authentication"></a>Använd grundläggande autentisering

Ange egenskapen **authenticationType** till **Basic**. Förutom de generiska egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användarnamnet som ska användas för att komma åt REST-slutpunkten. | Ja |
| password | Lösenordet för användaren **(värdet användarnamn).** Markera det här fältet som en **SecureString-typ** för att lagra det säkert i Data Factory. Du kan också [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

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

### <a name="use-aad-service-principal-authentication"></a>Använd AAD-tjänstens huvudautentisering

Ange egenskapen **authenticationType** till **AadServicePrincipal**. Förutom de generiska egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange Azure Active Directory-programmets klient-ID. | Ja |
| servicePrincipalKey | Ange nyckeln till Azure Active Directory-programmet. Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |
| aadResourceId | Ange den AAD-resurs som du begär för `https://management.core.windows.net`auktorisering, t.ex.| Ja |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Använda hanterade identiteter för Azure-resursautentisering

Ange **egenskapen authenticationType** till **ManagedServiceIdentity**. Förutom de generiska egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| aadResourceId | Ange den AAD-resurs som du begär för `https://management.core.windows.net`auktorisering, t.ex.| Ja |

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

Det här avsnittet innehåller en lista över egenskaper som REST-datauppsättningen stöder. 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Så här kopierar du data från REST:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens **typegenskap** måste anges till **RestResource**. | Ja |
| släktingUrl | En relativ URL till resursen som innehåller data. När den här egenskapen inte anges används bara webbadressen som anges i den länkade tjänstdefinitionen. HTTP-kopplingen kopierar data från `[URL specified in linked service]/[relative URL specified in dataset]`den kombinerade URL:en: . | Inga |

Om du `requestMethod`ställer `additionalHeaders` `requestBody` in `paginationRules` , och i datauppsättningen stöds det fortfarande som det är, medan du föreslås använda den nya modellen i aktivitetskällan framöver.

**Exempel:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som REST-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST som källa

Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till **RestSource**. | Ja |
| begäranMethod | HTTP-metoden. Tillåtna värden är **Get** (standard) och **Bokför**. | Inga |
| additionalHeaders | Ytterligare HTTP-begäranden. | Inga |
| begäranKropp | Brödtexten för HTTP-begäran. | Inga |
| paginationRules | Sidnumreringsreglerna för att skriva nästa sidbegäranden. Mer information finns i [avsnittet sidnumreringsstöd.](#pagination-support) | Inga |
| httpRequestTimeout (På) | Timeout **(TimeSpan-värdet)** för HTTP-begäran för att få ett svar. Det här värdet är tidsgränsen för att få ett svar, inte timeout för att läsa svarsdata. Standardvärdet är **00:01:40**.  | Inga |
| requestInterval | Tiden att vänta innan du skickar begäran om nästa sida. Standardvärdet är **00:00:01** |  Inga |

>[!NOTE]
>REST-kopplingen ignorerar alla "Acceptera"-huvud som anges i `additionalHeaders`. Eftersom REST-anslutning endast stöder svar i JSON, `Accept: application/json`kommer det automatiskt att generera en rubrik på .

**Exempel 1: Använda metoden Hämta med sidnumrering**

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
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel 2: Använda metoden Post**

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
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Stöd för sidnumrering

Normalt begränsar REST API sin svarsnyttolaststorlek för en enda begäran under ett rimligt antal. medan du ska returnera stora mängder data, delar det resultatet i flera sidor och kräver att de som ringer skickar på varandra följande begäranden för att få nästa sida av resultatet. Vanligtvis är begäran om en sida dynamisk och består av den information som returneras från svaret från föregående sida.

Den här generiska REST-kopplingen stöder följande sidnumreringsmönster: 

* Nästa begärans absoluta eller relativa URL = egenskapsvärde i den aktuella svarstexten
* Nästa begärans absoluta eller relativa URL = huvudvärde i aktuella svarshuvuden
* Frågeparametern för nästa begäran = egenskapsvärde i den aktuella svarstexten
* Frågeparametern för nästa begäran = huvudvärde i aktuella svarshuvuden
* Rubriken på nästa begäran = egenskapsvärdet i den aktuella svarstexten
* Sidhuvudet för nästa begäran = huvudvärde i aktuella svarshuvuden

**Sidnumreringsregler** definieras som en ordlista i datauppsättningen som innehåller ett eller flera skiftlägeskänsliga nyckelvärdespar. Konfigurationen används för att generera begäran från den andra sidan. Kopplingen slutar iterera när http-statuskoden 204 (Inget innehåll) eller något av JSONPath-uttrycken i "paginationRules" returnerar null.

**Nycklar som stöds** i sidnumreringen:

| Nyckel | Beskrivning |
|:--- |:--- |
| AbsoluteUrl (absolutur) | Anger webbadressen för att utfärda nästa begäran. Det kan **vara antingen absolut URL eller relativ WEBBADRESS**. |
| QueryParameters. *request_query_parameter* ELLER QueryParameters['request_query_parameter'] | "request_query_parameter" är användardefinierad som refererar till ett frågeparameternamn i nästa HTTP-begärande-URL. |
| Headers. *request_header* ELLER-rubriker['request_header'] | "request_header" är användardefinierad som refererar till ett rubriknamn i nästa HTTP-begäran. |

**Värden som stöds** i sidnumreringens regler:

| Värde | Beskrivning |
|:--- |:--- |
| Headers. *response_header* ELLER-rubriker['response_header'] | "response_header" är användardefinierad som refererar till ett rubriknamn i det aktuella HTTP-svaret, vars värde kommer att användas för att utfärda nästa begäran. |
| Ett JSONPath-uttryck som börjar med "$" (som representerar roten till svarstexten) | Svarstexten bör endast innehålla ett JSON-objekt. JSONPath-uttrycket ska returnera ett enda primitivt värde, som ska användas för att utfärda nästa begäran. |

**Exempel:**

Facebook Graph API returnerar svar i följande struktur, i vilket fall nästa sidas WEBBADRESS representeras i ***personsökning.next:***

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

Motsvarande REST-kopieringsaktivitetskälla `paginationRules` konfiguration särskilt är följande:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Använda OAuth
I det här avsnittet beskrivs hur du använder en lösningsmall för att kopiera data från REST-anslutning till Azure Data Lake Storage i JSON-format med OAuth. 

### <a name="about-the-solution-template"></a>Om lösningsmallen

Mallen innehåller två aktiviteter:
- **Webbaktivitet** hämtar innehavartoken och skickar den sedan till efterföljande kopieringsaktivitet som auktorisering.
- **Kopiera** aktivitetskopior data från REST till Azure Data Lake Storage.

Mallen definierar två parametrar:
- **SinkContainer** är sökvägen till rotmappen där data kopieras till i din Azure Data Lake Storage. 
- **SinkDirectory** är katalogsökvägen under roten där data kopieras till i din Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Gå till **mallen Kopiera från REST eller HTTP med OAuth.** Skapa en ny anslutning för Källanslutning. 
    ![Skapa nya anslutningar](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Nedan finns viktiga steg för nya inställningar för länkad tjänst (REST):
    
     1. Under **Bas-URL**anger du url-parametern för din egen resttjänst för källa. 
     2. För **autentiseringstyp**väljer du *Anonym*.
        ![Ny REST-anslutning](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Skapa en ny anslutning för målanslutning.  
    ![Ny Gen2-anslutning](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Välj **Använd den här mallen**.
    ![Använd den här mallen](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Du ser att pipelinen har skapats ![som visas i följande exempel: Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Välj **Webbaktivitet.** I **Inställningar**anger du motsvarande **URL,** **Metod**, **Rubriker**och **Brödtext** för att hämta OAuth-innehavartoken från inloggnings-API:et för den tjänst som du vill kopiera data från. Platshållaren i mallen visar ett exempel på Azure Active Directory (AAD) OAuth. AAD-autentisering stöds internt av REST-anslutning, här är bara ett exempel för OAuth-flöde. 

    | Egenskap | Beskrivning |
    |:--- |:--- |:--- |
    | URL |Ange url:en som OAuth-bärarekenkenken ska hämtas från. t.ex. i provet här är dethttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metod | HTTP-metoden. Tillåtna värden är **Bokför** och **Hämta**. | 
    | Rubriker | Huvudet är användardefinierat, som refererar till ett rubriknamn i HTTP-begäran. | 
    | Innehåll | Brödtexten för HTTP-begäran. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. I **Kopiera dataaktivitet** väljer du *Fliken Källa,* du kan se att innehavartoken (access_token) som hämtats från föregående steg skulle skickas till Kopiera dataaktivitet som **auktorisering** under Ytterligare rubriker. Bekräfta inställningar för följande egenskaper innan du startar en pipeline-körning.

    | Egenskap | Beskrivning |
    |:--- |:--- |:--- | 
    | Metod för begäran | HTTP-metoden. Tillåtna värden är **Get** (standard) och **Bokför**. | 
    | Ytterligare rubriker | Ytterligare HTTP-begäranden.| 

   ![Kopiera autentisering av källa](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.
   ![Pipeline-körning](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. När pipelinekörningen har slutförts visas resultatet som liknar följande ![exempel: Pipeline-körningsresultat](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klicka på ikonen "Utdata" i kolumnen WebActivity in **Actions,** så visas de access_token som returneras av tjänsten.

   ![Tokenutdata](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klicka på ikonen "Indata" i kolumnen CopyActivity i **Åtgärder,** du ser access_token som hämtas av WebActivity skickas till CopyActivity för autentisering. 

    ![Tokenindata](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Om du vill undvika att token loggas i oformaterad text aktiverar du "Säker utdata" i webbaktivitet och "Säker inmatning" i kopieringsaktivitet.


## <a name="export-json-response-as-is"></a>Exportera JSON-svar i dess

Du kan använda den här REST-kopplingen för att exportera REST API JSON-svar som det är till olika filbaserade butiker. Om du vill uppnå en sådan schemaa-agnostisk kopia hoppar du över avsnittet "struktur" (kallas även *schema)* i datauppsättning och schemamappning i kopieringsaktivitet.

## <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från REST-slutpunkten till tabellmottagaren läser du [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns [i Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
