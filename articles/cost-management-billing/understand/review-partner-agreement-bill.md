---
title: Granska ditt Microsoft partner avtals faktura – Azure
description: Lär dig hur du granskar din fakturerings-och resursanvändning och hur du kontrollerar debiteringar för din prenumeration på Microsoft partner avtal.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: e3823a9eb2e0713f7f42e4e02808ef957efc5944
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990845"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Självstudie: granska din Microsoft partner Agreement-faktura

 I faktureringskontot för ett Microsoft-partneravtal genereras en faktura varje månad för varje faktureringsprofil. Fakturan innehåller alla kundavgifter från föregående månad. Du kan få bättre förståelse för avgifterna på din faktura genom att analysera de enskilda transaktionerna i Azure-portalen. Du kan också visa dina fakturor i Azure Portal och jämföra avgifterna med användnings detalj filen.

Du kan läsa mer i [Ladda ned fakturor från Azure-portalen](download-azure-invoice.md).

Den här självstudien gäller endast för Azure-partner med ett Microsoft partner avtal.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Granska fakturerade transaktioner i Azure Portal
> * Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura
> * Analysera avgifterna för din Azure-användning

## <a name="prerequisites"></a>Krav

Du måste ha åtkomst till ett fakturerings konto för ett Microsoft-partner avtal.

Det måste vara mer än 30 dagar från den dag som du prenumererar på Azure. Azure debiterar dig i slutet av din fakturaperiod.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal

Kontrollera avtalstypen för att avgöra om du har åtkomst till ett faktureringskonto för ett Microsoft-partneravtal.

I Azure Portal skriver du *Cost Management + debitering* i rutan Sök och väljer sedan **Cost Management + fakturering**.

![Skärmbild som visar en sökning i Azure-portalen](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Om du bara har åtkomst till ett faktureringsomfång väljer du **Egenskaper** till vänster. Du har åtkomst till ett faktureringskonto för ett Microsoft-partneravtal om faktureringskontotypen är **Microsoft-partneravtal**.

![Skärm bild som visar Microsoft partner Agreement på sidan Egenskaper](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Om du har åtkomst till flera faktureringsomfång kontrollerar du typen i kolumnen Faktureringskonto. Du har åtkomst till ett faktureringskonto för ett Microsoft-partneravtal om faktureringskontotypen för något av omfången är **Microsoft-partneravtal**.

![Skärm bild som visar Microsoft partner Agreement på sidan för debiterings konto lista](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Granska fakturerade transaktioner i Azure Portal

I Cost Management + fakturering väljer du **alla transaktioner** på sidans vänstra sida. Beroende på din åtkomst kan du behöva välja ett faktureringskonto, en faktureringsprofil eller en kund och sedan välja **Alla transaktioner**.

På sidan Alla transaktioner visas följande information:

![Skärmbild som visar en lista med fakturerade transaktioner](./media/review-partner-agreement-bill/all-transactions.png)

|Kolumn  |Definition  |
|---------|---------|
|Datum     | Datum för transaktionen  |
|Faktura-ID     | ID:t för fakturan som transaktionen debiterats till. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda ärendet |
|Transaktionstyp     |  Transaktionstypen, till exempel inköp, uppsägning eller användning  |
|Produktfamilj     | Produktkategorin, till exempel beräkning för virtuella datorer eller databas för Azure SQL-databaser|
|Produkt SDKU     | En unik kod som identifierar instansen av produkten |
|Mängd     |  Transaktionsbeloppet      |
|Faktureringsprofil     | Transaktionen visas på den här faktureringsprofilens faktura |

Sök efter ett faktura-ID för att filtrera transaktionerna för fakturan.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura

Avgifterna är uppskattningar och betraktas som väntande tills de faktureras. Du kan visa väntande avgifter för faktureringsprofilen för ditt Microsoft-partneravtal i Azure-portalen så att du får en uppfattning om nästa faktura. Väntande avgifter är uppskattningar och inkluderar inte skatt. De faktiska kostnaderna på nästa faktura behöver inte matcha de väntande avgifterna.

### <a name="view-pending-transactions"></a>Visa väntande transaktioner

När du identifierar väntande avgifter kan du få bättre förståelse för kostnaderna genom att analysera de enskilda transaktioner som har bidragit till avgifterna. För närvarande visas inte väntande användningsavgifter på sidan Alla transaktioner. Du kan visa väntande användningsavgifter på sidan för Azure-prenumerationen.

I Cost Management + fakturering väljer du en fakturerings profil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

Välj **Alla transaktioner** till vänster på sidan.

Sök efter *väntande*. Använd filtret **Tidsintervall** till att visa väntande avgifter för aktuell eller föregående månad.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Visa väntande avgifter per kund

I Cost Management + fakturering väljer du en fakturerings profil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

Välj **Kunder** till vänster på sidan.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

På sidan Kunder ser du den aktuella månadens och föregående månads avgifter för respektive kund som är associerad med faktureringsprofilen. Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande.

### <a name="view-pending-usage-charges"></a>Visa väntande användningsavgifter

I Cost Management + fakturering väljer du en fakturerings profil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

Välj **Azure-prenumerationer** till vänster på sidan. På sidan för Azure-prenumerationen ser du den aktuella månadens och föregående månads avgifter för respektive prenumeration i faktureringsprofilen. Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analysera avgifterna för din Azure-användning

Använd CSV-filen med Azure-användning och avgifter när du ska analysera dina användningsavgifter. Du kan filtrera filen med Azure-användning och kostnader så att du ser användningsavgifterna för varje produkt i fakturafilen. Om du vill visa detaljerade användningskostnader för en viss produkt filtrerar du kolumnen **product** i csv-filen med Azure-användning och kostnader så att du bara ser det aktuella produktnamnet.

Du kan också filtrera kolumnen **customerName** i csv-filen med Azure-användning och kostnader så att du ser dagliga användningskostnader för var och en av dina kunder. Om du vill visa dagliga användningskostnader per Azure-prenumeration filtrerar du kolumnen **subscriptionName**.

## <a name="pay-your-bill"></a>Betala din faktura

Du hittar betalningsanvisningar längst ned på fakturan. Du kan betala med check eller banköverföring inom 60 dagar från fakturadatumet.

Om du redan har betalat din faktura kan du kontrollera betalningens status på fakturasidan i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Granska fakturerade transaktioner i Azure Portal
> * Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura
> * Analysera avgifterna för din Azure-användning

Lär dig mer om att använda Azure Cost Management för partner.

> [!div class="nextstepaction"]
> [Kom igång med Azure Cost Management för partner](../costs/get-started-partners.md)
