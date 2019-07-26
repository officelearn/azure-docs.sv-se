---
title: Översikt över Azure förbruknings-API | Microsoft Docs
description: 'Lär dig hur Azures förbruknings-API: er ger dig program mässig åtkomst till kostnader och användnings data för dina Azure-resurser.'
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 2a2922daef5e75af41252119f9a467fae976bcda
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443260"
---
# <a name="azure-consumption-api-overview"></a>Översikt över Azure förbruknings-API 

Med Azure Consumption-API:erna får du programmatisk åtkomst till kostnads- och användningsdata för dina Azure-resurser. Dessa API: er stöder för närvarande endast företags registreringar och webb direkt prenumerationer (med några få undantag). API: erna uppdateras kontinuerligt för att stödja andra typer av Azure-prenumerationer.

Azure Consumption-API:er ger åtkomst till:
- Enterprise- och Web Direct-kunder 
    - Användningsinformation 
    - Marketplace-debiteringar 
    - Reservationsrekommendationer 
    - Information om reservation 
    - Reservationssammanfattningar 
- Endast Enterprise-kunder 
    - Prisdokument 
    - Budgetar 
    - Saldon 

## <a name="usage-details-api"></a>API för användnings information

Använd API: et för användnings information för att få information om avgifter och användning för alla Azures 1: e parts resurser. Information anges i form av användnings detalj poster som för närvarande genereras en gång per mätning per resurs per dag. Information kan användas för att summera kostnaderna för alla resurser eller undersöka kostnader/användning för en eller flera resurser. 

API: et innehåller:

-   **Förbruknings data på mätar nivå** – se data inklusive användnings kostnad, den mätare som ger avgiften och vilken Azure-resurs som avgiften avser. Alla användnings detalj poster mappar till en daglig Bucket.
-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** – trimma API-resultatet till en mindre uppsättning användnings detalj poster med följande filter:
    - Användnings slut/användnings start
    - Resursgrupp
    - Resursnamn
-   **Data agg regering** – Använd OData för att tillämpa uttryck för att aggregera användnings information med taggar eller filter egenskaper
-   **Användning för olika erbjudande typer** – information om användnings information är för närvarande tillgänglig för företags-och webb direkt kunder.

