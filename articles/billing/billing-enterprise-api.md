---
title: 'Azure Billing Enterprise-API: er | Microsoft Docs'
description: "Läs mer om Reporting API: erna som gör att företag Azure-kunder att dra förbrukningsdata programmässigt."
services: 
documentationcenter: 
author: anandedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: 62a69aeb7499a961f95739fb3836942b670c7320
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Översikt över Reporting API: er för Enterprise-kunder
API: er Reporting aktivera Enterprise Azure-kunder kan hämta programmässigt användnings- och faktureringsinformation till önskade verktyg för dataanalys. 

## <a name="enabling-data-access-to-the-api"></a>Aktivera dataåtkomst till API: et
* **Generera eller hämta API-nyckeln** - logga in på Enterprise portal och Följ guiden under Hjälp - Reporting API: er. Det första avsnittet under den här hjälpartikeln förklarar hur du generera eller hämta API-nyckeln för den angivna registreringen.
* **Skicka nycklar i API: et** -API: N nyckel måste överföras för varje anrop för autentisering och auktorisering. Egenskapen följande måste vara att HTTP-huvuden

|Begäran sidhuvud nyckel | Värde|
|-|-|
|Auktorisering| Ange värdet i det här formatet: **ägar {API_KEY}** <br/> Exempel: ägar eyr... 09|

## <a name="consumption-apis"></a>API: er för förbrukning
Swagger-slutpunkten är tillgänglig [här](https://consumption.azure.com/swagger/ui/index) för API: erna som beskrivs nedan som ska aktivera enkel introspection API och möjligheten att generera klient-SDK med hjälp av [AutoRest](https://github.com/Azure/AutoRest) eller [Swagger CodeGen](http://swagger.io/swagger-codegen/). Data från 1 maj 2014 är tillgängliga via den här API: T. 

* **Belastningsutjämning och sammanfattning** – [saldo och översikt över API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) ger information om saldon, nya inköp, avgifter för Azure Marketplace, justeringar och överförbrukning avgifter månatligen.

* **Användningsinformation** – [användning detalj API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) erbjuder en daglig sammanställning av förbrukade antalen och beräknade kostnader genom en registrering. Resultatet innehåller också information om instanser, mätare och avdelningar. API: et kan efterfrågas med fakturering punkt eller vid ett angivet datum för start- och slutdatum. 

* **Marketplace Store kostnad** – [Marketplace Store kostnad API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) returnerar användningsbaserad marketplace avgifter sammanställning per dag för fakturering tidsperioden eller start- och slutdatum (en gång avgifter inte ingår).

* **Prisdokument** – [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ger den tillämpliga hastigheten för varje mätaren för den angivna registrering och fakturering Period. 

## <a name="helper-apis"></a>Helper-API: er
 **Lista över fakturering punkter** – [fakturering punkter API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) returnerar en lista över fakturering punkter som har förbrukningsdata för den angivna registreringen i omvänd kronologisk ordning. Varje Period innehåller en egenskap som pekar på API-väg för de fyra datauppsättningarna - BalanceSummary, UsageDetails, Marketplace-debiteringar och Prisdokument.


## <a name="api-response-codes"></a>API-svarskoder  
|Svarets statuskod|Meddelande|Beskrivning|
|-|-|-|
|200| OKEJ|Inga fel|
|401| Behörighet saknas| API-nyckel hittades inte, ogiltig upphört att gälla osv.|
|404| Inte tillgänglig| Rapporten slutpunkten hittades inte|
|400| Felaktig begäran| Ogiltiga parametrar – datumintervall, EA siffror osv.|
|500| Serverfel| Unexoected fel vid bearbetning av begäran| 









