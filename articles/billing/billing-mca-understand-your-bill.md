---
title: Förstå avgifter på fakturan för ditt Microsoft-kundavtal – Azure
description: Lär dig hur du läser och förstår avgifterna på din faktura.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e9c74fd18bf80dfb8fa406912795fb53e655bd88
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226161"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Förstå avgifter på fakturan för ditt Microsoft-kundavtal

Du kan få bättre förståelse för kostnaderna på din faktura genom att analysera de enskilda transaktionerna. I faktureringskontot för ett Microsoft-kundavtal genereras en faktura varje månad för varje faktureringsprofil. Fakturan innehåller alla avgifter från föregående månad. Du kan visa dina fakturor i Azure-portalen. Du kan läsa mer i [Ladda ned fakturor för ett Microsoft-kundavtal](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Visa transaktioner för en faktura i Azure-portalen

1. Logga in på [Azure-portalen](https://www.azure.com).

2. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj **Alla transaktioner** till vänster på sidan. Beroende på din åtkomst kan du behöva välja ett faktureringskonto, en faktureringsprofil eller ett fakturaavsnitt och sedan välja **Alla transaktioner**.

4. På sidan Alla transaktioner visas följande information:

    ![Skärmbild som visar en lista med fakturerade transaktioner](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Kolumn  |Definition  |
    |---------|---------|
    |Date     | Datum för transaktionen  |
    |Faktura-ID     | ID:t för fakturan som transaktionen debiterats till. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda ärendet |
    |Transaktionstyp     |  Transaktionstypen, till exempel inköp, uppsägning eller användning  |
    |Produktfamilj     | Produktkategorin, till exempel beräkning för virtuella datorer eller databas för Azure SQL-databaser|
    |Produkt-SKU     | En unik kod som identifierar instansen av produkten |
    |Belopp     |  Transaktionsbeloppet      |
    |Fakturaavsnitt     | Transaktionen visas i det här avsnittet av faktureringsprofilens faktura |
    |Faktureringsprofil     | Transaktionen visas på den här faktureringsprofilens faktura |

5. Sök efter faktura-ID:t om du vill filtrera ut fakturans transaktioner.

### <a name="view-transactions-by-invoice-sections"></a>Visa transaktioner efter fakturaavsnitt

Med fakturaavsnitt kan du organisera kostnaderna på en faktureringsprofils faktura. Mer information finns i [Förstå fakturaavsnitt](billing-mca-overview.md#invoice-sections). När en faktura skapas så visas avgifter för alla avsnitt i faktureringsprofilen på fakturan.

I följande bild ser du avgifter för fakturaavsnittet Accounting Dept på en exempelfaktura.

![Exempelbild som visar information efter fakturaavsnitt](./media/billing-understand-your-bill-mca/invoicesection-details.png)

När du har identifierat avgifterna för ett fakturaavsnitt kan du få bättre förståelse för transaktionerna i Azure-portalen.

1. Gå till sidan Alla transaktioner i Azure-portalen om du vill visa transaktionerna för en faktura. Mer information finns i [Visa transaktioner för en faktura i Azure-portalen](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Visa transaktioner genom att filtrera efter fakturaavsnitt.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura

I faktureringskontot för ett Microsoft-kundavtal uppskattas olika kostnader, och de betraktas som väntande tills de har fakturerats. Du kan visa väntande avgifter i Azure-portalen om du vill göra en uppskattning av nästa faktura. Väntande avgifter är uppskattningar och inkluderar inte skatt. De faktiska kostnaderna på nästa faktura behöver inte matcha de väntande avgifterna.

### <a name="view-summary-of-pending-charges"></a>Sammanfattning av väntande avgifter

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering och fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

4. Välj fliken **Sammanfattning** längst upp på skärmen.

5. I avsnittet med avgifter visas avgifterna hittills under månaden och för föregående månad.

   ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande och tas upp på nästa faktura.

### <a name="view-pending-transactions"></a>Visa väntande transaktioner

När du identifierar väntande avgifter kan du få bättre förståelse för kostnaderna genom att analysera de enskilda transaktioner som har bidragit till avgifterna. För närvarande visas inte väntande användningsavgifter på sidan Alla transaktioner. Du kan visa väntande användningsavgifter på sidan för Azure-prenumerationen. Mer information finns i [Visa väntande användningsavgifter](#view-pending-usage-charges)

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering och fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

4. Välj **Alla transaktioner** till vänster på sidan.

5. Sök efter *väntande*. Använd filtret **Tidsintervall** till att visa väntande avgifter för aktuell eller föregående månad.

   ![Skärmbild som visar listan med väntande transaktioner](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Visa väntande användningsavgifter

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter *Kostnadshantering och fakturering*.

   ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

4. Välj **Alla prenumerationer** till vänster på sidan.

5. På sidan för Azure-prenumerationen ser du den aktuella månadens och föregående månads avgifter för respektive prenumeration i faktureringsprofilen. Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande.

    ![Skärmbild som visar listan med Azure-prenumerationer för faktureringsprofilen](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analysera avgifterna för din Azure-användning

Använd CSV-filen med Azure-användning och avgifter när du ska analysera dina användningsavgifter. Du kan ladda ned filen antingen för en faktura eller för väntande avgifter. Mer information finns i [Hämta information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Visa detaljerad användning per fakturaavsnitt

Du kan filtrera filen med Azure-användning och avgifter så att du ser användningsavgifterna för dina olika fakturaavsnitt.

Så här stämmer du av beräkningsavgifterna för fakturaavsnittet Accounting Dept:

![Exempelbild som visar information efter fakturaavsnitt](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Faktura-PDF | CSV med Azure-användning och avgifter |
 | --- | --- |
 |Accounting Dept |invoiceSectionName |
 |Användningsavgifter – Microsoft Azure Plan |productOrderName |
 |Compute |serviceFamily |

1. Filtrera kolumnen **invoiceSectionName** i CSV-filen efter **Accounting Dept**.
2. Filtrera kolumnen **productOrderName** i CSV-filen efter **Microsoft Azure Plan**.
3. Filtrera kolumnen **serviceFamily** i CSV-filen efter **Microsoft.Compute**.

![Skärmbild som visar filen med användning och avgifter per fakturaavsnitt](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Visa detaljerad användning per prenumeration

Du kan filtrera CSV-filen med Azure-användning och avgifter för att stämma av användningsavgifterna för dina olika prenumerationer. Om du vill visa alla prenumerationer i en faktureringsprofil kan du läsa [Visa väntande användningsavgifter](#view-pending-usage-charges).

När du ska identifiera avgifter för en prenumeration analyserar du kostnaderna i CSV-filen med Azure-användning och avgifter.

I följande bild ser du en lista med prenumerationer i Azure-portalen.

![Skärmbild som visar listan med Azure-prenumerationer för faktureringsprofilen](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtrera kolumnen **subscriptionName** i CSV-filen med Azure-användning och avgifter efter **WA_Subscription** så att du ser detaljerade användningsavgifter för WA_Subscription.

![Skärmbild som visar filen med användning och avgifter per prenumeration](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Betala din faktura

Du hittar betalningsanvisningar längst ned på fakturan. [Läs om hur du betalar](billing-mca-understand-your-invoice.md#how-to-pay).

Om du redan har betalat din faktura kan du kontrollera betalningens status på fakturasidan i Azure-portalen.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om din faktura och detaljerad användning finns i:

- [Så här hämtar du information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Förstå termerna på fakturan för ditt Microsoft-kundavtal](billing-mca-understand-your-invoice.md)
- [Förstå termerna i CSV-filen för användning inom ditt Microsoft-kundavtal](billing-mca-understand-your-usage.md)
