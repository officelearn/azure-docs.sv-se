---
title: Självstudie – Optimera reserverad instanskostnad med Cloudyn i Azure | Microsoft Docs
description: I den här självstudien får du lära dig hur du optimerar dina reserverade instanskostnader för Azure och Amazon Web Services (AWS).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: e705a0f57b598eb60932ca1056131cedc1416ffc
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394484"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Självstudie: Optimera reserverade instanser

I den här självstudien får du lära dig hur Cloudyn kan hjälpa dig att optimera dina reserverade instanskostnader och din användning för Azure och Amazon Web Services (AWS). En reserverad instans hos en molntjänstleverantör är ett åtagande om ett långsiktigt avtal där du binder dig i förväg att använda den virtuella datorn. Avtalet kan ibland ge betydande besparingar jämfört med VM-prismodeller av standardtyp där du betalar per användning. Potentiella besparingar realiseras bara när du utnyttjar dina reserverade instansers kapacitet fullt ut.

I den här självstudien beskrivs hur Azure och AWS reserverade instanser (RI) stöds av Cloudyn. Kursen beskriver också hur du kan optimera reserverade instanskostnader. Det gör du främst genom att säkerställa att du utnyttjar dina resurser fullt ut. I den här kursen ska du:

> [!div class="checklist"]
> * Lära dig mer om Azure RI-kostnader
> * Lära dig om fördelarna med reserverade instanser
> * Optimera kostnader för Azure RI
> * Visa RI-kostnader
> * Utvärdera kostnadseffektivitet för Azure RI
> * Optimera AWS RI-kostnader
> * Köpa rekommenderade RI
> * Ändra oanvända reservationer

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha ett Azure-konto.
- Du måste antingen ha en utvärderingsregistrering eller en betald prenumeration för Cloudyn.
- Du måste ha köpt dina RI i Azure eller AWS.

## <a name="understand-azure-ri-costs"></a>Lära dig mer om Azure RI-kostnader

När du köper reserverade instanser av virtuella datorer i Azure betalar du direkt för framtida användning. Förskottsbetalningen täcker kostnaden för din framtida användning av de virtuella datorerna:

- för en specifik typ
- i en viss region
- för en period om ett eller tre år
- upp till en köpt VM-kvantitet.

Du kan visa dina köpta, reserverade instanser av virtuella datorer under [Reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) på Azure-portalen.

Begreppet  _reserverad Azure VM-instans_ avser enbart en prismodell. Den förändrar inte på något sätt de virtuella datorer du redan kör. Begreppet är specifikt för Azure och benämns vanligtvis som en _reserverad instans_ eller _reservation_. De reserverade instanser som du har köpt gäller inte för specifika virtuella datorer, utan kan användas för alla matchande virtuella datorer. Till exempel en reservation för en VM-typ som körs i en region som du har valt för din köpta reservation.

