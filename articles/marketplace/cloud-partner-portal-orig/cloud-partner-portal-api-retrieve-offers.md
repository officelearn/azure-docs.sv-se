---
title: Hämta erbjuder API | Microsoft Docs
description: 'API: et hämtar en sammanfattad lista över erbjudanden i ett namnområde för utgivare.'
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
ms.openlocfilehash: 3429917fcee520ae932253f1fdfead4ffb6535e6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811262"
---
<a name="retrieve-offers"></a>Hämta erbjuder
===============

Hämtar en sammanfattad lista över erbjudanden i ett namnområde för utgivare.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI-parametrar
--------------

| ** Namn **        |  **Beskrivning**                         |  **Datatyp** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identifierare för utgivare, till exempel `contoso` |   Sträng    |
|  API-versionen     | Senaste versionen av API                    |    Date        |
|  |  |


<a name="header"></a>Sidhuvud
------

|  **Namn**        |         **Värde**       |
|  --------------- |       ----------------  |
|  Innehållstyp    | `application/json`      |
|  Auktorisering   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Brödtext exempel
------------

### <a name="response"></a>Svar

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Egenskaper för rapportinnehåll i svaret

|  **Namn**       |       **Beskrivning**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifierar typ av erbjudande                                                                                           |
|  publisherId    | Identifieraren som unikt identifierar utgivaren                                                                      |
|  status         | Status för erbjudandet. Listan över möjliga värden, se [erbjuder status](#offer-status) nedan.                         |
|  id             | GUID som unikt identifierar erbjudandet i publisher-namnområdet.                                                    |
|  version        | Aktuell version av erbjudandet. Att går inte ändra versionsegenskapen av klienten. Det ökar stegvis när varje publicering. |
|  definition     | Innehåller en sammanfattande vy över den faktiska definitionen av arbetsbelastningen. För att få en detaljerad definition kan du använda [hämta specifika erbjudandet] (. / cloud-partner-portal-api-retrieve-specific-offer.md) API. |
|  changedTime    | UTC-tid då erbjudandet senast ändrades                                                                              |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod**  |  **Beskrivning**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Begäran har bearbetats och alla erbjudanden under utgivaren har returnerats till klienten.  |
|  400      | `Bad/Malformed request` -Fel svarstexten kan innehålla mer information.                                    |
|  403      | `Forbidden` -Klienten har inte åtkomst till det angivna namnområdet.                                          |
|  404      | `Not found` -Den angivna entiteten finns inte.                                                                 |
|  |  |


### <a name="offer-status"></a>Status för erbjudande

|  **Namn**                    | **Beskrivning**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Erbjudandet har inte publicerats.                  |
|  NotStarted                  | Erbjudandet är nytt, men har startats inte.                 |
|  WaitingForPublisherReview   | Erbjudande väntar på godkännande av utgivaren.         |
|  Körs                     | Erbjud bidrag bearbetas.             |
|  Lyckades                   | Erbjudandet bidrag har bearbetat.       |
|  Avbrutna                    | Erbjudandet överföringen avbröts.                   |
|  Misslyckad                      | Det gick inte att skicka för erbjudandet.                         |
|  |  |
