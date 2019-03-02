---
title: Förstå Azure-fakturan | Microsoft Docs
description: Lär dig att läsa och förstå din användningen och fakturan för din Azure-prenumeration
services: ''
documentationcenter: ''
author: tonguyen10
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: ac36c76e8605df0fee3e39341c8be0fef7e58ddf
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246604"
---
# <a name="understand-your-microsoft-azure-bill"></a>Förstå fakturan för Microsoft Azure
Jämför din faktura med detaljerade dagliga användningsfil och kostnadshanteringsrapporter i Azure-portalen för att förstå fakturan för Azure.

Den här artikeln gäller inte för Azure-kunder med ett Enterprise-avtal (EA-kunder). Om du är en EA-kund, se [förstå fakturan för Azure-kunder med ett Enterprise Agreement](billing-understand-your-bill-ea.md).

Den här artikeln gäller inte för Azure-kunder med ett Microsoft-kundavtal. Om du har något faktureringskonto för en Microsoft-kundavtal Se [förstå Azure-avgifter på fakturan Microsoft kundavtal](billing-mca-understand-your-bill.md).

En beskrivning av hur fakturering fungerar i Azure Cloud Solution Provider (Azure CSP)-program, inklusive fakturering cykel, priser och användning, se [fakturering översikt över Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Granska dina kostnader

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Om det finns en avgift på fakturan som du vill ha mer information om, kan du jämföra användning och kostnader med användningsfilen eller med Azure-portalen.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Alternativ 1: Jämför användning och kostnader med användningsfil

Detaljerad användning CSV-filen visar dina kostnader per faktureringsperiod och daglig användning. Filen finns [hämta din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md).

Avgifter för användning visas på nivån mätaren. Följande villkor betyda samma sak i både fakturan och detaljerad användningsfil. Till exempel är faktureringsperiod på fakturan samma som den faktureringsperioden som visas i filen detaljerad användning.

 | Faktura (PDF) | Detaljerad användning (CSV)|
 | --- | --- |
|Faktureringscykel | Faktureringsperiod |
 |Namn |Mätarkategori |
 |Type |Mätarunderkategori |
 |Resurs |Mätarnamn |
 |Region |Mätarregion |
 |Förbrukad |Förbrukat antal |
 |Ingår |Inkluderad mängd |
 |Faktureringsbar |Överbliven kvantitet |

Den **Användningskostnader** delen av din faktura finns det totala värdet för varje mätare som förbrukades under din faktureringsperiod. Följande skärmbild visar till exempel användning debiteras du för tjänsten Azure Scheduler.

![Avgifter för användning av faktura](./media/billing-understand-your-bill/1.png)

Den **instruktionen** delen av detaljerad användningsinformation CSV visas samma kostnad. Både den *förbrukade* belopp och *värdet* matchar fakturan.

![Avgifter för användning av CSV](./media/billing-understand-your-bill/2.png)

Om du vill se en daglig sammanställning av den här avgiften, går du till den **daglig användning** avsnittet i CSV-filen. Filtrera efter *Scheduler* under *Mätningskategori*. Du kan se vilka dagar mätaren har använts och hur mycket förbrukades. Den *Resource* och *resursgrupp* informationen visas också för jämförelse. Den *förbrukade* värden bör lägga till vad som visas på fakturan.

![Avsnittet för daglig användning i CSV-filen](./media/billing-understand-your-bill/3.png)

För att få kostnaden per dag, multiplicera den *förbrukade* belopp för med den *Rate* värdet från den **instruktionen** avsnittet.

Du kan läsa mer här:

- [Förstå fakturan för Azure](billing-understand-your-invoice.md)
- [Förstå Azure detaljerad användningsinformation](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Alternativ 2: Jämför användning och kostnader i Azure portal

Azure-portalen kan också hjälpa dig att kontrollera dina kostnader. Visa management kostnadstabeller för att få en snabb överblick över fakturerade användning och kostnader.

1. I Azure-portalen går du till [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj prenumerationen > **analys av kostnader**.
1. Filtrera efter **Timespan**.
1. Om du vill fortsätta i föregående exempel kan du se användning debiteras du för tjänsten Azure Scheduler.

   ![Cost analysis-vyn i Azure-portalen](./media/billing-understand-your-bill/4.png)

1. Välj den raden att se dagliga kostnadsdata.

   ![KOSTNADSHISTORIK vyn i Azure-portalen](./media/billing-understand-your-bill/5.png)

Mer information finns i [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md#costs).

## <a name="external"></a>Externa tjänster faktureras separat

Externa tjänster eller marketplace-avgifter är för resurser som har skapats av programvara från tredje part-leverantörer. Dessa resurser är tillgängliga för användning från Azure marketplace. Till exempel är en Barracuda brandvägg en Azure marketplace-resurs som erbjuds av tredje part. Alla avgifter för brandväggen och dess motsvarande mätare visas som externa tjänstavgifter.

Avgifterna för externa tjänster faktureras separat. Kostnaderna som visas inte på fakturan för Azure. Mer information finns i [förstå din Azure externa tjänstavgifter](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Resurser som debiteras enligt taxor för användning

Azure inte direkt faktura baserat på den resurs som kostnad. Avgifter för en resurs beräknas med hjälp av en eller flera mätare. Taxor används för att spåra användningen av en resurs under hela dess livslängd. Mätarnas används sedan för att beräkna fakturan.

Den har till exempel en eller flera mätnings-instanser som skapas när du skapar en enda Azure-resurs, t.ex. en virtuell dator. Taxor används för att spåra användningen av resursen över tid. Varje mätare som genererar användningsposter som används av Azure för att beräkna fakturan.

En enskild virtuell dator (VM) skapas i Azure kan till exempel ha följande taxor skapas för att spåra användningen:

- Instanstimmar
- IP-Adresstimmar
- Dataöverföring in
- Dataöverföring ut
- Standard Managed Disk
- Hanterade standarddiskar åtgärder
- Standard IO-Disk
- Standard-i/o-Block Blob Läs
- Standard-i/o-Block Blob-Write
- Borttagning av standard-i/o – Blockblob

När den virtuella datorn har skapats börjar varje mätare som sänder användningsposter. Den här användningen och mätarens pris spåras i Azure för Avläsning av system.

## <a name="payment"></a>Betala fakturan

Om du har konfigurerat ett kreditkort eller bankkort krävs en som din betalningsmetod debiteras betalningen automatiskt inom 10 dagar efter det fakturering löpt ut. På kontoutdraget kreditkort radartikeln skulle säger **MSFT Azure**.

Om du vill ändra det kreditkort eller bankkort kortet som debiteras [lägga till, uppdatera eller ta bort ett kreditkort eller bankkort kort för Azure](billing-how-to-change-credit-card.md).

Om du [betala med faktura](billing-how-to-pay-by-invoice.md), skicka betalningen till platsen som visas längst ned på fakturan.

Kontrollera statusen för din betalning [skapa ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tips för kostnadshantering

- Beräkna kostnader med hjälp av den:
  - [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/)
  - [Totalkostnad för ägarskap Kalkylatorn](https://aka.ms/azure-tco-calculator)
  - [Detaljerad information om priser för varje tjänst](https://azure.microsoft.com/pricing/)
- [Granska användning och kostnader regelbundet i Azure-portalen](billing-getting-started.md#costs).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="learn-more"></a>Läs mer

- [Få dina Azure-faktura och uppgifter om daglig användning](billing-download-azure-invoice-daily-usage-date.md)
- [Förstå villkoren på din faktura för Microsoft Azure](billing-understand-your-invoice.md)
- [Förstå villkoren på Microsoft Azure detaljerad användning](billing-understand-your-usage.md)
- [Kostnadshantering för Azure portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md#costs)
