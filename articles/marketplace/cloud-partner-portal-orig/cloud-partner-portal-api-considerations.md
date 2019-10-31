---
title: API-överväganden | Azure Marketplace
description: 'Versions hantering, fel hantering och auktoriseringsfel vid användning av Marketplace-API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 762c90b62ed2a9347ae88a50a11bfe02f3b23ba4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162661"
---
# <a name="api-considerations"></a>API-överväganden


<a name="api-versioning"></a>API-versionshantering
--------------

Det kan finnas flera versioner av API: et som är tillgängliga på samma gång. Klienterna måste ange vilken version de vill anropa användningen genom att ange parametern `api-version` som en del av frågesträngen.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Svaret på en begäran med en okänd eller ogiltig API-version är en HTTP-kod 400. Det här felet returnerar samlingen kända API-versioner i svars texten.

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

API: et svarar på fel med motsvarande HTTP-statuskod och eventuellt ytterligare information i svaret som är serialiserad som JSON.
När du får ett fel, särskilt ett 400-klass fel, ska du inte försöka utföra begäran igen innan du korrigerar den bakomliggande orsaken. I exempel svaret ovan bör du till exempel reparera API-version-parametern innan du skickar begäran igen.

<a name="authorization-header"></a>Authorization-huvud
--------------------

För alla API: er i denna referens måste du skicka Authorization-huvudet tillsammans med Bearer-token som hämtats från Azure Active Directory (Azure AD). Den här rubriken krävs för att få ett giltigt svar. om detta inte visas returneras ett `401 Unauthorized` fel. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
