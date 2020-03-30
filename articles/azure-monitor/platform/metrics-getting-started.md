---
title: Komma igång med Utforskaren för Azure-mått
description: Lär dig hur du skapar ditt första måttdiagram med Azure-statistikutforskaren.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248780"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Komma igång med Azure Metrics Explorer

## <a name="where-do-i-start"></a>Var börjar jag
Azure Monitor-måttutforskaren är en komponent i Microsoft Azure-portalen som gör det möjligt att rita diagram, visuellt korrelera trender och undersöka toppar och dips i måttens värden. Använd statistikutforskaren för att undersöka hälso- och användning av dina resurser. Börja i följande ordning:

1. [Välj en resurs och ett mått](#create-your-first-metric-chart) och du ser ett enkelt diagram. Välj sedan [ett tidsintervall](#select-a-time-range) som är relevant för din undersökning.

1. Prova [att använda dimensionsfilter och dela](#apply-dimension-filters-and-splitting). Med filtren och delningen kan du analysera vilka segment av måttet som bidrar till det övergripande måttvärdet och identifiera möjliga extremvärden.

1. Använd [avancerade inställningar](#advanced-chart-settings) för att anpassa diagrammet innan du fäster på instrumentpaneler. [Konfigurera aviseringar för](alerts-metric-overview.md) att ta emot meddelanden när måttvärdet överskrider eller sjunker under ett tröskelvärde.

## <a name="create-your-first-metric-chart"></a>Skapa ditt första måttdiagram

Om du vill skapa ett måttdiagram öppnar du fliken Mått från resurs-, resursgrupp-, **prenumerations-** eller Azure Monitor-vyn:

1. Med hjälp av resursväljaren väljer du den resurs som du vill visa mått för. (Resursen är förvald om du öppnade **mått** i kontexten för en viss resurs).

    > ![Välj en resurs](./media/metrics-getting-started/resource-picker.png)

2. För vissa resurser måste du välja ett namnområde. Namnområdet är bara ett sätt att ordna mått så att du enkelt kan hitta dem. Lagringskonton har till exempel separata namnområden för lagring av mått för filer, tabeller, blobbar och köer. Många resurstyper har bara ett namnområde.

3. Välj ett mått från en lista över tillgängliga mått.

    > ![Välj ett mått](./media/metrics-getting-started/metric-picker.png)

4. Du kan också ändra måttaggregeringen. Du kanske till exempel vill att diagrammet ska visa minimi-, maximi- eller medelvärden för måttet.

> [!NOTE]
> Använd knappen **Lägg till mått** och upprepa dessa steg om du vill se flera mått som ritas i samma diagram. För flera diagram i en vy väljer du knappen **Lägg till diagram** överst.

## <a name="select-a-time-range"></a>Välj ett tidsintervall

Som standard visar diagrammet de senaste 24 timmarnas mätdata. Använd **tidsväljaren** för att ändra tidsintervallet, zooma in eller zooma ut i diagrammet. 

![Panelen Ändra tidsintervall](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Använd **tidsborsten** för att undersöka ett intressant område i diagrammet (spik eller ett dopp). Placera muspekaren i början av området, klicka och håll ned vänster musknapp, dra till andra sidan av området och släpp sedan knappen. Diagrammet zoomar in på det tidsintervallet. 

## <a name="apply-dimension-filters-and-splitting"></a>Använda dimensionsfilter och delning

[Filtrering](metrics-charts.md#apply-filters-to-charts) och [delning](metrics-charts.md#apply-splitting-to-a-chart) är kraftfulla diagnosverktyg för de mått som har dimensioner. Dessa funktioner visar hur olika måttsegment ("dimensionsvärden") påverkar det totala värdet för måttet och gör att du kan identifiera möjliga extremvärden.

- **Med filtrering** kan du välja vilka dimensionsvärden som ska ingå i diagrammet. Du kanske till exempel vill visa lyckade begäranden när du kartlägger *svarstiden för servern.* Du skulle behöva använda filtret på *lyckades begäran* dimension. 

- **Dela upp** styr om diagrammet visar separata linjer för varje värde i en dimension eller sammanställer värdena till en enda rad. Du kan till exempel se en rad för en genomsnittlig svarstid i alla serverinstanser eller se separata rader för varje server. Du måste använda delning på *serverinstansdimensionen* för att se separata rader.

Se [exempel på diagram](metric-chart-samples.md) som har filtrering och delning tillämpade. Artikeln visar stegen användes för att konfigurera diagrammen.

## <a name="advanced-chart-settings"></a>Avancerade diagraminställningar

Du kan anpassa diagramformat, rubrik och ändra avancerade diagraminställningar. När du är klar med anpassningen fäster du den på en instrumentpanel för att spara ditt arbete. Du kan också konfigurera måttaviseringar. Följ [produktdokumentationen](metrics-charts.md) för att lära dig mer om dessa och andra avancerade funktioner i Utforskaren för Azure Monitor-mått.

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om avancerade funktioner i Metrics Explorer](metrics-charts.md)
* [Felsökning av Metrics Explorer](metrics-troubleshoot.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Visa exempel på konfigurerade diagram](metric-chart-samples.md)
