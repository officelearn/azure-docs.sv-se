---
title: Ord lista för dina data med hjälp av avdelningens kontroll Insights
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll Insights-rapporter om dina data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576782"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Ord lista för dina data i Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar avdelningens kontrolls ord insikts rapporter för dina data.

I den här instruktions guiden lär du dig att:

> [!div class="checklist"]
> - Gå till insikter från ditt avdelningens kontroll-konto
> - Få en fågel ögon-vy över dina data

## <a name="prerequisites"></a>Krav

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

- Konfigurera dina Azure-resurser och fyll i kontot med data

- Konfigurera och slutför en sökning på käll typen

- Skapa en ord lista och bifoga till gångar till ord listans villkor

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Använd avdelningens kontroll Glossy Insights

I Azure avdelningens kontroll kan du skapa ord lista och bifoga dem till till gångar. Senare kan du Visa ord listans distribution i ord lista. Detta ger dig en status för din ord lista med villkor kopplade till till gångar. Det visar också villkor för status och distribution av roller efter antal användare.

**Så här visar du ord insikter:**

1. Gå till skärmen **Azure avdelningens kontroll** [instance i Azure Portal](https://aka.ms/purviewportal) och välj ditt avdelningens kontroll-konto.

1. På sidan **Översikt** går du till avsnittet **Kom igång** och väljer konto panelen **Starta avdelningens kontroll** .

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Starta avdelningens kontroll från Azure Portal":::

1. På **Start** sidan för avdelningens kontroll väljer du panelen **Visa insikter** för att komma åt dina **insikter** - :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: områden.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Visa dina insikter i Azure Portal":::

1. I avsnittet **insikter** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: väljer du **ord lista** för att Visa avdelningens kontroll- **ordlista** .

Sidan **ord för ord lista** visar följande områden:
1. **KPI: er på hög nivå** för att visa ord listans villkor och katalog användare

2. De **främsta ord listans villkor och antalet till gångar** visar de 5 populäraste orden med till gångar som är kopplade till dem. Alla andra till gångar redovisas i kategorin "Övrigt" i diagrammet.

3. **Ord listans villkor per term status** visar fördelningen av ord lista efter status, till exempel "Draft", "godkänt", "Alert" och "förfallet". 

1. Hovra eller klicka på diagrammets segment med en status och notera antalet villkor med denna status.

1. **Distributionen av roller efter antal användare** visar distributionen av roller efter antal användare per roll i avdelningens kontroll.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Visa ord insikter":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure avdelningens kontroll Insight-rapporter genom [till gångs insikter](./asset-insights.md)