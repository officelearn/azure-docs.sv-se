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
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375762"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visa och ladda ned din faktura för Microsoft Azure

För de flesta prenumerationer kan du ladda ned din faktura från [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller få den skickad via e-post. Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du inte ladda ned organisationens fakturor. Fakturorna skickas till den person som tar emot fakturor för registreringen.

Endast vissa roller har behörighet att visa fakturor. Det gäller till exempel kontoadministratören och företagsadministratören. Mer information om att få åtkomst till faktureringsinformation finns i [Hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett Microsoft-kundavtal (MCA) måste du ha någon av följande roller för att kunna hämta fakturor:

- Faktureringsprofilsägare
- Deltagare
- Läsare
- Fakturaansvarig

Om du har ett Microsoft-partneravtal (MPA) måste du vara global administratör eller administratörsrepresentant för partnerorganisationen för at kunna visa och ladda ned Azure-fakturor. [Kontrollera typen av faktureringskonto](#check-your-billing-account-type) om du vill se vilka behörigheter du behöver. 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Orsaker till att du kanske inte får en faktura

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Azure debiterar dig i slutet av din fakturaperiod. Därför kan det hända att det inte har genererats någon faktura ännu. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att visa fakturor. Om du har ett MCA- eller MPA-avtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen. För andra prenumerationer ser du kanske inte gamla fakturor om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

- Om du har en kostnadsfri fri utvärderingsversion eller ett månatligt kreditbelopp med din prenumeration får du bara en faktura när du överskrider det månatliga kreditbeloppet. Om du har ett Microsoft-kundavtal eller Microsoft-partneravtal får du alltid en faktura. 

## <a name="download-invoices-in-the-azure-portal"></a>Ladda ned fakturor i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.
1. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil.
1. Välj **Fakturor** under **Fakturering** i den vänstra menyn.
1. I fakturarutnätet letar du upp raden för den faktura som du vill ladda ned.
1. Klicka på nedladdningsikonen eller ellipsen (`...`) i slutet av raden.
1. Välj **Faktura** från nedladdningsmenyn.

Mer information om din faktura finns i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Om du behöver hjälp med att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Få fakturor för din prenumeration via e-post

Du kan välja och konfigurera ytterligare mottagare som ska få din Azure-faktura via e-post. Den här funktionen är kanske inte tillgänglig för vissa prenumerationer såsom supporterbjudanden, Enterprise-avtal eller Azure i Open.

1. Välj din prenumeration på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl dig för varje prenumeration som du äger. Klicka på **Fakturor** och sedan på **Skicka min faktura via e-post**.

    ![Skärmbild som visar anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klicka på **Anmäl dig** och godkänn villkoren.

    ![Skärmbild som visar steg 2 i anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. När du har godkänt avtalet kan du konfigurera ytterligare mottagare. När du tar bort en mottagare lagras inte längre e-postadressen. Om du ändrar dig måste du lägga till personen igen.

    ![Skärmbild som visar steg 3 i anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Om du inte får något e-postmeddelande när du har följt de här stegen kontrollerar du att e-postadressen i [profilens kommunikationsinställningar](https://account.windowsazure.com/profile) är rätt angiven.

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Avanmäl dig från att få prenumerationens fakturor via e-post

Om du vill avanmäla dig från att få fakturor via e-post följer du föregående steg och klickar på **Välj bort e-postfakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera mottagare om du anmäler dig igen.

 ![Skärmbild som visar avanmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Få fakturor för Microsoft-kundavtal via e-post

Om du har ett Microsoft-kundavtal kan du anmäla dig för att få dina fakturor via e-post. Alla ägare, deltagare, läsare och fakturaansvariga för faktureringsprofilen får fakturan via e-post. Läsare kan inte uppdatera inställningen för fakturor via e-post.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.
1. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
1. Under **Inställningar** väljer du **Egenskaper**.
1. Under **E-postfaktura** väljer du **Uppdatera inställning för e-postfaktura**.

    ![Skärmbild som visar egenskaper för e-postfaktura](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Välj **Anmäl dig**.
1. Klicka på **Uppdatera**.

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Avanmäla sig från att få fakturor för Microsoft-kundavtal via e-post

Om du vill avanmäla dig från att få fakturor via e-post följer du föregående steg och klickar på **Avanmäl**. Alla ägare, deltagare, läsare och fakturaansvariga avanmäls också från att få fakturan via e-post. Om du är läsare kan du inte ändra inställningen för fakturor via e-post.

## <a name="check-your-billing-account-type"></a>Kontrollera typen av faktureringskonto
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

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
