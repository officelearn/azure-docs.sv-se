---
title: Vanlig användning av kostnadsanalyser i Azure Cost Management
description: I den här artikeln beskrivs hur du hämtar resultat från vanliga kostnadsanalyser i Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/31/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: a333cad51e6fc089e7e6994c7b89210b12686cd5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520881"
---
# <a name="common-cost-analysis-uses"></a>Vanlig användning av kostnadsanalyser

Azure Cost Management-användare vill ofta ha svar på samma frågor som många andra. Den här artikeln visar hur du hämtar resultat från vanliga kostnadsanalyser i Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Visa kostnadsuppdelning efter Azure-tjänst

Om du visar kostnaderna för en Azure-tjänst, blir det enklare att förstå vilka delar av infrastrukturen som kostar mest. Beräkningskostnaderna för virtuella datorer kan till exempel vara låga. Men du kan ändå ha avsevärda nätverkskostnader på grund av mängden information som genereras från de virtuella datorerna. Det är viktigt att förstå de primära kostnadsdrivkrafterna för dina Azure-tjänster, så att du kan anpassa tjänstanvändningen vid behov.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Kostnad efter tjänst** och gruppera sedan efter **Tjänstnivå**.
1. Ändra vyn till **Tabell**.

[![Kostnadsuppdelning efter Azure-tjänst](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Visa kostnadsuppdelning efter Azure-resurs

Dina tjänster skapas med Azure-resurser. Att granska kostnader baserat på resurser kan hjälpa dig att snabbt identifiera dina primära kostnadsfaktorer. Om en tjänst har resurser som är för dyra, bör du överväga ändringar som minskar kostnaderna.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Kostnad efter resurs**.
1. Ändra vyn till **Tabell**.

[![Visa kostnadsuppdelning efter Azure-resurs](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Visa kostnadsuppdelning efter valda dimensioner

Med dimensionerna kan du organisera dina kostnader baserat på olika metadatavärden som visas i dina avgifter. Du kan till exempel gruppera dina kostnader efter plats.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj filtret **Gruppera efter**.  
    [![Välj ett objekt i Gruppera efter](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Du kan även spara vyn för senare användning.
1. Klicka på ett cirkeldiagram under grafen om du vill se mer detaljerade data.  
    [![Visa kostnadsuppdelning efter valda dimensioner](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Visa kostnader per dag eller per månad

Att titta på dagliga och månatliga kostnader kan hjälpa dig att se om det finns en tid i veckan eller under året när kostnaderna är högre. Leder det till en motsvarande ökning av dina Azure-kostnader om du har mer kundtrafik under en semesterperiod? Är fredag en dyrare dag än måndag?

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Ange **Kornighet** till **Varje månad** eller **Varje dag**.

[![Visa kostnader per dag](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Visa dina avgifter för virtuella Spot-datorer

Virtuella Spot-datorer kan ge stora besparingar för arbetsbelastningar som kan hantera avbrott. Arbetsbelastningar körs på outnyttjad Azure-kapacitet. Eftersom de kan uteslutas när som helst får de virtuella Spot-datorerna en betydande rabatt. Använd följande steg för att visa dina kostnader för virtuella Spot-datorer.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Till exempel **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
2. Lägg till ett filter för **prismodell: Spot**.

![Exempel som visar filtret för virtuella Spot-datorer](./media/cost-analysis-common-uses/spot-vm-filter.png)

Prismodellsdimensionen används också för att visa på-begäran- och reservationsavgifter.

## <a name="view-your-reservation-charges"></a>Visa dina reservationsavgifter

Reserverade instanser är ett sätt för dig att spara pengar med Azure. Med reservationer betalar du i förskott för ett visst antal resurser över tid. Kostnadsanalyser visar kostnaderna som de visas på din faktura. Avgifterna visas som faktiska kostnader eller som avskrivningar under din reservationsperiod.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Till exempel **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Lägg till ett filter för **Prismodell: Reservation**.
1. Under **omfång** och bredvid den kostnad som visas klickar du på nedåtpilen och väljer antingen **Verklig kostnad** eller **Periodiserad kostnad** som mått.

![Välj ett kostnadsmått](./media/cost-analysis-common-uses/metric-cost.png)

Varje mått påverkar hur data visas för dina reservationsavgifter.

**Faktisk kostnad** – visar inköpet som det visas på fakturan. Om du till exempel köpte en reservation för ett år för 1200 USD i januari, så visar kostnadsanalysen en kostnad på 1200 USD i januari månad för reservationen. Den visar ingen reservationskostnad för årets övriga månader. Om du grupperar dina faktiska kostnader per virtuell dator skulle en virtuell dator som tog emot reservationsförmånen för en månad ha noll kostnader för den månaden.

**Periodiserad kostnad** – visar ett reservationsköp uppdelat som en periodiserad kostnad under reservationsperiodens varaktighet. I exemplet ovan visar kostnadsanalys en kostnad på 100 USD för varje månad under året, om du har köpt en reservation för ett år för 1200 USD i januari. Om du grupperar kostnader per virtuell dator i det här exemplet skulle du se kostnaden för varje virtuell dator som har tagit emot reservationsförmånen.

## <a name="view-your-reservation-utilization"></a>Visa reservationsförbrukningen

När du har köpt en reservation är det viktigt att spåra dess användning så att du får det du betalar för. Om du till exempel köpte 10 virtuella datorer för ett år och bara använder fem av dem, är halva köpet i stort sett bortkastat. Det finns två olika sätt att utvärdera din användning:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Visa oförbrukade RI-kostnader i kostnadsanalys

Följ stegen nedan för att identifiera vilken kostnad som för närvarande går till spillo varje månad av ditt reservationsköp.

1. Gå till Azure Portal och navigera till kostnadsanalys för omfånget där reservationen tillämpas. Till exempel **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Lägg till ett filter för **Prismodell: Reservation**.
1. Välj vyn **periodiserad kostnad**.
1. Ange Kornighet till **Månadsvis**.
1. Ange tidsperioden till det aktuella året eller din reservationsperiod.
1. Ange diagramtypen till **Kolumn (staplad)** .
1. Gruppera avgifter efter **avgiftstyp**.
1. Granska resultaten för `unusedreservation`-värden.

[![Exempel som visar reservationsanvändning](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Visa användning i reservationer

Detaljerade anvisningar finns i [Optimera reservationanvändningen](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Visa kostnader för en specifik tagg

Många Azure-användare taggar sina resurser, till exempel ett kostnadsställe eller en utvecklingsmiljö (produktion och test) för att kategorisera sina kostnader på ett bättre sätt. Taggar visas som en dimension i kostnadsanalysen. Du kan använda dimensionen för att få insikter i dina anpassade taggningskategoriseringar.

Stödet för taggar gäller användning som rapporterats *efter* att taggen tillämpades på resursen. Taggar tillämpas inte retroaktivt på samlade kostnader.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Gruppera efter** för taggen.

[![Visa kostnader för en specifik tagg](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Ladda ned din användningsinformation

Rapportfilen med användningsinformationen (i CSV-format) innehåller en uppdelning av alla avgifter på en faktura. Du kan använda rapporten för att jämföra den med och bättre förstå din faktura. Varje fakturerad avgift på fakturan motsvarar nedbrutna avgifter i användningsrapporten.

1. I Azure-portalen går du till fliken **Användning och avgifter** för ett faktureringskonto eller en prenumeration. Ett exempel: **Cost Management + Fakturering** > **Fakturering** > **Användning + avgifter**.
1. Välj det radobjekt som du vill ladda ned från och klicka sedan på nedladdningssymbolen.  
    [![Ladda ned användning och avgifter](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Välj den användningsfil som ska laddas ned.  
    ![Välj en användningsfil som ska laddas ned](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Visa månatlig EA-kostnadsuppdelning

Din EA-registrering medför kostnader för hela organisationen. Att förstå hur kostnader uppstår och faktureras över tid hjälper dig att hitta lämpliga intressenter som kan säkerställa att kostnaderna hanteras på ett ansvarsfullt sätt.

Kostnaderna visas bara för din aktiva registrering. Om du överförde en registrering (inaktiv) till en ny (aktiv) visas inte kostnader för den föregående registreringen i Cost Management.


1. Gå till **Cost Management och fakturering** > **Översikt** i Azure-portalen.
1. Klicka på **Uppdelning** för den aktuella månaden och se uppdelningen av ditt ekonomiska åtagande.  
    [![Översikt över EA-kostnader – sammanfattning av uppdelning](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Klicka på fliken **Användning och avgifter** och visa den föregående månadens uppdelning under det valda tidsintervallet.  
    [![Fliken Användning och avgifter](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Visa registreringens månadskostnad per period

Använd en grafisk vy över registreringens månadskostnad för att förstå kostnadstrender och fakturerade belopp under en viss period.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj din registrering och ange registreringsperiod.
1. Ange kornigheten till varje månad och ställ sedan in vyn på **Kolumn     (staplad)** .

Du kan gruppera efter och filtrera dina data om du vill ha en mer detaljerad analys.

[![Månatlig registreringskostnad per period](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Visa ackumulerade kostnader för EA-registrering

Se de nettoavgifter som har ackumulerats över tid för att förstå övergripande kostnader för din organisation under en viss period.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj din registrering och visa dina aktuella ackumulerade kostnader.

[![Ackumulerade registreringskostnader](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Nästa steg
- Om du inte redan har slutfört den första snabbstarten för Cost Management, kan du läsa den i [Börja analysera kostnader](quick-acm-cost-analysis.md).
- Läs [Cost Management-dokumentationen](../index.yml).
