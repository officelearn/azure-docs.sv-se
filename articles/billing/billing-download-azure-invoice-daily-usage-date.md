---
title: Hämta Azure billing faktura och dagligen användningsdata | Microsoft Docs
description: Beskriver hur du laddar ned eller visa din Azure faktura och daglig användning faktureringsinformation.
keywords: fakturering faktura, ladda ned faktura, azure faktura, azure användning
services: ''
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b18f568fdeead10fadad0e403ce0ca7fe4b3213a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Hämta eller visa din Azure fakturering faktura och dagliga användningsdata
Du kan hämta fakturan från den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller låta skickas via e-post. För att hämta det dagliga arbetet, gå till den [Azure Kontocenter](https://account.azure.com/Subscriptions). Endast vissa roller som har behörighet att hämta faktureringsinformation faktura och information om användning, som kontoadministratör. Läs mer om att få tillgång till faktureringsinformationen i [hantera åtkomst till Azure fakturering med roller](billing-manage-access.md).

>[!NOTE]
>Den här artikeln gäller inte för kunder med Enterprise-avtal (EA). Om du är en EA-kund skickas fakturor direkt till registrering av administratörer.

## <a name="get-your-invoice-in-email-pdf"></a>Hämta fakturan i e-post (PDF)
Du kan välja och konfigurera ytterligare mottagare för att ta emot din Azure faktura i ett e-postmeddelande. Den här funktionen är kanske inte tillgängliga för vissa prenumerationer som stöd för erbjudanden, Enterprise-avtal eller Azure i Open.

1. Välj din prenumeration från den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Anmäl för varje prenumeration som du äger. Klicka på **fakturor** sedan **e-min faktura**. 

    ![Skärmbild som visar opt-in-flöde](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Klicka på **välja** och acceptera villkoren.

    ![Skärmbild som visar opt-in-flöde](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. När du har accepterat avtalet, kan du konfigurera ytterligare mottagare.

    ![Skärmbild som visar opt-in-flöde](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Om du inte får ett e-postmeddelande när du har följt stegen, kontrollera din e-postadress är korrekt i den [kommunikationsinställningar i din profil](https://account.windowsazure.com/profile).

## <a name="download-invoice-from-azure-portal-pdf"></a>Ladda ned faktura från Azure-portalen (PDF)

1. Välj din prenumeration från den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen som [en användare med åtkomst till fakturor](billing-manage-access.md).

2. Välj **fakturor**. 

    ![Skärmbild som visar alternativet fakturerings- och användningsdata](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Klicka på **ladda ned faktura** att visa en kopia av fakturan PDF. Om det står **inte tillgänglig**, se [Varför ser jag en faktura för den senaste faktureringsperioden?](#noinvoice)

    ![Skärmbild som visar fakturering punkter, hämtningsalternativ och totala debiteringen för varje faktureringsperioden](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Du kan också visa det dagliga arbetet genom att klicka på faktureringsperioden. 

Mer information om fakturan finns [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Hjälp med att hantera kostnader finns [förhindrar oväntade kostnader med Azure fakturerings- och kostnaden management](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Ladda ned användning från Kontocenter (.csv)

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.

2. Välj den prenumeration som du vill ha information om faktura och användning.

3. Välj **FAKTURERING HISTORIK**. 

    ![Skärmbild som visar historik betalningsalternativ](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Du kan se din instruktioner för de senaste sex fakturering perioderna och aktuell ofakturerad period. 

    ![Skärmbild som visar fakturering perioder, alternativ för att ladda ned faktura och daglig användning och totala debiteringen för varje faktureringsperioden](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Välj **Visa aktuella instruktionen** att se en uppskattning av dina debiteringar när uppskattningen har genererats. Den här informationen uppdateras bara dagligen och får inte innehålla din användning. Fakturan månatliga kan skilja sig från den här beräkningen.

    ![Skärmbild som visar alternativet Visa aktuella instruktionen](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Skärmbild som visar uppskattning av aktuella avgifter](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Välj **ladda ned användning** att hämta dagliga användningsdata som en CSV-fil. Om du ser två versioner som är tillgängliga, hämta version 2.

    ![Skärmbild som visar alternativet ladda ned användning](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Enbart administratörskontot kan komma åt Azure Kontocenter. Andra fakturering administratörer, till exempel en ägare får användning informationen med hjälp av den [fakturerings-API: er](billing-usage-rate-card-overview.md).

Mer information om det dagliga arbetet finns [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Hjälp med att hantera kostnader finns [förhindrar oväntade kostnader med Azure fakturerings- och kostnaden management](billing-getting-started.md).

## <a name="noinvoice"></a> Varför ser jag en faktura för den senaste faktureringsperioden

Det kan finnas flera skäl till att du inte ser en faktura:

- Du har ett månatliga kreditbelopp med din prenumeration som du inte överskrider eller du har en kostnadsfri utvärderingsversion. En faktura skapas bara när du är skyldig pengar.

- Det är mindre än 30 dagar från den dag som du prenumererar på Azure.

- Fakturan är inte genererats ännu. Vänta till slutet av faktureringsperioden.

- Om du inte är kontoadministratören kanske inte äldre fakturor tillgängliga för dig.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.

