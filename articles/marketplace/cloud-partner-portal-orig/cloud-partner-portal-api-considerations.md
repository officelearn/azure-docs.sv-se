---
title: API-överväganden | Azure Marketplace
description: Versionshantering, felhantering och auktoriseringsproblem när du använder marketplace-API:erna.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256339"
---
# <a name="api-considerations"></a>API-överväganden

<a name="api-versioning"></a>API-versionshantering
--------------

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

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
