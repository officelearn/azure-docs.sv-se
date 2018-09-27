---
title: Förstå fakturan för Azure | Microsoft Docs
description: Lär dig att läsa och förstå din användningen och fakturan för din Azure-prenumeration
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
ms.author: cwatson
ms.openlocfilehash: f7ec113a7fa51fc6d3684c2e7ee9379bd1534e30
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392422"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Förstå fakturan för Microsoft Azure
Jämför din faktura med detaljerade dagliga användningsfil och kostnadshanteringsrapporter i Azure-portalen för att förstå fakturan för Azure.

>[!NOTE]
>Den här artikeln gäller inte för kunder med Enterprise Agreement (EA). Om du är en EA-kund [faktura dokumentationen hittar på Enterprise Portal.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Om du vill ha en PDF-fil på din faktura och en kopia av din detaljerad daglig användning CSV Filhämtning, se [hämta din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md). 

Detaljerade villkor och beskrivningar av din faktura och detaljerad dagliga användningsfil finns i [Förstå villkoren på din Microsoft Azure-faktura](billing-understand-your-invoice.md) och [Förstå villkoren på Microsoft Azure detaljerad användning](billing-understand-your-usage.md). 

Mer information om kostnadshanteringsrapporter finns [Kostnadshantering för Azure-portalen](https://docs.microsoft.com/azure/billing/billing-getting-started).

> [!div class="nextstepaction"]
> [Hjälp till att förbättra faktureringsdokument för Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>Hur gör jag till att kostnader på min faktura är korrekta?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Om det finns en avgift på fakturan som du vill veta mer på, finns det ett par alternativ.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Alternativ 1: Granska din faktura och jämför användning och kostnader med detaljerad användning CSV-fil

Detaljerad användning CSV-filen visar dina kostnader per faktureringsperiod och daglig användning. Om du vill ha detaljerad användning CSV-filen, se [hämta din Azure-fakturering och daglig användningsdata](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Avgifter för användning visas på nivån mätaren. Följande villkor betyda samma sak i både fakturan och detaljerad användningsfil. Faktureringsperiod på fakturan motsvarar till exempel i den faktureringsperioden som visas i filen detaljerad användning.

 | Faktura (PDF) | Detaljerad användning (CSV)|
 | --- | --- |
|Faktureringscykel | Faktureringsperiod |
 |Namn |Mätarkategori |
 |Typ |Mätarunderkategori |
 |Resurs |Mätarnamn |
 |Region |Mätarregion |
 |Förbrukad |Förbrukat antal |
 |Ingår |Inkluderad mängd |
 |Faktureringsbar |Överbliven kvantitet |

Den **Användningskostnader** delen av din faktura finns det totala värdet för varje mätare som förbrukades under din faktureringsperiod. Följande skärmbild visar till exempel användning debiteras du för tjänsten Azure Scheduler.

![Avgifter för användning av faktura](./media/billing-understand-your-bill/1.png)

Den **instruktionen** delen av detaljerad användningsinformation CSV visas samma kostnad. Både den *förbrukade* belopp och *värdet* matchar fakturan.

![Avgifter för användning av CSV](./media/billing-understand-your-bill/2.png)

Om du vill se en specifikation av den här avgiften per dag, går du till den **daglig användning** avsnittet i CSV-filen. Filtrera efter ”Scheduler” *Mätningskategori* och du kan se vilka dagar mätaren har använts och hur mycket förbrukades. Den *Resource* och *resursgrupp* informationen visas också för jämförelse. Den *förbrukade* värden bör lägga till vad som visas på fakturan.

![Avsnittet för daglig användning i CSV-filen](./media/billing-understand-your-bill/3.png)

För att få kostnaden per dag, multiplicera den *förbrukade* belopp för med den *Rate* värdet från den **instruktionen** avsnittet.

Läs mer om fakturan i [förstå fakturan Azure](billing-understand-your-invoice.md).

Läs om var och en av kolumnerna i CSV-filen i [förstå Azure detaljerad användningsinformation](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Alternativ 2: Granska din faktura och jämför med användning och kostnader i Azure portal

Azure-portalen kan också hjälpa dig att kontrollera dina kostnader. Azure-portalen ger en snabb översikt över din användning och avgifter på fakturan kostnadstabeller för hantering.

Om du vill fortsätta med exemplet ovan finns det [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), Välj din prenumeration och välj sedan **analys av kostnader**. Därifrån kan du ange tidsintervallet och se användning kostar Azure Scheduler-tjänsten.

![Cost analysis-vyn i Azure-portalen](./media/billing-understand-your-bill/4.png)

Se dagliga kostnadsdata i **KOSTNADSHISTORIK**, klickar du på raden.

![KOSTNADSHISTORIK vyn i Azure-portalen](./media/billing-understand-your-bill/5.png)

Mer information finns i [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md#costs).

## <a name="external"></a>Vad gäller externa tjänstavgifter?
Externa tjänster (även kallat Azure Marketplace order) tillhandahålls av oberoende tjänsteleverantörer och faktureras separat. Kostnaderna som visas inte på fakturan för Azure. Mer information finns i [förstå din Azure externa tjänstavgifter](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Hur gör jag en betalning?

Om du har konfigurerat ett kreditkort eller bankkort krävs en som din betalningsmetod debiteras betalningen automatiskt inom 10 dagar efter det fakturering löpt ut. På kontoutdraget kreditkort radartikeln skulle säger **MSFT Azure**.

Om du [betala per fakturering](billing-how-to-pay-by-invoice.md), skicka betalningen till platsen som visas längst ned på fakturan. Mer hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Hur jag för att kontrollera status för en betalning med kreditkort?

[Skapa ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) fråga efter statusen för din betalning. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Finns det olika kundtyper för Azure? Hur vet jag vilka kundtyp jag?
Det finns olika typer av Azure-kunder. För att bättre förstå din faktura och priser, se följande kundtyp beskrivningar.

- **Enterprise**: företagskunder har registrerat ett Enterprise-avtal med Azure för att göra den förhandlade monetära åtaganden och få åtkomst till anpassad prissättning för Azure-resurser.
- **Web Direct**: Web Direct-kunder har inte loggat något anpassade avtal med Azure. Dessa kunder har registrerat dig för Azure via azure.com och få offentliga internetuppkopplade priser för alla Azure-resurser.
- **Molnleverantör**: Molntjänstleverantörer vanligen är ett företag som har rekryterats som slutkund kan skapa lösningar ovanpå Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Varför ser jag kostnaden resursen som jag har skapat på min faktura?
Azure stöder inte faktura direkt baserat på resurskostnader. Debiteringen görs beroende av en eller flera värden som används för att spåra användningen av en resurs under hela dess livslängd. Mätarnas används sedan för att beräkna fakturan. Läs mer om Azure för Avläsning av nedan.

## <a name="how-does-azure-charge-metering-work"></a>Hur debiterar Azure Avläsning av arbete?
När sätta upp en enda Azure-resurs, till exempel en virtuell dator har en eller flera mätnings-instanser som skapas också. Mätarnas används för att spåra användningen av resursen över tid. Varje mätare som genererar användningsposter som sedan används av Azure i vår kostnaden för Avläsning av systemet för att beräkna fakturan. 

En enskild virtuell dator som skapats i Azure kan till exempel ha följande taxor skapas för att spåra användningen:

- Instanstimmar
- IP-Adresstimmar
- Dataöverföring in
- Dataöverföring ut
- Hanterade standarddiskar
- Hanterade standarddiskar åtgärder
- Standard IO-Disk
- Standard-i/o-Block Blob Läs
- Standard-i/o-Block Blob-Write
- Borttagning av standard-i/o – Blockblob

När den virtuella datorn skapas, börjar var och en av mätarna som ovan avger användningsposter. Denna användning ska sedan användas i Azures Avläsning av systemet tillsammans med mätarens priset för att avgöra hur mycket kunden debiteras.

> [!Note]
> Ovanstående exempel mätarna kan endast vara en delmängd av de mätare som skapat en virtuell dator som har skapats.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Vad är skillnaden mellan Azure debitering för 1 part och kostnader för Azure Marketplace?
Azure 1 part debiteras för de resurser som direkt utvecklas och erbjuds av Azure. Azure Marketplace-avgifter är avsedda för resurser som har skapats av tredjeparts-programvaruleverantörer som kan användas via Azure marketplace. Till exempel är en brandvägg för Barracuda en Azure marketplace-resurs som erbjuds av tredje part. Alla avgifter för brandväggen och dess motsvarande mätare visas som marketplace-avgifter. 

## <a name="tips-for-cost-management"></a>Tips för kostnadshantering
- Beräkna kostnader med hjälp av den [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) och [totalkostnad för ägarskap Kalkylatorn](https://aka.ms/azure-tco-calculator), och få den [detaljerad information om priser för varje tjänst](https://azure.microsoft.com/pricing/).
- [Ställa in faktureringsvarningar](billing-set-up-alerts.md).
- [Granska användning och kostnader regelbundet i Azure-portalen](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
