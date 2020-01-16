---
title: EA-uppgifter i ett Microsoft-kund avtal – Azure
description: Lär dig hur du utför olika Enterprise-avtalsuppgifter i ditt nya faktureringskonto.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: 0cdf95b8d91938d6b9b4de413bc5d5dcb8caa2f4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991248"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Utföra Enterprise-avtalsuppgifter i faktureringskontot för ett Microsoft-kundavtal

Om din organisation har signerat ett Microsoft-kundavtal för att förnya din Enterprise-avtalsregistrering, skapas ett nytt faktureringskonto för avtalet. Faktureringen i det nya kontot skiljer sig från den för ditt Enterprise-avtal. Den här artikeln beskriver hur du kan använda det nya faktureringskontot för att utföra uppgifter som du utförde i ditt Enterprise-avtal.

## <a name="billing-organization-in-the-new-account"></a>Faktureringen i det nya kontot

Följande diagram beskriver hur faktureringen ser ut i det nya faktureringskontot.

![Bild på ea-mca-post-transition-hierarchy](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Microsofts kundavtal    |
|------------------------|--------------------------------------------------------|
| Registrering            | Du använder en faktureringsprofil för att hantera faktureringen för din organisation på ett liknande sätt som för din Enterprise-avtalsregistrering. Företagsadministratörer blir ägare till faktureringsprofilen. Mer information om faktureringsprofiler finns i [Förstå faktureringsprofiler](../understand/mca-overview.md#billing-profiles).
| Avdelning            | Du kan använda ett fakturaavsnitt för att ordna dina kostnader, ungefär som avdelningar i Enterprise-avtalsregistreringen. Avdelning blir fakturaavsnitt, och avdelningsadministratörer blir ägare av respektive fakturaavsnitt. Mer information om fakturaavsnitt finns i [Förstå fakturaavsnitt](../understand/mca-overview.md#invoice-sections). |
| Konto               | De konton som skapades i ditt Enterprise-avtal stöds inte i det nya faktureringskontot. Kontots prenumerationer tillhör respektive fakturaavsnitt för deras avdelning. Kontoägare kan skapa och hantera prenumerationer för sina fakturaavsnitt. |

## <a name="changes-for-enterprise-administrators"></a>Ändringar för företagsadministratörer

Följande ändringar gäller för företagsadministratörer i ett Enterprise-avtal som har förnyats till ett Microsoft-kundavtal.

- En faktureringsprofil skapas för din registrering. Du använder faktureringsprofilen för att hantera faktureringen för din organisation på ett liknande sätt som med Enterprise-avtalsregistreringen. Mer information om faktureringsprofiler finns i avsnittet [Förstå faktureringsprofiler](../understand/mca-overview.md#billing-profiles).
- Ett fakturaavsnitt skapas för varje avdelning i Enterprise-avtalsregistreringen. Du använder fakturaavsnitten för att hantera dina avdelningar. Du kan skapa ett nytt fakturaavsnitt om du vill konfigurera fler avdelningar. Mer information om fakturaavsnitt finns i [förstå fakturaavsnitt](../understand/mca-overview.md#invoice-sections).
- Du använder rollen som skapare för Azure-prenumerationer i fakturaavsnitt för att ge andra behörighet att skapa en Azure-prenumeration, exempelvis de konton vi skapade i Enterprise-avtalsregistreringen.
- Du använder [Azure-portalen](https://portal.azure.com) för att hantera faktureringen för din organisation, i stället för Azure EA-portalen.

Du tilldelas följande roller för det nya faktureringskontot:

**Ägare av faktureringsprofil** – Du tilldelas rollen som ägare av faktureringsprofilen som skapades när avtalet signerades. Med den här rollen kan du hantera faktureringen för din organisation. Du kan visa kostnader och fakturor, ordna kostnader på fakturan, hantera betalningsmetoder och kontrollera åtkomsten till organisationens fakturering.

**Ägare av fakturaavsnitt** – Du tilldelas rollen som ägare till fakturaavsnitt för alla fakturaavsnitt som skapas för avdelningarna i din Enterprise-avtalsregistrering. Med den här rollen kan du kontrollera vem som kan skapa Azure-prenumerationer och köpa andra produkter.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Visa kostnader och kreditsaldon för din organisation

Du använder din faktureringsprofil för att spåra avgifter och Azure-kreditsaldot för din organisation, på liknande sätt som i Enterprise-avtalsregistreringen.

Information om hur du visar kreditsaldot för en faktureringsprofil finns i [Spåra Azure-kreditsaldot för din faktureringsprofil](mca-check-azure-credits-balance.md).

Information om hur du visar debiteringar för en faktureringsprofil finns i avsnittet [Förstå avgifterna på fakturan för Microsoft-kundavtal](../understand/review-customer-agreement-bill.md).

### <a name="view-charges-for-a-department"></a>Visa avgifter för en avdelning

Ett fakturaavsnitt skapas för varje avdelning som fanns i ditt Enterprise-avtal. Du kan visa debiteringar för en faktura på Azure-portalen. Mer information finns i [Visa transaktioner efter fakturaavsnitt](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Visa avgifter för ett konto

De konton som skapades i Enterprise-avtalsregistreringen stöds inte i det nya faktureringskontot. Kontots prenumerationer tillhör respektive fakturaavsnitt för deras avdelning. Kontoägare kan skapa och hantera prenumerationer för sina fakturaavsnitt.

Om du vill visa sammanställda kostnader för prenumerationer som hör till ett konto måste du ange ett kostnadsställe för varje prenumeration. Sedan kan du använda CSV-filen med information om Azure-användning och Azure-debitering för att filtrera prenumerationerna efter kostnadsstället.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Ladda ned CSV-filen med användnings- och avgiftsinformation, prisdokument och skattedokument

En månadsfaktura skapas för varje faktureringsprofil i ditt faktureringskonto. För varje faktura kan du ladda ned CSV-filen med information om Azure-användning och Azure-debitering, prisdokument och skattedokument (om tillämpligt). Du kan också ladda ned CSV-filen med information om Azure-användning och Azure-avgifter för den aktuella månadens kostnader.

Information om hur du hämtar CSV-filen med information om Azure-användning och Azure-debitering finns i [Ladda ned användning för ditt Microsoft-kundavtal](../understand/download-azure-daily-usage.md).

Information om hur du laddar ned prisdokument finns i [Ladda ned priser för ditt Microsoft-kundavtal](ea-pricing.md).

Information om hur du laddar ned skattedokument finns i [Visa skattedokument för ditt Microsoft-kundavtal](../understand/mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Lägga till en till företagsadministratör

Ge användarna åtkomst till faktureringsprofilen så att de kan visa och hantera faktureringen för din organisation. Du kan använda sidan **Access Control (IAM)** på Azure-portalen för att ge åtkomst.  Mer information om roller för faktureringsprofiler finns i [Faktureringsprofilroller och uppgifter](understand-mca-roles.md#billing-profile-roles-and-tasks).

Information om hur du ger åtkomst till din faktureringsprofil finns i [Hantera faktureringsroller på Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Skapa en ny avdelning

Skapa ett fakturaavsnitt för att ordna dina kostnader utifrån dina behov, ungefär som avdelningar i Enterprise-avtalsregistreringen. Du kan skapa ett nytt fakturaavsnitt på Azure-portalen. Mer information finns i [Skapa avsnitt på din faktura och organisera dina kostnader](mca-section-invoice.md).

### <a name="create-a-new-account"></a>Skapa ett nytt konto

Tilldela rollen som skapare för Azure-prenumerationer i fakturaavsnitt för att ge andra behörighet att skapa en Azure-prenumeration, som kontona som skapas i Enterprise-avtalsregistreringen. Du kan läsa mer om att tilldela roller i [Hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>Ändringar för avdelningsadministratörer

Följande ändringar gäller för avdelningsadministratörer i ett Enterprise-avtal som har förnyats till ett Microsoft-kundavtal.

- Ett fakturaavsnitt skapas för varje avdelning i Enterprise-avtalsregistreringen. Du använder fakturaavsnitten för att hantera dina avdelningar. Mer information om fakturaavsnitt finns i [förstå fakturaavsnitt](../understand/mca-overview.md#invoice-sections).
- Du använder rollen som skapare för Azure-prenumerationer i fakturaavsnittet för att ge andra behörighet att skapa en Azure-prenumeration, som kontona som skapas i Enterprise-avtalsregistreringen.
- Du använder Azure-portalen för att hantera faktureringen för din organisation, i stället för Azure EA-portalen.

Du tilldelas följande roller för det nya faktureringskontot:

**Ägare av fakturaavsnitt** – Du tilldelas rollen som ägare av fakturaavsnitt i fakturaavsnittet som skapas för de avdelningar som fanns i Enterprise-avtalet. Med den här rollen kan du visa och spåra kostnader och kontrollera vem som kan skapa Azure-prenumerationer och köpa andra produkter för fakturaavsnittet.

### <a name="view-charges-for-your-department"></a>Visa avgifter för din avdelning

Du kan visa debiteringar för fakturaavsnittet som skapats för din avdelning på sidan [Kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) på Azure-portalen.

### <a name="add-an-additional-department-administrator"></a>Lägg till en till avdelningsadministratör

Ett fakturaavsnitt skapas för varje avdelning som fanns i ditt Enterprise-avtal. Du kan använda sidan **Access Control (IAM)** på Azure-portalen för att ge andra behörighet att visa och hantera fakturaavsnittet. Mer information om fakturaavsnittsroller finns i [Fakturaavsnittsroller och uppgifter](understand-mca-roles.md#invoice-section-roles-and-tasks).

Information om hur du ger åtkomst till ditt fakturaavsnitt finns i [Hantera faktureringsroller på Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Skapa ett nytt konto i din avdelning

Tilldela användare rollen som skapare av Azure-prenumerationer på avsnittsfakturan som skapats för din avdelning. Du kan läsa mer om att tilldela roller i [Hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>Visa avgifter för konton i dina avdelningar

De konton som skapades i Enterprise-avtalsregistreringen stöds inte i det nya faktureringskontot. Kontots prenumerationer tillhör respektive fakturaavsnitt för deras avdelning. Kontoägare kan skapa och hantera prenumerationer för sina fakturaavsnitt.

Om du vill visa sammanställda kostnader för prenumerationer som hör till ett konto i din avdelning måste du ange ett kostnadsställe för varje prenumeration. Sedan kan du använda filen med information om Azure-användning och Azure-debitering för att filtrera prenumerationerna efter kostnadsstället.

## <a name="changes-for-account-owners"></a>Ändringar för kontoägare

Kontoägare i Enterprise-avtal får behörighet att skapa Azure-prenumerationer i det nya faktureringskontot. Dina befintliga Azure-prenumerationer tillhör fakturaavsnittet som skapas för din avdelning. Om ditt konto inte är kopplat till någon avdelning tillhör dina prenumerationer ett fakturaavsnitt med namnet Standardfaktura.

Om du skapar fler Azure-prenumerationer tilldelas du följande roll i det nya faktureringskontot.

**Skapare av Azure-prenumeration** – Du tilldelas rollen som skapare av Azure-prenumerationer i fakturaavsnittet som skapas för din avdelning i Enterprise-avtalet. Om ditt konto inte är kopplat till någon avdelning tilldelas du rollen som skapare av Azure-prenumerationer i ett avsnitt med namnet Standardfaktura. Med den här rollen kan du skapa Azure-prenumerationer för fakturaavsnittet.

### <a name="create-an-azure-subscription"></a>Skapa en Azure-prenumeration

Du kan skapa Azure-prenumerationer för ditt fakturaavsnitt på Azure-portalen. Mer information finns i [Skapa ytterligare en Azure-prenumeration för Microsoft-kundavtal](create-subscription.md)

### <a name="view-charges-for-your-account"></a>Visa avgifter för ditt konto

Om du vill visa avgifter för prenumerationer som hör till ett konto går du till [sidan Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) på Azure-portalen. På sidan Prenumerationer visas avgifter för alla dina prenumerationer.

### <a name="view-charges-for-a-subscription"></a>Visa avgifter för en prenumeration

Du kan visa avgifter för en prenumeration på [sidan Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller Azure-kostnadsanalys. Mer information om Azure-kostnadsanalys finns i [Utforska och analysera kostnader med kostnadsanalys](../costs/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonton för Microsofts-kundavtal](../understand/mca-overview.md)
- [Förstå din faktura](../understand/review-individual-bill.md)
- [Förstå fakturan](../understand/understand-invoice.md)
- [Få faktureringsägarskap för Azure-prenumerationer från andra användare](mca-request-billing-ownership.md)
