---
title: Kom igång med faktureringskontot för ditt Microsoft-partneravtal – Azure CSP
description: Få bättre förståelse för faktureringskontot för ditt Microsoft-partneravtal (CSP)
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: banders
ms.openlocfilehash: 2a15ad41850fdf574262c396bf70e43b3b08399c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989688"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Kom igång med faktureringskontot för ditt Microsoft-partneravtal

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot till att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Du kan även ha åtkomst till Azure via organisationens Enterprise-avtal, Microsoft-kundavtal eller Microsoft-partneravtal. För var och en av dessa scenarier skulle du ha ett separat faktureringskonto.

Den här artikeln gäller faktureringskonton för Microsoft-partneravtal. Dessa konton skapas för att molnlösningsleverantörer (CSP:er) ska kunna hantera faktureringen för sina kunder i den nya e-handelsmiljön. Den nya miljön är bara tillgänglig för partner som har minst en kund som har accepterat ett Microsoft-kundavtal och har ett Azure-prenumerationsavtal. [Kontrollera om du har åtkomst till ett Microsoft-partneravtal](#check-access-to-a-microsoft-partner-agreement). Med en [Azure-plan](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) får kunderna tillgång till Azure-tjänster till betala per användning-priser enligt ett Microsoft-kundavtal.

## <a name="your-billing-account"></a>Ditt faktureringskonto

Ditt faktureringskonto för Microsoft-partneravtalet innehåller en faktureringsprofil för varje valuta som du gör affärer i. Med faktureringsprofilen kan du hantera dina fakturor för den aktuella valutan. När du upprättar relationer med kunder så faktureras Azure-prenumerationer och andra inköp mot respektive faktureringsprofil beroende på kundens valuta.

I följande diagram ser du relationen mellan ett faktureringskonto, faktureringsprofiler, kunder och återförsäljare.

![Diagram som visar faktureringshierarkin för ett Microsoft-partneravtal](./media/mpa-overview/mpa-hierarchy.svg)

Användare med roll som **global administratör** och **administratörsrepresentant** för organisationen kan hantera faktureringskonton, faktureringsprofiler och kunder. Läs mer i [Partner Center – Tilldela användarroller och behörigheter](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Faktureringsprofiler

Använd en faktureringsprofil till att hantera dina fakturor i en valuta. I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller kostnader i fakturaprofilens valuta för alla Azure-prenumerationer och andra inköp från föregående månad.

Du kan visa fakturan och ladda ned relaterade dokument som användningsfilen och prisdokumentet från Azure-portalen. Du kan läsa mer i [Ladda ned fakturor för ett Microsoft-partneravtal](download-azure-invoice.md).

> [!IMPORTANT]
>
> Fakturorna för faktureringsprofiler innehåller avgifter för kunder med Azure-prenumerationsavtal samt SaaS-, Azure Marketplace- och reservationsköp för kunder som inte har godkänt ett Microsoft-kundavtal och inte har något Azure-prenumerationsavtal.

## <a name="customers"></a>Kunder

Du kan visa och hantera kunder som har godkänt ett Microsofts-kundavtal och har ett Azure-prenumerationsavtal i Azure-portalen. Du kan visa kostnader och transaktioner samt skapa och hantera Azure-prenumerationsavtal för dessa kunder.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Aktivera policyn för att ge insyn i kostnaderna

Använd policyn för att kontrollera om användare i kundorganisationen kan visa och analysera kostnader enligt taxan Betala per användning för sin Azure-förbrukning. Policyn är inaktiv som standard och användarna kan inte se kostnaden. När den är aktiverad kan de användare som har tillräcklig [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)-åtkomst till en prenumeration visa och analysera kostnaden för prenumerationen.

Så här aktiverar du policyn:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter **Kostnadshantering och fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/mpa-overview/search-cmb.png)

1. Välj **Kunder** till vänster och sedan en kund i listan.

   ![Skärmbild som visar val av kunder](./media/mpa-overview/mpa-customers.png)

1. Välj **Policyer** till vänster.

   ![Skärmbild som visar policyer](./media/mpa-overview/mpa-change-policy.png)

1. Välj **Ja**.

## <a name="resellers"></a>Återförsäljare

Indirekta leverantörer i [modellen med två nivåer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview#azure-csp-direct-and-azure-csp-indirect) för CSP kan välja en återförsäljare när de skapar prenumerationer för kunder i Azure-portalen. Efteråt kan de visa listan med prenumerationer filtrerad efter återförsäljare och analysera kostnaden för en kund per återförsäljare i Azures-kostnadsanalysen.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrollera åtkomsten till ett Microsoft-partneravtal
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du läsa mer om ditt faktureringskonto:

- [Skapa ytterligare en Azure-prenumeration för Microsoft-partneravtal](../manage/create-subscription.md)
- Integrera faktureringsdata i ditt eget rapportsystem med hjälp av [API:erna för Azure-fakturering](https://docs.microsoft.com/rest/api/billing/)
- [Snabbstartsguide till Azure Cost Management för partner](https://go.microsoft.com/fwlink/?linkid=2106482)
