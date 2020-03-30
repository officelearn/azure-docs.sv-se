---
title: API-överväganden | Azure Marketplace
description: Versionshantering, felhantering och auktoriseringsproblem när du använder marketplace-API:erna.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4e04f521ed2023dfb9cd562549cb2e1bcd319b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288639"
---
# <a name="api-considerations"></a>API-överväganden


<a name="api-versioning"></a>API-versionshantering
--------------

Det kan finnas flera versioner av API:et som är tillgängliga samtidigt. Klienter måste ange vilken version de vill `api-version` anropa genom att ange parametern som en del av frågesträngen.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Svaret på en begäran med en okänd eller ogiltig API-version är en HTTP-kod 400. Det här felet returnerar samlingen av kända API-versioner i svarstexten.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Fel
------

API:et svarar på fel med motsvarande HTTP-statuskoder och eventuellt ytterligare information i svaret serialiseras som JSON.
NÃ¤s en error, särskilt ett 400-klassfel, nÃ¤nder du inte fÃ¶nde av begäran igen innan du åtgärdar den underliggande orsaken. I exempel svaret ovan korrigeringar du parametern API-version innan begäran skickas igen.

<a name="authorization-header"></a>Auktoriseringshuvud
--------------------

För alla API:er i den här referensen måste du skicka auktoriseringshuvudet tillsammans med innehavartoken som hämtats från Azure Active Directory (Azure AD). Det här huvudet krävs för att få ett giltigt svar. Om det inte `401 Unauthorized` finns returneras ett fel. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
