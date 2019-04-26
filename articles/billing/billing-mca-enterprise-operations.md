---
title: Utföra åtgärder för Enterprise-avtal i Microsoft kundavtal – Azure | Microsoft Docs
description: Lär dig hur du utför uppgifterna i Enterprise-avtal i ditt nya faktureringskonto.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371347"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Utför Enterprise-avtal uppgifter i ditt faktureringskonto för en Microsoft-kundavtal

Om din organisation har undertecknat ett avtal med Microsoft kunden att förnya ditt Enterprise Agreement-registrering, skapas en ny faktureringskonto för avtalet. Fakturering i det nya kontot organiseras annorlunda än ditt Enterprise Agreement. Den här artikeln beskrivs hur du kan använda det nya faktureringskontot för att utföra uppgifter som du utförde i ditt Enterprise Agreement.

## <a name="how-billing-is-organized-in-the-new-account"></a>Hur organiseras fakturering i det nya kontot

Följande diagram visar hur fakturering är ordnade i ditt nya faktureringskonto.

![Bild av ea-mca-post-övergång-hierarki](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise-avtal   | Microsofts kundavtal    |
|------------------------|--------------------------------------------------------|
| Registrering            | Du kan använda en profil för fakturering för att hantera fakturering för din organisation, som ditt Enterprise Agreement-registrering. Företagsadministratörer bli ägare av fakturering profilen. Läs mer om fakturering profiler i [förstå fakturering profiler](billing-mca-overview.md#understand-billing-profiles).
| Avdelning            | Du kan använda en faktura-avsnittet för att organisera dina kostnader, liknar avdelningar i ditt Enterprise Agreement-registrering. Avdelning blir faktura avsnitt och avdelning administratörer bli ägare av avsnitten respektive faktura. Läs mer om fakturan avsnitt i [förstå fakturan avsnitt](billing-mca-overview.md#understand-invoice-sections). |
| Konto               | De konton som har skapats i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet respektive faktura för sina fakultet. Kontoinnehavare kan skapa och hantera prenumerationer för sin faktura-avsnitt. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>Ändringar för Företagsadministratörer i det nya faktureringskontot

Följande ändringar gäller för företagets administratörer med ett Enterprise Agreement som har förnyats till en Microsoft-kundavtal.

- En fakturering profil har skapats för din registrering. Du använder fakturering profilen för att hantera fakturering för din organisation, t.ex. ditt Enterprise Agreement-registrering. Mer information om fakturering profiler [förstå fakturering profiler](billing-mca-overview.md#understand-billing-profiles).
- Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement-registrering. Du använder faktura-avsnitt för att hantera dina avdelningar. Du kan skapa nya faktura avsnitt för att ställa in ytterligare avdelningar. Läs mer om fakturan avsnitt i [förstå fakturan avsnitt](billing-mca-overview.md#understand-invoice-sections).
- Du använder Azure-prenumeration skapare-rollen på fakturan avsnitt för att ge andra användare behörighet att skapa Azure-prenumeration, t.ex. konton som har skapats i Enterprise Agreement-registrering.
- Du kommer att använda den [Azure-portalen](https://portal.azure.com) att hantera faktureringen för din organisation, i stället för Azure EA-portalen.

Du får följande roller på det nya faktureringskontot:

**Fakturering profilens ägare** -du har tilldelats rollen ägare fakturering profil för på fakturering profilen som skapades när avtalet undertecknades. Rollen kan du hantera faktureringen för din organisation. Du kan visa avgifter och fakturor, ordna kostnader på fakturan, hantera betalningsmetoder och styra åtkomsten till din organisations fakturering.

**Faktura avsnittet ägare** -tilldelas du rollen faktura avsnittet ägare på alla faktura avsnitt som har skapats för avdelningar i ditt Enterprise Agreement-registrering. Rollen kan du styra vem som kan skapa Azure-prenumerationer och köpa andra produkter.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Visa avgifter och krediter saldo för din organisation

Du kan använda din faktureringsinformation profil för att spåra kostnader och Azure-krediter balansen för din organisation som liknar ditt Enterprise Agreement-registrering.

Läs hur du visar saldot för en profil för fakturering i [spåra Azure saldot för din faktureringsinformation profil](billing-mca-check-azure-credits-balance.md).

Läs hur du visar avgifter för en profil för fakturering i [förstå debiteringar på ditt Microsoft kundavtal faktura](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Visa debiteringar för en avdelning

Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement. Du kan se kostnader för ett faktura-avsnitt i Azure-portalen. Mer information finns i [Visa transaktioner med faktura avsnitt](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Visa debiteringar för ett konto

De konton som har skapats i ditt Enterprise Agreement-registrering stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet respektive faktura för sina fakultet. Kontoinnehavare kan skapa och hantera prenumerationer för sin faktura-avsnitt.

Om du vill visa sammanlagda kostnaden för prenumerationer som tillhör ett konto, måste du ange ett kostnadsställe för varje prenumeration. Du kan använda Azure användning och avgifter csv-filen för att filtrera prenumerationer av kostnadsstället.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Ladda ned användning och avgifter csv, prisdokument och skattedokument

En månadsfaktura skapas för varje fakturering profil i ditt faktureringskonto. För varje faktura, kan du hämta Azure användning och avgifter csv-fil och prisdokument skatt dokumentet (om tillämpligt). Du kan också hämta Azure användning och avgifter csv-filen för den aktuella månadens avgifter.

Läs hur du laddar ned Azure användning och avgifter csv-fil i [ladda ned användningsdata för ditt Microsoft-kundavtal](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Läs hur du laddar ned prisdokument i [ladda ned priserna för ditt Microsoft-kundavtal](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Läs hur du laddar ned skattedokument i [visa skattedokument för ditt Microsoft-kundavtal](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Lägg till en ytterligare företagsadministratör

Ge användare åtkomst till faktureringen profilen och låter dem visa och hantera faktureringen för din organisation. Du kan använda den **åtkomstkontroll (IAM)** sida i Azure portal för att ge åtkomst.  Läs mer om fakturering profil roller i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Läs hur du ger åtkomst till din faktureringsinformation profil i [hantera fakturering roller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Skapa en ny avdelning

Skapa en faktura-avsnittet för att organisera dina kostnader utifrån dina behov, t.ex. avdelningar i ditt Enterprise Agreement-registrering. Du kan skapa ett nytt faktura-avsnitt i Azure-portalen. Mer information finns i [skapa avsnitt på din faktura för att organisera dina kostnader](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Skapa ett nytt konto

Tilldela användare rollen Azure-prenumeration skapare på fakturan avsnitt ge dem behörighet att skapa Azure-prenumeration, t.ex. konton som skapas i Enterprise Agreement-registrering. Mer information finns i [ge andra användare behörighet att skapa Azure-prenumerationer](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>Ändringar för avdelning administratörer i det nya faktureringskontot

Följande ändringar gäller avdelning administratörer med ett Enterprise Agreement som har förnyats till en Microsoft-kundavtal.

- Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement-registrering. Du använder faktura-avsnitt för att hantera dina department(s). Läs mer om fakturan avsnitt i [förstå fakturan avsnitt](billing-mca-overview.md#understand-invoice-sections).
- Du använder Azure-prenumeration skapare roll i avsnittet faktura för att ge andra användare behörighet att skapa Azure-prenumeration, t.ex. konton som skapas i Enterprise Agreement-registrering.
- Du kommer använda Azure-portalen för att hantera fakturering för din organisation, i stället för Azure EA-portalen.

Du får följande roller på det nya faktureringskontot:

**Faktura avsnittet ägare** -tilldelas du rollen faktura avsnittet ägare i avsnittet faktura som skapas för department(s) i Enterprise-avtal. Rollen kan du visa och spåra kostnader och kontroll som kan skapa Azure-prenumerationer och köpa andra produkter för avsnittet faktura.

### <a name="view-charges-for-your-department"></a>Visa debiteringar för din avdelning

Du kan se kostnader för faktura-avsnitt som har skapats för din avdelning i Azure portal [kostnadshantering + fakturering sidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Lägg till administratör ytterligare avdelning

Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement. Du kan använda den **åtkomst Control(IAM)** sida i Azure portal för att ge andra användare åtkomst att visa och hantera avsnittet faktura. Läs mer om fakturan avsnittet roller i [faktura avsnittet roller och uppgifter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Läs hur du ger åtkomst till din faktura-avsnittet i [hantera fakturering roller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Skapa ett nytt konto på din avdelning

Tilldela användare rollen Azure-prenumeration skapare på fakturan avsnitt som har skapats för din avdelning. Mer information finns i [ge andra användare behörighet att skapa Azure-prenumerationer](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Visa debiteringar för konton i dina fakultet

De konton som har skapats i ditt Enterprise Agreement-registrering stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet respektive faktura för sina fakultet. Kontoinnehavare kan skapa och hantera prenumerationer för sin faktura-avsnitt.

Om du vill visa sammanlagda kostnaden för prenumerationer som tillhör ett konto på din avdelning, måste du ange ett kostnadsställe för varje prenumeration. Du kan sedan använda filen Azure användning och avgifter för att filtrera prenumerationer av kostnadsstället.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>Ändringar för kontoinnehavare i det nya faktureringskontot

Kontoinnehavare på Enterprise-avtal får behörighet att skapa Azure-prenumerationer på det nya faktureringskontot. Din befintliga Azure-prenumerationer som hör till avsnittet faktura som har skapats för din avdelning. Om ditt konto inte tillhör en avdelning, tillhör dina prenumerationer en faktura-avsnittet med namnet standardavsnittet faktura.

Om du vill skapa ytterligare Azure-prenumerationer, får du följande roll på det nya faktureringskontot.

**Azure-prenumeration skapare** -du har tilldelats rollen azure-prenumeration skapare i avsnittet faktura som har skapats för din avdelning i Enterprise-avtal. Om ditt konto inte tillhör en avdelning, får du Azure-prenumeration skapare roll på ett avsnitt som heter standardavsnittet faktura. Rollen kan du skapa Azure-prenumerationer för faktura-avsnittet.

### <a name="create-an-azure-subscription"></a>Skapa en Azure-prenumeration

Du kan skapa Azure-prenumerationer för din faktura i Azure-portalen. Mer information finns i [skapa en ytterligare Azure-prenumeration för Microsoft kundavtal](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Visa debiteringar för ditt konto

Om du vill visa avgifter för prenumerationer som tillhör ett konto går du till den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen. Prenumerationssidan visar avgifter för din prenumeration.

### <a name="view-charges-for-a-subscription"></a>Visa debiteringar för en prenumeration

Du kan antingen se kostnader för en prenumeration på den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller Azure analys av kostnader. Läs mer på Azure kostnadsanalys [utforska och analysera kostnaderna med kostnadsanalys](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonto för en Microsoft-kundavtal](billing-mca-overview.md)
- [Förstå fakturan](billing-understand-your-bill.md)
- [Förstå fakturan](billing-understand-your-invoice.md)
- [Hämta faktureringsägarskapet av Azure-prenumerationer från andra användare](billing-mca-request-billing-ownership.md)
