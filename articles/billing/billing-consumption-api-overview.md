---
title: Översikt över Azure-förbrukningen API | Microsoft Docs
description: 'Lär dig hur Azure förbrukning API: er ger Programmeringsåtkomst till kostnads- och användningsdata för Azure-resurser.'
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 69e148089f48ebe04faceff87b08eb3f7127f8a3
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248375"
---
# <a name="azure-consumption-api-overview"></a>Översikt över Azure-förbrukningen API 

API: er för förbrukning i Azure ger du Programmeringsåtkomst till kostnads- och användningsdata för Azure-resurser. Dessa API: er stöds för närvarande endast Enterprise registreringar och Web direkt prenumerationer (med några få undantag). API: erna uppdateras kontinuerligt för att stödja andra typer av Azure-prenumerationer.

Azure förbrukning API: er ger åtkomst till:
- Enterprise- och direkt kunder 
    - Användningsinformation 
    - Marketplace-debiteringar 
    - Rekommendationer för reservation 
    - Reservation information 
    - Reservation sammanfattningar 
- Enterprise-kunder 
    - Prisdokument 
    - Budgetar 
    - Saldon 

## <a name="usage-details-api"></a>Användningsinformation API

Använd användning information API för att hämta kostnad och användningsdata för alla Azure 1 part resurser. Informationen är i form av information om poster som för närvarande orsakat en gång per mätaren per resurs per dag. Informationen kan användas för att lägga till upp kostnaderna för alla resurser eller undersöka kostnader / användning på specifika resurser. 

Detta API innehåller:

-   **Läsa nivå förbrukningsdata** – se data, inklusive användning kostnad, mätaren avger kostnad, och vilka Azure-resurs som tillägget gäller. Alla detaljer användningsposter mappar till en daglig bucket.
-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -Trim resultatet API till en mindre uppsättning användningsposter med hjälp av följande filter:
    - Användning end / användning start
    - Resursgrupp
    - Resursnamn
-   **Datasammanställning** -Använd OData att använda uttryck i sammanställd användningsinformation efter taggar eller filtrera egenskaper
-   **Syntax för olika erbjudandetyper** -information om användning är tillgängliga för Web direkt och Enterprise-kunder.

Mer information finns i den tekniska specifikationen för den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Marketplace-debiteringar API

Använd Marketplace avgifter API för att hämta data för kostnad och användning på alla Marketplace-resurser (Azure 3 part erbjudanden). Dessa data kan användas för att lägga till kostnader för alla Marketplace-resurser eller undersöka kostnader / användning på specifika resurser. 

Detta API innehåller:

-   **Läsa nivå förbrukningsdata** – se data, inklusive marketplace användning kostnad, mätaren avger kostnad, och vilken resurs som tillägget gäller. Alla detaljer användningsposter mappar till en daglig bucket.
-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -Trim resultatet API till en mindre uppsättning marketplace-poster med hjälp av följande filter:
    - Användning start / användning slut
    - Resursgrupp
    - Resursnamn
-   **Syntax för olika erbjudandetyper** -Marketplace-information är tillgänglig för kunder med Enterprise och Web direkt.

