---
title: Välja mellan Azure Cost Management och Cloudyn
description: Den här artikeln hjälper dig att avgöra om Azure Cost Management eller Cloudyn passar bäst för dina kostnadshanteringsbehov.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684974"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Välja mellan Azure Cost Management och Cloudyn

Cloudyn dras tillbaka i slutet av 2020. Befintliga Cloudyn-funktioner integreras direkt på Azure-portalen då det är möjligt. Med undantag för CSP-kunder kan inga nya kunder registreras för närvarande. Support för den befintliga produkten finns kvar tills produkten har tagits ur bruk helt.

Microsoft har förvärvat Cloudyn och migrerar kostnadshanteringsfunktionerna från Cloudyn-portalen till att ingå i Azure. Om du vill använda de nya funktionerna loggar du in på Azure-portalen och går till [Cost Management och fakturering](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) i listan med Azure-tjänster. Jämfört med Cloudyn ger den inbyggda upplevelsen förbättrad prestanda och en förkortad datafördröjning med cirka åtta timmar.

Migreringen av nyckelfunktionerna Enterprise-avtal, Betala per användning och MSDN-erbjudanden till Azure Cost Management har slutförts. CSP-prenumerationer håller på att migreras till Azure Cost Management.

Om du har en erbjudandekategori som ännu inte har migrerats, bör du fortsätta att använda Cloudyn-portalen. Alla andra kan använda Azure Cost Management.

## <a name="recommended-services-by-offer"></a>Rekommenderade tjänster efter erbjudande

| Microsoft Azure-erbjudanden | Rekommenderad kostnadshanteringstjänst |
| --- | --- |
| Azure Enterprise-avtal | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Microsoft-kundavtal | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Microsoft-kundavtal som stöds av partner | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Tillgängliga Cost Management-funktioner

Några av följande funktioner är tillgängliga i Cloudyn, men alla är tillgängliga nu i Azure Cost Management.

- API:er
- Rekommendationer för Azure-kostnadsoptimering, inklusive men inte begränsat till:
    - Rekommendationer om optimal storlek och avstängning för Azure-instanser
    - Reservationsrekommendationer för Azure
- Budgetar
- Kostnadsanalys
- Exportera data till ett Azure-lagringskonto
- Lägre fördröjning
- Power BI-mallapp
- Stöd för resurstaggar
- Stöd för kostnadsanalys mellan moln för AWS

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Cost Management](../cost-management-billing-overview.md).