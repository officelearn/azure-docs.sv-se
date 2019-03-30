---
title: Visa och ladda ned Azure-användning och avgifter | Microsoft Docs
description: Beskriver hur du ladda ned eller visa din dagliga användning och kostnader.
keywords: Debiterad användning, hämta avgifter för användning, användning, visa användning azure faktura, azure-användning
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648854"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visa och hämta din Azure-användning och kostnader

Om du är EA-kund eller har en [Microsoft kundavtal](#check-your-access-to-a-microsoft-customer-agreement), du kan ladda ned Azure-användning och kostnader på den [Azure-portalen](https://portal.azure.com/). För andra prenumerationer, går du till den [Azure Kontocenter](https://account.azure.com/Subscriptions) att ladda ned användning.

Endast vissa roller har behörighet att hämta information om Azure-användning, till exempel kontoadministratör eller företagsadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har en [Microsoft kundavtal](#check-your-access-to-a-microsoft-customer-agreement), du måste vara en fakturering profil ägare, deltagare, läsare, eller fakturera manager för att visa dina Azure-användning och kostnader. Läs mer om fakturering administratörsroller för Microsoft Customer avtal i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

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

Läs mer om din dagliga användning i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Läs hur du hanterar dina kostnader, [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund, måste du vara en Enterprise-administratör, ägare, eller avdelning Admin när vyn debiterar principen är aktiverad.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter *Cost Management + fakturering*.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Välj **användning och kostnader**.
1. För den månad som du vill ladda ned, väljer **hämta**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Ladda ned användningsdata för ditt Microsoft-kundavtal

Om du har ett Microsoft-kundavtal, kan du hämta din Azure-användning och kostnader för ditt fakturering. Du måste vara en fakturering profil ägare, deltagare, läsare, eller fakturera manager för att ladda ned Azure-användning och kostnader CSV.

### <a name="download-usage-for-billed-charges"></a>Ladda ned användningsdata för fakturerade kostnader

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Sök på **Cost Management + fakturering**.
3. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto först beroende på din åtkomst.
4. Välj **fakturor**.
5. Hitta raden på fakturan som motsvarar den användning som du vill hämta i rutnätet för fakturan.
6. Klicka på ellipsknappen (`...`) i slutet av raden.

    ![Skärmbild som visar de tre punkterna i slutet av raden](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. I snabbmenyn download väljer **Azure-användning och kostnader**.

     ![Skärmbild som visar Azure-användning och kostnader som har valts](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Ladda ned användningsdata för väntande avgifter

Du kan också hämta månad hittills användning för den aktuella faktureringsperioden. Dessa kostnader som inte har fakturerats ännu.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Sök på **Cost Management + fakturering**.
3. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto först beroende på din åtkomst.
4. I den **översikt** bladet hitta nedladdningslänkarna under månaden-till-date-avgifter.
5. Välj **Azure-användning och kostnader**.

    ![Skärmbild som visar nedladdning från översikt](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrollera din åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om din faktura och användning debiteringar finns:

- [Förstå villkoren på Microsoft Azure detaljerad användning](billing-understand-your-usage.md)
- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta din organisations Azure-prissättning](billing-ea-pricing.md)

Om du har ett Microsoft-kundavtal, se:

- [Förstå villkoren på din Microsoft Customer avtal Azure detaljerad användning](billing-mca-understand-your-usage.md)
- [Förstå debiteringar på fakturan Microsoft kundavtal](billing-mca-understand-your-bill.md)
- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta skattedokument för ditt Microsoft-kundavtal](billing-mca-download-tax-document.md)
- [Visa och hämta din organisations Azure-prissättning](billing-ea-pricing.md)
