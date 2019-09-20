---
title: Förstå Azure-fakturan
description: Lär dig hur du läser och förstår din användning samt fakturan för din Azure-prenumeration.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: banders
ms.openlocfilehash: 51143644a62a77a61c4540d9f2ad3dce401c496b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "68610130"
---
# <a name="understand-your-microsoft-azure-bill"></a>Förstå din Microsoft Azure-faktura
För att förstå din Azure-faktura jämför du fakturan med filen med detaljerad daglig användning samt kostnadshanteringsrapporterna i Azure-portalen.

Den här artikeln gäller inte för följande kunder:
- Azure-kunder med ett Enterprise-avtal (EA-kunder). Om du är EA-kund hittar du mer information i avsnittet om [hur du förstår din faktura för Azure-kunder med ett Enterprise-avtal](billing-understand-your-bill-ea.md).
- Azure-kunder med ett [Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement). Om du har ett Microsoft-kundavtal kan du läsa mer i artikeln om [hur du förstår Azure-avgifterna på din faktura för Microsoft-kundavtal](billing-mca-understand-your-bill.md).

En förklaring av hur fakturering fungerar i Azure Cloud Solution Provider-programmet (Azure CSP), däribland faktureringscykel, priser och användning, finns i [översikten för Azure CSP-fakturering](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Granska dina avgifter

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Om det finns en avgift på din faktura som du vill ha mer information om kan du jämföra användning och kostnader med användningsfilen eller med Azure-portalen.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Alternativ 1: Jämföra användning och kostnader med användningsfilen

I CSV-filen med detaljerad användning visas dina avgifter per faktureringsperiod och daglig användning. Information om hur du laddar ned eller visar filen finns i [Hämta din Azure-faktura och information om daglig användning](billing-download-azure-invoice-daily-usage-date.md).

Dina användningsavgifter visas på mätarnivå. Följande termer innebär samma sak både i fakturan och i filen med detaljerad användning. Till exempel är faktureringsperioden på fakturan samma som den faktureringsperiod som visas i filen med detaljerad användning.

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

I avsnittet **Användningsavgifter** på fakturan visas det totala värdet för varje mätare som förbrukades under faktureringsperioden. Till exempel visar följande bild en användningsavgift för tjänsten Azure Scheduler.

![Användningsavgifter på faktura](./media/billing-understand-your-bill/1.png)

I avsnittet **Utdrag** i CSV-filen med detaljerad användning visas samma avgift. Både *Förbrukat* belopp och *Värde* matchar fakturan.

![CSV-användningsavgifter](./media/billing-understand-your-bill/2.png)

Om du vill se en dagbaserad indelning av avgiften går du till avsnittet **Daglig användning** i CSV-filen. Filtrera på *Scheduler* under *Mätarkategori*. Du kan se vilka dagar mätaren användes och hur mycket som förbrukades. Informationen för *Resurs* och *Resursgrupp* visas också i jämförelsesyfte. Värden för *Förbrukat* bör sammanlagt bli samma som det som visas på fakturan.

![Avsnittet Daglig användning i CSV-filen](./media/billing-understand-your-bill/3.png)

Om du vill se kostnaden per dag multiplicerar du *Förbrukat* belopp med värdet för *Pris* från avsnittet **Utdrag**.

Du kan läsa mer här:

- [Förstå din Azure-faktura](billing-understand-your-invoice.md)
- [Förstå din detaljerade Azure-användning](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Alternativ 2: Jämföra användningen och kostnaderna i Azure-portalen

Azure-portalen kan även hjälpa dig att verifiera dina avgifter. Du kan få en snabb översikt av din fakturerade användning och dina avgifter genom att visa diagrammen för kostnadshantering.

1. I Azure-portalen går du till [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj din prenumeration > **Kostnadsanalys**.
1. Filtrera efter **Tidsintervall**.
1. Som en fortsättning på föregående exempel ser du en användningsavgift för tjänsten Azure Scheduler.

   ![Vy för kostnadsanalys i Azure-portalen](./media/billing-understand-your-bill/4.png)

1. Välj den rad som visar avgiften för att se analysen av dagliga kostnader.

   ![Vy för kostnadshistorik i Azure-portalen](./media/billing-understand-your-bill/5.png)

Mer information finns i [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md#costs).

## <a name="external"></a>Externa tjänster debiteras separat

Externa tjänster eller Marketplace-avgifter är för resurser som har skapats av programvaruleverantörer från tredje part. Dessa resurser är tillgängliga för användning från Azure Marketplace. Till exempel är Barracuda Firewall en Azure Marketplace-resurs som erbjuds av en tredje part. Alla avgifter för brandväggen och dess motsvarande mätare visas som avgifter för externa tjänster.

Avgifter för externa tjänster debiteras separat. Avgifterna står inte på din Azure-faktura. Mer information finns i [Förstå dina avgifter för externa Azure-tjänster](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Resurser debiteras per användningsmätare

Azure debiterar inte direkt baserat på resurskostnaden. Avgifter för en resurs beräknas med hjälp av en eller flera mätare. Mätare används för att spåra användningen av en resurs under hela dess livslängd. Dessa mätare används sedan för att beräkna fakturan.

När du till exempel skapar en enskild Azure-resurs, till exempel en virtuell dator, skapas en eller flera mätarinstanser för den. Mätare används för att spåra användningen av resursen över tid. Varje mätare skickar användningsposter som används av Azure för att beräkna fakturan.

Till exempel kan en enskild virtuell dator (VM) som skapats i Azure ha följande mätare som skapas för att spåra dess användning:

- Beräkningstimmar
- IP-adresstimmar
- Inkommande dataöverföring
- Utgående dataöverföring
- Standard – hanterad disk
- Standard – åtgärder för hanterad disk
- Standard – I/O-disk
- Standard – I/O-blockblobsläsning
- Standard – I/O-blockblobsskrivning
- Standard – I/O-blockblobsborttagning

När den virtuella datorn skapas börjar varje mätare att skicka användningsposter. Den här användningen och priset för mätaren spåras i Azure-mätarsystemet.

## <a name="payment"></a>Betala din faktura

Om du konfigurerar ett kreditkort som betalningsmetod debiteras betalningen automatiskt inom 10 dagar efter faktureringsperiodens slut. På ditt kreditkortsutdrag skulle det stå **MSFT Azure** på radobjektet.

Information om hur du ändrar det kreditkort som debiteras finns i [Lägga till, uppdatera eller ta bort ett kreditkort för Azure](billing-how-to-change-credit-card.md).

Om du [betalar med faktura](billing-how-to-pay-by-invoice.md) skickar du betalningen till den plats som anges längst ned på fakturan.

Om du vill kontrollera statusen för din betalning [skapar du en supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tips för kostnadshantering

- Beräkna kostnader med hjälp av:
  - [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Kalkylator för total ägandekostnad](https://aka.ms/azure-tco-calculator)
  - [Detaljerad prisinformation för varje tjänst](https://azure.microsoft.com/pricing/)
- [Granska användning och kostnader regelbundet i Azure-portalen](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Läs mer

- [Hämta din Azure-faktura och information om daglig användning](billing-download-azure-invoice-daily-usage-date.md)
- [Förstå termerna på din Microsoft Azure-faktura](billing-understand-your-invoice.md)
- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](billing-understand-your-usage.md)
- [Kostnadshantering i Azure-portalen](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md#costs)
