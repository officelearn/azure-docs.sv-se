---
title: Använda rapporter för kostnaden i Azure kostnaden Management | Microsoft Docs
description: Den här artikeln beskriver hur du använder olika kostnad rapporter i Cloudyn-portalen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: fa263a4b6f41e2b31328f46b8d1341d0d74c9a85
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="use-cost-management-reports"></a>Använda Cost Management-rapporter

Den här artikeln beskriver hur du använder olika kostnad rapporter i Cloudyn-portalen. De flesta Cloudyn rapporter är intuitiva och har ett enhetligt utseende. En översikt över hur Cloudyn rapporter finns i [förstå kostnad rapporter](understanding-cost-reports.md). Artikeln beskriver också olika alternativ och fält som används i de flesta rapporter.

## <a name="cost-analysis-reports"></a>Kostnad analysrapporter

Kostnad analysrapporter visa faktureringsinformation från Cloud-providers. Med hjälp av rapporter, kan du gruppera och öka detaljnivån i olika datasegment i filen fakturering. Rapporterna Aktivera detaljerade kostnaden navigering mellan moln leverantörer rådata faktureringsinformation.

Kostnad analysrapporter gruppera aldrig kostnader efter taggar. Taggen-baserade reporting är endast tillgängligt i de kostnad rapporter ange när du skapar en kostnad modell med kostnaden allokering 360.

### <a name="actual-cost-analysis"></a>Analys av faktiska kostnader

Verklig kostnad rapporten visar dina viktigaste kostnaden deltagare, inklusive kostnader och enstaka avgifter.

 Använd den faktiska kostnaden analysrapporten som:

- Analysera och övervaka faktiska kostnader som användes under en angiven tidsperiod
- Schemalägga en avisering om tröskelvärde
- Analysera showback och återbetalning kostnader

#### <a name="to-use-the-actual-cost-analysis-report"></a>Att använda faktiska kostnaden analysrapporten

Utför följande steg minimum. Du kan också använda andra alternativ och fält.

1. Välj ett datumintervall.
2. Välj ett filter.

Du kan högerklicka på rapportresultat för att öka detaljnivån i dem och visa mer detaljerad information.

