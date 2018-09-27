---
title: Ladda ned Azure-fakturering och daglig användningsdata | Microsoft Docs
description: Beskriver hur du ladda ned eller visa Azure fakturering och daglig användningsdata.
keywords: faktura, nedladdning av faktura, azure-faktura, azure-användning
services: billing
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: f0cdfef50c07674a08766933f2f7edfc946462a4
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395023"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Ladda ned eller visa din faktura för Azure och uppgifter om daglig användning
Du kan hämta din faktura från den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller så har den skickas via e-post. För att hämta det dagliga arbetet, går du till den [Azure Kontocenter](https://account.azure.com/Subscriptions). Endast vissa roller har behörighet att hämta faktura och information om användning, som kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Den här artikeln gäller inte för kunder med Enterprise Agreement (EA). Om du är en EA-kund, skickas dina fakturor direkt till administratörer för registrering.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

> [!div class="nextstepaction"]
> [Hjälp till att förbättra faktureringsdokument för Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="get-your-invoice-in-email-pdf"></a>Hämta din faktura i e-post (PDF)
Du kan välja och konfigurera ytterligare mottagare för att ta emot din Azure-faktura i ett e-postmeddelande. Den här funktionen är kanske inte tillgänglig för vissa prenumerationer som stöd för erbjudanden, Enterprise-avtal eller Azure i Open.

1. Välj din prenumeration från den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl dig till varje prenumeration som du äger. Klicka på **fakturor** sedan **e min faktura**. 

    ![Skärmbild som visar valet i flödet](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Klicka på **anmäla** och godkänner villkoren.

    ![Skärmbild som visar valbar flow steg 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. När du har accepterat avtalet, kan du konfigurera ytterligare mottagare. När en mottagare har tagits bort, lagras inte längre den e-postadressen. Om du ändrar dig kan behöva du lägga till dem igen.

    ![Skärmbild som visar valbar flow steg 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Om du inte får ett e-postmeddelande när du har följt stegen, kontrollera din e-postadress är korrekt i den [kommunikationsinställningar i din profil](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Avanmäla dig från att få din faktura i e-post
Om du inte vill få din faktura i e-post, klickar du på Opt ut av e-postdistribuerade fakturor. Detta tar bort alla e-postadresser som är inställd på att ta emot fakturor via e-post. Om du väljer kommer tillbaka i du måste konfigurera om mottagare.

 ![Skärmbild som visar välja bort flödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

## <a name="download-invoice-from-azure-portal-pdf"></a>Ladda ned faktura från Azure-portalen (PDF)

1. Välj din prenumeration från den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure portal enligt [en användare med åtkomst till fakturor](billing-manage-access.md).

2. Välj **fakturor**. 

    ![Skärmbild som visar alternativet fakturering och användning](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Klicka på **hämta faktura** vill se en kopia av PDF-fakturor. Om status är Stopped **inte tillgänglig**, se [Varför ser jag en faktura för den senaste faktureringsperioden?](#noinvoice)

    ![Skärmbild som visar faktureringsperioder, hämtningsalternativ och totala kostnaden för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Du kan också visa det dagliga arbetet genom att klicka på faktureringsperioden. 

Läs mer om fakturan [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Läs hur du hanterar kostnader, [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Ladda ned användning från Azure Kontocenter (.csv)

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.

2. Välj den prenumeration som du vill ha information om faktura och användning.

3. Välj **FAKTURERINGSHISTORIK**. 

    ![Skärmbild som visar historik betalningsalternativ](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Du kan se dina utdrag för de senaste sex faktureringsperioderna och den aktuella, ännu inte fakturerade perioden. 

    ![Skärmbild som visar faktureringsperioder, alternativ för att ladda ned faktura och daglig användning och totala kostnaden för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Välj **Visa aktuellt meddelande** om du vill se en uppskattning av dina avgifter, vid den tidpunkt då uppskattningen genererades. Den här informationen uppdateras varje dag och kanske inte omfattar all användning. Din månadsfaktura kan skilja sig från den här beräkningen.

    ![Skärmbild som visar alternativet Visa aktuellt meddelande](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Skärmbild som visar uppskattning av aktuella debiteringar](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Välj **Ladda ned användning** om du vill ladda ned dina dagliga användningsdata som en CSV-fil. Om det finns två tillgängliga versioner laddar du ned version två.

    ![Skärmbild som visar alternativet ladda ned användning](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Är bara kontoadministratören kan komma åt Azure Kontocenter. Andra fakturering administratörer, till exempel en ägare kan få användning informationen med hjälp av den [fakturerings-API: er](billing-usage-rate-card-overview.md).

Läs mer om din dagliga användning i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Läs hur du hanterar kostnader, [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

## <a name="noinvoice"></a> Varför ser jag en faktura för den senaste faktureringsperioden?

Det kan finnas flera orsaker till att du inte ser en faktura:

- Du har en månatlig kredit med din prenumeration som du inte överskrider eller så har en kostnadsfri utvärderingsversion. En faktura skapas bara när du är skyldig pengar.

- Det är mindre än 30 dagar från den dag som du prenumererar på Azure.

- Fakturan är inte genererat än. Vänta tills slutet av faktureringsperioden.

- Om du inte är kontoadministratören kanske inte äldre fakturor tillgängliga för dig.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

