---
title: Kom igång med faktureringskontot för ditt Microsoft-kundavtal – Azure
description: Få bättre förståelse för faktureringskontot för ditt Microsoft-kundavtal
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 835686d639679cca7e9a83b5297b365953835e47
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880735"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Kom igång med faktureringskontot för ditt Microsoft-kundavtal

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot till att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Du kan även ha åtkomst till Azure via organisationens Enterprise-avtal eller Microsoft-kundavtal. I den här situationen skulle du ha två separata faktureringskonton.

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Ditt faktureringskonto

Ditt faktureringskonto för Microsoft-kundavtalet innehåller en eller flera faktureringsprofiler som gör att du kan hantera fakturor och betalningsmetoder. Varje faktureringsprofil har ett eller flera fakturaavsnitt där du kan organisera kostnaderna på faktureringsprofilens faktura.

I följande diagram visas relationen mellan ett faktureringskonto, faktureringsprofilerna och fakturaavsnitten.

![Diagram som visar faktureringshierarkin för Microsoft-kundavtal](./media/billing-mca-overview/mca-billing-hierarchy.png)

Roller för faktureringskontot har den högsta behörighetsnivån. Som standard är det bara den användare som har registrerat sig för Azure som får åtkomst till faktureringskontot. De här rollerna bör tilldelas till användare som behöver visa fakturor och spåra kostnader för hela organisationen, som ekonomi- eller IT-chefer. Mer information finns i [Roller och uppgifter för faktureringskonton](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Faktureringsprofiler

Använd en faktureringsprofil till att hantera dina fakturor och betalningsmetoder. I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller respektive avgifter för alla Azure-prenumerationer och andra inköp från föregående månad.

Det skapas automatiskt en faktureringsprofil för ditt faktureringskonto. Den innehåller ett fakturaavsnitt som standard. Du kan skapa fler avsnitt så att du enkelt kan spåra och organisera kostnaderna efter behov, oavsett om du vill dela in efter projekt, avdelning eller utvecklingsmiljö. Dessa avsnitt visas på faktureringsprofilens faktura och anger användningen för respektive prenumeration och de köp du har tilldelat till dem.

Roller för faktureringsprofiler har behörighet att visa och hantera fakturor och betalningsmetoder. Tilldela dessa roller till användare som betalar fakturor, som medarbetare på organisationens redovisningsavdelning. Mer information finns i [Roller och uppgifter för faktureringsprofiler](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Varje faktureringsprofil får en faktura varje månad

I början av månaden skapas en månadsfaktura för varje faktureringsprofil. Fakturan innehåller alla avgifter från föregående månad.

I Azure-portalen kan du visa fakturan, ladda ned dokument och ändra inställningen för att få framtida fakturor via e-post. Du kan läsa mer i [Ladda ned fakturor för ett Microsoft-kundavtal](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Betalningsmetoder för fakturor

Varje faktureringsprofil har en egen betalningsmetod som används till att betala dess fakturor. Det finns stöd för följande metoder:

| Typ             | Definition  |
|------------------|-------------|
|Azure-krediter    |  Krediterna tillämpas automatiskt på de berättigade avgifterna på din faktura, vilket minskar beloppet du måste betala. Mer information finns i [Spåra Azure-kreditsaldot för din faktureringsprofil](billing-mca-check-azure-credits-balance.md). |
|Check/banköverföring | Om ditt konto är godkänt för betalning via check/banköverföring. Du kan betala fakturabeloppet via check/banköverföring. Betalningsanvisningar står på fakturan |
|Kreditkort | Kunder som registrerat sig för Azure på Azure-webbplatsen kan betala med kreditkort. |

### <a name="apply-policies-to-control-purchases"></a>Använd policyer för att styra inköp

Med en faktureringsprofil kan du använda policyer för köp på Azure Marketplace och reservationsköp. Du kan ställa in policyer som förhindrar köp av reservationer och produkter från Marketplace. När en sådan policy används går det inte att genomföra sådana köp med prenumerationer som faktureras till faktureringsprofilen.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Priser och servicenivåavtal för prenumerationer fastställs i Azure-planer

Priser och servicenivåavtal för Azure-prenumerationer fastställs i Azure-planer. De aktiveras automatiskt när du skapar en faktureringsprofil. Alla fakturaavsnitt som är associerade med faktureringsprofilen kan använda dessa planer. Användare med åtkomst till fakturaavsnittet kan använda de här planerna till att skapa Azure-prenumerationer. Du kan använda följande Azure-planer i faktureringskonton för Microsoft-kundavtal:

| Planera             | Definition  |
|------------------|-------------|
|Microsoft Azure Plan   | Användare kan skapa prenumerationer som kan köra alla typer av arbetsbelastningar.  |
|Microsoft Azure Plan for Dev/Test | Visual Studio-prenumeranter kan skapa prenumerationer som är begränsade till arbetsbelastningar för utveckling eller tester. Dessa prenumerationer har fördelar som lägre priser och åtkomst till exklusiva avbildningar av virtuella datorer i Azure-portalen. |

## <a name="invoice-sections"></a>Fakturaavsnitt

Skapa fakturaavsnitt för att organisera kostnaderna på din faktura. Du kanske till exempel behöver ha en enda faktura för organisationen, men du vill ändå ordna dela upp kostnaderna efter avdelning, grupp eller projekt. I det här scenariot har du en enda faktureringsprofil där du skapar ett fakturaavsnitt för varje avdelning, grupp eller projekt.

När du skapar ett fakturaavsnitt kan du ge andra behörighet att skapa Azure-prenumerationer som fakturerar till avsnittet. Eventuella användningskostnader och inköp för prenumerationerna faktureras sedan till avsnittet.

Roller för fakturaavsnitt har behörighet att styra vem som skapar Azure-prenumerationer. Tilldela dessa roller till användare som konfigurerar Azure-miljön för team i organisationen, som ingenjörer och tekniska arkitekter. Mer information finns i [Roller och uppgifter för fakturaavsnitt](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du läsa mer om ditt faktureringskonto:

- [Förstå administrativa roller för Microsoft-kundavtal i Azure](billing-understand-mca-roles.md)
- [Skapa ytterligare en Azure-prenumeration för Microsoft-kundavtal](billing-mca-create-subscription.md)
- [Skapa avsnitt på din faktura och organisera dina kostnader](billing-mca-section-invoice.md)
