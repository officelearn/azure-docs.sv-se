---
title: API-överväganden | Azure Marketplace
description: 'Versions hantering, fel hantering och auktoriseringsfel vid användning av Marketplace-API: er.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256339"
---
# <a name="api-considerations"></a>API-överväganden

<a name="api-versioning"></a>API-versioner
--------------

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Det kan finnas flera versioner av API: et som är tillgängliga på samma gång. Klienterna måste ange vilken version de vill anropa användningen genom att ange `api-version` parametern som en del av frågesträngen.

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

För alla API: er i denna referens måste du skicka Authorization-huvudet tillsammans med Bearer-token som hämtats från Azure Active Directory (Azure AD). Den här rubriken krävs för att få ett giltigt svar. om detta inte finns returneras `401 Unauthorized` ett fel. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
