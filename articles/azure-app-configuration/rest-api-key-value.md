---
title: Azure App konfiguration REST API-Key-Value
description: Referens sidor för att arbeta med nyckel värden med Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 50d97a330507e9361674776acf29d1007ee5bf58
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424384"
---
# <a name="key-values"></a>Key-Values

API-version: 1,0

Ett nyckel värde är en resurs som identifieras av en unik kombination av `key`  +  `label` . `label` är valfritt. Om du explicit vill referera till ett nyckel värde utan etiketten använder du "\ 0" (URL kodad som ``%00`` ). Se information om varje åtgärd.

## <a name="operations"></a>Operations

- Hämta
- Visa flera
- Ange
- Ta bort

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Hämta Key-Value

**Krävs:** ``{key}`` , ``{api-version}``  
*Valfritt:* ``label`` – Om det utelämnas används ett nyckel värde utan en etikett

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Om nyckeln inte finns returneras följande svar:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Hämta (villkorligt)

Använd `If-Match` eller begär huvuden för att förbättra cachelagring av klienter `If-None-Match` . `etag`Argumentet är en del av nyckel representationen. Se [avsnitt 14,24 och 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Följande begäran hämtar bara nyckel värde om den aktuella representationen inte matchar den angivna `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Registrera**

```http
HTTP/1.1 304 NotModified
```

eller

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Lista Key-Values

Se **filtrera** efter ytterligare alternativ

*Valfritt:* ``key`` – om detta inte anges förutsätts det **någon** nyckel.
*Valfritt:* ``label`` -om detta inte anges betyder det att det finns **en** etikett.

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Svarade**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

## <a name="pagination"></a>Sidnumrering

Resultatet blir en sid brytning om antalet returnerade objekt överskrider svars gränsen. Följ de valfria svarshuvuden `Link` och Använd `rel="next"` för navigering.
Alternativt innehåller innehållet en nästa länk i form av `@nextLink` egenskapen. Den länkade URI: n innehåller `api-version` argumentet.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Svarade**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Filtrering

En kombination av `key` och `label` filtrering stöds.
Använd valfria `key` parametrar och `label` frågesträngar.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filter som stöds

|Nyckel filter|Effekt|
|--|--|
|`key` utelämnas eller `key=*`|Matchar **alla** nycklar|
|`key=abc`|Matchar en nyckel med namnet **ABC**|
|`key=abc*`|Matchar namn på nycklar som börjar med **ABC**|
|`key=abc,xyz`|Matchar nycklarnas namn **ABC** eller **XYZ** (begränsat till 5 CSV)|

|Etikett filter|Effekt|
|--|--|
|`label` utelämnas eller `label=*`|Matchar **alla** etiketter|
|`label=%00`|Matchar KV utan etikett|
|`label=prod`|Matchar etiketten **Prod**|
|`label=prod*`|Matchar etiketter som börjar med **Prod**|
|`label=prod,test`|Matchar etiketter **Prod** eller **test** (begränsat till 5 CSV)|

**_Reserverade tecken_* _

`_`, `\`, `,`

Om ett reserverat tecken ingår i värdet måste det föregås av `\{Reserved Character}` . Icke-reserverade tecken kan också undantas.

***Filter verifiering** _

Om ett filter verifierings fel används är svaret HTTP `400` med fel information:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

_ *Exempel**

- Alla

    ```http
    GET /kv?api-version={api-version}
    ```

- Nyckel namnet börjar med **ABC** och inkluderar alla etiketter

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Nyckel namnet börjar med **ABC** och etiketten är lika med **v1** eller **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Begär vissa fält

Använd den valfria frågesträngparametern `$select` och ange en kommaavgränsad lista med begärda fält. Om `$select` parametern utelämnas innehåller svaret standard uppsättningen.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based åtkomst

Få en representation av resultatet som det var vid en tidigare tidpunkt. Se avsnitt [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). Sid brytning stöds fortfarande enligt definitionen ovan.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Svarade**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>Ange nyckel

- **Krävs:**``{key}``
- *Valfritt:* ``label`` -Om inget annat anges eller etikett = %00, betyder det KV utan etikett.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Om objektet är låst får du följande svar:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Ange nyckel (villkorligt)

Använd `If-Match` eller begär huvuden för att förhindra tävlings förhållanden `If-None-Match` . `etag`Argumentet är en del av nyckel representationen.
Om `If-Match` eller `If-None-Match` utelämnas blir åtgärden ovillkorlig.

Följande svar uppdaterar värdet endast om den aktuella representationen matchar den angivna `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Följande svar uppdaterar värdet endast om den aktuella representationen *inte* matchar den angivna `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Följande begäran lägger bara till värdet om det redan finns en representation:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

Följande begäran lägger till värdet endast om en representation *inte* redan finns:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Svar**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

eller

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Ta bort

- **Krävs:** `{key}` , `{api-version}`
- *Valfritt:* `{label}` -Om inget annat anges eller etikett = %00, betyder det KV utan etikett.

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Svar:** Returnera det borttagna nyckel-eller none-värdet om nyckeln inte finns.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

eller

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Ta bort nyckel (villkorligt)

Liknar **Ange nyckel (villkorligt)**
