---
title: Visa och ladda ned information om användning och avgifter i Azure
description: Här beskrivs hur du laddar ned eller visar din dagliga användning och dina avgifter i Azure.
keywords: billing usage,usage charges, usage download, view usage, azure invoice,azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491432"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visa och ladda ned information om din användning och dina avgifter i Azure

Om du är EA-kund eller har ett [Microsoft-kundavtal](#check-your-access-to-a-microsoft-customer-agreement) kan du ladda ned Azure-användning och -avgifter i [Azure-portalen](https://portal.azure.com/). För andra prenumerationer går du till [Azure-kontocentret](https://account.azure.com/Subscriptions) om du vill ladda ned användningsinformation.

Det är endast vissa roller som har behörighet att hämta Azure-användningsinformation, bland annat kontoadministratören och företagsadministratören. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett [Microsoft-kundavtal](#check-your-access-to-a-microsoft-customer-agreement) måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen för att kunna visa Azure-användning och -avgifter. Du kan läsa mer om faktureringsroller för Microsoft-kundavtal i [Roller och uppgifter i faktureringsprofiler](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Ladda användningsinformation från kontocentret (.csv)

1. Logga in på [Azure-kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.

2. Välj den prenumeration som du vill ha användningsinformation och faktura för.

3. Välj **FAKTURERINGSHISTORIK**.

    ![Skärmbild som visar alternativet faktureringshistorik](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Du ser utdrag för de senaste sex faktureringsperioderna och den aktuella, ännu inte fakturerade perioden.

    ![Skärmbild som visar faktureringsperioder, alternativ för att ladda ned faktura och daglig användning och de totala avgifterna för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Välj **Visa aktuellt meddelande** om du vill se en uppskattning av dina avgifter, vid den tidpunkt då uppskattningen genererades. Den här informationen uppdateras bara en gång per dygn och omfattar kanske inte all användning. Din månadsfaktura kan skilja sig från den här uppskattningen.

    ![Skärmbild som visar alternativet Visa aktuellt utdrag](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Skärmbild som visar en uppskattning av aktuella avgifter](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Välj **Ladda ned användning** om du vill ladda ned dina dagliga användningsdata som en CSV-fil. Om det finns två tillgängliga versioner laddar du ned version två.

    ![Skärmbild som visar alternativet Ladda ned användning](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Endast kontoadministratören har åtkomst till Azure-kontocentret. Andra faktureringsadministratörer, till exempel ägare, kan få användningsinformation via [API:erna för fakturering](billing-usage-rate-card-overview.md).

Läs mer om din dagliga användning i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Om du behöver hjälp att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund måste du vara Enterprise-administratör, kontoägare eller avdelningsadministratör med principen för att visa avgifter aktiverad.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.

    ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Välj **Användning och avgifter**.
1. Välj **Ladda ned** för den månad du vill ladda ned.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Ladda ned användning för ditt Microsoft-kundavtal

Om du har ett Microsoft-kundavtal kan du ladda ned Azure-användning och -avgifter för din faktureringsprofil. Du måste vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen för att kunna ladda ned CSV-filen med Azure-användning och -avgifter.

### <a name="download-usage-for-billed-charges"></a>Ladda ned användning för fakturerade avgifter

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter *Kostnadshantering + fakturering*.
3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
4. Välj **Fakturor**.
5. I rutnätet med fakturor letar du upp raden med den faktura som motsvarar den användning som du vill ladda ned.
6. Klicka på ellipsen (`...`) i slutet av raden.

    ![Skärmbild som visar ellipsen i slutet av raden](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. I snabbmenyn för nedladdning väljer du **Användning och avgifter för Azure**.

     ![Skärmbild som visar Användning och avgifter för Azure valt](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Ladda ned användning för väntande avgifter

Du kan även ladda ned användningen hittills under månaden för den aktuella faktureringsperioden. De här användningsavgifterna har ännu inte fakturerats.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter *Kostnadshantering + fakturering*.
3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
4. I området **Översikt** finns nedladdningslänkarna nedanför avgifterna hittills under månaden.
5. Välj **Användning och avgifter för Azure**.

    ![Skärmbild som visar nedladdning från Översikt](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrollera din åtkomst till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och användningsavgifter finns i:

- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](billing-understand-your-usage.md)
- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Visa och ladda ned din Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och ladda ned din organisations Azure-priser](billing-ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå termerna i den detaljerade Azure-användningen för ditt Microsoft-kundavtal](billing-mca-understand-your-usage.md)
- [Förstå avgifterna på fakturan för ditt Microsoft-kundavtal](billing-mca-understand-your-bill.md)
- [Visa och ladda ned din Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och ladda ned skattedokument för ditt Microsoft-kundavtal](billing-mca-download-tax-document.md)
- [Visa och ladda ned din organisations Azure-priser](billing-ea-pricing.md)
