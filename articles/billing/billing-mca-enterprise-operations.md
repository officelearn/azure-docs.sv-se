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
ms.openlocfilehash: 5980b8b701695d002d58b6b5239e6fa04c02fb2f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249103"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Utför Enterprise-avtal uppgifter i ditt faktureringskonto för en Microsoft-kundavtal

Om din organisation har undertecknat ett avtal med Microsoft kunden att förnya ditt Enterprise Agreement-registrering, måste du ställa in den nya faktureringskonto som skapats för avtalet. Ditt nya faktureringskonto ger dig förbättrad funktioner för hantering av fakturerings- och via en ny smidig och enhetlig hanteringsmiljö. Mer information finns i [konfigurera ditt faktureringskonto för en Microsoft-kundavtal](billing-mca-setup-account.md).

När installationen är klar kan du använda den här artikeln för att förstå hur du utför uppgifter som du utförde i ditt Enterprise Agreement-registrering.

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-your-access-to-a-microsoft-customer-agreement).

## <a name="mapping-of-enterprise-agreements-billing-to-the-new-billing-account"></a>Mappning av fakturering för Enterprise-avtal till det nya faktureringskontot

Fakturering i det nya kontot organiseras annorlunda än ditt Enterprise Agreement-registrering. I följande tabell beskriver mappningen för fakturering från din Enterprise Agreement-registrering till din nya faktureringskonto. Läs mer om nya kontot i [Kom igång med ditt faktureringskonto för Microsoft kundavtal](billing-mca-overview.md).