Mer information finns i den tekniska specifikationen för den [Marketplace avgifter API](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Saldon API

Enterprise-kunder kan använda balanserar-API för att hämta ett månatligt för information om saldon, nya inköp, Azure Marketplace serviceavgifter, justeringar och överförbrukning avgifter. Du kan hämta den här informationen för den aktuella faktureringsperioden eller föregående period. Företag kan använda informationen för att utföra en jämförelse med manuellt beräknade sammanfattning avgifter. Detta API ger inte resurs-specifik information och en aggregerad vy över kostnaderna. 

Detta API innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Bara Enterprise-kunder** detta API är endast tillgängliga EA-kunder. 
    - Kunderna måste ha företagsadministratör behörighet att anropa denna API 

Mer information finns i den tekniska specifikationen för den [saldon API](https://docs.microsoft.com/rest/api/consumption/getbalancesbybillingaccount).

## <a name="budgets-api"></a>Budgetar API

Enterprise-kunder kan använda detta API för att skapa kostnaden eller användning budgetar för resurser, resursgrupper eller fakturering mätare. När informationen har fastställts kan kan aviseringar konfigureras för att meddela när användardefinierade budget tröskelvärden överskrids. 

Detta API innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Bara Enterprise-kunder** -detta API är endast tillgängliga EA-kunder.
-   **Konfigurera meddelanden** -ange användare som ska meddelas när budget utlöses.
-   **Användnings- eller kostnaden baserat budgetar** -skapa din budget baserat på förbrukningen eller kostnaden som krävs av ditt scenario.
-   **Filtrering** -filtrera din budget till en mindre deluppsättning av resurser med hjälp av följande konfigurerbara filter
    - Resursgrupp
    - Resursnamn
    - Meter
-   **Konfigurerbara budget tidsperioder** -ange hur ofta budget ska återställa och hur länge budget är giltigt för.

Mer information finns i den tekniska specifikationen för den [budgetar API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Rekommendationer för reservation API

Använd detta API för att få rekommendationer om köp av VM reserverade instanser. Rekommendationer är avsedda att möjligheten för kunder att analysera förväntade besparingar och inköp. 

Detta API innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -skräddarsy rekommendation resultatet med hjälp av följande filter:
    - Omfång
    - Lookback period
-   **Reservation info för olika erbjudandetyper** -reservationsinformation är tillgänglig för kunder med Enterprise och Web direkt.

Mer information finns i den tekniska specifikationen för den [Reservation rekommendationer API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Reservation information API

Använd API: et Reservation information att visa information på tidigare har köpt VM reservationer, till exempel hur mycket förbrukning har reserverats jämfört med hur mycket används faktiskt. Du kan se data på en nivå detaljer per virtuell dator. 

Detta API innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -Trim resultatet API till en mindre uppsättning reservationer för följande filter:
    - Datumintervall
-   **Reservation info för olika erbjudandetyper** -reservationsinformation är tillgänglig för kunder med Enterprise och Web direkt.

Mer information finns i den tekniska specifikationen för den [Reservation information API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Reservation sammanfattningar API

Använd detta API om du vill visa samlar in information om tidigare har köpt VM reservationer, till exempel hur mycket förbrukning har reserverats jämfört med hur mycket verkligen används i sammansatt. 

Detta API innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -skräddarsy dina resultat när du använder i dag med följande filter:
    - Datum för användning
-   **Reservation info för olika erbjudandetyper** -reservationsinformation är tillgänglig för kunder med Enterprise och Web direkt.
-   **Varje dag eller varje månad aggregeringar** – anropare kan ange om de vill sina sammanfattningsdata för reservation i grain dagar eller månader.

Mer information finns i den tekniska specifikationen för den [Reservation sammanfattningar API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Price Sheet API
Enterprise-kunder kan använda detta API för att hämta sina egna priser för alla mätare. Företag kan använda den i kombination med information om användning av information och marknadsplatser användning för att utföra beräkningarna använder användnings-och marketplace. 

Detta API innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Bara Enterprise-kunder** -detta API är endast tillgängliga EA-kunder. Web direkt kunder bör använda RateCard-API för att få priser. 

Mer information finns i den tekniska specifikationen för den [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenarier

Här följer några scenarier som är möjliga via förbrukning API: er:

-   **Faktura avstämning** -har Microsoft debiterar mig rätt belopp?  Vad är min faktura och kan jag beräkna den själv?
-   **Mellan avgifter** – nu när jag vet hur mycket jag är att du debiteras, som i min organisations behöver betala?
-   **Kostnad optimering** -jag vet hur mycket jag har debiterats... hur kan jag få ut mer av pengar jag utgifter i Azure?
-   **Kostnadsuppföljning** -jag vill se hur mycket jag utgifter och använder Azure över tid. Vad är trenderna? Hur kan jag göra bättre?
-   **Azure utgifter under månaden** – hur mycket är min aktuella månaden har tillbringar hittills? Behöver jag göra eventuella justeringar i min utgifter och/eller användning av Azure? När under månaden kan jag använda Azure mest?
-   **Ställa in aviseringar** - jag vill ställa in resursbaserade förbrukning eller monetära-baserade aviseringar baserat på en budget.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder Azure Billing API: er att genom programmering få bättre överblick på din användning av Azure finns [fakturering API översikt över Azure](billing-usage-rate-card-overview.md).



