---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51858027"
---
Nu när du har data i Power BI Desktop kan du skapa rapporter som baseras på dessa data. Du skapar en enkel rapport med ett stapeldiagram som visar skada på grödor per delstat.

1. På vänster sida i Power BI-huvudfönstret väljer du rapportvyn.

    ![Rapportvy](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. I fönstret **Visualiseringar** väljer du grupperat stapeldiagram.

    ![Lägg till stapeldiagram](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    Ett tomt diagram har lagts till i arbetsytan.

    ![Tomt diagram](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. I listan **FÄLT** väljer du **DamageCrops** och **Delstat**.

    ![Välj fält](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Nu har du ett diagram som visar skadorna på grödor för de översta 1000 raderna i tabellen.

    ![Skada på grödor efter delstat](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Spara rapporten.