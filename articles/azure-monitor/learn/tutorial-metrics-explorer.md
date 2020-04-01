---
title: Självstudiekurs - Skapa ett måttdiagram i Azure Monitor
description: Lär dig hur du skapar ditt första måttdiagram med Azure-statistikutforskaren.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79082820"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Självstudiekurs: Skapa ett måttdiagram i Azure Monitor
Metrics Explorer är en funktion i Azure Monitor i Azure-portalen som gör att du kan skapa diagram från måttvärden, visuellt korrelera trender och undersöka toppar och dips i måttvärden. Använd statistikutforskaren för att undersöka hälso- och användning av dina Azure-resurser eller för att rita diagram från anpassade mått. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Välj ett mått som du vill rita ett diagram för
> * Utföra olika aggregeringar av måttvärden
> * Ändra tidsintervall och granularitet för diagrammet

Följande är en video som visar ett mer omfattande scenario än den procedur som beskrivs i den här artikeln. Om du inte har något nytt för mått föreslår vi att du läser igenom den här artikeln först och sedan visar videon för att se mer detaljer. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du en Azure-resurs för att övervaka. Du kan använda alla resurser i din Azure-prenumeration som stöder mått. För att avgöra om en resurs stöder mått, gå till dess meny i Azure-portalen och kontrollera att det finns ett **måttalternativ** i avsnittet **Övervakning** på menyn.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="open-metrics-explorer-and-select-a-scope"></a>Öppna statistikutforskaren och välj ett scope
Du kan öppna statistikutforskaren antingen från Azure Monitor-menyn eller från en resurs meny i Azure-portalen. Mått från alla resurser är tillgängliga oavsett vilket alternativ du använder. 

1. Välj **Mått** på **Azure Monitor-menyn** eller från avsnittet **Övervakning** på en resurs meny.

1. Välj **scope**, som är den resurs som du vill visa mått för. Scopet är redan ifyllt om du öppnade statistikutforskaren från en resurs meny.

    ![Välj ett scope](media/tutorial-metrics-explorer/scope-picker.png)

2. Välj ett **namnområde** om scopet har mer än ett. Namnområdet är bara ett sätt att ordna mått så att du enkelt kan hitta dem. Lagringskonton har till exempel separata namnområden för lagring av mått för filer, tabeller, blobbar och köer. Många resurstyper har bara ett namnområde.

3. Välj ett mått från en lista över tillgängliga mått för det valda scopet och namnområdet.

    ![Välj ett mått](media/tutorial-metrics-explorer/metric-picker.png)

4. Du kan också ändra **måttaggregering**. Detta definierar hur måttvärdena ska aggregeras över tidsgranulariteten för diagrammet. Om tidsgranulariteten till exempel är inställd på 15 minuter och aggregeringen är inställd på summa, blir varje punkt i diagrammet summan av alla insamlade värden över varje 15-minuterssegment.

    ![Diagram](media/tutorial-metrics-explorer/chart.png)

5. Använd knappen **Lägg till mått** och upprepa dessa steg om du vill se flera mått som ritas i samma diagram. För flera diagram i en vy väljer du knappen **Nytt diagram.**

## <a name="select-a-time-range-and-granularity"></a>Välj ett tidsintervall och en detaljeradhet

Som standard visar diagrammet de senaste 24 timmarnas mätdata. Använd tidsväljaren för att ändra **tidsintervallet** för diagrammet eller **tidsgranulariteten** som definierar tidsintervallet för varje datapunkt. Diagrammet använder den angivna aggregeringen för att beräkna alla provvärden över den angivna tidsgranulariteten.

![Panelen Ändra tidsintervall](media/tutorial-metrics-explorer/time-picker.png)


Använd **tidsborsten** för att undersöka ett intressant område i diagrammet, till exempel en topp eller ett dopp. Placera muspekaren i början av området, klicka och håll ned vänster musknapp, dra till andra sidan av området och släpp knappen. Diagrammet zoomar in på det tidsintervallet. 

![Tidsborste](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Använda dimensionsfilter och delning
Se följande referenser för avancerade funktioner som gör att du kan utföra ytterligare analyser av dina mått och identifiera potentiella extremvärden i dina data.

- [Med filtrering](../platform/metrics-charts.md#apply-filters-to-charts) kan du välja vilka dimensionsvärden som ska ingå i diagrammet. Du kanske till exempel bara vill visa lyckade begäranden när du kartlägger ett *svarstidsmått för serverns svarstid.* 

- [Dela upp](../platform/metrics-charts.md#apply-splitting-to-a-chart) styr om diagrammet visar separata linjer för varje värde i en dimension eller sammanställer värdena till en enda rad. Du kanske till exempel vill se en rad för en genomsnittlig svarstid i alla serverinstanser eller så kanske du vill ha separata rader för varje server. 

Se [exempel på diagram](../platform/metric-chart-samples.md) som har filtrering och delning tillämpade.

## <a name="advanced-chart-settings"></a>Avancerade diagraminställningar

Du kan anpassa diagramformat, rubrik och ändra avancerade diagraminställningar. När du är klar med anpassningen fäster du den på en instrumentpanel för att spara ditt arbete. Du kan också konfigurera måttaviseringar. Se [Avancerade funktioner i Azure Metrics Explorer](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) om du vill veta mer om dessa och andra avancerade funktioner i Utforskaren för Azure Monitor-mått.


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du arbetar med mått i Azure Monitor kan du läsa om hur du använder mått för att skicka proaktiva aviseringar.

> [!div class="nextstepaction"]
> [Skapa, visa och hantera måttaviseringar med Azure Monitor](../platform/alerts-metric.md)

