---
title: Avbryt åtgärden API | Azure Marketplace
description: Avbryta åtgärder.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 70ffd13be4ba934b423e3bb5344eea0a9c36886c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935549"
---
# <a name="cancel-operation"></a>Avbryt åtgärden 

Detta API avbryter en åtgärd pågår på erbjudandet. Använd den [hämta åtgärdens API](./cloud-partner-portal-api-retrieve-operations.md) att hämta en `operationId` ska skickas till den här API: et. Annulleringen är vanligtvis en synkron åtgärd, men i vissa komplexa situationer kan det krävas en ny åtgärd att avbryta en befintlig. I det här fallet innehåller HTTP-svarstext åtgärdens plats som ska användas för att fråga status.

Du kan ange en kommaavgränsad lista över e-postadresser med begäran och API: et meddelar dessa adresser om förloppet för åtgärden.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**    |      **Beskrivning**                                  |    **Datatyp**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identifierare för utgivare, till exempel `contoso`         |   String          |
| offerId      |  Erbjudande-ID                                     |   String          |
| API-versionen  |  Aktuell version av API                               |    Date           |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**              |  **Värde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Auktorisering         |  DIN ÄGARTOKEN |
|  |  |


<a name="body-example"></a>Brödtext exempel
------------

### <a name="request"></a>Förfrågan

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Begära egenskaper för rapportinnehåll

|  **Namn**                |  **Beskrivning**                                               |
|  --------                |  ---------------                                               |
|  e-postaviseringar     | Kommaavgränsad lista över e-post-ID: N för att aviseras om förloppet för publiceringsåtgärden. |
|  |  |


### <a name="response"></a>Svar

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL som kan efterfrågas för att fastställa den aktuella statusen för åtgärden. |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod**  |  **Beskrivning**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Okej. Begäran har bearbetats och åtgärden avbryts synkront. |
|  202      | Accepteras. Begäran har bearbetats och åtgärden avbryts. Platsen för åtgärden för att avbryta returneras i svarshuvudet. |
|  400      | Felaktig/Malformed begäran. Svarstexten fel kan ge mer information.  |
|  403      | Access Forbidden. Klienten har inte åtkomst till det namnområde som angavs i begäran. |
|  404      | Kunde inte hittas. Den angivna entiteten finns inte. |
|  |  |
