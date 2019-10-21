---
title: Förstå avgifterna på fakturan för ditt Microsoft-partneravtal – Azure
description: Lär dig hur du läser och förstår avgifterna på din faktura.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 579a052f8bde780ac33de85c5a08d9b3e79d3096
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515763"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Förstå avgifterna på fakturan för ditt Microsoft-partneravtal

 I faktureringskontot för ett Microsoft-partneravtal genereras en faktura varje månad för varje faktureringsprofil. Fakturan innehåller alla kundavgifter från föregående månad. Du kan få bättre förståelse för avgifterna på din faktura genom att analysera de enskilda transaktionerna i Azure-portalen. Du kan också visa dina fakturor i Azure-portalen. Du kan läsa mer i [Ladda ned fakturor från Azure-portalen](billing-download-azure-invoice.md).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-partneravtal. [Kontrollera om du har åtkomst till ett Microsoft-partneravtal](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Visa transaktioner för en faktura i Azure-portalen

1. Logga in på [Azure-portalen](https://www.azure.com).

2. Sök efter *Kostnadshantering + fakturering*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Välj **Alla transaktioner** till vänster på sidan. Beroende på din åtkomst kan du behöva välja ett faktureringskonto, en faktureringsprofil eller en kund och sedan välja **Alla transaktioner**.

4. På sidan Alla transaktioner visas följande information:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Kolumn  |Definition  |
    |---------|---------|
    |Date     | Datum för transaktionen  |
    |Faktura-ID     | ID:t för fakturan som transaktionen debiterats till. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda ärendet |
    |Transaktionstyp     |  Transaktionstypen, till exempel inköp, uppsägning eller användning  |
    |Produktfamilj     | Produktkategorin, till exempel beräkning för virtuella datorer eller databas för Azure SQL-databaser|
    |Produkt-SKU     | En unik kod som identifierar instansen av produkten |
    |Belopp     |  Transaktionsbeloppet      |
    |Faktureringsprofil     | Transaktionen visas på den här faktureringsprofilens faktura |

5. Sök efter faktura-ID:t om du vill filtrera ut fakturans transaktioner.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura

Avgifterna är uppskattningar och betraktas som väntande tills de faktureras. Du kan visa väntande avgifter för faktureringsprofilen för ditt Microsoft-partneravtal i Azure-portalen så att du får en uppfattning om nästa faktura. Väntande avgifter är uppskattningar och inkluderar inte skatt. De faktiska kostnaderna på nästa faktura behöver inte matcha de väntande avgifterna.

### <a name="view-pending-transactions"></a>Visa väntande transaktioner

När du identifierar väntande avgifter kan du få bättre förståelse för kostnaderna genom att analysera de enskilda transaktioner som har bidragit till avgifterna. För närvarande visas inte väntande användningsavgifter på sidan Alla transaktioner. Du kan visa väntande användningsavgifter på sidan för Azure-prenumerationen. Mer information finns i [Visa väntande användningsavgifter](#view-pending-usage-charges)

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter *Kostnadshantering + fakturering*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

4. Välj **Alla transaktioner** till vänster på sidan.

5. Sök efter *väntande*. Använd filtret **Tidsintervall** till att visa väntande avgifter för aktuell eller föregående månad.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Visa väntande avgifter per kund

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter *Kostnadshantering + fakturering*.

3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

4. Välj **Kunder** till vänster på sidan.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. På sidan Kunder ser du den aktuella månadens och föregående månads avgifter för respektive kund som är associerad med faktureringsprofilen. Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande.

### <a name="view-pending-usage-charges"></a>Visa väntande användningsavgifter

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter *Kostnadshantering + fakturering*.

3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

4. Välj **Azure-prenumerationer** till vänster på sidan.

5. På sidan för Azure-prenumerationen ser du den aktuella månadens och föregående månads avgifter för respektive prenumeration i faktureringsprofilen. Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analysera avgifterna för din Azure-användning

Använd CSV-filen med Azure-användning och avgifter när du ska analysera dina användningsavgifter. Du kan [ladda ned en csv-fil med din Azure-användning och dina kostnader från Azure-portalen](billing-download-azure-daily-usage.md).

Du kan filtrera filen med Azure-användning och kostnader så att du ser användningsavgifterna för varje produkt i fakturafilen. Om du vill visa detaljerade användningskostnader för en viss produkt filtrerar du kolumnen **product** i csv-filen med Azure-användning och kostnader så att du bara ser det aktuella produktnamnet.

Du kan också filtrera kolumnen **customerName** i csv-filen med Azure-användning och kostnader så att du ser dagliga användningskostnader för var och en av dina kunder. Om du vill visa dagliga användningskostnader per Azure-prenumeration filtrerar du kolumnen **subscriptionName**.


## <a name="pay-your-bill"></a>Betala din faktura

Du hittar betalningsanvisningar längst ned på fakturan. Du kan betala med check eller banköverföring inom 60 dagar från fakturadatumet.

Om du redan har betalat din faktura kan du kontrollera betalningens status på fakturasidan i Azure-portalen.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrollera åtkomsten till ett Microsoft-partneravtal
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om din faktura och detaljerad användning finns i:
