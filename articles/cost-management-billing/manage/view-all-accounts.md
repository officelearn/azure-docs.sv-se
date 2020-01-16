---
title: Visa dina faktureringskonton i Azure-portalen | Microsoft Docs
description: Lär dig hur du visar dina faktureringskonton i Azure-portalen.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994134"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Fakturerings konton och scope i Azure Portal

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Du kan även ha åtkomst via organisationens Enterprise-avtal eller Microsoft-kundavtal. För var och en av dessa scenarier skulle du ha ett separat faktureringskonto.

Azure Portal stöder följande typer av fakturerings konton:

- **Microsoft Online Services-program**: ett fakturerings konto för ett Microsoft Online Services-program skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise-avtal**: ett fakturerings konto för en Enterprise-avtal skapas när din organisation signerar ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) för att använda Azure.

- **Microsofts kund avtal**: ett fakturerings konto för ett Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att signera ett Microsofts kund avtal. Vissa kunder i utvalda regioner, som registrerar sig via Azure-webbplatsen för ett [konto med priser enligt principen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning eller ett [kostnads fritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan ha ett fakturerings konto för ett Microsofts kund avtal. Mer information finns i artikeln om att [komma igång med ditt faktureringskonto för Microsoft-kundavtal](../understand/mca-overview.md).

- **Microsofts partner avtal**: ett fakturerings konto för ett Microsoft partner avtal skapas för leverantörer av moln lösningar (CSP) för att hantera sina kunder i den nya Commerce-upplevelsen. Partner måste ha minst en kund med en Azure- [prenumeration](https://docs.microsoft.com/partner-center/purchase-azure-plan) för att kunna hantera sina fakturerings konton i Azure Portal. Mer information finns i [Kom igång med ditt fakturerings konto för Microsoft partner Agreement](../understand/mpa-overview.md).

Information om hur du avgör typen av ditt faktureringskonto finns i [Kontrollera typen av ditt faktureringskonto](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Omfång för faktureringskonton
Ett omfång är en nod i ett fakturerings konto som du använder för att visa och hantera fakturering. Det är här du hanterar fakturerings data, betalningar, fakturor och genomför allmän konto hantering.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

 ![Skärm bild som visar MOSP-hierarki](./media/view-all-accounts/mosp-hierarchy.png)

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar ett avtal som en kund accepterar för att använda Azure. Den innehåller en eller flera prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. Fakturan genereras i detta omfång. Annan fakturerings information som betalnings metoder och användnings adress är kopplad till det här omfånget.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Skärm bild som visar EA-hierarki](./media/view-all-accounts/ea-hierarchy.png)

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise-avtalsregistrering. Den innehåller en eller flera avdelningar och konton. Fakturan genereras i detta omfång. |
|Avdelning     |  Valfri gruppering av konton för att segmentera kostnader i logiska grupper och ange budget.     |
|Konto     |  Representerar en enskild kontoägare. Konto ägare har behörighet att skapa och hantera Azure-prenumerationer som debiteras för registreringen. |

### <a name="microsoft-customer-agreement"></a>Microsofts kundavtal

![Skärm bild som visar MCA-hierarki](./media/view-all-accounts/mca-hierarchy.png)

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett avtal som en kund accepterar för att använda Microsofts produkter och tjänster. Den innehåller en eller flera fakturerings profiler. |
|Faktureringsprofil     |   Representerar en faktura och relaterad fakturerings information som betalnings metoder och fakturerings adress. Den innehåller ett eller flera faktura avsnitt. |
|Fakturaavsnitt     |   Representerar en gruppering av kostnader i en faktura. Azure-prenumerationer och andra inköp, till exempel Azure Marketplace och program käll produkter, är kopplade till det här omfånget.    |

### <a name="microsoft-partner-agreement"></a>Microsoft partner avtal

![Skärm bild som visar MPA-hierarki](./media/view-all-accounts/mpa-hierarchy.png)

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett partner avtal för att hantera kunders Microsoft-produkter och-tjänster i den nya Commerce-upplevelsen. Den innehåller en eller flera fakturerings profiler och kunder.   |
|Faktureringsprofil     |   Representerar en faktura för en valuta.     |
|Kund    |   Representerar en kund för en partner för moln lösnings leverantör (CSP).  Azure-prenumerationer och andra inköp, till exempel Azure Marketplace och program käll produkter, är kopplade till det här omfånget.  |
|Återförsäljare    |   Åter försäljare som tillhandahåller tjänster till en kund. Det är ett valfritt fält för en prenumeration och gäller endast för indirekta leverantörer i CSP-modellen med två nivåer.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Byta faktureringsomfång i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. På sidan Översikt väljer du **switch-omfång**.

   ![Skärm bild som visar fakturerings omfång](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Du kan inte se växel omfånget om du bara har åtkomst till ett omfång.

4. Välj ett omfång för att visa information.

   ![Skärm bild som visar fakturerings omfång](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Kontrol lera typen av konto
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du börjar [analysera dina kostnader](../costs/quick-acm-cost-analysis.md).
