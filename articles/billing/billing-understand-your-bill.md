---
title: Förstå Azure-fakturan
description: Lär dig hur du läser och förstår din användning och faktura för din Azure-prenumeration.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 69659e700ee47c8961ec21f0fb23573e13d72588
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383626"
---
# <a name="understand-your-microsoft-azure-bill"></a>Förstå din Microsoft Azure faktura
För att förstå din Azure-faktura, jämför du din faktura med den detaljerade dagliga användnings filen och med Cost Management-rapporter i Azure Portal.

Den här artikeln gäller inte för Azure-kunder med en Enterprise-avtal (EA-kunder). Om du är EA-kund kan du läsa [förstå din faktura för Azure-kunder med en Enterprise-avtal](billing-understand-your-bill-ea.md).

Den här artikeln gäller inte för Azure-kunder med ett [kund avtal från Microsoft](#check-access-to-a-microsoft-customer-agreement). Om du har ett Microsoft-kundavtal kan du läsa mer i ta [reda på Azure-avgifterna på din Microsoft-kund avtals faktura](billing-mca-understand-your-bill.md).

En förklaring av hur faktureringen fungerar i Azure CSP-programmet (Cloud Solution Provider), inklusive fakturerings cykel, prissättning och användning, finns i [Översikt över Azure CSP-fakturering](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Granska dina avgifter

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Om du har en avgift på din faktura som du vill ha mer information om kan du jämföra användning och kostnader med användnings filen eller med Azure Portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Alternativ 1: Jämför användning och kostnader med användnings filen

I den detaljerade användningen CSV-filen visas dina avgifter per fakturerings period och daglig användning. Information om hur du hämtar eller visar filen finns i [Hämta fakturerings faktura för Azure och dagliga användnings data](billing-download-azure-invoice-daily-usage-date.md).

Dina användnings kostnader visas på mätar nivån. Följande villkor innebär samma sak i både fakturan och den detaljerade användnings filen. Fakturerings perioden på fakturan är till exempel samma som den fakturerings period som visas i den detaljerade användnings filen.

 | Faktura (PDF) | Detaljerad användning (CSV)|
 | --- | --- |
|Faktureringscykel | Faktureringsperiod |
 |Namn |Mätarkategori |
 |type |Mätar under kategori |
 |Resource |Mätarnamn |
 |Region |Region för mätning |
 |Förbrukad |Använd kvantitet |
 |Ingår |Inkluderad kvantitet |
 |Faktureringsbar |Överskott |

I avsnittet **användnings avgifter** på fakturan visas det totala värdet för varje mätare som förbrukades under fakturerings perioden. Följande bild visar till exempel en användnings avgift för tjänsten Azure Scheduler.

![Faktura användnings kostnader](./media/billing-understand-your-bill/1.png)

I avsnittet **instruktion** i din detaljerade användnings CSV visas samma avgift. Både den  förbrukade mängden och *värdet* matchar fakturan.

![Användnings kostnader för CSV](./media/billing-understand-your-bill/2.png)

Om du vill se en dags uppdelning av avgiften går du till avsnittet **daglig användning** i CSV-filen. Filtrera efter *Scheduler* under *mätar kategori*. Du kan se vilka dagar mätaren användes och hur mycket som förbrukades. *Resurs* -och *resurs grupps* informationen visas också för jämförelse. De  förbrukade värdena ska läggas till och matcha vad som visas på fakturan.

![Avsnittet daglig användning i CSV-filen](./media/billing-understand-your-bill/3.png)

Om du vill få kostnaden per dag multiplicerar  du de förbrukade beloppen med värdet *rate* i avsnittet **sats** .

Du kan läsa mer här:

- [Förstå din Azure-faktura](billing-understand-your-invoice.md)
- [Förstå din Azure-detaljerade användning](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Alternativ 2: Jämför användning och kostnader i Azure Portal

Azure Portal kan också hjälpa dig att verifiera dina avgifter. Visa kostnads hanterings diagram för att få en snabb överblick över din fakturerade användning och dina kostnader.

1. I Azure Portal går du till [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj din prenumeration > **kostnads analys**.
1. Filtrera efter **TimeSpan**.
1. Om du vill fortsätta med det tidigare exemplet visas en användnings avgift för tjänsten Azure Scheduler.

   ![Vyn kostnads analys i Azure Portal](./media/billing-understand-your-bill/4.png)

1. Välj raden som visar avgiften för att se den dagliga kostnads uppdelningen.

   ![Vyn kostnads historik i Azure Portal](./media/billing-understand-your-bill/5.png)

Mer information finns i [förhindra oväntade kostnader med fakturering och kostnads hantering i Azure](billing-getting-started.md#costs).

## <a name="external"></a>Externa tjänster debiteras separat

Externa tjänster, eller Marketplace-avgifter, är för resurser som har skapats av program leverantörer från tredje part. Dessa resurser är tillgängliga för användning från Azure Marketplace. En Barracuda-brandvägg är till exempel en Azure Marketplace-resurs som erbjuds av en tredje part. Alla avgifter för brand väggen och dess motsvarande mätare visas som externa tjänst avgifter.

Avgifter för externa tjänster debiteras separat. Avgifterna visas inte på din Azure-faktura. Mer information finns i [förstå dina avgifter för Azures externa tjänster](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Resurser som debiteras per användnings mätare

Azure faktureras inte direkt baserat på resurs kostnaden. Avgifter för en resurs beräknas med hjälp av en eller flera mätare. Mätare används för att spåra användningen av en resurs under hela sin livs längd. Dessa mätare används sedan för att beräkna fakturan.

När du till exempel skapar en enda Azure-resurs, t. ex. en virtuell dator, har en eller flera mätnings instanser skapats. Mätare används för att spåra användningen av resursen över tid. Varje mätning avger användnings poster som används av Azure för att beräkna fakturan.

Till exempel kan en enskild virtuell dator (VM) som skapats i Azure ha följande mätare som skapats för att spåra användningen:

- Beräkningstimmar
- IP-adresstimmar
- Dataöverföring in
- Dataöverföring ut
- Standard-hanterad disk
- Standard – åtgärder för hanterad disk
- Standard-i/o-disk
- Standard IO – Block-Blob Read
- Standard i/o-skrivning av BLOB
- Standard IO-Block Blob Delete

När den virtuella datorn skapas börjar varje mätning att sända användnings poster. Den här användningen och mätar priset spåras i Azure-mätar systemet.

## <a name="payment"></a>Betala din faktura

Om du skapar ett kredit korts kort som betalnings metod debiteras betalningen automatiskt inom 10 dagar efter att fakturerings perioden är slut. På ditt kredit korts utdrag skulle rad objektet säga **MSFT Azure**.

Information om hur du ändrar det kredit kort som debiteras finns i [lägga till, uppdatera eller ta bort ett kredit kort för Azure](billing-how-to-change-credit-card.md).

Om du [betalar per faktura](billing-how-to-pay-by-invoice.md)skickar du betalningen till den plats som anges längst ned på fakturan.

Om du vill kontrol lera statusen för din betalning [skapar du ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tips för kostnads hantering

- Beräkna kostnader med hjälp av:
  - [Pris kalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Total kostnad för ägar kalkylatorn](https://aka.ms/azure-tco-calculator)
  - [Detaljerad pris information för varje tjänst](https://azure.microsoft.com/pricing/)
- [Granska användning och kostnader regelbundet i Azure Portal](billing-getting-started.md#costs).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrol lera åtkomsten till ett Microsofts kund avtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Lär dig mer

- [Hämta din fakturerings faktura för Azure och dagliga användnings data](billing-download-azure-invoice-daily-usage-date.md)
- [Förstå villkoren på din Microsoft Azure faktura](billing-understand-your-invoice.md)
- [Förstå villkoren i Microsoft Azure detaljerad användning](billing-understand-your-usage.md)
- [Azure Portal kostnads hantering](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Förhindra oväntade kostnader med fakturering och kostnads hantering i Azure](billing-getting-started.md#costs)
