---
title: Förstå din faktura för Azure | Microsoft Docs
description: Lär dig hur du läst och förstått användnings- och faktura för din Azure-prenumeration
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: 689ea9e0d029bb65bc579fc914c6ed3073b4a96b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064120"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Förstå fakturan för Microsoft Azure
Jämför fakturan med filen detaljerad daglig användning och kostnader management rapporterna i Azure-portalen för att förstå fakturan Azure.

>[!NOTE]
>Den här artikeln gäller inte för kunder med Enterprise-avtal (EA). Om du är en EA-kund [faktura dokumentationen hittar på Enterprise Portal.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

En PDF-fil på fakturan och en kopia av din detaljerad daglig användning CSV Filhämtning finns [hämta ditt Azure billing faktura och dagligen användningsdata](billing-download-azure-invoice-daily-usage-date.md). 

Detaljerade villkor och beskrivningar av din faktura och detaljerad daglig användning fil finns [Förstå villkoren på fakturan Microsoft Azure](billing-understand-your-invoice.md) och [förstå villkor i Microsoft Azure detaljerad användning](billing-understand-your-usage.md). 

Mer information om de kostnad rapporterna finns [Azure-portalen kostnadshanteringen](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="charges"></a>Hur gör jag till att tillägg i min faktura är korrekta?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Om en avgift på fakturan som du vill ha mer information om finns det ett par olika alternativ.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Alternativ 1: Granska fakturan och jämför användning och kostnader med detaljerad användning CSV-fil

CSV-fil för detaljerad användning visar dina avgifter per faktureringsperiod och daglig användning. För detaljerad användning CSV-filen, se [hämta ditt Azure billing faktura och dagligen användningsdata](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Avgifter för användning visas på nivån mätaren. Följande villkor betyda samma sak i både fakturan och filen detaljerad användning. Faktureringsperioden på fakturan är till exempel motsvarar den faktureringsperiod som visas i filen detaljerad användning.

 | Fakturan (PDF) | Detaljerad användning (CSV)|
 | --- | --- |
|Faktureringscykel | Faktureringsperiod |
 |Namn |Mätarkategori |
 |Typ |Mätaren underkategori |
 |Resurs |Mätarnamn |
 |Region |Mätarregion |
 |Förbrukad |Förbrukat antal |
 |Ingår |Inkluderad mängd |
 |Faktureringsbar |Överbliven kvantitet |

Den **Användningskostnader** avsnittet för fakturan innehåller det totala värdet för varje mätaren förbrukades under din faktureringsperioden. Följande skärmbild visar exempelvis en avgift för användning för Azure-Schemaläggaren.

![Avgifterna för användning av faktura](./media/billing-understand-your-bill/1.png)

Den **instruktionen** delen av din detaljerad användning CSV visas samma kostnad. Både den *förbrukad* belopp och *värdet* matchar fakturan.

![Avgifterna för användning av CSV](./media/billing-understand-your-bill/2.png)

Om du vill se en sammanställning av denna avgift dagligen, gå till den **daglig användning** avsnitt i CSV-filen. Filtrera efter ”Scheduler” *mätaren kategori* och du kan se vilka dagar mätaren användes och hur mycket har förbrukats. Den *resurs* och *resursgruppen* informationen visas också för jämförelse. Den *förbrukad* värden ska lägga till vad som ska visas på fakturan.

![Daglig användning avsnitt i CSV-filen](./media/billing-understand-your-bill/3.png)

För att få kostnaden per dag, multiplicera den *förbrukad* belopp med den *hastighet* värdet från den **instruktionen** avsnitt.

Mer information om fakturan finns [förstå fakturan Azure](billing-understand-your-invoice.md).

Mer information om varje kolumn i CSV: N, se [förstå din Azure detaljerad användning](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Alternativ 2: Granska din faktura och jämföra med användning och kostnader i Azure-portalen

Azure-portalen kan också hjälpa dig att kontrollera dina debiteringar. Azure portal tillhandahåller kostnaden management diagram för en snabb överblick över din användning och kostnader på fakturan.

Om du vill fortsätta med exemplet ovan, finns det [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), väljer din prenumeration och välj sedan **kostnad analysis**. Därifrån kan du ange tidsperiod och visas syntax för Azure-Schemaläggaren.

![Visa kostnad analys i Azure-portalen](./media/billing-understand-your-bill/4.png)

Se dagliga arbete och omkostnader i **kostnad historik**, klickar du på raden.

![Kostnad historikvyn i Azure-portalen](./media/billing-understand-your-bill/5.png)

Läs mer i [förhindrar oväntade kostnader med Azure fakturerings- och kostnaden management](billing-getting-started.md#costs).

## <a name="external"></a>Nyheter om externa serviceavgifter?
Externa tjänster (även kallat Azure Marketplace order) tillhandahålls av oberoende tjänsteleverantörer och faktureras separat. Avgifterna visas inte på fakturan Azure. Läs mer i [förstå Azure externa avgifterna](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Hur gör jag en betalning

Om du har ställt in ett kreditkort eller en bankkort som betalningsmetoden debiteras betalningen automatiskt inom 10 dagar efter faktureringsperioden tar slut. På ditt kreditkort instruktionen raden säger **MSFT Azure**.

Om du [betala av fakturering](billing-how-to-pay-by-invoice.md), skickar du betalningen till platsen som anges längst ned på fakturan. Mer hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Hur jag för att kontrollera status för en betalning kreditkort?

[Skapa ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och be om statusen för din betalning. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Finns det olika Azure-kundtyper? Hur vet vilken kund-typ som jag?
Det finns olika typer av Azure-kunder. För att bättre förstå din faktura och prissättning, finns i följande beskrivningar för kund-typen.

- **Enterprise**: Enterprise-kunder som har undertecknat ett Enterprise-avtal med Azure för att göra förhandlade monetära åtaganden och få åtkomst till anpassade priser för Azure-resurser.
- **Web Direct**: Web direkt kunder har inte skrivit något anpassade avtal med Azure. Dessa kunder har registrerat dig för Azure via azure.com och få offentliga Internetriktade priser för alla Azure-resurser.
- **Cloud Service Provider**: molntjänst-Providers är vanligtvis företag som har anlitat av en end-kund att skapa lösningar på Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Varför ser jag kostnaden resursen har skapats i min faktura?
Azure har inte faktura direkt baserat på resursen. Debitering görs beroende av en eller flera mätare som används för att spåra användningen av en resurs under hela sin livslängd. Dessa mätare används sedan för att beräkna växeln. Läs mer om Azure avläsning nedan.

## <a name="how-does-azure-charge-metering-work"></a>Hur debiterar avläsning arbete i Azure?
När du få igång en enda Azure resurs, till exempel en virtuell dator har en eller flera mätaren instanser som skapats samt. Dessa mätare används för att spåra användningen av resursen över tid. Varje mätaren avger användningsposter som sedan används av Azure i vår kostnaden avläsning systemet för att beräkna växeln. 

En enskild virtuell dator som skapats i Azure kan till exempel ha följande mätare skapas för att spåra användningen:

- Instanstimmar
- IP-Adresstimmar
- Dataöverföring in
- Dataöverföring ut
- Standard hanterade diskar
- Åtgärder för hanterade diskar som standard
- Standard-i/o-Disk
- Standard-i/o-Block-Blob Läs
- Standard-i/o-Block-Blob-skrivåtgärder
- Ta bort standard-i/o-Block-Blob

När den virtuella datorn har skapats börjar var och en av de ovanstående mätare avger användningsposter. Denna användning ska sedan användas i Azures avläsning datorn tillsammans med den mätaren pris för att avgöra hur mycket kunden debiteras.

> [!Note]
> I ovanstående exempel mätare kan endast vara en delmängd av mätare skapas en virtuell dator som har skapats.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Vad är skillnaden mellan Azure 1 part avgifter och kostnader för Azure Marketplace?
Azure 1 part avgifterna är för resurser som är direkt utvecklas och som Azure erbjuder. Azure Marketplace-debiteringar är resurser som har skapats av tredje part programleverantörer som är tillgängliga via Azure marketplace. Till exempel är en Barracuda brandvägg en Azure marketplace-resurs som erbjuds av en tredje part. Alla avgifter för brandväggen och dess motsvarande mätare visas som marketplace-debiteringar. 

## <a name="tips-for-cost-management"></a>Tips för hantering av kostnad
- Uppskatta kostnader med hjälp av den [prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) och [totalkostnad för ägarskap Kalkylatorn](https://aka.ms/azure-tco-calculator), och få de [utförlig prisinformation för varje tjänst](https://azure.microsoft.com/pricing/).
- [Ställ in fakturering aviseringar](billing-set-up-alerts.md).
- [Granska din användning och kostnader regelbundet i Azure portal](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
