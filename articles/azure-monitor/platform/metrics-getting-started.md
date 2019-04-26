---
title: Komma igång med Azure metrics explorer
description: Lär dig hur du skapar din första måttdiagram med Azure metrics explorer.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254077"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Komma igång med Azure Metrics Explorer

## <a name="where-do-i-start"></a>Var ska jag börja
Azure Monitor metrics explorer är en komponent i Microsoft Azure-portalen som tillåter ritområdet diagram, visuellt korrelera trender och undersöka toppar och dalar mått '. Använd metrics explorer för att undersöka hälsa och användning av dina resurser. Starta i följande ordning:

1. [Välj en resurs och ett mått](#create-your-first-metric-chart) och du ser ett grundläggande diagram. Sedan [välja ett tidsintervall](#select-a-time-range) som är relevant för din undersökning.

1. Försök [de dimension filtren har använts och dela](#apply-dimension-filters-and-splitting). Filtren och dela kan du analysera vilka segment av måttet bidra till det övergripande måttvärdet och identifiera möjliga extremvärden.

1. Använd [avancerade inställningar](#advanced-chart-settings-and-next-steps) att anpassa diagrammet innan du fäster till instrumentpaneler. [Konfigurera aviseringar](alerts-metric-overview.md) att ta emot meddelanden när måttet överskrider eller sjunker under ett tröskelvärde.

## <a name="create-your-first-metric-chart"></a>Skapa din första måttdiagram

Om du vill skapa ett måttdiagram från resurs, resursgrupp, prenumeration eller Azure Monitor-visa, öppna den **mått** fliken och följ de här stegen:

1. Med hjälp av resursväljaren, Välj den resurs som du vill se mått. (Resursen är förvalda om du har öppnat **mått** i kontexten för en specifik resurs).

    > ![Välj en resurs](./media/metrics-getting-started/resource-picker.png)

2. Du måste välja ett namnområde för vissa resurser. Namnområdet är bara ett sätt att ordna mått så att du lätt kan hitta dem. Storage-konton har till exempel separata namnområden för att lagra filer, tabeller, Blobbar och köer mått. Många resurstyper kan bara ha ett namnområde.

3. Välj ett mått från en lista över tillgängliga mått.

    > ![Välj ett mått](./media/metrics-getting-started/metric-picker.png)

4. Alternativt kan du ändra sammanställning av mått. Exempelvis kanske du vill att diagrammet ska visa minsta, högsta och genomsnittliga värden för måttet.

> [!NOTE]
> Använd den **Lägg till mått** knappen och upprepa dessa steg om du vill se flera mått som ritas i samma diagram. Flera diagram i en vy, Välj den **Lägg till diagram** längst upp.

## <a name="select-a-time-range"></a>Välj ett tidsintervall

Diagrammet visar de senaste 24 timmarna av mätvärden som standard. Använd den **tidsväljare** panelen för att ändra tidsintervallet, Zooma in eller Zooma ut i diagrammet. 

![Ändra tid panel för värdeintervall](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Lägga till dimensionsfilter och dela

[Filtrering](metrics-charts.md#apply-filters-to-charts) och [uppdelningen](metrics-charts.md#apply-splitting-to-a-chart) är kraftfulla diagnostikverktyg för mått med dimensioner. Dessa funktioner och visar hur olika mått segment (”dimensionsvärden”) påverkan det övergripande värdet för måttet och hjälpa dig att identifiera möjliga extremvärden.

- **Filtrering** låter dig välja vilka dimensionsvärden som ingår i diagrammet. Du kanske till exempel vill visa lyckade begäranden när diagram i *serversvarstid* mått. Skulle du behöva använda filtret på den *framgången för begäran* dimension. 

- **Dela upp** kontroller om diagrammet visar separata rader för varje värde i en dimension eller aggregerar värden i en enda rad. Du kan till exempel finns i en rad för en genomsnittlig svarstid över alla serverinstanser eller finns i separata rader för varje server. Skulle du behöva använda delning på den *serverinstansen* dimension i separata rader.

Se [exempel på diagrammen](metric-chart-samples.md) att filtrera och dela ansökt. Artikeln beskriver steg som användes för att konfigurera diagrammen.

## <a name="advanced-chart-settings-and-next-steps"></a>Avancerade inställningar och nästa steg

Du kan anpassa organisationsstruktur, titel, och ändra avancerade inställningar. När du är klar med anpassningen kan du fästa den på en instrumentpanel för att spara ditt arbete. Du kan också konfigurera aviseringar för mått. Följ [produktdokumentationen](metrics-charts.md) Lär dig mer om dessa och andra avancerade funktioner i Azure Monitor metrics explorer.

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Lär dig mer om de avancerade funktionerna i Metric Explorer](metrics-charts.md)
* [Se exempel på konfigurerade diagram](metric-chart-samples.md)
