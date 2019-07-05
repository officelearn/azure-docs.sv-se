---
title: Förstå debiteringar på ditt Microsoft kundavtal faktura - Azure
description: Lär dig mer om att läsa och förstå debiteringar på fakturan.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490681"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Förstå debiteringar på fakturan Microsoft kundavtal

Du kan förstå debiteringar på fakturan genom att analysera enskilda transaktioner. I faktureringskonto för en Microsoft-kundavtal skapas en faktura varje månad för varje fakturering profil. Fakturan omfattar alla avgifter från föregående månad. Du kan visa dina fakturor i Azure-portalen. Mer information finns i [ladda ner fakturor för en Microsoft-kundavtal](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Visa transaktioner för en faktura i Azure portal

1. Logga in på [Azure Portal](https://www.azure.com).

2. Sök efter **Cost Management + fakturering**.

    ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj **alla transaktioner** till vänster på sidan. Beroende på din åtkomst, du kan behöva välja ett faktureringskonto, fakturering profil eller en faktura-avsnittet och välj sedan **alla transaktioner**.

4. Sidan alla transaktioner visar följande information:

    ![Skärmbild som visar listan faktureras transaktioner](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Kolumn  |Definition  |
    |---------|---------|
    |Date     | Datum för transaktionen  |
    |Faktura-ID     | Identifierare för fakturan som transaktionen har faktureras. Om du skickar en supportförfrågan, dela det ID: T med Azure-supporten påskynda din supportbegäran |
    |Transaktionstyp     |  Vilken typ av transaktion som avgifter för inköp, Avbryt och användning  |
    |Produktfamilj     | Produktkategori som beräkning för virtuella datorer eller databas för Azure SQL-databas|
    |Produkt-sku     | En unik kod som identifierar instansen av din produkt |
    |Mängd     |  Beloppet för transaktion      |
    |Faktura-avsnitt     | Transaktionen som visas i det här avsnittet av fakturering profilens faktura |
    |Fakturering profil     | Transaktionen som visas i den här fakturering profilen faktura |

5. Sök efter faktura-ID att filtrera transaktioner för fakturan.

### <a name="view-transactions-by-invoice-sections"></a>Visa transaktioner med faktura avsnitt

Faktura avsnitt kan du ordna kostnaderna för en fakturering profil faktura. Mer information finns i [förstå fakturan avsnittet](billing-mca-overview.md#invoice-sections). När en faktura skapas, visas avgifter för alla avsnitt i fakturerings-profilen på fakturan.

Följande bild visar avgifter för avsnittet Redovisning Avd fakturan på en exempel-faktura.

![Exempelbild som visar information av avsnittet fakturainformation](./media/billing-understand-your-bill-mca/invoicesection-details.png)

När du har identifierat avgifterna för en faktura-avsnittet, kan du visa transaktioner i Azure portal för att förstå debiteringar.

1. Gå till sidan alla transaktioner i Azure portal för att visa transaktioner för en faktura. Mer information finns i [Visa transaktioner för en faktura i Azure-portalen](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtrera efter namn på avsnittet faktura Visa transaktioner.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Granska väntande avgifter för att uppskatta ditt nästa faktura

I faktureringskonto för en Microsoft-kundavtal uppskattade och anses vara väntande tills de har fakturerats avgifter. Du kan visa väntande kostnader på Azure portal för att beräkna din nästa faktura. Väntande kostnader är uppskattade och de inkluderar inte skatt. De faktiska kostnader på din nästa faktura varierar från de väntande kostnaderna.

### <a name="view-summary-of-pending-charges"></a>Visa en sammanfattning av väntande avgifter

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto beroende på din åtkomst. Faktureringskonto, Välj **fakturering profiler** väljer en profil för fakturering.

4. Välj **sammanfattning** fliken högst upp på skärmen.

5. I avsnittet debiteringar Visa månad-till-date och avgifter förra månaden.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Månad-till-date-avgifter är väntande avgifter för den aktuella månaden och faktureras när fakturan skapas för månaden. Om fakturan för senaste månaden genereras fortfarande inte så avgifter förra månaden är också väntande och visas på din nästa faktura.

### <a name="view-pending-transactions"></a>Visa väntande transaktioner

När du har identifierat väntande avgifter kan förstå du kostnaderna genom att analysera de enskilda transaktioner som ingår avgifterna. Väntande användning visas inte nu avgifter på sidan alla transaktioner. Du kan visa de väntande användningskostnader på sidan för Azure-prenumerationer. Mer information finns i [vyn väntande avgifter för användning](#view-pending-usage-charges)

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto beroende på din åtkomst. Faktureringskonto, Välj **fakturering profiler** väljer en profil för fakturering.

4. Välj **alla transaktioner** till vänster på sidan.

5. Sök efter *väntande*. Använd den **Timespan** filter för att visa väntande avgifter för aktuella eller senaste månaden.

   ![Skärmbild som visar listan över väntande transaktioner](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Visa väntande användningsdebitering

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter *Cost Management + fakturering*.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto beroende på din åtkomst. Faktureringskonto, Välj **fakturering profiler** väljer en profil för fakturering.

4. Välj **alla prenumerationer** något till vänster på sidan.

5. Azure-prenumerationssidan visar förra månadens avgifter för varje prenumeration och aktuellt i profilen för fakturering. Månad-till-date-avgifter är väntande avgifter för den aktuella månaden och faktureras när fakturan skapas för månaden. Om fakturan för senaste månaden genereras fortfarande inte så avgifter förra månaden är också väntande.

    ![Skärmbild som visar listan över Azure-prenumerationer för fakturering profil](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analysera användningen av Azure-avgifter

Använd Azure användning och avgifter CSV-filen för att analysera dina användningsbaserade avgifter. Du kan ladda ned filen för en faktura eller för väntande avgifter. Mer information finns i [hämta din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Se detaljerad användningsinformation faktura avsnittet

Du kan filtrera filen Azure användning och avgifter för att stämma av användningskostnader för faktura-avsnitt.

Följande steg beskriver hur du stämma av beräkningsrelaterade avgifter för avsnittet Redovisning Avd faktura:

![Exempelbild som visar information av avsnittet fakturainformation](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Faktura PDF | Azure-användning och kostnader CSV |
 | --- | --- |
 |Redovisning Avd |invoiceSectionName |
 |Avgifter för användning – Microsoft Azure-Plan |productOrderName |
 |Compute |serviceFamily |

1. Filter i **invoiceSectionName** kolumn i CSV-filen till **redovisning Avd**.
2. Filter i **productOrderName** kolumn i CSV-filen till **Microsoft Azure planera**.
3. Filter i **serviceFamily** kolumn i CSV-filen till **Microsoft.Compute**.

![Skärmbild som visar användningen och debiterar filen filtreras efter faktura avsnittet](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Se detaljerad användningsinformation per prenumeration

Du kan filtrera Azure användning och avgifter CSV-filen för att stämma av användningskostnader för dina prenumerationer. Om du vill visa alla prenumerationer i en profil för fakturering, se [vyn väntande användningskostnader](#view-pending-usage-charges).

När du identifiera kostnader för en prenumeration kan du använda Azure användning och avgifter CSV-filen för att analysera kostnaderna.

Följande bild visar en lista över prenumerationer i Azure-portalen.

![Skärmbild som visar listan över Azure-prenumerationer för fakturering profil](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filter i **subscriptionName** kolumn i Azure användning och avgifter CSV-filen till **WA_Subscription** att visa användningsdebitering för WA_Subscription.

![Skärmbild som visar användningen och debiterar filen filtreras efter prenumeration](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Betala fakturan

Anvisningar för att betala fakturan visas längst ned på fakturan. [Lär dig att betala](billing-mca-understand-your-invoice.md#how-to-pay).

Om du har redan betalat fakturan, kan du kontrollera status för betalning sidan fakturor i Azure-portalen.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om din faktura och detaljerad användning, se:

- [Så här hämtar du information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Förstå villkoren på din faktura för Microsoft kundavtal](billing-mca-understand-your-invoice.md)
- [Förstå villkoren på din Microsoft kundavtal användning CSV](billing-mca-understand-your-usage.md)
