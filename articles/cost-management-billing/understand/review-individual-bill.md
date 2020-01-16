---
title: Granska din enskilda Azure-faktura
description: Lär dig hur du kan förstå din fakturerings-och resursanvändning och kontrol lera avgifterna för din enskilda Azure-prenumeration.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987350"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Självstudie: granska din enskilda Azure-faktura

Den här artikeln hjälper dig att förstå och granska din Azure-faktura. För varje fakturerings period får du normalt en faktura i e-postmeddelandet. Fakturan är en representation av din Azure-faktura. Samma kostnads information på fakturan är tillgänglig i Azure Portal. I den här självstudien kommer du att jämföra din faktura med den detaljerade dagliga användnings filen och med kostnads analys i Azure Portal.

Den här självstudien gäller endast för Azure-kunder med en enskild prenumeration. Vanliga enskilda prenumerationer är de med priser enligt principen betala per användning som köpts direkt från Azure-webbplatsen.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Jämför fakturerade kostnader med användnings filen
> * Jämför avgifter och användning i kostnads analys

## <a name="prerequisites"></a>Krav

Du måste ha ett betalt *Microsoft Online Services-program* fakturerings konto. Kontot skapas när du registrerar dig för Azure via Azure-webbplatsen. Om du till exempel har ett [konto med priser enligt principen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning eller om du är en [Visual Studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Fakturor för [kostnads fria Azure-konton](https://azure.microsoft.com/offers/ms-azr-0044p/) skapas bara när månads kredit beloppet har överskridits.

Det måste vara mer än 30 dagar från den dag som du prenumererar på Azure. Azure debiterar dig i slutet av din fakturaperiod.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="compare-invoiced-charges-with-usage-file"></a>Jämför fakturerade kostnader med användnings filen

<a name="charges"></a>

Det första steget för att jämföra användning och kostnader är att ladda ned din faktura och dina användnings filer. I CSV-filen med detaljerad användning visas dina avgifter per faktureringsperiod och daglig användning. Den innehåller ingen skatte information. För att kunna hämta filerna måste du vara konto administratör eller ha ägar rollen.

Skriv *prenumerationer* i rutan sök i Azure Portal och klicka sedan på [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Navigera till prenumerationer](./media/review-individual-bill/navigate-subscriptions.png)

Klicka på prenumerationen i listan över prenumerationer.

Klicka på **fakturor**under **fakturering**.

Leta efter den som du vill ladda ned i listan över fakturor och klicka sedan på nedladdnings symbolen. Du kan behöva ändra TimeSpan för att Visa äldre fakturor. Det kan ta några minuter att generera filen med användnings information och faktura.

![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/review-individual-bill/download-invoice.png)

I fönstret Hämta användning och kostnader klickar du på **Hämta CSV** och **laddar ned faktura**.

![Skärmbild som visar Ladda ned faktura och förbrukningssidan](./media/review-individual-bill/usageandinvoice.png)

Om det **inte står tillgängligt** finns det flera orsaker till att du inte ser användnings information eller en faktura:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.
- Det finns ingen användning för fakturerings perioden.
- Ingen faktura har genererats ännu. Vänta till slutet av faktureringsperioden.
- Du har inte behörighet att visa fakturor. Du kanske inte ser gamla fakturor om du inte är konto administratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](../manage/manage-billing-access.md).
- Om du har en kostnadsfri utvärderingsversion eller ett månatligt kreditbelopp för din prenumeration som du inte har överskridit får du ingen faktura såvida du inte har ett Microsoft-kundavtal.

Därefter granskar du avgifterna. Din faktura visar värden för skatter och dina användnings kostnader.

![Exempel på Azure-faktura](./media/review-individual-bill/invoice-usage-charge.png)

Öppna den CSV-konfigurationsfil som du laddade ned. Summera värdet för alla objekt i kolumnen *kostnad* i slutet av filen.

![Exempel på användnings fil med summerad kostnad](./media/review-individual-bill/usage-file-usage-charges.png)

 Det summerade *kostnad* svärdet bör matcha priset för *användnings kostnader* på din faktura.

Dina användningsavgifter visas på mätarnivå. Följande termer innebär samma sak både i fakturan och i filen med detaljerad användning. Till exempel är faktureringsperioden på fakturan samma som den faktureringsperiod som visas i filen med detaljerad användning.

| Faktura (PDF) | Detaljerad användning (CSV)|
| --- | --- |
|Faktureringscykel | BillingPeriodStartDate BillingPeriodEndDate |
|Namn |Mätarkategori |
|Typ |Mätarunderkategori |
|Resurs |MeterName |
|Region |MeterRegion |
|Förbrukad | Kvantitet |
|Ingår |Kvantitet som ingår |
|Faktureringsbar |Överbliven kvantitet |
|Kurs | EffectivePrice|
| Värde | Kostnad |

Avsnittet **användnings debiteringar** i fakturan visar det totala värdet (kostnad) för varje mätare som förbrukades under fakturerings perioden. Följande bild visar till exempel en användnings avgift för Azure Storage tjänsten för resursen *P10 disks* .

![Användningsavgifter på faktura](./media/review-individual-bill/invoice-usage-charges.png)

I CSV-konfigurationsfilen filtrerar du efter *MeterName* för motsvarande resurs som visas på din faktura. Summera sedan värdet *kostnad* för objekt i kolumnen. Här är ett exempel.

![Summerings värde för användnings filen för MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Det summerade *kostnad* svärdet bör exakt matcha *förbruknings* kostnaden för den enskilda resursen som debiteras på din faktura.

Mer information finns i [förstå din Azure-faktura](understand-invoice.md) och [förstå din Azure-detaljerade användning](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Jämför avgifter och användning i kostnads analys

Kostnads analys i Azure Portal kan också hjälpa dig att verifiera dina avgifter. För att få en snabb överblick över din fakturerade användning och kostnader väljer du din prenumeration på [sidan prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal. Klicka sedan på **kostnads analys** och sedan på **faktura Detaljer**i listan vyer.

![Exempel som visar val av faktura Detaljer](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Sedan väljer du en tids period för fakturan i listan datum intervall. Lägg till ett filter för faktura nummer och välj sedan den InvoiceNumber som matchar den på din faktura. Kostnads analys visar kostnads information för dina fakturerade artiklar.

![Exempel som visar information om fakturerad kostnad i kostnads analys](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Kostnader som visas i kostnads analys bör överensstämma exakt med kostnaden för *användnings kostnader* för den enskilda resursen som debiteras på din faktura.

![Användningsavgifter på faktura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Externa tjänster debiteras separat

Externa tjänster eller Marketplace-avgifter är för resurser som har skapats av programvaruleverantörer från tredje part. Dessa resurser är tillgängliga för användning från Azure Marketplace. Till exempel är Barracuda Firewall en Azure Marketplace-resurs som erbjuds av en tredje part. Alla avgifter för brandväggen och dess motsvarande mätare visas som avgifter för externa tjänster.

Avgifter för externa tjänster debiteras separat. Avgifterna står inte på din Azure-faktura. Mer information finns i [Förstå dina avgifter för externa Azure-tjänster](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Resurser debiteras per användningsmätare

Azure debiterar inte direkt baserat på resurskostnaden. Avgifter för en resurs beräknas med hjälp av en eller flera mätare. Mätare används för att spåra användningen av en resurs under hela dess livslängd. Dessa mätare används sedan för att beräkna fakturan.

När du skapar en enda Azure-resurs, till exempel en virtuell dator, har en eller flera mätnings instanser skapats. Mätare används för att spåra användningen av resursen över tid. Varje mätare skickar användningsposter som används av Azure för att beräkna fakturan.

Till exempel kan en enskild virtuell dator (VM) som skapats i Azure ha följande mätare som skapas för att spåra dess användning:

- Instanstimmar
- IP-adresstimmar
- Dataöverföring in
- Dataöverföring ut
- Standard – hanterad disk
- Standard – åtgärder för hanterad disk
- Standard – I/O-disk
- Standard – I/O-blockblobsläsning
- Standard – I/O-blockblobsskrivning
- Standard – I/O-blockblobsborttagning

När den virtuella datorn skapas börjar varje mätare att skicka användningsposter. Den här användningen och priset för mätaren spåras i Azure-mätarsystemet.

Du kan se de mätare som användes för att beräkna din faktura i CSV-filen för användning.

## <a name="payment"></a>Betala din faktura

Om du ställer in ett kredit kort som betalnings metod debiteras betalningen automatiskt inom 10 dagar efter att fakturerings perioden är slut. På ditt kreditkortsutdrag skulle det stå **MSFT Azure** på radobjektet.

Information om hur du ändrar det kreditkort som debiteras finns i [Lägga till, uppdatera eller ta bort ett kreditkort för Azure](../manage/change-credit-card.md).

Om du [betalar med faktura](../manage/pay-by-invoice.md) skickar du betalningen till den plats som anges längst ned på fakturan.

Om du vill kontrollera statusen för din betalning [skapar du en supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Jämför fakturerade kostnader med användnings filen
> * Jämför avgifter och användning i kostnads analys

Börja använda kostnads analys genom att slutföra snabb starten.

> [!div class="nextstepaction"]
> [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md)
