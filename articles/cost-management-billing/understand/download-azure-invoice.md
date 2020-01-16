---
title: Visa och ladda ned din faktura för Microsoft Azure
description: Beskriver hur du visar och laddar ned din Microsoft Azure-faktura.
keywords: billing invoice,invoice download,azure invoice,azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: 0f413d38565202d379c81570b5cb169c2ed8effe
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987829"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visa och ladda ned din faktura för Microsoft Azure

För de flesta prenumerationer kan du ladda ned din faktura från [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller få den skickad via e-post. Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du inte ladda ned organisationens fakturor. Fakturorna skickas till den person som tar emot fakturor för registreringen.

Endast vissa roller har behörighet att visa fakturor. Det gäller till exempel kontoadministratören och företagsadministratören. Mer information om att få åtkomst till faktureringsinformation finns i [Hantera åtkomst till Azure-fakturering med hjälp av roller](../manage/manage-billing-access.md).

Om du har ett Microsoft-kundavtal (MCA) måste du ha någon av följande roller för att kunna hämta fakturor:

- Faktureringsprofilsägare
- Deltagare
- Läsare
- Fakturaansvarig

Om du har ett Microsoft-partneravtal (MPA) måste du vara global administratör eller administratörsrepresentant för partnerorganisationen för at kunna visa och ladda ned Azure-fakturor. [Kontrollera typen av faktureringskonto](#check-your-billing-account-type) om du vill se vilka behörigheter du behöver.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Orsaker till att du kanske inte får en faktura

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Azure debiterar dig i slutet av din fakturaperiod. Därför kan det hända att det inte har genererats någon faktura ännu. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att visa fakturor. Om du har ett MCA- eller MPA-avtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen. För andra prenumerationer ser du kanske inte gamla fakturor om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](../manage/manage-billing-access.md).

- Om du har en kostnadsfri fri utvärderingsversion eller ett månatligt kreditbelopp med din prenumeration får du bara en faktura när du överskrider det månatliga kreditbeloppet. Om du har ett Microsoft-kundavtal eller Microsoft-partneravtal får du alltid en faktura.

## <a name="download-invoices-in-the-azure-portal"></a>Ladda ned fakturor i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.
1. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil.
1. Välj **Fakturor** under **Fakturering** i den vänstra menyn.
1. Leta rätt på raden med fakturan du vill ladda ned i rutnätet.
1. Klicka på nedladdningsikonen eller ellipsen (`...`) i slutet av raden.
1. Välj **Faktura** från nedladdningsmenyn.

Mer information om din faktura finns i [Förstå fakturan för Microsoft Azure](review-individual-bill.md). Om du behöver hjälp med att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](../manage/getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Få prenumerationens fakturor via e-post

Du kan välja och konfigurera ytterligare mottagare som ska få din Azure-faktura via e-post. Den här funktionen är kanske inte tillgänglig för vissa prenumerationer såsom supporterbjudanden, Enterprise-avtal eller Azure i Open.

1. Välj din prenumeration på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl dig för varje prenumeration som du äger. Klicka på **Fakturor** och sedan på **Skicka min faktura via e-post**.

    ![Skärmbild som visar anmälningsflödet](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Klicka på **Anmäl dig** och godkänn villkoren.

    ![Skärmbild som visar steg 2 i anmälningsflödet](./media/download-azure-invoice/invoicearticlestep02.png)

3. När du har godkänt avtalet kan du konfigurera ytterligare mottagare. När du tar bort en mottagare lagras inte längre e-postadressen. Om du ändrar dig måste du lägga till personen igen.

    ![Skärmbild som visar steg 3 i anmälningsflödet](./media/download-azure-invoice/invoicearticlestep03.png)

Om du inte får något e-postmeddelande när du har följt de här stegen kontrollerar du att e-postadressen i [profilens kommunikationsinställningar](https://account.windowsazure.com/profile) är rätt angiven.

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Avanmäl dig från att få prenumerationens fakturor via e-post

Om du vill avanmäla dig från att få fakturor via e-post följer du föregående steg och klickar på **Välj bort e-postfakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera mottagare om du anmäler dig igen.

 ![Skärmbild som visar avanmälningsflödet](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Få fakturor för Microsoft-kundavtal via e-post

Om du har ett fakturerings konto för Microsofts kund avtal kan du välja att få din faktura i ett e-postmeddelande. Alla användare med rollen ägare, deltagare, läsare eller faktura hanterare på en fakturerings profil får sin faktura i e-post. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/download-azure-invoice/search-cmb.png)

1. Välj **fakturerings profiler** från vänster sida. I listan fakturerings profiler väljer du en fakturerings profil för att få fakturorna i e-postmeddelandet.

   [![skärm bild som visar listan över fakturerings profiler](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Välj **Egenskaper** från vänster sida och välj sedan **Inställningar för uppdatera e-postfaktura**.

   [![skärm bild som visar listan över fakturerings profiler](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Välj **opt in** och klicka sedan på **Uppdatera**.

   [![skärm bild som visar listan över fakturerings profiler](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Avanmäla sig från att få fakturor för Microsoft-kundavtal via e-post

Om du vill välja att inte få fakturan via e-post följer du stegen ovan och klickar på ta **bort**. Alla användare med rollen ägare, deltagare, läsare eller faktura hanterare avregistreras från e-post. 

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Ge andra till gång till dina kund avtals fakturor från Microsoft

Du kan ge andra åtkomst till att visa, ladda ned och betala fakturor genom att tilldela dem rollen som faktura hanterare för en fakturerings profil. Om du har valt att hämta din faktura via e-post får dessa användare även fakturorna i e-post. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/download-azure-invoice/search-cmb.png)

1. Välj **fakturerings profiler** från vänster sida. I listan fakturerings profiler väljer du en fakturerings profil för vilken du vill tilldela en rollen som faktura hanterare.

   [![skärm bild som visar listan över fakturerings profiler](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Välj **Access Control (IAM)** från vänster sida och välj sedan **Lägg till** överst på sidan.

   [![skärm bild som visar sidan åtkomst kontroll](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. Välj **faktura hanteraren**i list rutan roll. Ange e-postadressen för den användare som du vill ge åtkomst till. Välj **Spara** för att tilldela rollen.

   [![skärm bild som visar hur du lägger till en användare som en faktura hanterare](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Kontrollera typen av faktureringskonto
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och avgifter finns i:

- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](download-azure-daily-usage.md)
- [Förstå fakturan för Microsoft Azure](review-individual-bill.md)
- [Förstå termerna på din Azure-faktura](understand-invoice.md)
- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](understand-usage.md)
- [Visa organisationens Azure-priser](../manage/ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå avgifterna på fakturan för din faktureringsprofil](review-customer-agreement-bill.md)
- [Förstå termerna på fakturan för din faktureringsprofil](mca-understand-your-invoice.md)
- [Förstå filen med användning och avgifter för Azure för din faktureringsprofil](mca-understand-your-usage.md)
- [Visa och ladda ned skattedokument för din faktureringsprofil](mca-download-tax-document.md)
- [Visa organisationens Azure-priser](../manage/ea-pricing.md)