![Exempel på verklig kostnadsanalys](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Verklig kostnad över tid

Verklig kostnad över tid rapporten är en standardkostnaden analysrapporten distribuerar kostnaden över en angiven tidpunkt-lösning. Rapporten visar utgifter över tid så att du kan se trender och identifiera utgiftsgränsen oegentligheter. Den här rapporten visar dina viktigaste kostnaden deltagare inklusive kostnader och enstaka reserverade instans avgifter som används under en vald tidsperiod.

Använd rapporten faktiska kostnaden över tid för att:

- Visa kostnaden trender över tid.
- Hitta oegentligheter kostnaden.
- Hitta alla kostnad-relaterade frågor som rör Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Använda rapporten faktiska kostnaden över tid:

Utför följande steg minimum. Du kan också använda andra alternativ och fält.

- Välj ett datumintervall.

Du kan exempelvis välja grupper om du vill visa kostnaderna över tid. Och sedan lägga till filter för att begränsa resultaten.

![Exempel på verklig kostnad över tid](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Amorterade kostnadsrapporter

Den här uppsättningen amorterade kostnad rapporter visar linjär icke-användningen baserat serviceavgifter eller en betalning kostnader och utspridda kostnaderna på jämnt under sin livslängd.

Till exempel kan en avgifter innehålla:

- Årliga supportavgifter
- Årliga komponenten avgifter för säkerhet
- Reserverade instanser köpa avgifter
- Vissa Azure Marketplace-objekt

I filen fakturering enstaka avgifter kännetecknas när tjänsten förbrukning start- och slutdatum, eller tidsstämpel har samma värden. Cloudyn identifierar dem sedan som en avgifter som kan vara amorteras. Vara det går inte att amorteras andra förbrukning-baserade tjänster med kostnader för användning på begäran.

Granska följande exempelbild av en verklig kostnad över tidrapport för att illustrera amorterade kostnader. I det här exemplet visar en insamling kostnaden på 23 augusti. Det kan verka avvikelser jämfört med vanliga dagliga kostnadstrend. Rotorsak analys och data navigering identifieras kostnaden som en årliga AWS service APN-reservation, vilket är en enstaka avgift köpts och debiteras på samma dag. Du kan se hur den här kostnaden amorteras i nästa avsnitt.

![Verklig kostnad över tid rapporten exemplet visar enstaka kostnad](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Använda rapporten amorteras kostnaden över tid:

Utför följande steg minimum. Du kan också använda andra alternativ och fält.

1. Välj ett datumintervall.
2. Välj en tjänst och en Provider.

Genomförandet vidarebefordran i föregående exempel, du kan se att den enstaka kostnaden nu amorteras i följande bild:

![Exempel på amorterade kostnaden över tid](./media/use-reports/amort-cost-over-time.png)

Ovanstående bild visar amorterade kostnaden för APN-reservation kostnaden över tid. Den här rapporten visar en avgift amorteringen och APN kostnaden som en årliga reservation köp. APN kostnaden jämnt dagligen som 1/365th av reservation direkta kostnaden.

## <a name="cost-allocation-analysis-reports"></a>Kostnad allokering analysrapporter

Kostnad allokering analysrapporter är tillgängliga när du har skapat en kostnad-modell med kostnaden allokering 360. Cloudyn bearbetar kostnad/faktureringsinformation och matchar data till data användnings- och tagg moln-konton. Om du vill matcha data kräver Cloudyn åtkomst till din användningsdata. Konton som saknar referenser, är märkta som ej kategoriserade resurser.

### <a name="cost-analysis-report"></a>Cost Analysis-rapport

Kostnad analysrapporten ger inblick i dina moln-förbrukning och utgifter under en vald tidsperiod. De principer som angetts i kostnaden allokering Manager används i rapporten kostnaden.

Hur beräknas den här rapporten i Cloudyn?

Cloudyn garanterar allokering behåller integriteten för varje länkade konto genom att använda kontot tillhörighet. Det innebär ett konto som inte använder en specifik tjänst inte har några kostnader för den här tjänsten som allokerats till den. Kostnaderna påförs i att kontot finns kvar i det kontot och beräknas inte av principer för tilldelning. Du kan till exempel ha fem länkade konton. Om endast tre av dem använder lagringstjänster allokeras endast kostnaden för lagringstjänster över taggar i tre konton.

 Använd kostnad analysrapporten som:

- Visa en aggregerad vy över hela distributionen för en viss tidsperiod.
- Visa kostnaderna efter taggkategorier baserat på principer som skapas i modellen kostnaden.

#### <a name="to-use-the-cost-analysis-report"></a>Använda rapporten kostnad:

1. Välj ett datumintervall.
2. Lägga till taggar, vid behov.
3. Lägg till grupper.
4. Välj en modell för kostnad som du skapade tidigare.

Följande bild visar ett exempel kostnaden analysrapporten sol-format. Ringar visa grupper. Den yttersta ringen visar tjänsten och den inre cirkeln enhet.

![Exempel på kostnad analys](./media/use-reports/cost-analysis01.png)



Här är exempel på samma information i en tabellvy.

![Exempel på kostnad analys](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Rapport för kostnad över tid

Kostnaden över tid rapporten visar utgifter över tid så att du kan se trender och identifiera oegentligheter i distributionen. Den visas i stort sett kostnaderna fördelade över en definierad period. Rapporten innehåller de huvudsakliga kostnaden deltagare inklusive kostnader och enstaka reserverade instans avgifter som används under en vald tidsperiod. Principer som angetts i kostnaden Manager 360 graders kan användas i den här rapporten.

Använd rapporten kostnaden över tid för att:

- Se ändringar med tiden och vilken påverkan ändras från en dag (eller intervall) till nästa.
- Analysera kostnader med tiden för en specifik instans.
- Förstå varför ökade kostnader för en specifik instans.

#### <a name="to-use-the-cost-over-time-report"></a>Använda rapporten kostnaden över tid:

1. Välj ett datumintervall.
2. Lägga till taggar, vid behov.
3. Lägg till grupper.
4. Välj en modell för kostnad som du skapade tidigare.
5. Välj faktiska kostnader eller amorterade kostnader.
6. Välj om du vill tillämpa tilldelningsregler vyn rådata fakturering datavy eller till omberäknas kostnaden vyn.

Här är ett exempel på rapporten.

![Exempel för kostnaden över tid](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutförts första självstudierna för hantering av kostnader, läsa den på [granska användning och kostnader](tutorial-review-usage.md).
