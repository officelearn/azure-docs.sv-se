---
title: Översikt över Azure-förbrukning API | Microsoft Docs
description: 'Lär dig hur API: er för Azure-förbrukning får du programmeringsbaserad åtkomst till kostnads- och användningsdata för dina Azure-resurser.'
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
ms.openlocfilehash: c37ff7e2f81b3dbca42193777287dae873768e24
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630508"
---
# <a name="azure-consumption-api-overview"></a>Azure-förbrukning API-översikt 

API: erna för Azure förbrukning får du programmeringsbaserad åtkomst till kostnads- och användningsdata för dina Azure-resurser. Dessa API: er stöd för närvarande endast för Enterprise Enrollments och Web Direct Subscriptions (med några få undantag). API: erna uppdateras kontinuerlig stöd för andra typer av Azure-prenumerationer.

Azure Consumption-API: er ger åtkomst till:
- Enterprise- och Web Direct-kunder 
    - Användningsinformation 
    - Marketplace-avgifter 
    - Rekommendationer för reservation 
    - Information om reservation 
    - Reservation sammanfattningar 
- Enterprise-kunder 
    - Prisdokument 
    - Budgetar 
    - Saldo 

## <a name="usage-details-api"></a>Användningsinformation API

Använd användning information om API för att hämta kostnad-och användningsdata för alla Azure 1 part resurser. Informationen är i form av den användningsposter för information som genereras för närvarande en gång per mätnings per resurs per dag. Informationen kan användas för att lägga till upp kostnader över alla resurser eller undersöka kostnader / användning på specifika resurser. 

API: et innehåller:

-   **Mäter nivå förbrukningsdata** – se data, inklusive användning kostnad mätaren avger kostnad, och vilka Azure-resurs som avgiften gäller. Alla användningsposter detalj mappas till en daglig bucket.
-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -trimma resultatet API: et till en mindre uppsättning detaljposter med hjälp av följande filter:
    - Användning-slutpunkt / användning start
    - Resursgrupp
    - Resursnamn
-   **Datasammanställning** -Använd OData ska användas uttryck sammanställd användningsinformation efter taggar eller filtrera egenskaper
-   **Användningen av olika erbjudandetyper** -användning detaljerad information är för närvarande tillgängligt för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Marketplace-avgifter API

Använd Marketplace avgifter API för att hämta kostnad-och användningsdata på alla resurser i Marketplace (Azure erbjudanden med 3 part). Dessa data kan användas för att lägga till upp kostnader över alla resurser i Marketplace eller undersöka kostnader / användning på specifika resurser. 

API: et innehåller:

-   **Mäter nivå förbrukningsdata** – se data, inklusive marketplace användning kostnad mätaren avger kostnad, och vilka resurser som avgiften gäller. Alla användningsposter detalj mappas till en daglig bucket.
-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -trimma resultatet API: et till en mindre uppsättning marketplace-poster med hjälp av följande filter:
    - Användning start / slut användning
    - Resursgrupp
    - Resursnamn
