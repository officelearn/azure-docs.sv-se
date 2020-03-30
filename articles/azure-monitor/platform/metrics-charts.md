---
title: Avancerade funktioner i Azure Metrics Explorer
description: Lär dig mer om avancerade funktioner i Utforskaren för Azure Monitor Metrics
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2df1e0bb7d586edb13dc86e163f0e5728608d2a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371604"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Avancerade funktioner i Azure Metrics Explorer

> [!NOTE]
> Den här artikeln förutsätter att du är bekant med grundläggande funktioner i Metrics Explorer. Om du är en ny användare och vill lära dig hur du skapar ditt första måttdiagram läser [du Komma igång med Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Mått i Azure

[Mått i Azure Monitor](data-platform-metrics.md) är serien av uppmätta värden och antal som samlas in och lagras över tiden. Det finns standardmått (eller "plattform") och anpassade mått. Standardmåtten tillhandahålls av Själva Azure-plattformen. Standardmått återspeglar hälso- och användningsstatistiken för dina Azure-resurser. Medan anpassade mått skickas till Azure av dina program med hjälp av [Api:et för Application Insights för anpassade händelser och mått](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), Windows Azure [Diagnostics (WAD) tillägg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)eller av Azure Monitor REST [API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Skapa vyer med flera mått och diagram

Du kan skapa diagram som ritar flera måttlinjer eller visar flera måttdiagram samtidigt. Med den här funktionen kan du:

- korrelera relaterade mått i samma diagram för att se hur ett värde är relaterat till ett annat
- visa mätvärden med olika måttenheter i närheten
- visuellt aggregera och jämföra mått från flera resurser

Om du till exempel har fem lagringskonton och vill veta hur mycket totalt utrymme som förbrukas mellan dem, kan du skapa ett (staplat) ytdiagram som visar individen och summan av alla värden vid vissa tidpunkter.

### <a name="multiple-metrics-on-the-same-chart"></a>Flera mått i samma diagram

Skapa först [ett nytt diagram](metrics-getting-started.md#create-your-first-metric-chart). Klicka på **Lägg till mått** och upprepa stegen för att lägga till ett nytt mått i samma diagram.

   > [!NOTE]
   > Du vill vanligtvis inte ha mått med olika måttenheter (dvs. "millisekunder" och "kilobyte") eller med betydligt olika skala på ett diagram. Överväg i stället att använda flera diagram. Klicka på knappen Lägg till diagram om du vill skapa flera diagram i utforskaren för mått.

### <a name="multiple-charts"></a>Flera diagram

Klicka på **diagrammet Lägg till** och skapa ett annat diagram med ett annat mått.

### <a name="order-or-delete-multiple-charts"></a>Ordning eller ta bort flera diagram

Om du vill beställa eller ta bort flera diagram klickar du på ellipssymbolen ( **...** ) för att öppna diagrammenyn och väljer lämpligt menyalternativ i **Flytta uppåt,** **Flytta nedåt**eller Ta **bort**.

## <a name="apply-filters-to-charts"></a>Använda filter på diagram

Du kan använda filter på diagram som visar mått med dimensioner. Om måttet "Transaktionsantal" till exempel har en dimension, "Svarstyp", som anger om svaret från transaktioner lyckades eller misslyckades då filtrering på den här dimensionen skulle rita en diagramrad för endast lyckade (eller endast misslyckade) transaktioner. 

### <a name="to-add-a-filter"></a>Så här lägger du till ett filter

1. Välj **Lägg till filter** ovanför diagrammet

2. Välj vilken dimension (egenskap) som ska filtreras

   ![bild på mått](./media/metrics-charts/00006.png)

3. Välj vilka dimensionsvärden du vill inkludera när diagrammet ritas (i det här exemplet visas filtrering av lyckade lagringstransaktioner):

   ![bild på mått](./media/metrics-charts/00007.png)

4. När du har valt filtervärdena klickar du bort från filterväljaren för att stänga den. Nu visar diagrammet hur många lagringstransaktioner som har misslyckats:

   ![bild på mått](./media/metrics-charts/00008.png)

5. Du kan upprepa steg 1-4 om du vill använda flera filter på samma diagram.



## <a name="apply-splitting-to-a-chart"></a>Använda delning på ett diagram

Du kan dela ett mått efter dimension för att visualisera hur olika segment i måttet jämförs med varandra och identifiera de perdritsegmenten i en dimension.

### <a name="apply-splitting"></a>Använda delning

1. Klicka på **Använd delning** ovanför diagrammet.
 
   > [!NOTE]
   > Delning kan inte användas med diagram som har flera mått. Du kan också ha flera filter men bara en delningsdimension som tillämpas på ett enda diagram.

2. Välj en dimension som du vill segmentera diagrammet på:

   ![bild på mått](./media/metrics-charts/00010.png)

   Nu visar diagrammet nu flera rader, en för varje dimensionssegment:

   ![bild på mått](./media/metrics-charts/00012.png)

3. Klicka bort från **grupperingsväljaren** för att stänga den.

   > [!NOTE]
   > Använd både filtrering och delning på samma dimension för att dölja de segment som är irrelevanta för ditt scenario och göra diagram lättare att läsa.

## <a name="lock-boundaries-of-chart-y-axis"></a>Lås gränser för diagramy-axeln

Låsning av y-axelns område blir viktigt när diagrammet visar mindre fluktuationer i större värden. 

Till exempel, när volymen av lyckade begäranden sjunker från 99,99% till 99,5%, kan det innebära en betydande minskning av kvaliteten på tjänsterna. Att märka en liten numerisk värdefluktuationer skulle dock vara svårt eller till och med omöjligt från standarddiagraminställningarna. I det här fallet kan du låsa den lägsta gränsen för diagrammet till 99%, vilket skulle göra denna lilla droppe tydligare. 

Ett annat exempel är en fluktuation i det tillgängliga minnet, där värdet tekniskt aldrig kommer att nå 0. Om du fäster intervallet till ett högre värde kan det göra det lättare att upptäcka dropparna i tillgängligt minne. 

Om du vill styra y-axelområdet använder du "..." diagrammenyn och välj **Redigera diagram** för att komma åt avancerade diagraminställningar. Ändra värdena i avsnittet Y-Axelområde eller använd **knappen Auto** för att återgå till standardvärden.

![bild på mått](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Om du låser gränserna för y-axeln för diagram som spårar olika antal eller summor under en tidsperiod (och därmed använder antal, summa, minsta eller högsta aggregeringar) krävs vanligtvis att du anger en fast tidsgranularitet i stället för att förlita sig på de automatiska standardvärdena. Detta är nödvändigt beror på att värdena i diagram ändras när tidsgranulariteten automatiskt ändras av användarens storleksändring webbläsarfönster eller går från en skärmupplösning till en annan. Den resulterande ändringen av tidsgranularitet påverkar diagrammets utseende och ogiltigförklarar det aktuella valet av y-axelintervall.

## <a name="change-colors-of-chart-lines"></a>Ändra färger på diagramlinjer

När du har konfigurerat diagrammen tilldelas diagramlinjerna automatiskt en färg från en standardpalett. Du kan ändra färgerna.

Om du vill ändra färg på en diagramlinje klickar du på det färgade fältet i förklaringen som motsvarar diagrammet. Dialogrutan färgväljaren öppnas. Använd färgväljaren för att konfigurera färgen för linjen.

När diagramfärgerna har konfigurerats förblir de så när du fäster diagrammet på en instrumentpanel. I följande avsnitt visas hur du fäster ett diagram.

> [!NOTE]
> På grund av begränsningar i vårt versions- och publiceringsschema kräver ändringar av diagramlinjerna tillfälligt att [https://portal.azure.com/?feature.colorpicker=true](https://portal.azure.com/?feature.colorpicker=true)du skickar en särskild parameter **?feature.colorpicker=true** när Azure-portalen startas . Denna begränsning kommer att tas bort snart. 

![bild på mått](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Fästa diagram på instrumentpaneler

När du har konfigurerat diagrammen kanske du vill lägga till det på instrumentpanelerna så att du kan visa det igen, eventuellt i samband med annan övervakningstelemetri eller dela med ditt team.

Så här fäster du ett konfigurerat diagram på en instrumentpanel:

När du har konfigurerat diagrammet klickar du på menyn **Diagramåtgärder** i diagrammets högra övre hörn och klickar på **Fäst på instrumentpanelen**.

![bild på mått](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Skapa aviseringsregler

Du kan använda de villkor som du har angett för att visualisera dina mått som grund för en måttbaserad aviseringsregel. Den nya aviseringsregeln innehåller dina målresurs-, mått-, delnings- och filterdimensioner från diagrammet. Du kan ändra dessa inställningar senare i fönstret för att skapa varningsregeln.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Om du vill skapa en ny varningsregel klickar du på **Regel för ny avisering**

![Knappen Ny varningsregel markerad i rött](./media/metrics-charts/015.png)

Du kommer att tas till fönstret för att skapa varningsregel med de underliggande måttdimensionerna från diagrammet ifyllt för att göra det enklare att generera anpassade varningsregler.

![Skapa aviseringsregel](./media/metrics-charts/016.png)

Läs den här [artikeln](alerts-metric.md) om du vill veta mer om hur du konfigurerar måttaviseringar.

## <a name="troubleshooting"></a>Felsökning

*Jag ser inga data i diagrammet.*

* Filter gäller för alla diagram i fönstret. Se till att du inte har angett något filter som utesluter alla data på ett annat när du fokuserar på ett diagram.

* Om du vill ställa in olika filter på olika diagram skapar du dem i olika blad och sparar dem som separata favoriter. Om du vill kan du fästa dem på instrumentpanelen så att du kan se dem bredvid varandra.

* Om du segmenterar ett diagram efter en egenskap som inte har definierats i måttet, kommer det inte att finnas något i diagrammet. Prova att rensa segmenteringen (delning) eller välj en annan egenskap.

## <a name="next-steps"></a>Nästa steg

  Läs [Skapa anpassade KPI-instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) om du vill lära dig mer om de bästa metoderna för att skapa användbara instrumentpaneler med mått.

