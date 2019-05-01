---
title: Driftsätt | Azure Marketplace
description: 'Gå Live API: et initierar erbjudandet live lista processen.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ac56f86bad132f3e00a4b5c2507d65c0722c628c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935491"
---
<a name="go-live"></a>Starta direktsändning
=======

Detta API startar processen för att pusha en app till produktion. Den här åtgärden vanligtvis tidskrävande. Det här anropet använder e-postmeddelandelista från den [publicera](./cloud-partner-portal-api-publish-offer.md) API-åtgärden.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifierare för utgivare till erbjudandet ska hämtas, till exempel `contoso`       |  String       |
| offerId        | Erbjud identifierare för erbjudandet att hämta                                   |  String       |
| API-versionen    | Senaste versionen av API: et                                                   |  Date         |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Brödtext exempel
------------

### <a name="response"></a>Svar

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  URL till fråga för att fastställa den aktuella statusen för åtgärden            |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod** |  **Beskrivning**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Begäran har accepterats. Svaret innehåller en plats för att spåra Åtgärdsstatus. |
|  400     | `Bad/Malformed request` – Ytterligare felinformation hittar du i svarstexten. |
|  404     |  `Not found` -Den angivna entiteten finns inte.                                       |
|  |  |
