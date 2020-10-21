---
title: Alternativ för gruppering och filter i Azure Cost Management
description: Den här artikeln beskriver hur du använder alternativ för gruppering och filter i Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: ac9828ca61009eb3ee39412169b2b454b9ecbd00
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131878"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Alternativ för gruppering och filter i kostnadsanalys

Kostnadsanalys har många grupperings- och filtreringsalternativ. Den här artikeln hjälper dig att förstå när du ska använda dem.

Om du vill se en video om grupperings- och filtreringsalternativen kan du se videon [Cost Management reporting by dimensions and tags](https://www.youtube.com/watch?v=2Vx7V17zbmk) (Cost Management-rapportering efter dimensioner och taggar). Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Egenskaper för gruppering och filtrering

I följande tabell visas några av de vanligaste alternativen för gruppering och filtrering i kostnadsanalys och när du ska använda dem.

| Egenskap | När du ska använda detta | Anteckningar |
| --- | --- | --- |
| **Tillgänglighetszoner** | Bryt ned AWS-kostnader per tillgänglighetszon. | Gäller endast för AWS-omfång och -hanteringsgrupper. Azure-data inkluderar inte tillgänglighetszon och visas som **Ej tillämpligt**. |
| **Faktureringsperiod** | Bryt ned PAYG-kostnader efter den månad som de fakturerades (eller kommer att faktureras). | Använd **Faktureringsperiod** för att få en korrekt representation av fakturerade PAYG-avgifter. Inkludera 2 extra dagar före och efter faktureringsperioden om du filtrerar ned till ett anpassat datumintervall. Begränsning av de exakta datumen för faktureringsperioden stämmer inte överens med fakturan. Visar kostnader från alla fakturor under faktureringsperioden. Använd **Faktura-ID** för att filtrera ned till en speciell faktura. Endast tillgängligt för PAYG-prenumerationer eftersom EA och MCA faktureras per kalendermånad. EA/MCA-konton kan använda kalendermånader i datumväljaren eller månadsvis kornighet för att utföra samma mål. |
| **Kostnadstyp** | Dela upp användning, köp, återbetalning och outnyttjade reservationskostnader. | Köp och återbetalningar av reservationer är bara tillgängliga när du använder faktiska kostnader och inte när du använder amorterade kostnader. Oanvända reservationskostnader är bara tillgängliga när du tittar på amorterade kostnader. |
| **Avdelning** | Dela upp kostnader efter EA-avdelning. | Endast tillgängligt för EA- och hanteringsgrupper. PAYG-prenumerationer har ingen avdelning och visas som **Ej tillämpligt** eller **ej tilldelade**. |
| **Registreringskonto** | Dela upp kostnader efter EA-kontots ägare. | Endast tillgängligt för EA-faktureringskonton, avdelningar och hanteringsgrupper. PAYG-prenumerationer har inga EA-registreringskonton och visas som **Ej tillämpligt** eller **ej tilldelade**. |
| **Frekvens** | Bryt ned användningsbaserade, engångs- och återkommande kostnader. | |
| **Faktura-ID** | Dela upp kostnader per fakturerad faktura. | Ofakturerade avgifter har ännu inget faktura-ID och EA-kostnader inkluderar inte fakturainformation och visas som **Inte tillämpligt**.  |
| **Mätare** | Dela upp kostnader per användningsmätare. | Köp och Marketplace-användning visas som **Ej tillämpligt**. Se **Kostnadstyp** för att identifiera inköp och **Typ av utgivare** för att identifiera Marketplace-avgifter. |
| **Åtgärd** | Bryt ned AWS-kostnader per åtgärd. | Gäller endast för AWS-omfång och -hanteringsgrupper. Azure-data inkluderar inte åtgärd och visas som **Ej tillämpligt** – använd **Mätare** i stället. |
| **Prismodell** | Dela upp kostnader per begäran, reservation eller spotanvändning. | Köp visas som **OnDemand**. Om du ser **Inte tillämpligt**kan du gruppera efter **Reservation** för att avgöra om användningen är en reservation eller användning på begäran och **Kostnadstyp** för att identifiera köp.
| **Leverantör** | Dela upp kostnader per AWS och Azure. | Endast tillgängligt för hanteringsgrupper. |
| **Typ av utgivare** | Dela upp AWS-, Azure- och Marketplace-kostnader. |  |
| **Reservation** | Dela upp kostnader per reservation. | All användning eller alla inköp som inte är associerade med en reservation visas som **Inte tillämpligt**. Gruppera efter **Typ av utgivare** för att identifiera andra Azure-, AWS- eller Marketplace-köp. |
| **Resurs** | Dela upp kostnader per resurs. | Köp visas som **Inte tillämpligt** eftersom de används på ett EA-/PAYG-faktureringskonto eller en MCA-faktureringsprofilnivå och inte är associerade med en speciell resurs. Gruppera efter **Typ av utgivare** för att identifiera andra Azure-, AWS- eller Marketplace-köp. |
| **Resursgrupp** | Dela upp kostnader per resursgrupp. | Köp, klientresurser som inte är associerade med prenumerationer, prenumerationsresurser som inte har distribuerats till en resursgrupp och klassiska resurser som inte har någon resursgrupp visas som **övriga**, **klassiska tjänster**, **$system**eller **Inte tillämpligt**. |
| **Resurstyp** | Dela upp kostnader per resurstyp. | Köp och klassiska tjänster har ingen Azure Resource Manager-resurstyp och visas som **andra**, **klassiska tjänster**eller **Inte tillämpligt**. |
| **Resursplats** | Dela upp kostnader efter plats eller region. | Köp- och Marketplace-användning kan visas som **ej tilldelad**, **okänd**, **omappad**eller **Inte tillämpligt**. |
| **Tjänstnamn** eller **Mätarkategori** | Dela upp kostnad per Azure-tjänst. | Köp och Marketplace-användning visas som **Inte tillämpligt** eller **Otilldelad**. |
| **Tjänstnivå** eller **Mätarunderkategori** | Dela upp kostnaden efter Azure-användningsmätarens underklassificering. | Köp och Marketplace-användning visas som **Inte tillämpligt** eller **Otilldelad**. |
| **Prenumeration** | Dela upp kostnader per Azure-prenumeration och AWS-länkat konto. | Köp- och klientresurser kan visas som **Ej tillämpligt**. |
| **Tag** | Dela upp kostnader efter taggvärden för en speciell taggnyckel. | Taggar är inte tillgängliga för köp, klientresurser som inte är associerade med prenumerationer, prenumerationsresurser som inte har distribuerats till en resursgrupp eller klassiska resurser. Vissa tjänster innehåller inte taggar i användningsdata. Läs mer om [stöd för taggar för varje resurstyp](../../azure-resource-manager/management/tag-support.md). |

Mer information om villkor finns i [Understand the terms used in the Azure usage and charges file](../understand/understand-usage.md) (Förstå villkoren som används i filen för Azure-användning och avgifter).

## <a name="next-steps"></a>Nästa steg

- [Börja analysera kostnader](./quick-acm-cost-analysis.md).