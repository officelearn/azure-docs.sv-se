---
title: Avancerade funktionerna i Azure Metrics Explorer
description: Lär dig mer om de avancerade funktionerna i Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271687"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Avancerade funktionerna i Azure Metrics Explorer

> [!NOTE]
> Den här artikeln förutsätter att du är bekant med grundläggande funktioner i Metrics Explorer. Om du är nybörjare och vill lära dig hur du skapar din första måttdiagram finns [komma igång med Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Mått i Azure

[Mått i Azure Monitor](data-platform-metrics.md) är serie mätvärden och antal som samlas in och lagras över tid. Det finns mått för standard (eller ”plattformen”) och anpassade mått. Standardmått tillhandahålls till dig av själva Azure-plattformen. Standardmått visas statistik för hälsa och användning av dina Azure-resurser. Medan anpassade mått som ska skickas till Azure genom att dina program med den [Application Insights API för anpassade händelser och mått](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [tillägget för Windows Azure Diagnostics SÄKERHETSSPECIFIKA](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), eller genom [Azure Övervaka REST-API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Skapa vyer med flera mått och diagram

Du kan skapa diagram som beskriver flera mått rader eller visa flera diagram med mätvärden på samma gång. Den här funktionen kan du:

- korrelera relaterade mått på samma diagram för att se hur ett värde som är relaterad till en annan
- Visa mått med olika enheter i närheten
- visuellt sammanställa och jämföra mått från flera resurser

Om du har 5 storage-konton och du vill veta hur mycket diskutrymme som används mellan dem, kan du skapa en (staplat) ytdiagram som visar person och summan av alla värden vid specifika tidpunkter i tid.

### <a name="multiple-metrics-on-the-same-chart"></a>Flera mått på samma diagram

Först [skapa ett nytt diagram](metrics-getting-started.md#create-your-first-metric-chart). Klicka på **Lägg till måttet** och upprepa stegen för att lägga till ett annat mått på samma diagram.

   > [!NOTE]
   > Du normalt vill inte ha mått med olika enheter (d.v.s. ”millisekunder” och ”kilobyte”) eller med betydligt olika skala på ett diagram. Överväg istället att använda flera diagram. Klicka på knappen Lägg till diagram för att skapa flera diagram i metrics explorer.

### <a name="multiple-charts"></a>Flera diagram

Klicka på den **Lägg till diagram** och skapa ett annat diagram med ett annat mått.

### <a name="order-or-delete-multiple-charts"></a>Sortera eller ta bort flera diagram

Om du vill sortera eller ta bort flera diagram, klickar du på ellipserna ( **...**  ) symbol att öppna diagram-menyn och väljer lämplig menyalternativet av **Flytta upp**, **Flytta ned**, eller **ta bort**.

## <a name="apply-filters-to-charts"></a>Använda filter diagram

Du kan använda filter på diagram som visar mått med dimensioner. Till exempel om måttet ”Transaktionsantal” har en dimension, ”svarstypen”, som anger om svaret från transaktioner har lyckats eller misslyckats sedan filtrering på den här dimensionen skulle rita en rad för diagram för endast lyckade (eller bara misslyckade) transaktioner. 

### <a name="to-add-a-filter"></a>Lägg till ett filter

1. Välj **Lägg till filter** ovanför diagrammet

2. Välj vilken dimension (egenskapen) som du vill filtrera

   ![mått-avbildning](./media/metrics-charts/00006.png)

3. Välj vilka dimensionsvärden som du vill inkludera när du gör i diagrammet (det här exemplet visar filtrerar ut lyckad storage-transaktioner):

   ![mått-avbildning](./media/metrics-charts/00007.png)

4. Klicka utanför Väljaren Filter att stänga den när du har valt filtervärdena. Diagrammet visar nu hur många lagringstransaktioner har misslyckats:

   ![mått-avbildning](./media/metrics-charts/00008.png)

5. Du kan upprepa steg 1 – 4 använda flera filter i samma diagram.



## <a name="apply-splitting-to-a-chart"></a>Tillämpa delar upp till ett diagram

Du kan dela ett mått med dimensionen att visualisera hur olika segment av mått jämför mot varandra och identifiera öar segmenten i en dimension.

### <a name="apply-splitting"></a>Tillämpa dela

1. Klicka på **gäller dela** ovanför diagrammet.
 
   > [!NOTE]
   > Dela kan inte användas med diagram som har flera mått. Du kan också ha flera filter men endast en delande dimension som tillämpas på ett enkelt diagram.

2. Välj en dimension som du vill segmentera diagrammet:

   ![mått-avbildning](./media/metrics-charts/00010.png)

   Diagrammet visar nu nu flera rader, en för varje segment för dimension:

   ![mått-avbildning](./media/metrics-charts/00012.png)

3. Klicka på bort från den **gruppering väljare** att Stäng den.

   > [!NOTE]
   > Använd både filtrera och dela på samma dimension för att dölja segment som är inte relevant för ditt scenario och göra det lättare att läsa diagram.

## <a name="lock-boundaries-of-chart-y-axis"></a>Lås gränserna för y-axeln i diagrammet

Låsning intervallet för y-axeln blir viktigt när diagrammet visar mindre variationer av större värden. 

När mängden lyckade förfrågningar rullas ned från 99,99% till 99,5%, kan det till exempel representerar en betydande minskning i tjänstkvaliteten. Dock märker en liten numeriskt värde variationerna skulle vara svåra eller omöjliga även från standardinställningar för diagrammet. I det här fallet kan du låsa den lägsta gränsen för diagrammet för att 99 procent, vilket gör den här små släpp tydligare. 

Ett annat exempel är en variationerna i det tillgängliga minnet och där värdet tekniskt aldrig når 0. Åtgärda intervallet till ett högre värde kan göra släpper i tillgängligt minne lättare att upptäcka. 

Om du vill styra y-axelintervall använder det ”...” Skapa diagram över menyn och välj **redigera diagram** att få åtkomst till avancerade inställningar. Ändra värden i y-axelintervall avsnitt eller Använd **automatisk** knappen för att återgå till standardvärden.

![mått-avbildning](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Låsa gränserna för y-axeln för diagram som spårar olika antal eller summerar under en viss tid (och därmed antal som används, sum, minimum eller maximum aggregeringar) kräver vanligtvis att ange en fast tidskornighet i stället för att förlita sig på automatisk standardvärdena. Detta är nödvändigt eftersom värden i diagram ändras när tidskornighet automatiskt ändras av användaren ändrar storlek på webbläsarfönster eller kommer från en skärmupplösning till en annan. Den resulterande ändras i tidskornighet effekterna utseendet på diagrammet, ogiltigförklara aktuella valet av y-axelintervall.

## <a name="pin-charts-to-dashboards"></a>Fäst diagram på instrumentpaneler

När du har konfigurerat diagrammen kan du lägga till den i instrumentpaneler så att du kan visa den igen, eventuellt i kontexten av andra övervakning telemetri, eller dela med ditt team.

Att fästa ett diagram som är konfigurerade på en instrumentpanel:

När du har konfigurerat schemat klickar du på den **diagram åtgärder** menyn i högra viktigaste hörnet i diagrammet och klicka på **fäst på instrumentpanelen**.

![mått-avbildning](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Skapa aviseringsregler

Du kan använda de kriterier som du har angett för att visualisera dina mått som grund för ett mått baserade varningsregeln. Ny aviseringsregel tas din målresursen, mått, dela och filterdimensioner från ditt diagram. Du kommer att kunna ändra inställningarna senare i fönstret Skapa en aviseringsregel.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Klicka för att skapa en ny varningsregel **ny aviseringsregel**

![Knapp för ny varningsregel markerat i rött](./media/metrics-charts/015.png)

Du kommer att tas till fönstret varningsregel skapas med de underliggande måttet dimensionerna från ditt diagram ifylld i förväg för att göra det enklare att skapa anpassade aviseringsregler.

![Skapa aviseringsregel](./media/metrics-charts/016.png)

Titta i den här [artikeln](alerts-metric.md) mer information om hur du konfigurerar aviseringar för mått.

## <a name="troubleshooting"></a>Felsökning

*Jag ser några data i diagrammet.*

* Filtren gäller för alla diagram i fönstret. Se till att även om du vill fokusera på ett diagram, ställa in ett filter som inte omfattar alla data på en annan.

* Om du vill definiera olika filter för olika diagram, skapa dem i olika bladen kan du spara dem som separata Favoriter. Om du vill kan fästa du dem på instrumentpanelen så att du kan se dem tillsammans med varandra.

* Om du segmentera ett diagram av en egenskap som inte har definierats för måttet sedan debiteras ingenting i diagrammet. Försök rensa segmentering (dela) eller välj en annan egenskap.

## <a name="next-steps"></a>Nästa steg

  Läs [skapa anpassade KPI-instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) vill veta mer om bästa praxis för att skapa användbara instrumentpaneler med mått.

