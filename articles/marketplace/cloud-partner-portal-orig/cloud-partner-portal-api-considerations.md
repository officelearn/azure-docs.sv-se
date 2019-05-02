---
title: API-överväganden | Azure Marketplace
description: 'Versionshantering, felhantering och auktorisering problem när du använder marketplace API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6bf27db27daee50f78552344ae1b2b116d48a5c0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935565"
---
# <a name="api-considerations"></a>API-överväganden


<a name="api-versioning"></a>API-versionshantering
--------------

Det kan finnas flera versioner av API: et som är tillgängliga på samma gång. Klienter måste ange vilken version som de vill anropa användning genom att tillhandahålla den `api-version` parametern som en del av frågesträngen.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Svaret på en begäran med ett okänt eller ogiltigt API-versionen är en HTTP-koden 400. Det här felet returnerar insamling av kända API-versioner i svarstexten.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Fel
------

API: et hanterar fel med motsvarande HTTP-statuskoder och eventuellt ytterligare information i serialiserad som JSON-svaret.
När du får ett fel, särskilt ett 400-klass-fel, försök inte att göra begäran innan åtgärda den underliggande orsaken. Åtgärda exempelvis parametern API-version i exempelsvaret ovan innan du skickar igen begäran.

<a name="authorization-header"></a>Auktoriseringsrubrik
--------------------

För alla API: erna i den här referensen, måste du ange auktoriseringsrubriken tillsammans med ägartoken som erhållits från Azure Active Directory (AD Azure). Den här rubriken krävs för att ta emot ett giltigt svar; Om den inte finns en `401 Unauthorized` fel returneras. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
