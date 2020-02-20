---
title: Granska din enskilda Azure-faktura
description: Lär dig hur du förstår din fakturerings- och resursanvändning och kontrollera avgifterna för din enskilda Azure-prenumeration.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 8ebda9716df2e9e45050760ee8d0434c12838370
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199134"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Självstudier: Granska din enskilda Azure-faktura

Den här artikeln hjälper dig att förstå och granska din Azure-faktura. För varje faktureringsperiod får du normalt en faktura via e-post. Fakturan är en representation av din Azure-räkning. Samma kostnadsinformation på fakturan är tillgänglig i Azure-portalen. I den här självstudien jämför du fakturan med filen med detaljerad daglig användning samt med kostnadsanalys i Azure-portalen.

Den här självstudien gäller endast för Azure-kunder som har en enskild prenumeration. Vanliga enskilda prenumerationer är sådana som har Betala per användning-priser och har köpts direkt från Azure-webbplatsen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Jämföra fakturerade kostnader med användningsfilen
> * Jämföra avgifter och användning i kostnadsanalys

## <a name="prerequisites"></a>Krav

Du måste ha ett betalt faktureringskonto för *Microsoft Online Services-programmet*. Kontot skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel om du har ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller om du är [Visual Studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Fakturor för [kostnadsfria Azure-konton](https://azure.microsoft.com/offers/ms-azr-0044p/) skapas endast när det månatliga kreditbeloppet överskrids.

Det måste ha gått mer än 30 dagar sedan du började prenumerera på Azure. Azure debiterar dig i slutet av din fakturaperiod.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="compare-invoiced-charges-with-usage-file"></a>Jämföra fakturerade kostnader med användningsfilen

<a name="charges"></a>

Det första steget för att jämföra användning och kostnader är att ladda ned din faktura och dina användningsfiler. I CSV-filen med detaljerad användning visas dina avgifter per faktureringsperiod och daglig användning. Den innehåller ingen skatteinformation. För att kunna ladda ned filerna måste du vara kontoadministratör eller ha rollen som ägare.

I Azure-portalen skriver du *prenumerationer* i sökrutan och klickar sedan på [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Gå till prenumerationer](./media/review-individual-bill/navigate-subscriptions.png)

Klicka på prenumerationen i listan över prenumerationer.

Under **Fakturering** klickar du på **Fakturor**.

I listan över fakturor letar du upp den som du vill ladda ned och klickar sedan på nedladdningssymbolen. Du kan behöva ändra tidsintervallet för att visa äldre fakturor. Det kan ta några minuter att generera filen med användningsinformation och fakturan.

![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/review-individual-bill/download-invoice.png)

I fönstret Ladda ned användning + kostnader klickar du på **Ladda ned CSV** och **Ladda ned faktura**.

![Skärmbild som visar Ladda ned faktura och förbrukningssidan](./media/review-individual-bill/usageandinvoice.png)

Om det står **Inte tillgängligt** finns det flera orsaker till att du inte ser användningsinformation eller en faktura:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.
- Det finns ingen användning för faktureringsperioden.
- Det har inte genererats någon faktura ännu. Vänta till slutet av faktureringsperioden.
- Du har inte behörighet att visa fakturor. Du ser kanske inte gamla fakturor om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](../manage/manage-billing-access.md).
- Om du har en kostnadsfri utvärderingsversion eller ett månatligt kreditbelopp för din prenumeration som du inte har överskridit får du ingen faktura såvida du inte har ett Microsoft-kundavtal.

Därefter granskar du avgifterna. Fakturan visar värden för skatter och dina användningsavgifter.

![Exempel på Azure-faktura](./media/review-individual-bill/invoice-usage-charge.png)

Öppna den CSV-användningsfil som du laddade ned. I slutet av filen summerar du värdet för alla objekt i kolumnen *Kostnad*.

![Exempel på användningsfil med summerad kostnad](./media/review-individual-bill/usage-file-usage-charges.png)

 Värdet för summerad *Kostnad* ska exakt matcha kostnaden för *användningsavgifter* på fakturan.

Dina användningsavgifter visas på mätarnivå. Följande termer innebär samma sak både i fakturan och i filen med detaljerad användning. Till exempel är faktureringsperioden på fakturan samma som den faktureringsperiod som visas i filen med detaljerad användning.

| Faktura (PDF) | Detaljerad användning (CSV)|
| --- | --- |
|Faktureringscykel | BillingPeriodStartDate BillingPeriodEndDate |
|Namn |Mätarkategori |
|Typ |Mätarunderkategori |
|Resurs |MeterName |
|Region |MeterRegion |
|Förbrukad | Kvantitet |
|Ingår |Inkluderad mängd |
|Faktureringsbar |Överbliven kvantitet |
|Pris | EffectivePrice|
| Värde | Kostnad |

I avsnittet **Användningsavgifter** på fakturan visas det totala värdet (kostnaden) för varje mätare som förbrukades under faktureringsperioden. Till exempel visar följande bild en användningsavgift för Azure Storage-tjänsten för resursen *P10-diskar*.

![Användningsavgifter på faktura](./media/review-individual-bill/invoice-usage-charges.png)

I CSV-användningsfilen filtrerar du efter *MeterName* för motsvarande resurs som visas på fakturan. Summera sedan värdet för *Kostnad* för objekt i kolumnen. Här är ett exempel.

![Summerat värde för användningsfilen för MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Värdet för summerad *Kostnad* ska exakt matcha kostnaden för *användningsavgifter* för den enskilda resurs som debiteras på fakturan.

Mer information finns i [Förstå din Azure-faktura](understand-invoice.md) och [Förstå din detaljerad Azure-användning](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Jämföra avgifter och användning i kostnadsanalys

Kostnadsanalys i Azure-portalen kan även hjälpa dig att verifiera dina avgifter. För att få en snabb överblick av din fakturerade användning och dina kostnader väljer du din prenumeration på sidan [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen. Klicka sedan på **Kostnadsanalys**. I listan över vyer klickar du på **Fakturainformation**.

![Exempel som visar val av Fakturainformation](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

I listan med datumintervall väljer du sedan en tidsperiod för fakturan. Lägg till ett filter för fakturanummer och välj sedan det InvoiceNumber som överensstämmer med din faktura. Kostnadsanalys visar kostnadsinformation för dina fakturerade objekt.

![Exempel som visar fakturerad kostnadsinformation i kostnadsanalys](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Kostnader som visas i kostnadsanalys ska exakt matcha kostnaden för *användningsavgifter* för den enskilda resurs som debiteras på fakturan.

![Användningsavgifter på faktura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Externa tjänster debiteras separat

Externa tjänster eller Marketplace-avgifter är för resurser som har skapats av programvaruleverantörer från tredje part. Dessa resurser är tillgängliga för användning från Azure Marketplace. Till exempel är Barracuda Firewall en Azure Marketplace-resurs som erbjuds av en tredje part. Alla avgifter för brandväggen och dess motsvarande mätare visas som avgifter för externa tjänster.

Avgifter för externa tjänster debiteras separat. Avgifterna står inte på din Azure-faktura. Mer information finns i [Förstå dina avgifter för externa Azure-tjänster](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Resurser debiteras per användningsmätare

Azure debiterar inte direkt baserat på resurskostnaden. Avgifter för en resurs beräknas med hjälp av en eller flera mätare. Mätare används för att spåra användningen av en resurs under hela dess livslängd. Dessa mätare används sedan för att beräkna fakturan.

När du skapar en enskild Azure-resurs, till exempel en virtuell dator, skapas en eller flera mätarinstanser för den. Mätare används för att spåra användningen av resursen över tid. Varje mätare skickar användningsposter som används av Azure för att beräkna fakturan.

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

Du kan se de mätare som användes för att beräkna din faktura i CSV-filen för användning.

## <a name="payment"></a>Betala din faktura

Om du konfigurerar ett kreditkort som betalningsmetod debiteras betalningen automatiskt inom 10 dagar efter faktureringsperiodens slut. På ditt kreditkortsutdrag skulle det stå **MSFT Azure** på radobjektet.

Information om hur du ändrar det kreditkort som debiteras finns i [Lägga till, uppdatera eller ta bort ett kreditkort för Azure](../manage/change-credit-card.md).

Om du [betalar med faktura](../manage/pay-by-invoice.md) skickar du betalningen till den plats som anges längst ned på fakturan.

Om du vill kontrollera statusen för din betalning [skapar du en supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Jämföra fakturerade kostnader med användningsfilen
> * Jämföra avgifter och användning i kostnadsanalys

Börja använda kostnadsanalys genom att slutföra snabbstarten.

> [!div class="nextstepaction"]
> [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md)
