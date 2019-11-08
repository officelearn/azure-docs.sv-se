---
title: Avbryt åtgärds-API | Azure Marketplace
description: Avbryt-åtgärder.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819791"
---
# <a name="cancel-operation"></a>Avbryt åtgärd 

Detta API avbryter en åtgärd som pågår för erbjudandet. Använd [API: er för att hämta åtgärder](./cloud-partner-portal-api-retrieve-operations.md) för att få en `operationId` att skicka till det här API: et. Annullering är vanligt vis en synkron åtgärd, men i vissa komplexa scenarier kan det krävas en ny åtgärd för att avbryta en befintlig åtgärd. I det här fallet innehåller HTTP-svars texten den åtgärds plats som ska användas för att fråga om status.

Du kan tillhandahålla en kommaavgränsad lista med e-postadresser med begäran och API: et kommer att meddela dessa adresser om åtgärdens förlopp.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**    |      **Beskrivning**                                  |    **Datatyp**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Utgivar identifierare, till exempel `contoso`         |   Sträng          |
| OfferId      |  Erbjudande-ID                                     |   Sträng          |
| API-version  |  Aktuell version av API                               |    Date           |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**              |  **Värde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Auktorisering         |  Förser din TOKEn |
|  |  |


<a name="body-example"></a>Body-exempel
------------

### <a name="request"></a>Förfrågan

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Egenskaper för begär ande brödtext

|  **Namn**                |  **Beskrivning**                                               |
|  --------                |  ---------------                                               |
|  meddelande – e-post     | Kommaavgränsad lista med e-post-ID: n för att få ett meddelande om förloppet för publicerings åtgärden. |
|  |  |


### <a name="response"></a>Svar

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                       |
|  ---------            |    ----------                      |
| Åtgärds plats    | URL, som kan tillfrågas för att fastställa aktuell status för åtgärden. |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Rikt**  |  **Beskrivning**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Okej. Begäran har bearbetats och åtgärden avbröts synkront. |
|  202      | Accept. Begäran har bearbetats och åtgärden håller på att avbrytas. Platsen för avbrotts åtgärden returneras i svars huvudet. |
|  400      | Felaktig/felaktig begäran. Fel svars texten kan innehålla mer information.  |
|  403      | Åtkomst förbjuden. Klienten har inte åtkomst till det namn område som anges i begäran. |
|  404      | Hittades inte. Den angivna entiteten finns inte. |
|  |  |
