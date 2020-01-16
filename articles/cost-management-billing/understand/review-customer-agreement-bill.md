---
title: Granska Microsofts kund avtals faktura – Azure
description: Lär dig hur du granskar din fakturerings-och resursanvändning och hur du kontrollerar debiteringar för din kund avtals faktura från Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987070"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Självstudie: granska din kund avtals faktura från Microsoft

Du kan granska avgifterna på din faktura genom att analysera de enskilda transaktionerna. I faktureringskontot för ett Microsoft-kundavtal genereras en faktura varje månad för varje faktureringsprofil. Fakturan innehåller alla avgifter från föregående månad. Du kan visa dina fakturor i Azure Portal och jämföra avgifterna med användnings detalj filen.

Den här självstudien gäller endast för Azure-kunder med ett kund avtal för Microsoft.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Granska fakturerade transaktioner i Azure Portal
> * Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura
> * Analysera avgifterna för din Azure-användning

## <a name="prerequisites"></a>Krav

Du måste ha ett fakturerings konto för ett Microsofts kund avtal.

Du måste ha åtkomst till ett Microsofts kund avtal. Du måste vara ägare till fakturerings profil, deltagare, läsare eller faktura hanterare för att kunna visa information om fakturering och användning. Du kan läsa mer om faktureringsroller för Microsoft-kundavtal i [Roller och uppgifter i faktureringsprofiler](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Det måste vara mer än 30 dagar från den dag som du prenumererar på Azure. Azure debiterar dig i slutet av din fakturaperiod.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal

Kontrollera avtalstypen för att avgöra om du har åtkomst till ett faktureringskonto för ett Microsoft-kundavtal.

I Azure Portal skriver du *Cost Management + debitering* i rutan Sök och väljer sedan **Cost Management + fakturering**.

![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Om du bara har åtkomst till ett faktureringsomfång väljer du **Egenskaper** till vänster. Du har åtkomst till ett faktureringskonto för ett Microsoft-kundavtal om faktureringskontotypen är **Microsoft Customer Agreement**.

![Skärm bild som visar Microsofts kund avtal på sidan Egenskaper](./media/review-customer-agreement-bill/billing-mca-property.png)

Om du har åtkomst till flera faktureringsomfång kontrollerar du typen i kolumnen Faktureringskonto. Du har åtkomst till ett faktureringskonto för ett Microsoft-kundavtal om faktureringskontotypen för något av omfången är **Microsoft Customer Agreement**.

![Skärm bild som visar Microsofts kund avtal i list sidan för debiterings konto](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Granska fakturerade transaktioner i Azure Portal

I Azure Portal väljer du **alla transaktioner** från vänster sida av sidan. Beroende på din åtkomst kan du behöva välja ett faktureringskonto, en faktureringsprofil eller ett fakturaavsnitt och sedan välja **Alla transaktioner**.

På sidan Alla transaktioner visas följande information:

![Skärmbild som visar en lista med fakturerade transaktioner](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Kolumn  |Definition  |
|---------|---------|
|Datum     | Datum för transaktionen  |
|Faktura-ID     | ID:t för fakturan som transaktionen debiterats till. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda ärendet |
|Transaktionstyp     |  Transaktionstypen, till exempel inköp, uppsägning eller användning  |
|Produktfamilj     | Produktkategorin, till exempel beräkning för virtuella datorer eller databas för Azure SQL-databaser|
|Produkt-SKU     | En unik kod som identifierar instansen av produkten |
|Mängd     |  Transaktionsbeloppet      |
|Fakturaavsnitt     | Transaktionen visas i det här avsnittet av faktureringsprofilens faktura |
|Faktureringsprofil     | Transaktionen visas på den här faktureringsprofilens faktura |

Sök efter ett faktura-ID för att filtrera transaktionerna för fakturan.

### <a name="view-transactions-by-invoice-sections"></a>Visa transaktioner efter fakturaavsnitt

Med fakturaavsnitt kan du organisera kostnaderna på en faktureringsprofils faktura. För mer information. När en faktura skapas så visas avgifter för alla avsnitt i faktureringsprofilen på fakturan.

I följande bild ser du avgifter för fakturaavsnittet Accounting Dept på en exempelfaktura.

![Exempelbild som visar information efter fakturaavsnitt](./media/review-customer-agreement-bill/invoicesection-details.png)

När du har identifierat avgifterna för ett fakturaavsnitt kan du få bättre förståelse för transaktionerna i Azure-portalen.

Gå till sidan Alla transaktioner i Azure-portalen om du vill visa transaktionerna för en faktura.

Visa transaktioner genom att filtrera efter fakturaavsnitt.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura

I fakturerings kontot för ett Microsoft-kundavtal beräknas avgifterna och betraktas som väntande tills de faktureras. Du kan visa väntande avgifter i Azure-portalen om du vill göra en uppskattning av nästa faktura. Väntande avgifter är uppskattningar och inkluderar inte skatt. De faktiska kostnaderna på nästa faktura behöver inte matcha de väntande avgifterna.

### <a name="view-summary-of-pending-charges"></a>Sammanfattning av väntande avgifter

Logga in på [Azure-portalen](https://portal.azure.com).

Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

Välj fliken **Sammanfattning** överst på skärmen.

I avsnittet med avgifter visas avgifterna hittills under månaden och för föregående månad.

![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande och tas upp på nästa faktura.

### <a name="view-pending-transactions"></a>Visa väntande transaktioner

När du identifierar väntande avgifter kan du få bättre förståelse för kostnaderna genom att analysera de enskilda transaktioner som har bidragit till avgifterna. För närvarande visas inte väntande användningsavgifter på sidan Alla transaktioner. Du kan visa väntande användningsavgifter på sidan för Azure-prenumerationen.

Logga in på [Azure-portalen](https://portal.azure.com).

I Azure Portal skriver du *Cost Management + debitering* i rutan Sök och väljer sedan **Cost Management + fakturering**.

Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

Välj **Alla transaktioner** till vänster på sidan.

Sök efter *väntande*. Använd filtret **Tidsintervall** till att visa väntande avgifter för aktuell eller föregående månad.

![Skärmbild som visar listan med väntande transaktioner](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Visa väntande användningsavgifter

Logga in på [Azure-portalen](https://portal.azure.com).

I Azure Portal skriver du *Cost Management + debitering* i rutan Sök och väljer sedan **Cost Management + fakturering**.

Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan en faktureringsprofil.

Välj **Alla prenumerationer** till vänster på sidan.

På sidan för Azure-prenumerationen ser du den aktuella månadens och föregående månads avgifter för respektive prenumeration i faktureringsprofilen. Avgifterna hittills under månaden är de väntande avgifterna för den aktuella månaden som debiteras när månadens faktura skapas. Om fakturan för föregående månad fortfarande inte har skapats så är föregående månads avgifter också väntande.

![Skärmbild som visar listan med Azure-prenumerationer för faktureringsprofilen](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analysera avgifterna för din Azure-användning

Använd CSV-filen med Azure-användning och avgifter när du ska analysera dina användningsavgifter. Du kan ladda ned filen antingen för en faktura eller för väntande avgifter.

### <a name="download-your-invoice-and-usage-details"></a>Ladda ned din faktura och användnings information

Beroende på åtkomst kan du behöva välja ett fakturerings konto eller en fakturerings profil i Cost Management + fakturering. Välj **Fakturor** under **Fakturering** i den vänstra menyn. Leta rätt på raden med fakturan du vill ladda ned i rutnätet. Klicka på nedladdnings symbolen eller ellipsen (...) i slutet av raden. I **hämtnings** rutan laddar du ned filen med användnings information och faktura.

### <a name="view-detailed-usage-by-invoice-section"></a>Visa detaljerad användning per fakturaavsnitt

Du kan filtrera filen med Azure-användning och avgifter så att du ser användningsavgifterna för dina olika fakturaavsnitt.

Följande information vägleder dig genom att stämma av beräknings kostnader för avsnittet redovisning av redovisning:

![Exempelbild som visar information efter fakturaavsnitt](./media/review-customer-agreement-bill/invoicesection-details.png)

| Faktura-PDF | CSV med Azure-användning och avgifter |
| --- | --- |
|Accounting Dept |invoiceSectionName |
|Användningsavgifter – Microsoft Azure Plan |productOrderName |
|Databearbetning |serviceFamily |

Filtrera **invoiceSectionName** -kolumnen i CSV-filen till **ekonomi avd**. Filtrera sedan kolumnen **productOrderName** i CSV-filen för att **Microsoft Azure planen**. Filtrera sedan kolumnen **serviceFamily** i CSV-filen till **Microsoft. Compute**.

![Skärmbild som visar filen med användning och avgifter per fakturaavsnitt](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Visa detaljerad användning per prenumeration

Du kan filtrera CSV-filen med Azure-användning och avgifter för att stämma av användningsavgifterna för dina olika prenumerationer. När du ska identifiera avgifter för en prenumeration analyserar du kostnaderna i CSV-filen med Azure-användning och avgifter.

I följande bild ser du en lista med prenumerationer i Azure-portalen.

![Skärmbild som visar listan med Azure-prenumerationer för faktureringsprofilen](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filtrera kolumnen **subscriptionName** i CSV-filen med Azure-användning och avgifter efter **WA_Subscription** så att du ser detaljerade användningsavgifter för WA_Subscription.

![Skärmbild som visar filen med användning och avgifter per prenumeration](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Betala din faktura

Du hittar betalningsanvisningar längst ned på fakturan. [Läs om hur du betalar](mca-understand-your-invoice.md#how-to-pay).

Om du redan har betalat din faktura kan du kontrollera betalningens status på fakturasidan i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Granska fakturerade transaktioner i Azure Portal
> * Granska väntande avgifter så att du kan göra en uppskattning av nästa faktura
> * Analysera avgifterna för din Azure-användning

Börja använda kostnads analys genom att slutföra snabb starten.

> [!div class="nextstepaction"]
> [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md)
