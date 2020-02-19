---
title: Förstå hur mått varningar fungerar i Azure Monitor.
description: Få en översikt över vad du kan göra med mått aviseringar och hur de fungerar i Azure Monitor.
author: rboucher
ms.author: robb
ms.date: 12/5/2019
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: abcf7d100a1c195d4a49c3061bf22710285c2a9f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444170"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Förstå hur mått varningar fungerar i Azure Monitor

Mått varningar i Azure Monitor fungerar ovanpå flerdimensionella mått. Dessa mått kan vara [plattforms mått](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [anpassade mått](../../azure-monitor/platform/metrics-custom-overview.md), [populära loggar från Azure Monitor konverteras till mått](../../azure-monitor/platform/alerts-metric-logs.md) och Application Insights mått. Mått varningar utvärderas med jämna mellanrum för att kontrol lera om villkoren på en eller flera metriska tids serier är sanna och meddelar dig när utvärderingarna är uppfyllda. Mått varningar är tillstånds känsliga, det vill säga de skickar bara meddelanden när tillstånd ändras.

## <a name="how-do-metric-alerts-work"></a>Hur fungerar mått varningar?

Du kan definiera en regel för mått varningar genom att ange en mål resurs som ska övervakas, Metric-namn, villkors typ (statisk eller dynamisk) och villkoret (en operator och ett tröskelvärde/känslighet) och en åtgärds grupp som ska utlösas när varnings regeln utlöses. Typen av villkor påverkar hur tröskelvärdena fastställs. [Läs mer om villkors typ och känslighets alternativ för dynamisk tröskel](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Varnings regel med statisk villkors typ

Anta att du har skapat en varnings regel för enkel statisk tröskel enligt följande:

- Mål resurs (den Azure-resurs som du vill övervaka): myVM
- Mått: procent andel CPU
- Villkors typ: statisk
- Tids agg regering (statistik som körs över rå metriska värden. Tids agg regeringar som stöds är min, Max, AVG, total, Count): genomsnitt
- Period (fönstret för att se hur Mät värden kontrol leras): de senaste 5 minuterna
- Frekvens (den frekvens med vilken måttets avisering kontrollerar om villkoren är uppfyllda): 1 min
- Operator: större än
- Tröskel: 70

När aviserings regeln skapas körs övervakaren var 1: e minut och tittar på mått värden under de senaste 5 minuterna och kontrollerar om genomsnittet för dessa värden överstiger 70. Om villkoret är uppfyllt, är den genomsnittliga procent andelen för de senaste 5 minuterna längre än 70, varnings regeln utlöser ett aktiverat meddelande. Om du har konfigurerat ett e-postmeddelande eller en webhook-åtgärd i den åtgärds grupp som är associerad med varnings regeln får du ett aktiverat meddelande på båda.

När du använder flera villkor i en regel, "ands" villkoren tillsammans.  Det vill säga att aviseringen utlöses när alla villkor i aviseringen utvärderas som sant och löses när ett av villkoren inte längre är sant. Och exempel på den här typen av avisering skulle vara en avisering när "CPU som är högre än 90%" och "Queue length är över 300-objekt". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Varnings regel med dynamisk villkors typ

Anta att du har skapat en mått regel för enkla dynamiska tröskelvärden enligt följande:

- Mål resurs (den Azure-resurs som du vill övervaka): myVM
- Mått: procent andel CPU
- Villkors typ: dynamisk
- Tids agg regering (statistik som körs över rå metriska värden. Tids agg regeringar som stöds är min, Max, AVG, total, Count): genomsnitt
- Period (fönstret för att se hur Mät värden kontrol leras): de senaste 5 minuterna
- Frekvens (den frekvens med vilken måttets avisering kontrollerar om villkoren är uppfyllda): 1 min
- Operator: större än
- Känslighet: medel
- Se tillbaka-perioder: 4
- Antal överträdelser: 4

När varnings regeln har skapats, kommer Machine Learning-algoritmen för dynamiska tröskelvärden att hämta historiska data som är tillgängliga, beräkna tröskel som bäst passar mått seriens beteende mönster och kommer kontinuerligt att lära sig utifrån nya data för att göra tröskelvärdet är mer exakt.

När aviserings regeln skapas körs övervakaren var 1: e minut och tittar på mått värden under de senaste 20 minuterna grupperade i 5 minuters perioder och kontrollerar om genomsnittet för period värden i var och en av de 4 perioderna överstiger det förväntade tröskelvärdet. Om villkoret är uppfyllt, vilket är den genomsnittliga procent andelen av processorn under de senaste 20 minuterna (fyra 5 minuter) som avviker från förväntat beteende fyra gånger, utlöses aviserings regeln av ett aktiverat meddelande. Om du har konfigurerat ett e-postmeddelande eller en webhook-åtgärd i den åtgärds grupp som är associerad med varnings regeln får du ett aktiverat meddelande på båda.

### <a name="view-and-resolution-of-fired-alerts"></a>Visa och matcha utlösta aviseringar

Ovanstående exempel på utlösare för varnings regler kan också visas i Azure Portal på bladet **alla aviseringar** .

Anta att användningen på "myVM" fortsätter att ligga över tröskelvärdet i efterföljande kontroller, att varnings regeln inte utlöses igen förrän villkoren har lösts.

Efter en stund kommer användningen av "myVM" att gå tillbaka till normal (hamnar under tröskelvärdet). Varnings regeln övervakar villkoret i två gånger för att skicka ett löst meddelande. Varnings regeln skickar ut ett löst/inaktiverat meddelande när varnings villkoret inte är uppfyllt under tre efterföljande perioder för att minska bruset i händelse av växlar-villkor.

När det lösta meddelandet skickas ut via Webhooks eller e-post, anges även status för varnings instansen (kallas övervaknings tillstånd) i Azure Portal till löst.

### <a name="using-dimensions"></a>Använda dimensioner

Mått varningar i Azure Monitor också stöd för övervakning av flera dimensions värde kombinationer med en regel. Nu ska vi ta reda på varför du kan använda flera dimensions kombinationer med hjälp av ett exempel.

Anta att du har en App Service plan för din webbplats. Du vill övervaka CPU-användning på flera instanser som kör webbplatsen/appen. Du kan göra det med en regel för mått varningar enligt följande:

- Mål resurs: myAppServicePlan
- Mått: procent andel CPU
- Villkors typ: statisk
- Dimensioner
  - Instans = InstanceName1, InstanceName2
- Tids agg regering: genomsnitt
- Period: under de senaste 5 minuterna
- Frekvens: 1 min
- Operator: GreaterThan
- Tröskel: 70

Precis som tidigare övervakar den här regeln om den genomsnittliga CPU-användningen för de senaste 5 minuterna överstiger 70%. Men med samma regel kan du övervaka två instanser som kör din webbplats. Varje instans kommer att övervakas individuellt och du får meddelanden individuellt.

Anta att du har en webbapp som kan se enorma behov och du behöver lägga till fler instanser. Regeln ovan övervakar fortfarande bara två instanser. Du kan dock skapa en regel på följande sätt:

- Mål resurs: myAppServicePlan
- Mått: procent andel CPU
- Villkors typ: statisk
- Dimensioner
  - Instans = *
- Tids agg regering: genomsnitt
- Period: under de senaste 5 minuterna
- Frekvens: 1 min
- Operator: GreaterThan
- Tröskel: 70

Den här regeln övervakar automatiskt alla värden för instansen, dvs. Du kan övervaka dina instanser när de kommer igång utan att behöva ändra måttet för mått aviseringen igen.

När du övervakar flera dimensioner kan aviserings regeln för dynamiska tröskelvärden skapa skräddarsydda tröskelvärden för hundratals mått serier i taget. Dynamiska tröskelvärden ger färre varnings regler för att hantera och betydande tid att spara vid hantering och skapande av aviserings regler.

Anta att du har en webbapp med många instanser och att du inte vet vad det bästa tröskelvärdet är. Reglerna ovan kommer alltid att använda tröskelvärdet 70%. Du kan dock skapa en regel på följande sätt:

- Mål resurs: myAppServicePlan
- Mått: procent andel CPU
- Villkors typ: dynamisk
- Dimensioner
  - Instans = *
- Tids agg regering: genomsnitt
- Period: under de senaste 5 minuterna
- Frekvens: 1 min
- Operator: GreaterThan
- Känslighet: medel
- Se tillbaka-perioder: 1
- Antal överträdelser: 1

Den här regeln övervakar om den genomsnittliga CPU-användningen för de senaste 5 minuterna överskrider det förväntade beteendet för varje instans. Samma regel du kan övervaka instanser när de kommer igång utan att behöva ändra mått varnings regeln igen. Varje instans får ett tröskelvärde som passar mått seriens beteende mönster och kommer kontinuerligt att ändras baserat på nya data för att göra tröskelvärdet mer exakt. Precis som tidigare övervakas varje instans individuellt och du får meddelanden individuellt.

Om du ökar antalet återställnings perioder och antalet överträdelser kan du också tillåta filtrerings aviseringar enbart för aviseringar i definitionen av en betydande avvikelse. [Lär dig mer om avancerade alternativ för dynamiska tröskelvärden](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Övervakning i skala med hjälp av mått varningar i Azure Monitor

Hittills har du sett hur du kan använda en enda mått avisering för att övervaka en eller flera metriska tids serier som är relaterade till en enda Azure-resurs. Många gånger kanske du vill att samma varnings regel ska tillämpas på många resurser. Azure Monitor också stöd för övervakning av flera resurser (av samma typ) med en mått varnings regel för resurser som finns i samma Azure-region. Den här funktionen stöds för närvarande endast i Azures offentliga moln och endast för virtuella datorer, SQL Server-databaser, elastiska pooler för SQL Server och data Box Edge-enheter. Den här funktionen är även tillgänglig för plattforms mått och stöds inte för anpassade mått.

Du kan ange omfattningen för övervakning av en regel för en enskild måtts avisering på något av tre sätt:

- som en lista över virtuella datorer i en Azure-region inom en prenumeration
- alla virtuella datorer (i en Azure-region) i en eller flera resurs grupper i en prenumeration
- alla virtuella datorer (i en Azure-region) i en prenumeration

Skapa mått varnings regler som övervakar flera resurser, till exempel att [skapa alla andra mått varningar](alerts-metric.md) som övervakar en enskild resurs. Den enda skillnaden är att du väljer alla resurser som du vill övervaka. Du kan också skapa dessa regler genom att [Azure Resource Manager mallar](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Du får enskilda meddelanden för varje övervakad resurs.

## <a name="typical-latency"></a>Typisk svars tid

För mått aviseringar visas vanligt vis att du får ett meddelande under 5 minuter om du ställer in frekvensen för varnings regeln på 1 min. Vid tung belastning för aviserings system kan du se längre svars tid.

## <a name="supported-resource-types-for-metric-alerts"></a>Resurs typer som stöds för mått aviseringar

Du hittar en fullständig lista över resurs typer som stöds i den här [artikeln](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du skapar, visar och hanterar mått varningar i Azure](alerts-metric.md)
- [Lär dig hur du distribuerar mått aviseringar med hjälp av Azure Resource Manager mallar](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Läs mer om åtgärds grupper](action-groups.md)
- [Läs mer om villkors typen för dynamiska tröskelvärden](alerts-dynamic-thresholds.md)
