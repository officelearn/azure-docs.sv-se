---
title: 'API: er för Azure-fakturerings företag | Microsoft Docs'
description: 'Lär dig mer om rapporterings-API: er som gör det möjligt för företags kunder att hämta förbruknings data program mässigt.'
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: banders
ms.openlocfilehash: f706ad86493981d5b38248ec209a7c8b936f6817
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443216"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Översikt över rapporterings-API: er för företags kunder
Med rapporterings-API: erna kan Enterprise Azure-kunder program mässigt Hämta förbruknings-och fakturerings data till önskade data analys verktyg. Företags kunder har signerat ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) med Azure för att göra förhandlade betalnings åtaganden och få till gång till anpassad prissättning för Azure-resurser.

## <a name="enabling-data-access-to-the-api"></a>Aktivera data åtkomst till API: et
* **Generera eller hämta API-nyckeln** – logga in på Enterprise Portal och gå till rapporter > hämta användning > API-åtkomst nyckel för att generera eller hämta API-nyckeln.
* **Skicka nycklar i API** – API-nyckeln måste skickas för varje anrop för autentisering och auktorisering. Följande egenskap måste vara till HTTP-huvudena

|Nyckel för begär ande huvud | Value|
|-|-|
|Authorization| Ange värdet i det här formatet: **Bearer {api_key}** <br/> Exempel: Bearer-Eyr.... 09| 

## <a name="consumption-apis"></a>Förbruknings-API: er
En Swagger-slutpunkt finns [här](https://consumption.azure.com/swagger/ui/index) för de API: er som beskrivs nedan, vilket bör möjliggöra enkel introspektionsfunktionerna för API: et och möjligheten att [](https://github.com/Azure/AutoRest) generera klient-SDK: er med hjälp av AutoRest-eller [Swagger-CodeGen](https://swagger.io/swagger-codegen/). Data från den 1 maj 2014 är tillgängliga via det här API: et. 

* **Saldo och sammanfattning** – [API: t för saldo och sammanfattning](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) erbjuder en månatlig Sammanfattning av information om saldon, nya inköp, Azure Marketplace service-kostnader, justeringar och kostnader för överanvändning.

* **Användnings information** – [API för användnings information](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) erbjuder en daglig analys av förbrukade kvantiteter och beräknade avgifter per registrering. Resultatet innehåller också information om instanser, mätare och avdelningar. API: et kan frågas efter fakturerings period eller av ett angivet start-och slutdatum. 

* **Marketplace Store** -avgift – [Marketplace Store avgifts-API: et](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) returnerar den användnings marknads kostnads fördelningen per dag för den angivna fakturerings perioden eller start-och slutdatum (en tids avgift ingår inte).

* **Pris dokument** – [pris dokumentets API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ger relevant hastighet för varje mätare för den aktuella registreringen och fakturerings perioden.

* **Reserverad instans information** – API för reserverad [instans användning](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) returnerar användningen av reserverade instans inköp. API för reserverad [instans avgift](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) visar de fakturerings transaktioner som gjorts. 

## <a name="data-freshness"></a>Dataaktualitet
ETags kommer att returneras i svaret på alla ovanstående API. En ändring i etag visar att data har uppdaterats.  I efterföljande anrop till samma API med samma parametrar skickar du den hämtade etag-filen med nyckeln "If-None-Match" i rubriken för http-begäran. Svars status koden skulle vara "NotModified" om data inte har uppdaterats längre och inga data returneras. API: et returnerar den fullständiga data uppsättningen för den begärda perioden när en etag-ändring görs.

## <a name="helper-apis"></a>Hjälp program-API: er
 **Lista över fakturerings perioder** – [API för fakturerings perioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) returnerar en lista med fakturerings perioder som har förbruknings data för den angivna registreringen i omvänd kronologisk ordning. Varje period innehåller en egenskap som pekar på API-vägen för de fyra uppsättningarna med data-BalanceSummary, UsageDetails, Marketplace-avgifter och pris dokument.


## <a name="api-response-codes"></a>API-svars koder   
|Svars status kod|Message|Beskrivning|
|-|-|-|
|200| Ok|Inget fel|
|401| Behörighet saknas| Det gick inte att hitta API-nyckeln, ogiltig, utgångs datum osv.|
|404| Ej tillgänglig| Rapport slut punkten hittades inte|
|400| Felaktig begäran| Ogiltiga parametrar – datum intervall, EA-nummer osv.|
|500| Serverfel| Ett oväntat fel vid bearbetning av begäran| 









