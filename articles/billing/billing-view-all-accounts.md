---
title: Visa din faktureringsinformation konton i Azure-portalen | Microsoft Docs
description: Lär dig hur du visar dina fakturering konton i Azure-portalen.
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
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490224"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Visa fakturering konton i Azure-portalen  

Ett faktureringskonto skapas när du registrerar dig att använda Azure. Du använder ditt faktureringskonto för att hantera dina fakturor, betalningar och spåra kostnader. Du kan ha åtkomst till flera konton för fakturering. Exempelvis kan kanske du har registrerat dig för Azure för dina personliga projekt. Du kan också ha åtkomst till din organisations Enterprise-avtal eller Microsoft kundavtal. För var och en av dessa scenarier behöver du en separat faktureringskonto.

Azure-portalen stöder för närvarande följande typ av fakturering konton:

- **Microsoft Online Services-programmet**: Faktureringskonto för en Microsoft onlinetjänster-programmet skapas när du registrerar dig för Azure via Azure-webbplatsen. Till exempel när du registrerar dig för en [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/), [konto med betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som en [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise-avtal**: Faktureringskonto för Enterprise Agreement-kund skapas när organisationen registrerar en [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) att använda Azure.

- **Microsoft kundavtal**: Faktureringskonto för en Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att registrera ett Microsoft-kundavtal. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för en [konto med betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) eller uppgradera sina [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan ha något faktureringskonto för en Microsoft Customer Samt avtalet. Mer information finns i [Kom igång med ditt faktureringskonto för Microsoft kundavtal](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Omfång för fakturering konton
Ett scope är en nod i ett faktureringskonto som användare använder för att visa och hantera fakturering. Det är där användare hantera faktureringsinformation, betalningar, fakturor, och utföra allmänna kontohantering. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

|Scope  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar en enskild ägare (kontoadministratör) för en eller flera Azure-prenumerationer. En kontoadministratör har behörighet att utföra olika fakturering uppgifter som att skapa prenumerationer, visa fakturor eller ändra faktureringen för prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. Fakturan skapas i den här omfattningen. Den har en egen betalningsmetoder som används för att betala dess fakturan.|


### <a name="enterprise-agreement"></a>Enterprise-avtal

|Scope  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise Agreement-registrering. Fakturan skapas i den här omfattningen. Det är strukturerad med avdelningar och registreringskonton.  |
|Avdelning     |  Valfri gruppering av registreringskonton.      |
|Registreringskonto     |  Representerar en enskild ägare. Azure-prenumerationer som skapats under det här omfånget.  |


### <a name="microsoft-customer-agreement"></a>Microsofts kundavtal

|Scope  |Uppgifter  |
|---------|---------|
|Faktureringskonto     |   Representerar en kundavtal för flera Microsoft-produkter och tjänster. Det är strukturerad med hjälp av profiler för fakturering och faktura avsnitt.   |
|Fakturering profil     |  Representerar en faktura och dess betalningsmetoder. Fakturan skapas i den här omfattningen. Den kan ha flera faktura-avsnitt.      |
|Faktura-avsnitt     |   Representerar en grupp av kostnader i en faktura. Prenumerationer och andra inköp som är kopplade till det här omfånget.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Växla fakturering omfång i Azure portal


1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Välj **alla fakturering omfång** till vänster.

   ![Skärmbild som visar alla fakturering omfång](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Du kommer inte se **alla fakturering omfång** om du bara har åtkomst till ett omfång.

4. Välj ett omfång för att visa detaljer.



## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du startar [analysera kostnaderna](../cost-management/quick-acm-cost-analysis.md).