-   **Användningen av olika erbjudandetyper** -Marketplace-informationen är för närvarande tillgängligt för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för den [Marketplace-avgifter API](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Saldo API

Enterprise-kunder kan använda balanserar-API för att få en månatlig sammanfattning av information om saldo, nya inköp, Azure Marketplace-tjänstavgifter, justeringar och överförbrukningskostnader. Du kan hämta den här informationen för den aktuella faktureringsperioden eller valfri punkt i förflutna. Företag kan använda dessa data för att utföra en jämförelse med manuellt beräknade sammanfattning avgifter. Den här API: et tillhandahåller inte resursspecifika information och en aggregerad vy över kostnaderna. 

API: et innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Enterprise-kunder bara** API: et är endast tillgängliga EA-kunder. 
    - Kunder måste ha företagsadministratör behörighet att anropa detta API 

Mer information finns i den tekniska specifikationen för den [saldo API](https://docs.microsoft.com/rest/api/consumption/getbalancesbybillingaccount).

## <a name="budgets-api"></a>Budgetar API

Enterprise-kunder kan använda detta API för att skapa budgetar för kostnader och användning för resurser, resursgrupper eller faktureringsmätare. När informationen har fastställts kan aviseringar konfigureras för att meddela när en användardefinierad budget tröskelvärden överskrids. 

API: et innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Enterprise-kunder bara** -API: et är endast tillgängliga EA-kunder.
-   **Konfigurerbara meddelanden** -ange användare vill meddelas när budgeten utlöses.
-   **Användnings- eller budgetar för kostnader baserade** – skapa din budget baserat på förbrukning eller kostnader som krävs för ditt scenario.
-   **Filtrering** -filtrera din budget för att en mindre deluppsättning av resurser med hjälp av följande konfigurerbara filter
    - Resursgrupp
    - Resursnamn
    - Meter
-   **Konfigurerbara budget tidsperioderna** – ange hur ofta budgeten bör återställa och hur länge budgeten är giltig för.

Mer information finns i den tekniska specifikationen för den [budgetar API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Reservation API för rekommendationer

Använd detta API för att få rekommendationer för att köpa reserverade VM-instanser. Rekommendationer är avsedda att kunden kan du analysera förväntade kostnadsbesparingar och köpa mängder som anges. 

API: et innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -skräddarsy dina rekommendation resultat med hjälp av följande filter:
    - Omfång
    - Lookback period
-   **Information för olika erbjudandetyper** -reservationsinformation är för närvarande tillgängligt för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för den [Reservation rekommendationer API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Information om reservation API

Använd API: et Reservation information att visa information på tidigare har köpt VM reservationer, till exempel hur mycket förbrukningen har reserverats jämfört med hur mycket används faktiskt. Du kan se data på en nivå detalj per virtuell dator. 

API: et innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -trimma resultatet API till en mindre uppsättning reservationer med hjälp av följande filter:
    - Datumintervall
-   **Information för olika erbjudandetyper** -reservationsinformation är för närvarande tillgängligt för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för den [Reservation information API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Reservation sammanfattningar API

Använd den här API: et för att se sammanställd information om tidigare har köpt VM reservationer, till exempel hur mycket förbrukningen har reserverats jämfört med hur mycket faktiskt som används i samlingen. 

API: et innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** -skräddarsy dina resultat när du använder den dagliga grain med följande filter:
    - Datum för användning
-   **Information för olika erbjudandetyper** -reservationsinformation är för närvarande tillgängligt för Enterprise- och Web Direct-kunder.
-   **Varje dag eller månad aggregeringar** – anropare kan ange om de vill sina reservation sammanfattningsdata i varje dag eller månad grain.

Mer information finns i den tekniska specifikationen för den [Reservation sammanfattningar API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Price Sheet API
Företagskund kan använda detta API för att hämta sina anpassad prissättning för alla mätare. Företag kan använda detta i kombination med information och marknadsplatser användning användningsinformation för kostnadsberäkningar med användnings-och marketplace. 

API: et innehåller:

-   **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) eller [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Enterprise-kunder bara** -API: et är endast tillgängliga EA-kunder. Web-Direct-kunder bör använda RateCard-API: et för att få priser. 

Mer information finns i den tekniska specifikationen för den [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenarier

Här följer några scenarier som görs möjligt via förbrukning av API: er:

-   **Fakturera avstämningen** -gjorde Microsoft debiterar mig rätt belopp?  Vad är min faktura och kan jag beräkna det mig?
-   **Mellan avgifter** – nu när jag vet hur mycket jag debiteras, som i min organisation behöver betala?
-   **Kostnad optimering** – jag vet hur mycket jag har debiterats... hur kan jag få ut mer av de pengar som jag tillbringar på Azure?
-   **Kostnadsuppföljning** -jag vill se hur mycket jag är utgifter och använda Azure över tid. Vilka är trenderna? Hur kan jag göra bättre?
-   **Azure-kostnader under månaden** – hur mycket är min aktuella månaden är spendera hittills? Behöver jag göra eventuella justeringar av i Mina utgifter och/eller användning av Azure? När under månaden ska jag använda Azure mest?
-   **Ställa in aviseringar** – jag vill ställa in Resursbaserad förbrukning eller monetära-baserade aviseringar baserat på en budget.

## <a name="next-steps"></a>Nästa steg

- Information om hur du programmässigt få insikt i din Azure-användning med Azure Billing API: er finns i [Azure Billing API-översikt](billing-usage-rate-card-overview.md).



