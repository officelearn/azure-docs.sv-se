---
title: Skanna insikter om dina data i Azure avdelningens kontroll
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll Insights-skannings rapportering på dina data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ea12bf8a8d93f14c5364864b97d1173fe8602765
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554426"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Skanna insikter om dina data i Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar insikter om Azure avdelningens kontroll-genomsökningar för dina data.

I den här instruktions guiden lär du dig att:

* Visa insikter från ditt avdelningens kontroll-konto.
* Få en fågel ögon-vy över dina genomsökningar.

## <a name="prerequisites"></a>Krav

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

* Konfigurera dina Azure-resurser och fyll i kontot med data.
* Konfigurera och slutför en sökning på data källan.

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Använda avdelningens kontroll Scan Insights

I Azure avdelningens kontroll kan du registrera och skanna käll typer. Du kan visa status för genomsökningen över tid i Skanna insikter. Insikterna talar om hur många genomsökningar som misslyckats, lyckades eller var avbruten inom en viss tids period.

### <a name="view-scan-insights"></a>Visa skannings insikter

1. Gå till skärmen **Azure avdelningens kontroll** instance i Azure Portal och välj ditt avdelningens kontroll-konto.

1. På sidan **Översikt** i avsnittet **Kom igång** väljer du panelen **Öppna avdelningens kontroll Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Starta avdelningens kontroll från Azure Portal":::

1. På **Start** sidan för avdelningens kontroll väljer du panelen **Visa insikter** för att komma åt dina **insikter** - :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: områden.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Visa dina insikter i Azure Portal":::

1. I avsnittet **insikter** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: väljer du **genomsökningar** för att visa rapporten avdelningens kontroll **Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Visa KPI: er på hög nivå för att visa antal genomsökningar efter status
 
KPI: er på hög nivå visar de totala genomsökningarna som körs inom en period. Tids perioden är standard under de senaste 30 dagarna. Men du kan även välja de senaste sju dagarna. Baserat på tids filtret återspeglar KPI-värdena antalet genomsökningar korrekt.


Baserat på det tids filter värde som valts kan du se distributionen av lyckade, misslyckade och avbrutna genomsökningar per vecka eller dag i diagrammet.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Visa skannings insikter":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure avdelningens kontroll Insight-rapporter med [till gångs insikter](./asset-insights.md)
