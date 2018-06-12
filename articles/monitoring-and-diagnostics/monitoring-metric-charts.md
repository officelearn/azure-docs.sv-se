---
title: Azure övervakaren metrics explorer
description: Lär dig mer om nya funktioner i Azure-Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: a2611f89d9eef1ec6bac34389fa4db833aecc087
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264096"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure-Monitor Metrics Explorer

Den här anvisningar beskriver nästa generation mått för Azure-Monitor diagram upplevelse som för närvarande i förhandsversion. Den nya upplevelsen stöder generering av diagram för både flerdimensionella mått och grundläggande mått med inga dimensioner. Du kan rita diagram överlappande mått från olika resurstyper, flera resursgrupper och prenumerationer. Diagram för flerdimensionella mått kan anpassas genom att använda dimensionsfilter samt gruppering. Diagram, inklusive anpassade diagram kan vara fäster du instrumentpaneler.

Om du letar efter information om den gamla upplevelse som bara stöder grundläggande mått med inga dimensioner, Läs avsnittet rätt ”åtkomst till mätvärden via portalen” i den [översikt över Microsoft Azure-mått guiden](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>Vad är Azure övervakaren Metrics Explorer?

Azure övervakaren Metrics Explorer är en komponent i Microsoft Azure-portalen som gör att rita upp diagram visuellt korrelerar trender och undersöka toppar och sjunker i mått värden. Metrics Explorer är ett viktigt utgångspunkt för att undersöka olika prestanda och tillgänglighetproblem med ditt program och sin infrastruktur finns i Azure eller övervakas av Azure-Monitor-tjänster. 

## <a name="what-are-metrics-in-azure"></a>Vad är mått i Azure?

Mått i Microsoft Azure är serien mätvärden och antal som samlas in och lagras över tid. Det finns mått standard (eller ”plattform”) och anpassade mått. Standard mått som du av Azure-plattformen sig själv. Standard mått avspeglar hälso- och användningsinformation statistiken för dina Azure-resurser. De anpassade mått skickas till Azure av dina program med den [Application Insights API för anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Anpassade mått lagras i Application Insights-resurser tillsammans med andra program specifika mått.

## <a name="what-are-multi-dimensional-metrics"></a>Vad är flerdimensionella mått?

Många av Azures resurser exponera nu flerdimensionella mått. De här måtten spåra flera serier med värden för en eller flera namngivna dimensioner. Ett mått ”tillgängligt diskutrymme” kan till exempel ha en dimension som kallas ”enhet” med värden ”C:”, ”D:”, vilket gör att visa antingen tillgängligt diskutrymme på alla enheter eller för varje enhet individuellt. 

I exemplet nedan två datamängder för ett hypotetiskt mått som kallas ”dataflödet i nätverket”. Den första datamängden har inga dimensioner. Andra datauppsättningen visas värden med två dimensioner, ”IP-adress” och ”riktning”:

### <a name="network-throughput"></a>Dataflödet i nätverket
(Det här måttet har inga dimensioner)

 |Tidsstämpel        | Måttet | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kbit/s | 
   | 8/9/2017 8:15 | 1,141.4 kbit/s |
   | 8/9/2017 8:16 | 1,110.2 kbit/s |

Mätvärdet icke-dimensionell kan bara besvara en enkel fråga som ”vad har dataflödet i nätverket vid en given tidpunkt”?

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Dataflödet i nätverket + två dimensioner (”IP” och ”riktning”)

| Tidsstämpel          | Dimensionen ”-IP | Dimensionen ”riktning” | Måttet| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = ”192.168.5.2” | Riktning = ”skicka”    | 646.5 kbit/s |
   | 8/9/2017 8:14 | IP = ”192.168.5.2” | Riktning = ”ta emot” | 420.1 kbit/s |
   | 8/9/2017 8:14 | IP = ”10.24.2.15”  | Riktning = ”skicka”    | 150.0 kbit/s | 
   | 8/9/2017 8:14 | IP = ”10.24.2.15”  | Riktning = ”ta emot” | 115,2 kbit/s |
   | 8/9/2017 8:15 | IP = ”192.168.5.2” | Riktning = ”skicka”    | 515.2 kbit/s |
   | 8/9/2017 8:15 | IP = ”192.168.5.2” | Riktning = ”ta emot” | 371.1 kbit/s |
   | 8/9/2017 8:15 | IP = ”10.24.2.15”  | Riktning = ”skicka”    | 155.0 kbit/s |
   | 8/9/2017 8:15 | IP = ”10.24.2.15”  | Riktning = ”ta emot” | 100.1 kbit/s |

Det här måttet kan besvara frågor, till exempel ”vad har nätverksgenomflöde för varje IP-adress”? och ”hur mycket data som har skickats och tagits emot”? Flerdimensionella mått utföra ytterligare analytiska och diagnostik värde jämfört med icke-dimensionell mått. 

## <a name="how-do-i-create-a-new-chart"></a>Hur skapar ett nytt diagram?

   > [!NOTE]
   > Några av funktionerna i den gamla mått upplevelsen finns ännu inte i den nya Metrics Explorer. När den nya upplevelsen är i förhandsversionen kan fortsätta du använda gamla (icke-dimensionell) mått vyn i Azure-Monitor. 

1. Öppna Azure-portalen
2. Navigera till den nya **övervakaren** och välj sedan **mått (förhandsgranskning)**.

   ![Mått förhandsgranskningsbild](./media/monitoring-metric-charts/001.png)

3. Den **mått selector** kommer automatiskt att vara öppen åt dig. Välj en resurs från listan för att visa dess associerade mått. Bara resurser med mått visas i listan.

   ![Mått förhandsgranskningsbild](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Om du har mer än en Azure-prenumeration Metrics Explorer hämtar ut resurser över alla prenumerationer som väljs i portalinställningar -> Filter av prenumerationer. Klicka på portalen kugghjulet inställningsikonen på skärmen och välj vilka prenumerationer som du vill använda för att ändra den.

4. För vissa resurser (d.v.s. Storage-konton och virtuella datorer) innan du väljer ett mått som du måste välja en **Sub tjänsten**. Varje sub-tjänst har en egen uppsättning mätvärden som är relevanta för den här sub tjänsten och inte till andra sub-tjänster.

   Varje Azure Storage har till exempel mätvärden för subservices ”BLOB”, ”filer”, ”köer” och ”tabeller”, som är alla delar av lagringskontot. Måttet ”kön meddelandet antal” är dock naturligt tillämpas för subservice ”kö” och inte för alla andra subservices med lagring.

   ![Mått förhandsgranskningsbild](./media/monitoring-metric-charts/003.png)

5. Markera ett mått i listan. Om du vet att en del av namnet på måttet som du vill att börjar du skriva in den i en filtrerad lista över tillgängliga mått finns:

   ![Mått förhandsgranskningsbild](./media/monitoring-metric-charts/004.png)

6. Markera ett mått och återges diagrammet med standard-sammanställning för de valda måtten. Klicka nu bara bort från den **mått selector** att stänga den. Du kan också växla diagrammet till en annan aggregering. För vissa mått kan växla aggregeringen du välja vilket värde som du vill visa i diagrammet. Du kan till exempel växla mellan genomsnittlig, lägsta och högsta värden. 

7. Genom att klicka på Lägg till mått-ikon ![ikon för mått](./media/monitoring-metric-charts/icon001.png) och upprepa steg 3-6 du kan lägga till flera mått på samma schema.

   > [!NOTE]
   > Du normalt vill inte ha mått med olika enheter (d.v.s. ”millisekunder” och ”kilobyte”) eller med en skala som skiljer sig på ett diagram. I stället använda flera diagram. Klicka på knappen Lägg till diagram för att skapa flera diagram i Metrics Explorer.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Hur installerar jag filter i diagrammen?

Du kan använda filter för diagram som visar mått med dimensioner. Till exempel måttet ”antal transaktioner” har en dimension, ”svarstypen”, som anger om svaret från transaktioner har lyckats eller misslyckats sedan filtrering på den här dimensionen skulle rita ett diagram för endast lyckade (eller endast misslyckade) transaktioner. 

### <a name="to-add-a-filter"></a>Lägg till ett filter:

1. Klicka på Lägg till Filter-ikon ![filterikonen](./media/monitoring-metric-charts/icon002.png) ovanför diagrammet

2. Välj vilken dimension (egenskap) som du vill filtrera

   ![mått bild](./media/monitoring-metric-charts/006.png)

3. Välj vilka värden du vill inkludera när du gör diagrammet (det här exemplet visar filtrera lyckade lagringstransaktioner):

   ![mått bild](./media/monitoring-metric-charts/007.png)

4. Klicka utanför Väljaren Filter för att stänga den när du har valt filtervärdena. Nu diagrammet visar hur många lagringstransaktioner har misslyckats:

   ![mått bild](./media/monitoring-metric-charts/008.png)

5. Du kan upprepa steg 1-4 för att tillämpa olika filter för samma diagram.

## <a name="how-do-i-segment-a-chart"></a>Hur jag för att segmentera ett diagram?

Du kan dela ett mått av dimension visualisera hur olika delar av mått jämför mot varandra och identifiera öar segmenten i en dimension. 

### <a name="to-segment-a-chart"></a>Segmentet ett diagram:

1. Klicka på Lägg till grupp-ikon  ![mått bild](./media/monitoring-metric-charts/icon003.png) ovanför diagrammet.
 
   > [!NOTE]
   > Du kan ha flera filter men bara en gruppering på ett enkelt diagram.

2. Välj en dimension som du vill dela ditt diagram: 

   ![mått bild](./media/monitoring-metric-charts/010.png)

   Nu visas i diagrammet flera rader, en för varje segment för dimension:

   ![mått bild](./media/monitoring-metric-charts/012.png)

3. Klicka på bort från den **gruppering Selector** att stänga den.

   > [!NOTE]
   > Använda både filtrering och gruppering på samma dimension för att dölja segment som är irrelevanta för ditt scenario och göra det lättare att läsa diagram.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Hur jag för att fästa diagram till instrumentpaneler?

När du har konfigurerat diagrammen kanske du vill lägga till den i instrumentpanelerna så att du kan visa den igen, eventuellt i samband med andra övervakning telemetri eller dela med ditt team. 

Fästa ett diagram som är konfigurerade på en instrumentpanel:

När du har konfigurerat ditt diagram, klicka på den **diagram åtgärder** menyn till höger uppifrån hörnet i diagrammet och klickar på **fäst på instrumentpanelen**.

   ![mått bild](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Nästa steg

  Läs [skapa anpassade instrumentpaneler för KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) vill veta mer om metodtips för att skapa tillämplig instrumentpaneler med mått.