---
title: Driftsätt | Microsoft Docs
description: 'Gå Live API: et initierar erbjudandet live lista processen.'
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c7d643f0c7885e64636a107d22ce332b1ba9371c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811298"
---
<a name="go-live"></a>Starta direktsändning
=======

Detta API startar processen för att pusha en app till produktion. Den här åtgärden vanligtvis tidskrävande. Det här anropet använder e-postmeddelandelista från den [publicera](./cloud-partner-portal-api-publish-offer.md) API-åtgärden.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifierare för utgivare till erbjudandet ska hämtas, till exempel `contoso`       |  Sträng       |
| offerId        | Erbjud identifierare för erbjudandet att hämta                                   |  Sträng       |
| API-versionen    | Senaste versionen av API: et                                                   |  Date         |
|  |  |  |


<a name="header"></a>Sidhuvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Innehållstyp    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Brödtext exempel
------------

### <a name="response"></a>Svar

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Åtgärden-plats    |  URL till fråga för att fastställa den aktuella statusen för åtgärden            |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod** |  ** Beskrivning **                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Begäran har accepterats. Svaret innehåller en plats för att spåra Åtgärdsstatus. |
|  400     | `Bad/Malformed request` – Ytterligare felinformation hittar du i svarstexten. |
|  404     |  `Not found` -Den angivna entiteten finns inte.                                       |
|  |  |
