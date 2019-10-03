---
title: Visa dina faktureringskonton i Azure-portalen | Microsoft Docs
description: Lär dig hur du visar dina faktureringskonton i Azure-portalen.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6df787dbc06f7ee56ba9a26c8382396bf3b6e74a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719598"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Visa faktureringskonton i Azure-portalen  

Ett faktureringskonto skapas när du registrerar dig för att använda Azure. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Du kan även ha åtkomst via organisationens Enterprise-avtal eller Microsoft-kundavtal. För var och en av dessa scenarier skulle du ha ett separat faktureringskonto.

Azure-portalen stöder för närvarande följande typer av faktureringskonton:

- **Microsoft Online Services-programmet**: Ett faktureringskonto för ett Microsoft Online Services-program skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise-avtal**: Ett faktureringskonto för ett Enterprise-avtal skapas när din organisation tecknar ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) för att använda Azure.

- **Microsoft-kundavtal**: Ett faktureringskonto för ett Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att teckna ett Microsoft-kundavtal. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller uppgraderar sitt [kostnadsfria Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan även ha ett faktureringskonto för ett Microsoft-kundavtal. Mer information finns i artikeln om att [komma igång med ditt faktureringskonto för Microsoft-kundavtal](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Omfång för faktureringskonton
Ett omfång är en nod i ett faktureringskonto som användarna använder för att visa och hantera fakturering. Det är där som användare hanterar faktureringsdata, betalningar och fakturor samt genomför allmän kontohantering. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar en enskild ägare (kontoadministratör) för en eller flera Azure-prenumerationer. En kontoadministratör har behörighet att utföra olika faktureringsuppgifter, till exempel skapa prenumerationer, visa fakturor eller ändra faktureringen för prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. Fakturan genereras i detta omfång. Den har sina egna betalningsmetoder som används för att betala fakturan.|


### <a name="enterprise-agreement"></a>Enterprise-avtal

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise-avtalsregistrering. Fakturan genereras i detta omfång. Det struktureras med hjälp av avdelningar och registreringskonton.  |
|Avdelning     |  Valfri gruppering av registreringskonton.      |
|Registreringskonto     |  Representerar en enskild kontoägare. Azure-prenumerationer skapas under detta omfång.  |


### <a name="microsoft-customer-agreement"></a>Microsoft-kundavtal

|Omfång  |Uppgifter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett kundavtal för flera Microsoft-produkter och -tjänster. Det struktureras med hjälp av faktureringsprofiler och fakturaavsnitt.   |
|Faktureringsprofil     |  Representerar en faktura och dess betalningsmetod. Fakturan genereras i detta omfång. Den kan ha flera fakturaavsnitt.      |
|Fakturaavsnitt     |   Representerar en grupp med kostnader i en faktura. Prenumerationer och andra inköp är kopplade till det här omfånget.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Byta faktureringsomfång i Azure-portalen


1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Välj **Alla faktureringsomfång** på vänster sida.

   ![Skärmbild som visar alla faktureringsomfång](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Du kommer inte att se **Alla faktureringsomfång** om du bara har åtkomst till ett enda omfång.

4. Välj ett omfång för att visa information.



## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du börjar [analysera dina kostnader](../cost-management/quick-acm-cost-analysis.md).