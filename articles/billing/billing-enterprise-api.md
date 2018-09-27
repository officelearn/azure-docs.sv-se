---
title: 'Azure Billing Enterprise API: er | Microsoft Docs'
description: 'Läs mer om Reporting API: er som gör att Azure Enterprise-kunder att hämta förbrukningsdata programmässigt.'
services: ''
documentationcenter: ''
author: anandedwin
manager: aedwin
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: erikre
ms.openlocfilehash: 7cc5b0cb12e806b0946ce421e7ee6daa4de76afa
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47390604"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Översikt över Reporting API: er för företagskunder
API: erna Reporting aktivera Enterprise Azure-kunder att hämta programmässigt förbrukning och faktureringsinformation till önskade data analysis-verktyg. Företagskunder har registrerat en [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) med Azure för att göra den förhandlade monetära åtaganden och få åtkomst till anpassad prissättning för Azure-resurser.

> [!div class="nextstepaction"]
> [Hjälp till att förbättra faktureringsdokument för Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="enabling-data-access-to-the-api"></a>Aktivera åtkomst till API: et
* **Generera eller hämta API-nyckeln** -logga in till Enterprise-portalen och gå till rapporter > ladda ned användningsdata > API-åtkomstnyckel att generera eller hämta API-nyckeln.
* **Skicka nycklarna i API: et** -API-nyckel som ska skickas för varje anrop för autentisering och auktorisering. Följande egenskap måste vara att HTTP-huvuden

|Begära huvud-nyckel | Värde|
|-|-|
|Auktorisering| Ange värdet i det här formatet: **ägar {API_KEY}** <br/> Exempel: ägar eyr... 09| 

## <a name="consumption-apis"></a>Förbrukning API: er
En Swagger-slutpunkten är tillgänglig [här](https://consumption.azure.com/swagger/ui/index) för API: erna som beskrivs nedan som ska aktivera enkelt introspektion av API: et och möjligheten att generera klient-SDK med hjälp av [AutoRest](https://github.com/Azure/AutoRest) eller [Swagger CodeGen](http://swagger.io/swagger-codegen/). Data från och med 1 maj 2014 är tillgängliga via den här API: et. 

* **Saldo och sammanfattning** – [saldo och sammanfattning API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) erbjuder månatligen information om saldo, nya inköp, tjänstavgifter för Azure Marketplace, justeringar och överförbrukningskostnader.

* **Användningsinformation** – [användning detalj API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) erbjuder en daglig sammanställning av förbrukade antalen och uppskattade kostnader genom en registrering. Resultatet innehåller också information om instanser, mätare och avdelningar. API: et kan frågas genom faktureringsperiod eller genom att ett visst datum för start- och slutdatum. 

* **Marketplace-Store kostnad** – [Marketplace Store kostnad API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) returnerar en analys på detaljnivå för användarbaserad marketplace-avgifter per dag för den angivna faktureringsperiod eller start- och slutdatumen (en gång avgifter inte ingår).

* **Prisdokument** – [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ger det gälla priset för varje mätare för den angivna registrering och faktureringsperiod. 

## <a name="data-freshness"></a>Dataaktualitet
Etags returneras i svaret på alla ovanstående API. En ändring i Etag anger data har uppdaterats.  I efterföljande anrop till samma API: et med samma parametrar, skickar du hämtade Etag med nyckeln ”If-None-Match” i rubriken i HTTP-begäran. Svarets statuskod skulle vara ”NotModified” om data inte har uppdaterats ytterligare och inga data kommer att returneras. API: et Returnerar hela datauppsättningen för den nödvändiga perioden när det finns en etag-ändring.

## <a name="helper-apis"></a>Hjälp-API: er
 **Lista över fakturering perioder** – [Billing perioder API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) returnerar en lista med faktureringsperioder som har förbrukningsdata för den angivna registreringen i omvänd kronologisk ordning. Varje Period innehåller en egenskap som pekar på API-väg för fyra uppsättningar data - BalanceSummary, UsageDetails, Marketplace-avgifter och Prisdokument.


## <a name="api-response-codes"></a>API-svarskoder   
|Svarets statuskod|Meddelande|Beskrivning|
|-|-|-|
|200| Ok|Inget fel|
|401| Behörighet saknas| API-nyckel som inte finns, är ogiltig, upphört att gälla osv.|
|404| Ej tillgänglig| Rapportslutpunkt hittades inte|
|400| Felaktig begäran| Ogiltiga parametrar – datumintervall, EA siffror osv.|
|500| Serverfel| Unexoected fel vid bearbetning av begäran| 









