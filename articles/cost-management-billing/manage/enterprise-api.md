---
title: API:er för Azure-fakturering för Enterprise-kunder | Microsoft Docs
description: Lär dig mer om rapporterings-API:erna som gör det möjligt för Enterprise-kunder att hämta förbrukningsdata via programmering.
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 513dac3a1cdcefa7a49116ea02af5410265af3ec
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992002"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Översikt över rapporterings-API:er för Enterprise-kunder
Med rapporterings-API:erna kan Enterprise-kunder hämta förbruknings- och faktureringsdata till önskade dataanalysverktyg. Enterprise-kunder har signerat [ett Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) med Azure om särskilda betalningsåtaganden som berättigar till anpassade priser för Azure-resurser.

## <a name="enabling-data-access-to-the-api"></a>Aktivera dataåtkomst till API:et
* **Generera eller hämta API-nyckeln** – Logga in på Enterprise Portal och gå till Rapporter > Hämta användning > API-åtkomstnyckel för att generera eller hämta API-nyckeln.
* **Skicka nycklar i API:et** – API-nyckeln måste skickas för varje anrop för autentisering och auktorisering. Följande egenskap krävs med HTTP-huvudena

|Nyckel för begärandehuvud | Värde|
|-|-|
|Auktorisering| Ange värdet i det här formatet: **bearer {API_KEY}** <br/> Exempel: bearer eyr....09|

## <a name="consumption-apis"></a>Förbruknings-API:er
En Swagger-slutpunkt [finns här](https://consumption.azure.com/swagger/ui/index) för API:erna som beskrivs nedan och bör underlätta introspektion av API:et och möjligheten att generera klient-SDK:er med hjälp av [AutoRest](https://github.com/Azure/AutoRest) eller [Swagger CodeGen](https://swagger.io/swagger-codegen/). Data från den 1 maj 2014 är tillgängliga via det här API:et.

* **Saldo och sammanfattning** – [API:et för saldon och sammanfattningar](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) ger tillgång till en månatlig sammanställning av information om saldon, nya inköp, kostnader för Azure Marketplace-tjänster, justeringar och kostnader för överförbrukning.

* **Användningsinformation** – [API:et för användningsinformation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) returnerar en daglig uppdelning av förbrukade kvantiteter och beräknade avgifter per registrering. Resultatet innehåller också information om instanser, mätare och avdelningar. Du kan köra frågor mot API:et baserat på faktureringsperiod eller start- och slutdatum.

* **Kostnader för Marketplace Store** – [API:et för kostnader för Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) returnerar en uppdelning av de användningsbaserade kostnaderna för Marketplace per dag för den angivna faktureringsperioden eller start- och slutdatum (engångsavgifter är inte inkluderade).

* **Prisdokument** – [API:et för prisdokument](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) returnerar relevant pris för respektive mätare för den aktuella registreringen och faktureringsperioden.

* **Information om reserverade instanser** – [API:et för användning av reserverade instanser](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) returnerar användningsdata om köpta reserverade instanser. [API:et för kostnader för reserverade instanser](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) returnerar genomförda betalningstransaktioner.

## <a name="data-freshness"></a>Dataaktualitet
ETags returneras i svaret för alla ovanstående API:er. En ändring i Etag indikerar att data har uppdaterats.  I efterföljande anrop till samma API med samma parametrar skickar du Etag-filen som hämtats med nyckeln ”If-None-Match” i huvudet för HTTP-begäran. Statuskoden för svaret är ”NotModified” om data inte har uppdaterats ytterligare och inga data returneras. API:et returnerar den fullständiga datauppsättningen för den begärda perioden när en Etag-ändring görs.

## <a name="helper-apis"></a>API:er för hjälpkomponenter
 **Lista över faktureringsperioder** – [API:et för faktureringsperioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) returnerar en lista över faktureringsperioder som innehåller förbrukningsdata för den angivna registreringen i omvänd kronologisk ordning. Varje period innehåller en egenskap som pekar på API-vägen för de fyra uppsättningarna med data – BalanceSummary, UsageDetails, MarketplaceCharges och PriceSheet.


## <a name="api-response-codes"></a>API-svarskoder   
|Statuskod för svar|Meddelande|Beskrivning|
|-|-|-|
|200| OK|Inget fel|
|401| Behörighet saknas| Det gick inte att hitta API-nyckeln – ogiltig, har gått ut eller liknande|
|404| Inte tillgänglig| Det gick inte att hitta rapportslutpunkten|
|400| Felaktig begäran| Ogiltiga parametrar – datumintervall, EA-nummer osv.|
|500| Serverfel| Ett oväntat fel inträffade när begäran bearbetades|
