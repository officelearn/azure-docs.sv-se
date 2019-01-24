---
title: Azure Monitor metrics explorer
description: Lär dig mer om nya funktioner i Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2b6d982f514f347dc2a59f9ca8f3c33b8c24849b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827456"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor Metrics Explorer

Azure Monitor Metrics Explorer är en komponent i Microsoft Azure-portalen som tillåter ritning diagram, visuellt korrelera trender och undersöka toppar och dalar mått '. Metrics Explorer är ett viktigt startpunkt för att undersöka olika prestanda och tillgänglighetsproblem med dina program och infrastruktur i Azure eller övervakas av Azure Monitor-tjänster.

## <a name="metrics-in-azure"></a>Mått i Azure

Mått i Microsoft Azure är serien med mätvärden och antal som samlas in och lagras över tid. Det finns mått för standard (eller ”plattformen”) och anpassade mått. Standardmått tillhandahålls till dig av själva Azure-plattformen. Standardmått visas statistik för hälsa och användning av dina Azure-resurser. Medan anpassade mått som ska skickas till Azure genom att dina program med den [Application Insights API för anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Anpassade mått lagras i Application Insights-resurser tillsammans med andra program specifika mått.

## <a name="create-a-new-chart"></a>Skapa ett nytt diagram

1. Öppna Azure portal
2. Gå till den nya **övervakaren** fliken och välj sedan **mått**.

   ![Bild av mått](./media/metrics-charts/00001.png)

3. Den **mått väljare** kommer automatiskt att vara öppen åt dig. Välj en resurs från listan för att visa dess tillhörande mått. Endast resurser med mått visas i listan.

   ![Bild av mått](./media/metrics-charts/00002.png)

   > [!NOTE]
   >Om du har mer än en Azure-prenumeration, Metrics Explorer-hämtningar i resurser i alla prenumerationer som har valts i Portal-inställningar -> Filter av prenumerationslista. Om du vill ändra den, klicka på kugghjulsikonen Portal inställningar på skärmen och välj vilka prenumerationer som du vill använda.

4. För vissa resurstyper (Storage-konton och virtuella datorer) innan du väljer ett mått måste du välja en **Namespace**. Varje namnområde har en egen uppsättning mått som är relevanta till endast det här namnområdet och inte till andra namnområden.

   Varje Azure Storage har till exempel mått för subservices ”BLOB”, ”filer”, ”Queues” och ”tabeller”, som är alla delar av storage-konto. Dock gäller mått ”Antal Kömeddelanden” naturligt till deltjänst ”kö” och inte till några andra subservices med lagring.

   ![Bild av mått](./media/metrics-charts/00003.png)

5. Välj ett mått i listan. Om du vet att en del av namnet på det mått som du vill kan du börja skriva den i att se en filtrerad lista över tillgängliga mått:

   ![Bild av mått](./media/metrics-charts/00004.png)

6. När du har valt ett mått återges i diagrammet med standardaggregeringen för det valda måttet. Klicka nu bara bort från den **mått väljare** att stänga den. Du kan även växla diagrammet till en annan aggregering. För vissa mått kan växlar aggregering du välja vilket värde som du vill visa i diagrammet. Exempelvis kan du växla mellan genomsnittlig, minsta och högsta värden. 

7. Genom att klicka på **Lägg till mått** och upprepa steg 3 – 6, du kan lägga till fler mått på samma diagram.

   > [!NOTE]
   > Du normalt vill inte ha mått med olika enheter (d.v.s. ”millisekunder” och ”kilobyte”) eller med betydligt olika skala på ett diagram. Överväg istället att använda flera diagram. Klicka på knappen Lägg till diagram för att skapa flera diagram i Metrics Explorer.

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

## <a name="segment-a-chart"></a>Segmentera ett diagram

Du kan dela ett mått med dimensionen att visualisera hur olika segment av mått jämför mot varandra och identifiera öar segmenten i en dimension. 

### <a name="to-segment-a-chart"></a>Ett diagram-segmentet

1. Klicka på **gäller dela** ovanför diagrammet.
 
   > [!NOTE]
   > Du kan ha flera filter men bara en dela/segmentering värdet på ett enkelt diagram.

2. Välj en dimension som du vill segmentera diagrammet:

   ![mått-avbildning](./media/metrics-charts/00010.png)

   Diagrammet visar nu nu flera rader, en för varje segment för dimension:

   ![mått-avbildning](./media/metrics-charts/00012.png)

3. Klicka på bort från den **gruppering väljare** att Stäng den.

   > [!NOTE]
   > Använd både filtrera och dela på samma dimension för att dölja segment som är inte relevant för ditt scenario och göra det lättare att läsa diagram.

### <a name="new-alert-rule"></a>Ny aviseringsregel

Du kan också använda de kriterier som du har angett för att visualisera dina mått som grund för den underliggande logiken för ett mått baserade varningsregeln. 

Om du klickar på **ny varningsregel**

![Knapp för ny varningsregel markerat i rött](./media/metrics-charts/015.png)

Du kommer att tas till fönstret varningsregel skapas med de underliggande måttet dimensionerna från ditt diagram ifylld i förväg för att göra det enklare att skapa anpassade aviseringsregler.

![Skapa aviseringsregel](./media/metrics-charts/016.png)

Titta i den här [artikeln](alerts-metric.md) mer information om hur du konfigurerar aviseringar för mått.

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

## <a name="troubleshooting"></a>Felsökning

*Jag ser några data i diagrammet.*

* Filtren gäller för alla diagram i fönstret. Se till att även om du vill fokusera på ett diagram, ställa in ett filter som inte omfattar alla data på en annan.

* Om du vill definiera olika filter för olika diagram, skapa dem i olika bladen kan du spara dem som separata Favoriter. Om du vill kan fästa du dem på instrumentpanelen så att du kan se dem tillsammans med varandra.

* Om du segmentera ett diagram av en egenskap som inte har definierats för måttet sedan debiteras ingenting i diagrammet. Försök rensa segmentering (dela) eller välj en annan egenskap.

## <a name="next-steps"></a>Nästa steg

  Läs [skapa anpassade KPI-instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) vill veta mer om bästa praxis för att skapa användbara instrumentpaneler med mått.