Köpta reserverade instanser gäller endast för den grundläggande maskinvaran. De omfattar inte programvarulicenser för en virtuell dator. Till exempel kanske du reserverar en instans och har en matchande virtuell dator som kör Windows. Den reserverade instansen omfattar bara baskostnaden för den virtuella datorn. I det här exemplet betalar du hela priset för alla Windows-licenser som krävs. För att få rabatt på operativsystemet eller annan programvara som körs på din virtuella dator bör du överväga att använda [Azure Hybrid-förmåner](https://azure.microsoft.com/pricing/hybrid-benefit). Med Hybrid-förmåner kan du få en liknande typ av rabatt för dina programvarulicenser som de reserverade instanserna får för de virtuella basdatorerna.

Användningen av reserverade instanser har ingen direkt påverkan på kostnaden. Med andra ord har det samma effekt om du kör en virtuell dator med 100 % eller 0 % processoranvändning: du betalar i förskott för VM-allokeringen – inte för den faktiska användningen.

Följande bild visar hur standardiserad på begäran-användning av virtuella datorer förhåller sig till kostnader för reserverade instanser:

![På begäran-kostnader kontra kostnader för reserverade instanser](./media/tutorial-optimize-reserved-instances/azure01.png)



De röda staplarna visar den ackumulerade kostnaden för köpet av den reserverade instansen. Du betalar bara engångsavgiften. Användningen av den virtuella datorn är kostnadsfri. De blå staplarna visar den ackumulerade kostnaden för samma virtuella dator, som här körs med betalning per användning eller en på begäran-prismodell. Någonstans mellan den sjunde och åttonde månaden i VM-användningen finns en *brytpunkt*. I det här exemplet börjar du spara pengar vid den åttonde månaden.

## <a name="benefits-of-ris"></a>Fördelar med reserverade instanser

Varje köp av en reserverad instans avser en virtuell dator av en specifik storlek och för en viss plats. Följande bild visar ett exempel med D2s \_v3 som körs på platsen Västra USA:

![Information om reserverad instans i Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

Köpet av den reserverade instansen blir fördelaktigt när en virtuell dator körs ett tillräckligt antal timmar för att nå brytpunkten för reservationen. Den virtuella datorn måste matcha storlek och en plats för din reserverade instans. Till exempel är brytpunkten ungefär vid sju och en halv månad i föregående diagram. Köpet blir med andra ord fördelaktigt när den virtuella dator som matchar reservationen körs i minst 7,5 månader \* 30 dagar \* 24 timmar = 5 400 timmar. Om den matchande virtuella datorn körs i mindre än 5 400 timmar blir reservationen dyrare än om du betalar per användning.

Brytpunkten kan variera för varje VM-storlek och för varje plats. Den beror också på ditt förhandlade betala per användning-pris för den virtuella datorn. Innan du genomför ett köp bör du kontrollera vilken brytpunkt som gäller i ditt fall.

En annan aspekt att ta hänsyn till när du köper reservationen är den reserverade instansens omfattning. Omfånget avgör om fördelen med reservationen ska delas eller om den gäller för en viss prenumeration. Delade reserverade instanser tillämpas slumpmässigt för alla dina prenumerationer på de matchande virtuella datorer som hittas först.

Omfattningen för det delade köpet är den mest flexibla och rekommenderas alltid när det är möjligt. Dina möjligheter att utnyttja alla dina reserverade instanser är betydligt högre med den delade omfattningen. När ägaren av en prenumeration betalar för den reserverade instansen kan det dock saknas andra alternativ än att köpa den med omfattningen Enstaka prenumeration.

## <a name="optimize-azure-ri-costs"></a>Optimera kostnader för Azure RI

Cloudyn stödjer reserverade instanser och Hybrid-förmåner genom att:

- Visa vilka kostnader som är förknippade med prissättningsmodellerna
- Spåra RI-användning
- Utvärdera effekten av RI
- Fördela RI-kostnader enligt dina principer

Den första åtgärden som du bör vidta innan du köper en reserverad instans är att bedöma effekten av RI-köpet:

- Hur mycket kostar det dig?
- Hur mycket sparar du?
- Var finns brytpunkten?

Effektrapporten för reserverade instanser kan hjälpa dig att besvara de här frågorna.

## <a name="assess-azure-ri-cost-effectiveness"></a>Utvärdera kostnadseffektivitet för Azure RI

På Cloudyn-portalen navigerar du till **Optimerare** > **RI-jämförelse** och väljer sedan **Reserved Instance Purchase Impact** (Köpeffekt för reserverad instans).

Välj VM-storlek (instanstyp), plats (Region), reservationsperiod, antal och förväntad körning i rapporten Reserved Instance Purchase Impact (Köpeffekt för reserverad instans). Därefter kan du bedöma om köpet innebär en besparing för dig.

Om du till exempel köper en reservation för en VM av typ DS1\_v2 i östra USA och den körs 24x7 under ett helt år, kan du spara 369,48 USD per år. Brytpunkten inträffar vid fem månader. Se följande bild:

![Brytpunkt för reserverad Azure-instans](./media/tutorial-optimize-reserved-instances/azure03.png)

Om den däremot bara körs 50 % av tiden blir brytpunkten vid 10 månader och besparingen endast 49,74 USD per år. Då har du kanske ingen fördel av att köpa reservationen instanstypen i det här exemplet. Se följande bild:

![Brytpunkt i Azure](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Visa RI-kostnader

När du köper en reservation gör du en engångsbetalning. Det finns två sätt att visa betalningen i Cloudyn:

- Verklig kostnad
- Amorterad kostnad

### <a name="actual-reserved-instance-cost"></a>Verklig kostnad för reserverad instans

Rapporterna för verklig kostnadsanalys och analys över tid visar det totala belopp som du har betalat för reservationen, med början från inköpsmånaden. De hjälper dig att visa dina faktiska utgifter under en period.

Gå till **Kostnad** > **Kostnadsanalys** > på Cloudyn-portalen och välj sedan **Actual Cost Analysis** (verklig kostnadsanalys) eller **Actual Cost Over Time** (verklig kostnad över tid). Nästa steg är att ställa in filter. Du kan till exempel filtrera enbart Azure/VM-tjänst och gruppera efter resurstyp och prismodell. Se följande bild:

![Verklig kostnad för reserverad instans](./media/tutorial-optimize-reserved-instances/azure05.png)

Du kan filtrera på en tjänst, i det här exemplet **Azure/VM**, och gruppera efter **Prismodell** och **Resurstyp** såsom följande bild visar:

![Grupper och filter i rapporten Verklig kostnad](./media/tutorial-optimize-reserved-instances/azure06.png)

Du kan också analysera den typ av betalningar som du har gjort i form av engångsavgifter och avgifter för användning och licenser.

### <a name="amortized-reserved-instance-cost"></a>Amorterad kostnad för reserverad instans

Du betalar en förskottsavgift som visas för inköpsmånaden när du köper en RI. Den syns inte på efterföljande fakturor. Därför kan det vara missledande att titta på din månatliga användning. Din månadskostnad motsvarar i själva verket din månatliga användning plus den proportionella (amorterade) delen av alla tidigare betalade engångsavgifter. Rapporten Amorterad kostnad kan hjälpa dig att få en realistisk bild.

Den amorterade kostnaden för den reserverade instansen beräknas genom att engångsavgiften för reservationen amorteras över reservationsperioden. I Verklig kostnad-rapporter syns engångsavgifter under månaden då reservationen köptes. Dagliga och månatliga utgifter visas inte i den faktiska kostnaden för distributionen. Rapporter av typen Amorterad kostnad visar den verkliga kostnaden för distributionen över tid.  Rapporten Amorterad kostnad är det enda sättet att visa dina verkliga kostnadstrender. Det är också det enda sättet att visa dina framtida utgifter.

I rapporten Verklig kostnad kunde du se en topp för ett RI-köp den 16 november på 747 USD. I rapporten Amorterad kostnad (se följande bild) finns en delkostnad för en dag den 16 november. Från den 17 november ser du den amorterade RI-kostnaden på 747/365 USD = 2,05 USD. Kanske lägger du också märke till att den köpta reservationen är oanvänd, vilket innebär att du kan optimera den genom att byta den mot en annan VM-storlek.

Du visar den genom att gå till **Kostnad** > **Kostnadsanalys** > och väljer sedan **Amortized Cost Analysis** (amorterad kostnadsanalys) eller **Amortized Cost Over Time** (amorterad kostnad över tid).

![Amorterad kostnad för reserverad instans](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Optimera AWS RI-kostnader

Reserverade instanser är ett öppet åtagande. De är användbara vid långvarig användning av virtuella datorer, eftersom reserverade instanser är billigare än på begäran-instanser. Det krävs dock en tillräcklig användningsfrekvens. Åtagandet är att använda resurser, oftast virtuella datorer, under en definierad period – ett eller tre år. När du gör åtagandet att köpa betalar du i förskott för resurserna med en reservation. Det är dock inte säkert att du använder vad du har åtagit dig i reservationen.

Till exempel kanske du bedömer din miljö och kommer fram till att du har haft 20 standardiserade D2-instanser som körts konstant under det senaste året. Du har då möjlighet att köpa en reservation för dem och spara betydande belopp. I ett annat exempel kanske du har åtagit dig att använda tio MA4-instanser under året. Hittills har du dock bara använt fem av dem. Båda exemplen visar på ineffektiv RI-användning. Det finns två sätt att optimera kostnader för reserverade instanser med Cloudyns optimeringsrapporter:

- Granska köprekommendationerna för vad du kan köpa baserat på din användningshistorik
- Ändra oanvända reservationer

Du använder rapporterna _EC2 RI Buying Recommendations_ (EC2 Köprekommendationer för RI) och _EC2 Currently Unused Reservations_ (EC2 Aktuella oanvända reservationer) för att förbättra RI-förbrukning och -kostnader.

## <a name="buy-recommended-ris"></a>Köpa rekommenderade RI

Cloudyn jämför användningen av på begäran-instanser med potentiella reserverade instanser. Om möjliga besparingar hittas visas rekommendationerna i rapporten EC2 Köprekommendationer.

Gå till rapportmenyn längst upp i portalen och klicka på **Optimizer** (Optimerare)  > **Pricing Optimization** (Prisoptimering)  > **EC2 RI Buying Recommendations** (EC2 Köprekommendationer för RI).

Följande bild visar köprekommendationerna från rapporten.

![Köprekommendationer](./media/tutorial-optimize-reserved-instances/aws01.png)

I det här exemplet innehåller Cloudyn\_-kontot 32 köprekommendationer för reserverade instanser. Om du följer alla köprekommendationer har du möjlighet att spara 137 770 USD årligen. Tänk på att rekommendationer för köp från Cloudyn förutsätter att användningen av dina befintliga arbetsbelastningar förblir oförändrad.

Om du vill visa information som förklarar varför varje köp rekommenderas klickar du på plustecknet ( **+** ) under **Berättiganden** . Här är ett exempel för den första rekommendationen i listan.

![Köpberättiganden](./media/tutorial-optimize-reserved-instances/aws02.png)

Föregående exempel visar att det skulle kosta 90 456 USD per år att köra arbetsbelastningen på begäran. Köper du i stället reservationen i förväg skulle samma arbetsbelastning kosta 56 592 USD och spara dig 33 864 USD per år.

Klicka på plustecknet bredvid **EC2 RI Purchase Impact** (EC2 Köpeffekt för RI) om du vill visa brytpunkten för ett år, så att du kan se ungefär när din investering realiseras. I följande exempel börjar den ackumulerade på begäran-kostnaden att överstiga den ackumulerade RI-kostnaden cirka åtta månader efter att du har genomfört köpet:

![Köpeffekt](./media/tutorial-optimize-reserved-instances/aws03.png)

Vid den tidpunkten börjar du spara pengar.

Du kan granska **Instances over Time** (Instanser över tid) för att verifiera hur väl den föreslagna köprekommendationen stämmer. I det här exemplet kan du se att i genomsnitt sex instanser har använts för arbetsbelastningen under den senaste 30-dagarsperioden.

![Instanser över tid](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Ändra oanvända reservationer

Oanvända reservationer är vanliga i många konsumenters datormiljöer i molnresurser. Genom att se till att oanvända reservationer utnyttjas fullt ut kan du spara pengar när du ändrar reservationerna, så att de tillgodoser dina befintliga behov. Du kan till exempel ha en prenumeration som innehåller standardiserade D3-instanser som körs på Linux. Om du inte kommer att utnyttja reservationen fullt ut kan du ändra instanstypen. Du kan också flytta de oanvända resurserna till en annan reservation eller till ett annat konto.

AWS säljer reserverade instanser för specifika tillgänglighetszoner och -regioner. Om du har köpt reserverade instanser för en specifik tillgänglighetszon kan du inte flytta reservationerna mellan olika zoner. Däremot kan du enkelt flytta regionala reserverade instanser mellan zoner med hjälp av rapporten **EC2 Currently Unused Reservations** (EC2 Aktuella oanvända reservationer). Alternativt kan du ändra dem så att de får ett regionalt omfång och tillämpar matchande instanser för samtliga tillgänglighetszoner.

Klicka på **Optimizer** (Optimerare)  > **Inefficiencies** (Ineffektivitet)  > **EC2 Currently Unused Reservations** (EC2 Aktuella oanvända reservationer).

Följande bilder visar rapporten med oanvända reserverade instanser.

![Oanvända reservationer](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Klicka på plustecknet under **Information** för att visa information om en specifik reservation.

![Information om oanvända reservationer](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

I föregående exempel finns totalt 77 oanvända reservationer i olika tillgänglighetszoner. Den första reservationen har 51 oanvända instanser. Längre ned i listan kan vi se potentiella ändringar av reserverade instanser som du kan göra med hjälp av instanstypen **m3.2xlarge** i tillgänglighetszonen **us-east-1c**.

Klicka på **Ändra** för den första reservationen i listan för att öppna sidan **Ändra RI**, som visar data om reservationen.

![Ändra RI](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

Reserverade instanser som du kan ändra visas i en lista. I följande exempelbild finns det 51 oanvända reservationer som du kan ändra, men det behövs 54 mellan de två reservationerna. Om du ändrar dina oanvända reservationer så att alla används kommer fyra instanser att fortsätta att köras på begäran. I det här exemplet kan du dela dina oanvända reservationer, så att den första reservationen använder 30 och den andra reservationen använder 21.

Klicka på plustecknet för den första reservationsposten och ange **30** som **Reservationskvantitet**. För den andra posten anger du **21** som reservationskvantitet och klickar sedan på **Tillämpa**.

![Ändra reservationskvantitet](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Alla dina oanvända instanser för reservationen används nu fullt ut och 51 instanser körs inte längre på begäran. I det här exemplet sparar du pengar åt din organisation genom att minska på begäran-användningen avsevärt och utnyttja reservationer som du redan betalar för.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du genomfört följande uppgifter:

> [!div class="checklist"]
> * Lärt dig mer om Azure RI-kostnader
> * Lärt dig om fördelarna med reserverade instanser
> * Optimerat kostnader för Azure RI
> * Visat RI-kostnader
> * Utvärderat kostnadseffektivitet för Azure RI
> * Optimerat kostnader för AWS RI
> * Köpt rekommenderade RI
> * Ändrat oanvända reservationer


Gå vidare till nästa självstudie om du vill lära dig att styra åtkomsten till data.

> [!div class="nextstepaction"]
> [Styra åtkomsten till data](tutorial-user-access.md)
