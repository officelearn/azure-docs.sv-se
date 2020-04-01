---
title: Förstå hur måttaviseringar fungerar i Azure Monitor.
description: Få en översikt över vad du kan göra med måttaviseringar och hur de fungerar i Azure Monitor.
ms.date: 03/17/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: a6860cad077b597df923274f8971f5652d4ba9e3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397979"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Förstå hur måttaviseringar fungerar i Azure Monitor

Måttaviseringar i Azure Monitor fungerar ovanpå flerdimensionella mått. Dessa mått kan vara [plattformsmått,](alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [anpassade mått,](../../azure-monitor/platform/metrics-custom-overview.md) [populära loggar från Azure Monitor konverterade till mått](../../azure-monitor/platform/alerts-metric-logs.md) och mätvärden för Application Insights. Måttaviseringar utvärderas med jämna mellanrum för att kontrollera om villkoren för en eller flera måtttidsserier är sanna och meddela dig när utvärderingarna är uppfyllda. Måttaviseringar är tillståndskänsliga, det vill säga de skickar bara ut meddelanden när tillståndet ändras.

## <a name="how-do-metric-alerts-work"></a>Hur fungerar måttaviseringar?

Du kan definiera en måttvarningsregel genom att ange en målresurs som ska övervakas, måttnamn, villkorstyp (statisk eller dynamisk) och villkoret (en operator och ett tröskelvärde/känslighet) och en åtgärdsgrupp som ska utlösas när aviseringsregeln utlöses. Typen av villkor påverkar hur tröskelvärdena fastställs. [Läs mer om villkorstyp och känslighetsalternativ för dynamiska tröskelvärden](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Varningsregel med statisk tillståndstyp

Anta att du har skapat en enkel statisk måttvarningsregel för statisktröskelvärde enligt följande:

- Målresurs (den Azure-resurs som du vill övervaka): myVM
- Mått: Procent CPU
- Villkorstyp: Statisk
- Tidsaggregering (Statistik som körs över råa måttvärden. Tidsaggregeringar som stöds är Min, Max, Medel, Totalt, Antal): Genomsnitt
- Period (Titta tillbaka fönster över vilka måttvärden är markerade): Under de senaste 5 minuter
- Frekvens (Frekvensen med vilken den metriska varningen kontrollerar om villkoren är uppfyllda): 1 min
- Operatör: Större än
- Tröskelvärde: 70

Från det att aviseringsregeln skapas körs övervakaren var 1:e minut och tittar på måttvärden för de senaste 5 minuterna och kontrollerar om medelvärdet av dessa värden överstiger 70. Om villkoret uppfylls, det vill ha, överskrider den genomsnittliga procentprocessorn för de senaste 5 minuterna 70, aviseringsregeln utlöser ett aktiverat meddelande. Om du har konfigurerat ett e-postmeddelande eller en webbkrokåtgärd i åtgärdsgruppen som är associerad med varningsregeln får du ett aktiverat meddelande på båda.

När du använder flera villkor i en regel, regeln "ands" villkoren tillsammans.  Det vill än, aviseringen utlöses när alla villkor i aviseringen utvärderas som sanna och löser när ett av villkoren inte längre är sant. Och exempel på denna typ av avisering skulle vara varning när "CPU högre än 90%" och "kölängd är över 300 objekt". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Varningsregel med dynamisk villkorstyp

Anta att du har skapat en enkel måttvarningsregel för dynamiska tröskelvärden enligt följande:

- Målresurs (den Azure-resurs som du vill övervaka): myVM
- Mått: Procent CPU
- Villkorstyp: Dynamisk
- Tidsaggregering (Statistik som körs över råa måttvärden. Tidsaggregeringar som stöds är Min, Max, Medel, Totalt, Antal): Genomsnitt
- Period (Titta tillbaka fönster över vilka måttvärden är markerade): Under de senaste 5 minuter
- Frekvens (Frekvensen med vilken den metriska varningen kontrollerar om villkoren är uppfyllda): 1 min
- Operatör: Större än
- Känslighet: Medium
- Tillbakablick: 4
- Antal överträdelser: 4

När varningsregeln har skapats hämtar machine learning-algoritmen för dynamiska tröskelvärden historiska data som är tillgängliga, beräknar tröskelvärdet som bäst passar beteendemönstret för måttserien och lär sig kontinuerligt baserat på nya data för att göra tröskelvärdet mer exakt.

Från det att aviseringsregeln skapas körs övervakaren var 1:e minut och tittar på måttvärden under de senaste 20 minuterna grupperade i 5 minuter och kontrollerar om medelvärdet av periodvärdena i var och en av de 4 perioderna överskrider det förväntade tröskelvärdet. Om villkoret uppfylls, det vill ha, avvek den genomsnittliga procentprocessorn under de senaste 20 minuterna (fyra 5 minuter) från förväntat beteende fyra gånger, aviseringsregeln ett aktiverat meddelande. Om du har konfigurerat ett e-postmeddelande eller en webbkrokåtgärd i åtgärdsgruppen som är associerad med varningsregeln får du ett aktiverat meddelande på båda.

### <a name="view-and-resolution-of-fired-alerts"></a>Visa och lösa sparken

Ovanstående exempel på aviseringar kan också visas i Azure-portalen i bladet **Alla aviseringar.**

Säg att användningen på "myVM" fortsätter att vara över tröskelvärdet i efterföljande kontroller, kommer varningsregeln inte att skjuta igen förrän villkoren har lösts.

Efter en tid, användningen på "myVM" kommer tillbaka ner till det normala (går under tröskeln). Varningsregeln övervakar villkoret i ytterligare två gånger för att skicka ut ett löst meddelande. Varningsregeln skickar ut ett löst/inaktiverat meddelande när varningsvillkoret inte uppfylls under tre på varandra följande perioder för att minska brus vid flaxande förhållanden.

När det lösta meddelandet skickas ut via webbkrokar eller e-post är statusen för varningsinstansen (kallas övervakarstatus) i Azure-portalen också inställd på att matchas.

### <a name="using-dimensions"></a>Använda dimensioner

Måttaviseringar i Azure Monitor stöder också övervakning av flera dimensionsvärdekombinationer med en regel. Låt oss förstå varför du kan använda flera dimensionskombinationer med hjälp av ett exempel.

Anta att du har en appserviceplan för din webbplats. Du vill övervaka CPU-användningen på flera instanser som kör din webbplats/app. Du kan göra det med hjälp av en måttaviseringsregel på följande sätt:

- Målresurs: myAppServicePlan
- Mått: Procent CPU
- Villkorstyp: Statisk
- Dimensioner
  - Instans = Instansnamn1, instansnamn2
- Tidsaggregering: Genomsnitt
- Period: Under de senaste 5 minuter
- Frekvens: 1 min
- Operatör: GreaterThan
- Tröskelvärde: 70

Precis som tidigare övervakar den här regeln om den genomsnittliga CPU-användningen under de senaste 5 minuterna överstiger 70 %. Med samma regel kan du dock övervaka två instanser som kör din webbplats. Varje instans kommer att övervakas individuellt och du kommer att få meddelanden individuellt.

Anta att du har en webbapp som ser en enorm efterfrågan och du måste lägga till fler instanser. Ovanstående regel övervakar fortfarande bara två instanser. Du kan dock skapa en regel på följande sätt:

- Målresurs: myAppServicePlan
- Mått: Procent CPU
- Villkorstyp: Statisk
- Dimensioner
  - Instans = *
- Tidsaggregering: Genomsnitt
- Period: Under de senaste 5 minuter
- Frekvens: 1 min
- Operatör: GreaterThan
- Tröskelvärde: 70

Den här regeln övervakar automatiskt alla värden för Du kan övervaka dina instanser när de kommer upp utan att behöva ändra din måttvarningsregel igen.

När du övervakar flera dimensioner kan regeln för dynamiska tröskelvärden skapa skräddarsydda tröskelvärden för hundratals måttserier åt gången. Dynamiska tröskelvärden resulterar i färre varningsregler för att hantera och betydande tidsbesparing på hantering och skapande av regler för aviseringar.

Anta att du har en webbapp med många instanser och att du inte vet vad den lämpligaste tröskeln är. Ovanstående regler kommer alltid att använda tröskel på 70%. Du kan dock skapa en regel på följande sätt:

- Målresurs: myAppServicePlan
- Mått: Procent CPU
- Villkorstyp: Dynamisk
- Dimensioner
  - Instans = *
- Tidsaggregering: Genomsnitt
- Period: Under de senaste 5 minuter
- Frekvens: 1 min
- Operatör: GreaterThan
- Känslighet: Medium
- Tillbakablick: 1
- Antal överträdelser: 1

Den här regeln övervakar om den genomsnittliga CPU-användningen för de senaste 5 minuterna överskrider det förväntade beteendet för varje instans. Samma regel som du kan övervaka instanser när de kommer upp utan att behöva ändra din måttvarningsregel igen. Varje instans får ett tröskelvärde som passar beteendemönstret för måttserier och ändras kontinuerligt baserat på nya data för att göra tröskelvärdet mer exakt. Precis som tidigare kommer varje instans att övervakas individuellt och du får aviseringar individuellt.

Ökande tillbakablicksperioder och antal överträdelser kan också göra det möjligt att filtrera aviseringar för att bara avisera din definition av en betydande avvikelse. [Läs mer om avancerade alternativ för dynamiska tröskelvärden](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Övervakning i stor skala med måttaviseringar i Azure Monitor

Hittills har du sett hur en enda måttavisering kan användas för att övervaka en eller flera måtttidsserier relaterade till en enda Azure-resurs. Många gånger kanske du vill att samma aviseringsregel ska tillämpas på många resurser. Azure Monitor stöder också övervakning av flera resurser (av samma typ) med en måttaviseringsregel, för resurser som finns i samma Azure-region. 

Den här funktionen stöds för närvarande för plattformsmått (inte anpassade mått) för följande tjänster i följande Azure-moln:

| Tjänst | Offentlig Azure | Government | Kina |
|:--------|:--------|:--------|:--------|
| Virtuella datorer  | **Ja** | Inga | Inga |
| SQL-serverdatabaser | **Ja** | **Ja** | Inga |
| Elastiska POOLER för SQL-servern | **Ja** | **Ja** | Inga |
| Kantenheter för databox | **Ja** | **Ja** | Inga |

Du kan ange omfattningen av övervakning av en enda måttaviseringsregel på ett av tre sätt. Med virtuella datorer kan du till exempel ange omfånget som:  

- en lista över virtuella datorer i en Azure-region inom en prenumeration
- alla virtuella datorer (i en Azure-region) i en eller flera resursgrupper i en prenumeration
- alla virtuella datorer (i en Azure-region) i en prenumeration

Att skapa måttaviseringsregler som övervakar flera resurser är som [att skapa andra måttaviseringar](alerts-metric.md) som övervakar en enda resurs. Enda skillnaden är att du skulle välja alla resurser som du vill övervaka. Du kan också skapa dessa regler via [Azure Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Du får individuella meddelanden för varje övervakad resurs.

> [!NOTE]
>
> I en måttaviseringsregel som övervakar flera resurser tillåts endast ett villkor.

## <a name="typical-latency"></a>Typisk latens

För måttaviseringar får du vanligtvis ett meddelande på mindre än 5 minuter om du ställer in varningsregelfrekvensen till 1 min. Vid tung belastning för meddelandesystem kan du se en längre svarstid.

## <a name="supported-resource-types-for-metric-alerts"></a>Resurstyper som stöds för måttaviseringar

Du hittar den fullständiga listan över resurstyper som stöds i den här [artikeln](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du skapar, visar och hanterar måttaviseringar i Azure](alerts-metric.md)
- [Lär dig hur du distribuerar måttaviseringar med Azure Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Läs mer om åtgärdsgrupper](action-groups.md)
- [Läs mer om villkorstyp för dynamiska tröskelvärden](alerts-dynamic-thresholds.md)
