---
title: Ladda ned din Azure-faktura och information om den dagliga användningen | Microsoft Docs
description: Här beskrivs hur du laddar ned eller visar din Azure-faktura och information om den dagliga användningen.
keywords: faktura,ladda ned faktura,Azure-faktura,Azure-användning
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491410"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Ladda ned eller visa din Azure-faktura och information om den dagliga användningen

För de flesta prenumerationer kan du ladda ned din faktura från [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller få den skickad via e-post. Om du är en Azure-kund med ett Enterprise-avtal (EA-kund) kan du inte ladda ned organisationens fakturor. Fakturorna skickas till den person som tar emot fakturor för registreringen.

Om du är EA-kund eller har ett [Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement) kan du ladda ned användningsinformation från [Azure-portalen](https://portal.azure.com/). För andra prenumerationer går du till [Azure-kontocentret](https://account.azure.com/Subscriptions) om du vill ladda ned användningsinformation.

Det är däremot endast vissa roller som har behörighet att hämta faktura- och användningsinformation, bland annat kontoadministratören och företagsadministratören. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett Microsoft-kundavtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen för att kunna visa fakturor och användningsinformation. Du kan läsa mer om faktureringsroller för Microsoft-kundavtal i [Roller och uppgifter i faktureringsprofiler](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Ladda ned dina Azure-fakturor (.pdf)

För de flesta prenumerationer kan du ladda ned dina fakturor från Azure-portalen. Om du har ett Microsoft-kundavtal kan du läsa under Ladda ned fakturor för en faktureringsprofil.

### <a name="download-invoices-for-an-individual-subscription"></a>Ladda ned fakturor för en enskild prenumeration

1. Välj prenumerationen på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen som [en användare med åtkomst till fakturor](billing-manage-access.md).

2. Välj **Fakturor**.

    ![Skärmbild som visar alternativet för fakturering och användning](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicka på **Hämta faktura** om du vill se en kopia av PDF-fakturan. Om du ser texten **Inte tillgänglig** kan du läsa mer under [Varför visas ingen faktura för den senaste faktureringsperioden?](#noinvoice)

    ![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Du kan också visa din dagliga användning genom att klicka på faktureringsperioden.

Läs mer om din faktura i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Om du behöver hjälp att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Ladda ned fakturor för Microsoft-kundavtal

Fakturor skapas för varje [faktureringsprofil](billing-mca-overview.md#billing-profiles) i Microsoft-kundavtalet. Du måste vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen för att kunna ladda ned fakturor från Azure-portalen.

1. Sök efter **Kostnadshantering + fakturering**.
2. Välj en faktureringsprofil.
3. Välj **Fakturor**.
4. Leta rätt på raden med fakturan du vill ladda ned i rutnätet.
5. Klicka på ellipsen (`...`) i slutet av raden.
6. Välj **Faktura** på snabbmenyn för nedladdningen.

Om du inte ser någon faktura för den senaste faktureringsperioden kan du läsa **Ytterligare information**. <!-- Fix this -->
### <a name="noinvoice"></a> Varför finns det inte en faktura för den senaste faktureringsperioden?

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Fakturan har inte genererats än. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att visa fakturor. Om du har ett Microsoft-kundavtal måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen. För andra prenumerationer kanske du inte ser gamla fakturor om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

- Om du har en kostnadsfri utvärderingsversion eller ett månatligt kreditbelopp för din prenumeration som du inte har överskridit får du ingen faktura såvida du inte har ett Microsoft-kundavtal.

## <a name="get-your-invoice-in-email-pdf"></a>Få din fakturera via e-post (.pdf)

Du kan välja och konfigurera ytterligare mottagare som ska få Azure-fakturan via e-post. Den här funktionen kanske inte är tillgänglig för vissa prenumerationer som supporterbjudanden, Enterprise-avtal eller Azure i Open. Om du har ett Microsoft-kundavtal kan du läsa under Få fakturor för faktureringsprofilen via e-post.

### <a name="get-your-subscriptions-invoices-in-email"></a>Få prenumerationens fakturor via e-post

1. Välj prenumerationen på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl dig för varje prenumeration du äger. Klicka på **Fakturor** och sedan på **Skicka min faktura via e-post**.

    ![Skärmbild som visar anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klicka på **Anmäl dig** och godkänn villkoren.

    ![Skärmbild som visar steg 2 i anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. När du har godkänt avtalet kan du konfigurera ytterligare mottagare. När du tar bort en mottagare lagras inte längre e-postadressen. Om du ändrar dig måste du lägga till personen igen.

    ![Skärmbild som visar steg 3 i anmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Om du inte får något e-postmeddelande när du har följt de här stegen kontrollerar du att e-postadressen i [profilens kommunikationsinställningar](https://account.windowsazure.com/profile) är rätt angiven.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Avanmäl dig från att få prenumerationens fakturor via e-post

Du kan välja att inte få din faktura via e-post genom att följa stegen ovan och klicka på **Välj bort e-postfakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera mottagare om du anmäler dig igen.

 ![Skärmbild som visar avanmälningsflödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

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

## <a name="download-usage"></a>Ladda ned användning

 För de flesta prenumerationer hittar du filen med din dagliga användning i [Azure-kontocentret](https://account.azure.com/Subscriptions). Om du är EA-kund eller har ett Microsoft-kundavtal kan du ladda ned användningsinformation från [Azure-portalen](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Ladda användningsinformation från kontocentret (.csv)

1. Logga in i [Azure-kontocentret](https://account.windowsazure.com/subscriptions) som kontoadministratör.

2. Välj den prenumeration som du vill ha användningsinformation och faktura för.

3. Välj **FAKTURERINGSHISTORIK**.

    ![Skärmbild som visar alternativet faktureringshistorik](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Du ser utdrag för de senaste sex faktureringsperioderna och den aktuella, ännu inte fakturerade perioden.

    ![Skärmbild som visar faktureringsperioder, alternativ för att ladda ned faktura och daglig användning och de totala avgifterna för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Välj **Visa aktuellt meddelande** om du vill se en uppskattning av dina avgifter, vid den tidpunkt då uppskattningen genererades. Den här informationen uppdateras bara en gång per dygn och kanske inte omfattar all användning. Din månadsfaktura behöver inte matcha den här uppskattningen.

    ![Skärmbild som visar alternativet Visa aktuellt meddelande](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Skärmbild som visar en uppskattning av aktuella avgifter](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Välj **Ladda ned användning** om du vill ladda ned dina dagliga användningsdata som en CSV-fil. Om det finns två tillgängliga versioner laddar du ned version två.

    ![Skärmbild som visar alternativet Ladda ned användning](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Endast kontoadministratören har åtkomst till Azure-kontocentret. Andra faktureringsadministratörer, till exempel ägare, kan få användningsinformation via [API:erna för fakturering](billing-usage-rate-card-overview.md).

Läs mer om din dagliga användning i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Om du behöver hjälp att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund måste du vara en Enterprise-administratör, kontoinnehavare eller avdelningsadministratör med policyn Visa avgifter aktiverad.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.

    ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

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
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och avgifter finns i:

- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Förstå termerna på din Azure-faktura](billing-understand-your-invoice.md)
- [Förstå termerna i den detaljerade informationen om din Azure-användning](billing-understand-your-usage.md)
- [Visa organisationens Azure-priser](billing-ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå avgifterna på fakturan för din faktureringsprofil](billing-mca-understand-your-bill.md)
- [Förstå termerna på fakturan för din faktureringsprofil](billing-mca-understand-your-invoice.md)
- [Förstå filen med användning och avgifter för Azure för din faktureringsprofil](billing-mca-understand-your-usage.md)
- [Visa och ladda ned skattedokument för din faktureringsprofil](billing-mca-download-tax-document.md)
- [Visa organisationens Azure-priser](billing-ea-pricing.md)
