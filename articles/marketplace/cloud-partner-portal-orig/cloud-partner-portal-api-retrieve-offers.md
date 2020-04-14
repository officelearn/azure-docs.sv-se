---
title: Hämta API för att hämta erbjudanden | Azure Marketplace
description: API hämtar en sammanfattad lista över erbjudanden under ett namnområde för utgivare.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255965"
---
<a name="retrieve-offers"></a>Hämta erbjudanden
===============

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Hämtar en sammanfattad lista med erbjudanden under ett namnområde för utgivare.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

| **Namn**         |  **Beskrivning**                         |  **Datatyp** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Publisher-identifierare, till exempel`contoso` |   Sträng    |
|  api-version     | Senaste versionen av API                    |    Date        |
|  |  |


<a name="header"></a>Huvud
------

|  **Namn**        |         **Värde**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Auktorisering   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Exempel på brödtext
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

### <a name="response-body-properties"></a>Egenskaper för svarstext

|  **Namn**       |       **Beskrivning**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifierar typen av erbjudande                                                                                           |
|  publisherId    | Identifierare som unikt identifierar utgivaren                                                                      |
|  status         | Erbjudandets status. Information om listan över möjliga värden finns i [Erbjudandestatus](#offer-status) nedan.                         |
|  id             | GUID som unikt identifierar erbjudandet i utgivarens namnområde.                                                    |
|  version        | Aktuell version av erbjudandet. Versionsegenskapen kan inte ändras av klienten. Det ökas efter varje publicering. |
|  definition     | Innehåller en sammanfattad vy över den faktiska definitionen av arbetsbelastningen. Om du vill få en detaljerad definition använder du [API:et hämta specifikt erbjudande.](./cloud-partner-portal-api-retrieve-specific-offer.md) |
|  ändradTid    | UTC-tid när erbjudandet senast ändrades                                                                              |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod**  |  **Beskrivning**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Begäran har behandlats och alla erbjudanden under utgivaren returnerades till klienten.  |
|  400      | `Bad/Malformed request`- Felsvarstexten kan innehålla mer information.                                    |
|  403      | `Forbidden`- Klienten har inte åtkomst till det angivna namnområdet.                                          |
|  404      | `Not found`- Den angivna entiteten finns inte.                                                                 |
|  |  |


### <a name="offer-status"></a>Status för erbjudande

|  **Namn**                    | **Beskrivning**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Aldrigpublicerat              | Erbjudandet har aldrig publicerats.                  |
|  Inte startad                  | Erbjudandet är nytt men startas inte.                 |
|  Väntar påpublicerAreView   | Erbjudandet väntar på utgivarens godkännande.         |
|  Körs                     | Inlämning av erbjudande bearbetas.             |
|  Lyckades                   | Erbjudandet inlämning har slutfört behandlingen.       |
|  Avbrutna                    | Inlämningen av erbjudandet avbröts.                   |
|  Misslyckades                      | Erbjudandet misslyckades.                         |
|  |  |
