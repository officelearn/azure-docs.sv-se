---
title: API-överväganden | Azure Marketplace
description: 'Versions hantering, fel hantering och auktoriseringsfel vid användning av Marketplace-API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819733"
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
