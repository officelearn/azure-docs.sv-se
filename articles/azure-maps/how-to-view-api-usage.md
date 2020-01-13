---
title: Visa Azure Maps användnings statistik för API | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar måtten för dina Microsoft Azure Maps API-anrop i Azure Portal.
author: walsehgal
ms.author: v-musehg
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7ba50f63fb015a8696904df3decd13c811625459
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911341"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Visa användningsstatistik för Azure Maps API

Den här artikeln visar hur du visar användnings måtten för API: er för ditt Azure Maps-konto i [portalen](https://portal.azure.com). Måtten visas i ett användbart diagram format längs en anpassad tids period.

## <a name="view-metric-snapshot"></a>Visa mått ögonblicks bild

Du kan se några vanliga mått på **översikts** sidan för ditt Maps-konto. Den visar för närvarande *Totalt antal förfrågningar*, *Totalt antal fel*och *tillgänglighet* under en tids period som kan väljas.

![Översikt över Azure Maps användnings statistik](media/how-to-view-api-usage/portal-overview.png)

Fortsätt till nästa avsnitt om du behöver anpassa diagrammen för just din analys.

## <a name="view-detailed-metrics"></a>Visa detaljerade mått

1. Logga in på din Azure-prenumeration i [portalen](https://portal.azure.com).

2. Klicka på meny alternativet **alla resurser** till vänster och navigera till ditt *Azure Maps-konto*.

3. När ditt Maps-konto är öppet klickar du på **mått** menyn till vänster.

4. I fönstret **mått** väljer du något av följande:

   1. **Tillgänglighet** – som visar *genomsnitt* av API-tillgänglighet under en viss tids period.
   2. **Användning** – som visar hur användnings *antalet* används för ditt konto.

      ![Fönstret Azure Maps användnings statistik](media/how-to-view-api-usage/portal-metrics.png)

5. Sedan kan du välja *tidsintervallet* genom att klicka på de **senaste 24 timmarna (automatiskt)** . Som standard är tidsintervallet inställt på 24 timmar. När du har klickat visas alla Selectable Time-intervall. Du kan välja *tids kornig het* och välja att visa tiden som *lokal* eller *GMT* i samma listruta. Klicka på **Använd**.

    ![Tidsintervallet Azure Maps mått](media/how-to-view-api-usage/time-range.png)

6. När du har lagt till ditt mått kan du sedan **lägga till filter** från bland de egenskaper som är relevanta för det måttet och sedan välja värdet för den egenskap som du vill visa grafen för.

    ![Filter för Azure Maps användnings statistik](media/how-to-view-api-usage/filter.png)

7. Du kan också **använda delning** för ditt mått baserat på din valda mått egenskap. Detta gör att grafen kan delas upp i flera grafer, ett för varje värde för egenskapen. I följande bild motsvarar färgen på varje diagram det egenskaps värde som visas längst ned i diagrammet.

    ![Delning av Azure Maps användnings statistik](media/how-to-view-api-usage/splitting.png)

8. Du kan också titta på flera mått i samma graf, helt enkelt genom att klicka på knappen **Lägg till mått** överst.

## <a name="next-steps"></a>Nästa steg

Läs mer om de Azure Maps-API: er som du vill spåra användningen för:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK How-to](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK How-to](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API-dokumentation](https://docs.microsoft.com/rest/api/maps)
