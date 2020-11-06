---
title: Hämta faktura och daglig användningsinformation för Azure
description: Här beskrivs hur du laddar ned eller visar din Azure-faktura och information om den dagliga användningen.
keywords: faktura,ladda ned faktura,Azure-faktura,Azure-användning
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2020
ms.author: banders
ms.openlocfilehash: e380aa1f4d50e6ced34254ceca9d899022142f6d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911298"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Ladda ned eller visa din Azure-faktura och information om den dagliga användningen

För de flesta prenumerationer kan du ladda ned din faktura från [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller få den skickad via e-post. Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du inte ladda ned organisationens fakturor. Fakturorna skickas till den person som tar emot fakturor för registreringen.

Om du är EA-kund eller har ett [Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement) kan du ladda ned användningsinformation från [Azure-portalen](https://portal.azure.com/).

Det är däremot endast vissa roller som har behörighet att hämta faktura- och användningsinformation, bland annat kontoadministratören och företagsadministratören. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](manage-billing-access.md).

Om du har ett Microsoft-kundavtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen för att kunna visa fakturor och användningsinformation. Du kan läsa mer om faktureringsroller för Microsoft-kundavtal i [Roller och uppgifter i faktureringsprofiler](understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Ladda ned dina Azure-fakturor (.pdf)

För de flesta prenumerationer kan du ladda ned dina fakturor från Azure-portalen. Om du har ett Microsoft-kundavtal kan du läsa under Ladda ned fakturor för en faktureringsprofil.

### <a name="download-invoices-for-an-individual-subscription"></a>Ladda ned fakturor för en enskild prenumeration

1. Välj prenumerationen på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen som [en användare med åtkomst till fakturor](manage-billing-access.md).

2. Välj **Fakturor**.

    ![Skärmbild som visar alternativet för fakturering och användning](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicka på nedladdningsknappen för att ladda ned en kopia av PDF-fakturan och välj sedan **Ladda ned faktura**. Om du ser texten **Inte tillgänglig** kan du läsa [Varför visas ingen faktura för den senaste faktureringsperioden?](#noinvoice)

    ![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. Du kan även ladda ned en daglig uppdelning av förbrukade kvantiteter och beräknade avgifter genom att klicka på **Ladda ned csv**.

    ![Skärmbild som visar Ladda ned faktura och förbrukningssidan](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

Mer information om din faktura finns i [Förstå fakturan för Microsoft Azure](../understand/review-individual-bill.md). Information om hur du hanterar dina kostnader finns i [Analysera oväntade avgifter](../understand/analyze-unexpected-charges.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Ladda ned fakturor för Microsoft-kundavtal

Fakturor genereras för varje [faktureringsprofil](../understand/mca-overview.md#billing-profiles) i Microsoft-kundavtalet. Du måste vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen för att kunna ladda ned fakturor från Azure-portalen.

1. Sök efter **Kostnadshantering + fakturering**.
2. Välj en faktureringsprofil.
3. Välj **Fakturor**.
4. I fakturarutnätet letar du upp raden för den faktura som du vill ladda ned.
5. Klicka på nedladdningsknappen i slutet av raden.
6. I snabbmenyn för nedladdning väljer du **Faktura**.

Om du inte ser någon faktura för den senaste faktureringsperioden kan du läsa **Ytterligare information**. <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Varför finns det inte en faktura för den senaste faktureringsperioden?

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Fakturan har inte genererats än. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att visa fakturor. Om du har ett Microsoft-kundavtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen. För andra prenumerationer ser du kanske inte gamla fakturor om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](manage-billing-access.md).

- Om du har en kostnadsfri utvärderingsversion eller ett månatligt kreditbelopp för din prenumeration som du inte har överskridit får du ingen faktura såvida du inte har ett Microsoft-kundavtal.

## <a name="get-your-invoice-in-email-pdf"></a>Få din fakturera via e-post (.pdf)

Du kan välja och konfigurera ytterligare mottagare som ska få din Azure-faktura via e-post. Den här funktionen kanske inte är tillgänglig för vissa prenumerationer som supporterbjudanden, Enterprise-avtal eller Azure i Open. Om du har ett Microsoft-kundavtal kan du läsa under Få fakturor för faktureringsprofilen via e-post.

### <a name="get-your-subscriptions-invoices-in-email"></a>Få prenumerationens fakturor via e-post

1. Välj prenumerationen på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl dig för varje prenumeration du äger. Klicka på **Fakturor** och sedan på **Skicka min faktura via e-post**.

    ![Skärmbild som visar anmälningsflödet](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. Klicka på **Anmäl dig** och godkänn villkoren.

    ![Skärmbild som visar steg 2 i anmälningsflödet](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. När du har godkänt avtalet kan du konfigurera ytterligare mottagare. När du tar bort en mottagare lagras inte längre e-postadressen. Om du ändrar dig måste du lägga till personen igen.

    ![Skärmbild som visar steg 3 i anmälningsflödet](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

Om du inte får något e-postmeddelande när du har följt de här stegen kontrollerar du att e-postadressen i [profilens kommunikationsinställningar](https://account.windowsazure.com/profile) är rätt angiven.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Avanmäl dig från att få prenumerationens fakturor via e-post

Du kan välja att inte få din faktura via e-post genom att följa stegen ovan och klicka på **Välj bort e-postfakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera mottagare om du anmäler dig igen.

 ![Skärmbild som visar avanmälningsflödet](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Få fakturor för Microsoft-kundavtal via e-post

Om du har ett Microsoft-kundavtal kan du anmäla dig för att få dina fakturor via e-post. Alla ägare, deltagare, läsare och fakturaansvariga för faktureringsprofilen får fakturan via e-post. Läsare kan inte uppdatera e-postinställningarna.

1. Sök efter **Kostnadshantering + fakturering**.
1. Välj en faktureringsprofil.
1. Under **Inställningar** väljer du **Egenskaper**.
1. Under **E-postfaktura** väljer du **Uppdatera inställningar för e-postfaktura**.
1. Välj **Anmäl dig**.
1. Klicka på **Uppdatera**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Avanmäl dig från att få fakturor för fakturaprofilen via e-post

Du kan välja att inte få din faktura via e-post genom att följa stegen ovan och klicka på **Avanmäl dig**. Alla ägare, deltagare, läsare och fakturaansvariga avanmäls också från att få fakturan via e-post. Om du är läsare kan du inte ändra e-postinställningarna.

## <a name="download-usage-in-azure-portal"></a>Nedladdningsförbrukning i Microsoft Azure-portalen

 För de flesta prenumerationer kan du följa följande steg för att hitta din dagliga användning:

1. Välj prenumerationen på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen som [en användare med åtkomst till fakturor](manage-billing-access.md).

2. Välj **Fakturor**.

    ![Skärmbild som visar alternativet för fakturering och användning](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicka på nedladdningsknappen för en fakturaperiod som du vill kontrollera.

4. Ladda ned en daglig uppdelning av förbrukade kvantiteter och beräknade avgifter genom att klicka på **Ladda ned csv**.  Det kan ta några minuter att förbereda csv-filen.

### <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund måste du vara Enterprise-administratör, kontoägare eller avdelningsadministratör med principen för att visa avgifter aktiverad.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.
1. Om du har åtkomst till flera faktureringskonton väljer du faktureringsomfånget för ditt EA-faktureringskonto.
1. Välj **Användning och avgifter**.
1. Välj **Ladda ned** för den månad du vill ladda ned.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Ladda ned användning för ditt Microsoft-kundavtal

Om du vill visa och ladda ned användningsdata för en faktureringsprofil måste du vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen.

#### <a name="download-usage-for-billed-charges"></a>Ladda ned användning för fakturerade avgifter

1. Sök efter **Kostnadshantering + fakturering**.
2. Välj en faktureringsprofil.
3. Välj **Fakturor**.
4. Leta rätt på raden med fakturan som motsvarar den användning som du vill ladda ned i rutnätet.
5. Klicka på ellipsen (`...`) i slutet av raden.
6. Välj **Användning och avgifter för Azure** på snabbmenyn för nedladdningen.

#### <a name="download-usage-for-open-charges"></a>Ladda ned användning för öppna avgifter

Du kan också ladda ned användningen hittills under månaden för den aktuella faktureringsperioden, vilket innebär att avgifterna inte har debiterats ännu.

1. Sök efter **Kostnadshantering + fakturering**.
2. Välj en faktureringsprofil.
3. Gå till bladet **Översikt** och klicka på **Hämta användning och avgifter för Azure**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och avgifter finns i:

- [Förstå fakturan för Microsoft Azure](../understand/review-individual-bill.md)
- [Förstå termerna på din Azure-faktura](../understand/understand-invoice.md)
- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](../understand/understand-usage.md)
- [Visa organisationens Azure-priser](ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå avgifterna på fakturan för din faktureringsprofil](../understand/review-customer-agreement-bill.md)
- [Förstå termerna på fakturan för din faktureringsprofil](../understand/mca-understand-your-invoice.md)
- [Förstå filen med användning och avgifter för Azure för din faktureringsprofil](../understand/mca-understand-your-usage.md)
- [Visa och ladda ned skattedokument för din faktureringsprofil](../understand/mca-download-tax-document.md)
- [Visa organisationens Azure-priser](ea-pricing.md)