| Enterprise Agreement   | Microsofts kundavtal    |
|------------------------|--------------------------------------------------------|
| Registrering            | Du kan använda en profil för fakturering för att hantera fakturering för din organisation, som ditt Enterprise Agreement-registrering. Företagsadministratörer bli ägare av fakturering profilen. Läs mer om fakturering profiler i [förstå fakturering profiler](billing-mca-overview.md#understand-billing-profiles).
| Avdelning            | Du kan använda en faktura-avsnittet för att organisera dina kostnader, liknar avdelningar i ditt Enterprise Agreement-registrering. Avdelning blir faktura avsnitt och avdelning administratörer bli ägare av avsnitten respektive faktura. Läs mer om fakturan avsnitt i [förstå fakturan avsnitt](billing-mca-overview.md#understand-invoice-sections). |
| Konto               | De konton som har skapats i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet respektive faktura för sina fakultet. Kontoinnehavare kan skapa och hantera prenumerationer för sin faktura-avsnitt. |

## <a name="tasks-performed-by-an-enterprise-administrator"></a>Uppgifter som utförs av en företagsadministratör

Om du är en enterprise-administratör med ett Enterprise Agreement som har förnyats till en Microsoft-kundavtal tilldelas följande roller på den nya faktureringskonto som skapats för avtalet:

**Fakturering profilens ägare** -du har tilldelats rollen ägare fakturering profil för på fakturering profilen som skapades när avtalet undertecknades. Rollen kan du hantera faktureringen för din organisation. Du kan visa avgifter och fakturor, ordna kostnader på fakturan, hantera betalningsmetoder och styra åtkomsten till din organisations fakturering.

**Faktura avsnittet ägare** -tilldelas du rollen faktura avsnittet ägare på alla faktura avsnitt som har skapats för avdelningar i ditt Enterprise Agreement-registrering. Rollen kan du styra vem som kan skapa Azure-prenumerationer och köpa andra produkter.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Visa avgifter och krediter saldo för din organisation

Du kan använda din faktureringsinformation profil för att spåra kostnader och Azure-krediter balansen för din organisation som liknar ditt Enterprise Agreement-registrering.

Läs hur du visar saldot för en profil för fakturering i [spåra Azure-saldot för din faktureringsinformation profil](billing-mca-check-azure-credits-balance.md).

Läs hur du visar avgifter för en profil för fakturering i [förstå debiteringar på ditt Microsoft kundavtal faktura](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Visa debiteringar för en avdelning

Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement. Du kan se kostnader för ett faktura-avsnitt i Azure-portalen. Mer information finns i [Visa transaktioner med faktura avsnitt](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Visa debiteringar för ett konto

De konton som har skapats i ditt Enterprise Agreement-registrering stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet respektive faktura för sina fakultet. Kontoinnehavare kan skapa och hantera prenumerationer för sin faktura-avsnitt.

Om du vill visa sammanlagda kostnaden för prenumerationer som tillhör ett konto, måste du ange ett kostnadsställe för varje prenumeration. Du kan använda Azure användning och avgifter csv-filen för att filtrera prenumerationer av kostnadsstället.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Ladda ned användning och avgifter csv, prisdokument och skattedokument

En månadsfaktura skapas för varje fakturering profil i ditt faktureringskonto. För varje faktura, kan du hämta Azure användning och avgifter csv-fil och prisdokument skatt dokumentet (om tillämpligt). Du kan också hämta Azure användning och avgifter csv-filen för den aktuella månadens avgifter.

Läs hur du laddar ned Azure användning och avgifter csv-fil i [ladda ned eller visa din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

Lär dig hur du ladda ned price sheet och skatt dokument, se [Download skattedokument för din faktura](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

### <a name="add-an-additional-enterprise-administrator"></a>Lägg till en ytterligare företagsadministratör

Använd den **åtkomst Control(IAM)** sida i Azure portal för att ge andra användare åtkomst att visa och hantera faktureringen profilen. Läs mer om fakturering profil roller i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Läs hur du ger åtkomst till din faktureringsinformation profil i [hantera fakturering roller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Skapa en ny avdelning

I ditt nya faktureringskonto använder du en faktura-avsnittet för att organisera dina kostnader utifrån dina behov, liknar avdelningar i ditt Enterprise Agreement-registrering. Du kan skapa ett nytt faktura-avsnitt i Azure portal för att konfigurera Azure för en ny avdelning. Mer information finns i [skapa avsnitt på din faktura för att organisera dina kostnader](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Skapa ett nytt konto

Fakturering kontokontexten i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Du kan dock ange Azure-prenumeration skapare roll till användare i din organisation så att de kan skapa Azure-prenumerationer. Mer information finns i [ge andra användare behörighet att skapa Azure-prenumerationer](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="tasks-performed-by-a-department-administrator"></a>Uppgifter som utförs av en administratör för avdelning

Om du är en avdelning som administratör på ett Enterprise-avtal förnyas till en Microsoft-kundavtal kan tilldelas på det nya faktureringskontot följande roller:

**Faktura avsnittet ägare** -tilldelas du rollen faktura avsnittet ägare i avsnittet faktura som skapas för avdelningar i Enterprise-avtal. Rollen kan du visa och spåra kostnader och kontroll som kan skapa Azure-prenumerationer och köpa andra produkter för avsnittet faktura.

Om du är en avdelning som administratör på flera avdelningar i Enterprise-avtal kan tilldelas ägarrollen för faktura-avsnittet på alla faktura avsnitt som skapats för avdelningarna.

### <a name="view-charges-for-your-department"></a>Visa debiteringar för din avdelning

Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement. Avsnittet faktura har samma namn som din avdelning i Enterprise-avtal. Du kan visa avgifter för faktura-avsnittet i Azure-portalen. <!-- Todo Add a link -->

### <a name="add-an-additional-department-administrator"></a>Lägg till administratör ytterligare avdelning

Ett avsnitt faktura skapas för varje avdelning i ditt Enterprise Agreement. Du kan använda den **åtkomst Control(IAM)** sida i Azure portal för att ge andra användare åtkomst att visa och hantera avsnittet faktura. Läs mer om fakturan avsnittet roller i [faktura profil roller och uppgifter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Läs hur du ger åtkomst till din faktura-avsnittet i [hantera fakturering roller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Skapa ett nytt konto på din avdelning

Fakturering kontokontexten i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Du kan dock ange Azure-prenumeration skapare roll till användare i din organisation så att de kan skapa Azure-prenumerationer. Mer information finns i [ge andra användare behörighet att skapa Azure-prenumerationer](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Visa debiteringar för konton i dina fakultet

Fakturering kontokontexten i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet faktura som skapats för din avdelning. Kontoinnehavare kan skapa och hantera prenumerationer för faktura-avsnitt.

Om du vill visa sammanlagda kostnaden för prenumerationer som tillhör ett konto på din avdelning, måste du ange ett kostnadsställe för varje prenumeration. Du kan sedan använda filen Azure användning och avgifter för att filtrera prenumerationer av kostnadsstället. <!-- Todo - can they go to cost analysis -->

## <a name="tasks-performed-by-an-account-owner"></a>Uppgifter som utförs av en kontoinnehavare

Om du är en kontoinnehavare med ett Enterprise Agreement som har förnyats till ett avtal för Microsoft-kund kan har du tilldelats följande roll på den nya faktureringskonto som skapats för avtalet:

**Azure-prenumeration skapare** -du har tilldelats rollen azure-prenumeration skapare i avsnittet faktura som har skapats för din avdelning i Enterprise-avtal. Om ditt konto inte tillhör en avdelning, dina prenumerationer hör till avsnittet faktura med namnet faktura standardavsnittet och du får Azure-prenumeration skapare i avsnittet. Rollen kan du skapa Azure-prenumerationer för faktura-avsnittet.

### <a name="view-charges-for-your-account"></a>Visa debiteringar för ditt konto

Fakturering kontokontexten i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Kontots prenumerationer hör till avsnittet faktura skapas för ditt konto avdelning.

Om du vill visa avgifter för prenumerationer som tillhör ett konto går du till den **prenumerationssidan** i Azure-portalen. Prenumerationssidan visar avgifter för din prenumeration. <!-- Todo - can they go to cost analysis -->

### <a name="view-charges-for-a-subscription"></a>Visa debiteringar för en prenumeration

Du kan använda Azure kostnadsanalys för att se kostnader för en prenumeration. Mer information finns i [utforska och analysera kostnaderna med kostnadsanalys](../cost-management/quick-acm-cost-analysis.md).

### <a name="create-an-azure-subscription"></a>Skapa en Azure-prenumeration

Du kan skapa Azure-prenumerationer för din faktura i Azure-portalen. Mer information finns i [skapa en ytterligare Azure-prenumeration för Microsoft kundavtal](billing-mca-create-subscription.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonto för en Microsoft-kundavtal](billing-mca-overview.md)
- [Förstå fakturan](billing-understand-your-bill.md)
- [Förstå fakturan](billing-understand-your-invoice.md)
- [Hämta faktureringsägarskapet av Azure-prenumerationer från andra användare](billing-mca-request-billing-ownership.md)
