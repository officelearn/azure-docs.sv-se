---
title: Visa och ladda ned din faktura för Microsoft Azure
description: Beskriver hur du visar och laddar ned din Microsoft Azure-faktura.
keywords: billing invoice,invoice download,azure invoice,azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "68321770"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visa och ladda ned din faktura för Microsoft Azure

För de flesta prenumerationer kan du ladda ned din faktura från [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller få den skickad via e-post. Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du inte ladda ned organisationens fakturor. Fakturorna skickas till den person som tar emot fakturor för registreringen.

Endast vissa roller har behörighet att visa fakturor. Det gäller till exempel kontoadministratören och företagsadministratören. Mer information om att få åtkomst till faktureringsinformation finns i [Hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett [Microsoft-kundavtal](#check-your-access-to-a-microsoft-customer-agreement) måste du ha en av följande roller för att hämta dina fakturor:

- Faktureringsprofilsägare
- Deltagare
- Läsare
- Fakturaansvarig

Mer information om faktureringsroller för Microsoft-kundavtal finns i [Roller och uppgifter i faktureringsprofiler](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a> Orsaker till att du kanske inte får en faktura

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Azure debiterar dig i slutet av din fakturaperiod. Därför kan det hända att det inte har genererats någon faktura ännu. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att visa fakturor. Om du har ett Microsoft-kundavtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen. För andra prenumerationer ser du kanske inte gamla fakturor om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

- Om du har en kostnadsfri fri utvärderingsversion eller ett månatligt kreditbelopp med din prenumeration får du bara en faktura när du överskrider det månatliga kreditbeloppet. Om du har ett Microsoft-kundavtal får du alltid en faktura.

## <a name="download-your-azure-invoices-pdf"></a>Ladda ned dina Azure-fakturor (.pdf)

För de flesta prenumerationer kan du ladda ned dina fakturor från Azure-portalen. Om du har ett Microsoft-kundavtal kan du läsa [Ladda ned fakturor för ett Microsoft-kundavtal](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Ladda ned fakturor för en enskild prenumeration

1. Välj prenumerationen på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen som [en användare med åtkomst till fakturor](billing-manage-access.md).

2. Välj **Fakturor**.

    ![Skärmbild som visar alternativet för fakturering och användning](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicka på **Ladda ned faktura** om du vill se en kopia av PDF-fakturan. Om du ser texten **Inte tillgänglig** kan du läsa [Varför visas ingen faktura för den senaste faktureringsperioden?](#noinvoice)

    ![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Du kan också visa din dagliga användning genom att klicka på faktureringsperioden.

Mer information om din faktura finns i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Om du behöver hjälp med att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Ladda ned fakturor för ett Microsoft-kundavtal

Fakturor genereras för varje [faktureringsprofil](billing-mca-overview.md#billing-profiles) i Microsoft-kundavtalet. Du måste vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen för att kunna ladda ned fakturor från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.
1. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
1. Välj **Fakturor**.
1. I fakturarutnätet letar du upp raden för den faktura som du vill ladda ned.
1. Klicka på ellipsen (`...`) i slutet av raden.
    ![Skärmbild som visar ellipsen i slutet av raden](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. I snabbmenyn för nedladdning väljer du **Faktura**.

    ![Skärmbild som visar snabbmenyn](./media/billing-download-azure-invoice/contextmenu.png)

Om du inte ser någon faktura för den senaste faktureringsperioden kan du läsa [Varför visas ingen faktura för den senaste faktureringsperioden?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Få din fakturera via e-post (.pdf)

Du kan välja och konfigurera ytterligare mottagare som ska få din Azure-faktura via e-post. Den här funktionen är kanske inte tillgänglig för vissa prenumerationer såsom supporterbjudanden, Enterprise-avtal eller Azure i Open. Om du har ett Microsoft-kundavtal kan du läsa nästa avsnitt, [Få fakturor för din faktureringsprofil via e-post](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Få fakturor för din prenumeration via e-post

1. Välj din prenumeration på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl dig för varje prenumeration som du äger. Klicka på **Fakturor** och sedan på **Skicka min faktura via e-post**.

    ![Skärmbild som visar anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klicka på **Anmäl dig** och godkänn villkoren.

    ![Skärmbild som visar steg 2 i anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. När du har godkänt avtalet kan du konfigurera ytterligare mottagare. När du tar bort en mottagare lagras inte längre e-postadressen. Om du ändrar dig måste du lägga till personen igen.

    ![Skärmbild som visar steg 3 i anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Om du inte får något e-postmeddelande när du har följt de här stegen kontrollerar du att e-postadressen i [profilens kommunikationsinställningar](https://account.windowsazure.com/profile) är rätt angiven.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Avanmäl dig från att få prenumerationens fakturor via e-post

Om du vill avanmäla dig från att få fakturor via e-post följer du föregående steg och klickar på **Välj bort e-postfakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera mottagare om du anmäler dig igen.

 ![Skärmbild som visar avanmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Få fakturor för Microsoft-kundavtal via e-post

Om du har ett Microsoft-kundavtal kan du anmäla dig för att få dina fakturor via e-post. Alla ägare, deltagare, läsare och fakturaansvariga för faktureringsprofilen får fakturan via e-post. Läsare kan inte uppdatera inställningen för fakturor via e-post.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.
1. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
1. Under **Inställningar** väljer du **Egenskaper**.
1. Under **E-postfaktura** väljer du **Uppdatera inställning för e-postfaktura**.

    ![Skärmbild som visar egenskaper för e-postfaktura](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Välj **Anmäl dig**.
1. Klicka på **Uppdatera**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Avanmäla sig från att få fakturor för Microsoft-kundavtal via e-post

Om du vill avanmäla dig från att få fakturor via e-post följer du föregående steg och klickar på **Avanmäl**. Alla ägare, deltagare, läsare och fakturaansvariga avanmäls också från att få fakturan via e-post. Om du är läsare kan du inte ändra inställningen för fakturor via e-post.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrollera din åtkomst till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och avgifter finns i:

- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](billing-download-azure-daily-usage.md)
- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Förstå termerna på din Azure-faktura](billing-understand-your-invoice.md)
- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](billing-understand-your-usage.md)
- [Visa organisationens Azure-priser](billing-ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå avgifterna på fakturan för din faktureringsprofil](billing-mca-understand-your-bill.md)
- [Förstå termerna på fakturan för din faktureringsprofil](billing-mca-understand-your-invoice.md)
- [Förstå filen med användning och avgifter för Azure för din faktureringsprofil](billing-mca-understand-your-usage.md)
- [Visa och ladda ned skattedokument för din faktureringsprofil](billing-mca-download-tax-document.md)
- [Visa organisationens Azure-priser](billing-ea-pricing.md)
