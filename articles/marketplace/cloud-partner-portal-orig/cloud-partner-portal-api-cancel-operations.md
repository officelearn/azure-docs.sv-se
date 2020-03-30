---
title: API för avbryt åtgärd | Azure Marketplace
description: Avbryt åtgärder .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d4c1f52f0f3b1e05ec06f5a66a36323f346d4eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280549"
---
# <a name="cancel-operation"></a>Avbryt åtgärd 

Det här API:et avbryter en pågående åtgärd för erbjudandet. Använd [API:et](./cloud-partner-portal-api-retrieve-operations.md) hämta `operationId` åtgärder för att få ett att skicka till det här API:et. Annullering är vanligtvis en synkron åtgärd, men i vissa komplexa scenarier kan en ny åtgärd krävas för att avbryta en befintlig. I det här fallet innehåller HTTP-svarstexten åtgärdens plats som ska användas för att fråga status.

Du kan ange en kommaavgränsad lista över e-postadresser med begäran, och API:et meddelar dessa adresser om åtgärdens förlopp.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**    |      **Beskrivning**                                  |    **Datatyp**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Publisher-identifierare, till exempel`contoso`         |   String          |
| offerId (erbjudandeId)      |  Identifierare för erbjudande                                     |   String          |
| api-version  |  Aktuell version av API                               |    Datum           |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**              |  **Värde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Auktorisering         |  Bärare din token |
|  |  |


<a name="body-example"></a>Exempel på brödtext
------------

### <a name="request"></a>Förfrågan

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Egenskaper för begäran om brödtext

|  **Namn**                |  **Beskrivning**                                               |
|  --------                |  ---------------                                               |
|  anmälan-e-post     | Kommaavgränsad lista över e-post-ID:er som ska meddelas om förloppet för publiceringsåtgärden. |
|  |  |


### <a name="response"></a>Svar

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                       |
|  ---------            |    ----------                      |
| Operation-plats    | URL, som kan efterfrågas för att fastställa åtgärdens aktuella status. |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod**  |  **Beskrivning**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Okej. Begäran har bearbetats och åtgärden avbryts synkront. |
|  202      | Accepterade. Begäran har bearbetats och åtgärden håller på att avbrytas. Platsen för annulleringsåtgärden returneras i svarshuvudet. |
|  400      | Felaktig/felaktig begäran. Felsvarstexten kan ge mer information.  |
|  403      | Tillträde Förbjudet. Klienten har inte åtkomst till det namnområde som anges i begäran. |
|  404      | Hittades inte. Den angivna entiteten finns inte. |
|  |  |
