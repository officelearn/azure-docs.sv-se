---
title: Visa och ladda ned din faktura för Microsoft Azure
description: Beskriver hur du visar och laddar ned din Microsoft Azure faktura.
keywords: fakturerings faktura, hämtning av faktura, Azure-faktura, Azure-användning
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
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321770"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visa och ladda ned din faktura för Microsoft Azure

För de flesta prenumerationer kan du ladda ned din faktura från [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller skicka den via e-post. Om du är en Azure-kund med en Enterprise-avtal (EA-kund) kan du inte ladda ned din organisations fakturor. Fakturor skickas till alla har kon figurer ATS för att ta emot fakturor för registreringen.

Endast vissa roller har behörighet att Visa fakturor. Till exempel konto administratören eller företags administratören. Mer information om hur du får åtkomst till fakturerings information finns i [Hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett [Microsoft](#check-your-access-to-a-microsoft-customer-agreement)-kundavtal måste du ha en av följande roller för att få dina fakturor:

- Ägare till fakturerings profil
- Deltagare
- Läsare
- Faktura hanterare

Mer information om fakturerings roller för Microsofts kund avtal finns i [fakturerings profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a>Varför du kanske inte får en faktura

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Azure debiterar dig i slutet av din faktura period. En faktura kanske ännu inte har genererats. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att Visa fakturor. Om du har ett Microsoft-kundavtal måste du vara ägare, deltagare, läsare eller faktura ansvarig för fakturerings profilen. För andra prenumerationer kanske du inte ser gamla fakturor om du inte är konto administratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

- Om du har en kostnads fri utvärderings version eller ett månatligt kredit belopp med din prenumeration får du bara en faktura när du överskrider det månatliga kredit beloppet. Om du har ett kund avtal från Microsoft får du alltid en faktura.

## <a name="download-your-azure-invoices-pdf"></a>Ladda ned dina Azure-fakturor (. pdf)

För de flesta prenumerationer kan du ladda ned fakturan från Azure Portal. Om du har ett Microsoft-kundavtal kan du läsa [Ladda ned fakturor för ett Microsofts kund avtal](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Ladda ned fakturor för en enskild prenumeration

1. Välj din prenumeration på [sidan prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal som [en användare med till gång till fakturor](billing-manage-access.md).

2. Välj **Fakturor**.

    ![Skärm bild som visar alternativet för fakturerings & användning](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicka på **Hämta faktura** om du vill visa en kopia av din PDF-faktura. Om den **inte är tillgänglig**, se [Varför ser jag inte en faktura för den senaste fakturerings perioden?](#noinvoice)

    ![Skärm bild som visar fakturerings perioder, nedladdnings alternativ och totala avgifter för varje fakturerings period](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Du kan också visa din dagliga användning genom att klicka på fakturerings perioden.

Mer information om din faktura finns i [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). För hjälp med att hantera dina kostnader, se [förhindra oväntade kostnader med fakturering och kostnads hantering i Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Ladda ned fakturor för ett Microsofts kund avtal

Fakturor skapas för varje [fakturerings profil](billing-mca-overview.md#billing-profiles) i Microsofts kund avtal. Du måste vara en fakturerings profil ägare, deltagare, läsare eller faktura hanterare för att kunna ladda ned fakturor från Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter *Cost Management + fakturering*.
1. Välj en fakturerings profil. Beroende på åtkomst kan du behöva välja ett fakturerings konto först.
1. Välj **Fakturor**.
1. Leta upp raden för den faktura som du vill ladda ned i rutnätet för faktura.
1. Klicka på ellipsen`...`() i slutet av raden.
    ![Skärm bild som visar ellipsen i slutet av raden](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. I snabb menyn Hämta väljer du **faktura**.

    ![Skärm bild som visar snabb menyn](./media/billing-download-azure-invoice/contextmenu.png)

Om du inte ser någon faktura för den senaste fakturerings perioden, se [Varför ser jag ingen faktura för den senaste fakturerings perioden?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Hämta din faktura i e-post (. pdf)

Du kan välja och konfigurera ytterligare mottagare för att ta emot din Azure-faktura i ett e-postmeddelande. Den här funktionen kanske inte är tillgänglig för vissa prenumerationer, till exempel Support erbjudanden, företags avtal eller Azure i Open. Om du har ett Microsoft-kundavtal går du till nästa avsnitt, [hämtar dina fakturerings profil fakturor via e-post](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Hämta prenumerationens fakturor via e-post

1. Välj din prenumeration på [sidan prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Välj att välja för varje prenumeration som du äger. Klicka på **fakturor** och **skicka e-post till min faktura**.

    ![Skärm bild som visar opt-in-flödet](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klicka på **Välj** och godkänn villkoren.

    ![Skärm bild som visar det opt-in Flow steg 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. När du har accepterat avtalet kan du konfigurera ytterligare mottagare. När en mottagare tas bort lagras inte längre e-postadressen. Om du ändrar dig måste du läsa dem.

    ![Skärm bild som visar det opt-in Flow steg 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Om du inte får ett e-postmeddelande när du har åtgärdat stegen kontrollerar du att din e-postadress stämmer överens med [kommunikations inställningarna i profilen](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Anmäl dig från att få prenumerationens fakturor i e-post

Om du vill välja att inte få fakturan via e-post följer du de föregående stegen och klickar på avanmälde **fakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera om mottagarna om du väljer igen.

 ![Skärm bild som visar det opt-out-flödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Hämta dina kund avtals fakturor från Microsoft via e-post

Om du har ett kund avtal från Microsoft kan du välja att få din faktura i ett e-postmeddelande. Alla fakturerings profil ägare, deltagare, läsare och faktura hanterare får fakturan via e-post. Läsarna kan inte uppdatera inställningarna för e-postfaktura.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter **Cost Management + fakturering**.
1. Välj en fakturerings profil. Beroende på åtkomst kan du behöva välja ett fakturerings konto först.
1. Under **Inställningar**väljer du **Egenskaper**.
1. Under **e-postfaktura**väljer du **Inställningar för uppdatera e-postfaktura**.

    ![Skärm bild som visar egenskaper för e-postfaktura](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Välj **opt**(Välj).
1. Klicka på **Uppdatera**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Anmäl dig från dina kund avtals fakturor från Microsoft via e-post

Om du vill välja att inte få fakturan via e-post följer du stegen ovan och klickar på ta **bort**. Alla ägare, deltagare, läsare och faktura hanterare har också valt att få fakturan via e-post. Om du är en läsare kan du inte ändra inställningarna för e-postfakturan.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrol lera åtkomsten till ett Microsofts kund avtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om din faktura och dina kostnader finns i:

- [Visa och ladda ned Microsoft Azure användning och avgifter](billing-download-azure-daily-usage.md)
- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Förstå villkoren på din Azure-faktura](billing-understand-your-invoice.md)
- [Förstå villkoren i Microsoft Azure detaljerad användning](billing-understand-your-usage.md)
- [Visa din organisations priser för Azure](billing-ea-pricing.md)

Om du har ett kund avtal från Microsoft kan du läsa:

- [Förstå avgifterna på fakturan för din fakturerings profil](billing-mca-understand-your-bill.md)
- [Förstå villkoren på fakturan för din fakturerings profil](billing-mca-understand-your-invoice.md)
- [Förstå Azures användnings-och avgifts fil för din fakturerings profil](billing-mca-understand-your-usage.md)
- [Visa och hämta skatte dokument för din fakturerings profil](billing-mca-download-tax-document.md)
- [Visa din organisations priser för Azure](billing-ea-pricing.md)