Mer information finns i teknisk specifikation för [API: et för användnings information](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API för Marketplace-debitering

Använd API för Marketplace-debitering för att hämta information om kostnad och användning på alla Marketplace-resurser (Azure-erbjudanden från tredje part). Dessa data kan användas för att lägga till kostnader i alla Marketplace-resurser eller undersöka kostnader/användning för en eller flera resurser. 

API: et innehåller:

-   **Förbruknings data för mätnings nivå** – se data inklusive kostnad för Marketplace, mätning av avgiften och vilken resurs som avgiften avser. Alla användnings detalj poster mappar till en daglig Bucket.
-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** – trimma API-resultatet till en mindre uppsättning Marketplace-poster med följande filter:
    - Användnings start/användnings slut
    - Resursgrupp
    - Resursnamn
-   **Användning för olika erbjudande typer** – information om Marketplace är för närvarande tillgänglig för företags-och webb direkt kunder.

Mer information finns i teknisk specifikation för [Marketplace charges-API: et](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Balanserar API

Företags kunder kan använda balans-API: et för att få en månatlig Sammanfattning av information om saldon, nya inköp, Azure Marketplace service-kostnader, justeringar och kostnader för överanvändning. Du kan hämta den här informationen för den aktuella fakturerings perioden eller en period tidigare. Företag kan använda dessa data för att utföra en jämförelse med manuellt beräknade sammanfattnings avgifter. Detta API tillhandahåller inte resursinformation och en sammanställd vy över kostnader. 

API: et innehåller:

-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Endast företags kunder** Detta API är endast tillgängligt för EA-kunder. 
    - Kunder måste ha företags administratörs behörighet för att anropa detta API 

Mer information finns i den tekniska specifikationen för Balancer- [API: et](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Budget-API

Företags kunder kan använda detta API för att skapa kostnads-eller användnings budgetar för resurser, resurs grupper eller fakturerings mätare. När den här informationen har fastställts kan avisering konfigureras för att meddela när användardefinierade budget trösklar överskrids. 

API: et innehåller:

-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Endast företags kunder** – detta API är endast tillgängligt för EA-kunder.
-   **Konfigurerbara meddelanden** – ange användare som ska meddelas när budgeten är utlöst.
-   **Användnings-eller kostnadsbaserade budgetar** – skapa din budget baserat på förbrukning eller kostnad som du behöver i ditt scenario.
-   **Filtrering** – filtrera din budget till en mindre delmängd resurser med följande konfigurerbara filter
    - Resursgrupp
    - Resursnamn
    - Mätare
-   **Konfigurerbara budget tids perioder** – ange hur ofta budgeten ska återställas och hur länge budgeten är giltig för.

Mer information finns i teknisk specifikation för [budget-API: et](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>API för reservations rekommendationer

Använd detta API för att få rekommendationer för att köpa reserverade VM-instanser. Rekommendationerna är utformade för att kunderna ska kunna analysera förväntade kostnads besparingar och inköps belopp. 

API: et innehåller:

-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrera** – skräddarsy dina rekommendations resultat med följande filter:
    - Omfång
    - Lookback period
-   **Reservations information för olika erbjudande typer** – reservations information är för närvarande tillgänglig för företags-och webb direkt kunder.

Mer information finns i teknisk specifikation för [API: et för reservations rekommendationer](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API för reservations information

Använd API för reservations information för att se information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukning som har reserver ATS jämfört med hur mycket som faktiskt används. Du kan se information på nivån per virtuell dator. 

API: et innehåller:

-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrering** – trimma API-resultatet till en mindre uppsättning reservationer med följande filter:
    - Datumintervall
-   **Reservations information för olika erbjudande typer** – reservations information är för närvarande tillgänglig för företags-och webb direkt kunder.

Mer information finns i teknisk specifikation för [API: et för reservations information](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>API för reservations Sammanfattning

Använd det här API: et för att se sammanställd information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukning som har reserver ATS jämfört med hur mycket som faktiskt används i mängden. 

API: et innehåller:

-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Filtrera** – skräddarsy resultaten när du använder den dagliga kornigheten med följande filter:
    - Användningsdatum
-   **Reservations information för olika erbjudande typer** – reservations information är för närvarande tillgänglig för företags-och webb direkt kunder.
-   **Dagliga eller månatliga agg regeringar** – anropare kan ange om de vill ha sina reservations sammanfattnings data i varje dag eller månads kornig het.

Mer information finns i teknisk specifikation för [API: et för reservations Sammanfattning](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Pris dokuments-API
Företags kunder kan använda detta API för att hämta egna priser för alla mätare. Företag kan använda detta i kombination med användnings information och information om Marketplace-användning för att utföra kostnads beräkningar med hjälp av data för användning och Marketplace. 

API: et innehåller:

-   **Azure-rollbaserade Access Control** -konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) -eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) -cmdletar för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration. 
-   **Endast företags kunder** – detta API är endast tillgängligt för EA-kunder. Webb direkt kunder bör använda RateCard-API: et för att få priser. 

Mer information finns i teknisk specifikation för [pris dokumentets API](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenarier

Här följer några av de scenarier som möjliggörs via förbruknings-API: erna:

-   **Faktura avstämning** – debiterade Microsoft mig rätt belopp?  Vad är faktura och kan jag beräkna det själv?
-   **Mellan debiteringar** – nu vet jag hur mycket jag debiteras, som i min organisation behöver betala?
-   **Kostnads optimering** – jag vet hur mycket jag har debiterat... Hur kan jag få ut mer av pengar jag kostar på Azure?
-   **Kostnads uppföljning** – jag vill se hur mycket jag kostar och använder Azure över tid. Vilka är trenderna? Hur kan jag göra bättre?
-   Kostar **Azure att spendera under månaden** – hur mycket är min nuvarande månads spenderade datum? Behöver jag göra några justeringar i mina utgifter och/eller användningen av Azure? När jag under månaden använder jag Azure?
-   **Konfigurera aviseringar** – jag vill ställa in resursbaserade konsumtion eller monetär avisering baserat på en budget.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder API:er för Azure-fakturering för att program mera inblick i din Azure-användning finns i [Översikt över Azure fakturerings-API](billing-usage-rate-card-overview.md).



