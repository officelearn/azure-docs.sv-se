---
title: Visa Azure Maps API-användningsmått | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar måtten för dina Microsoft Azure Maps API-anrop i Azure-portalen.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335151"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Visa användningsstatistik för Azure Maps API

Den här artikeln visar hur du visar API-användningsmåtten för ditt Azure Maps-konto i [Azure-portalen](https://portal.azure.com). Måtten visas i ett praktiskt diagramformat längs en anpassningsbar tidslängd.

## <a name="view-metric-snapshot"></a>Visa ögonblicksbild av mått

Du kan se några vanliga mått på **översiktssidan för** ditt Maps-konto. Den visar för närvarande *totala begäranden,* *totala fel*och *tillgänglighet* under en valbar tid.

![Översikt över användningsmått för Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Fortsätt till nästa avsnitt om du behöver anpassa dessa diagram för just din analys.

## <a name="view-detailed-metrics"></a>Visa detaljerade mått

1. Logga in på din Azure-prenumeration i [portalen](https://portal.azure.com).

2. Klicka på menyalternativet **Alla resurser** till vänster och navigera till ditt *Azure Maps-konto*.

3. När ditt Maps-konto är öppet klickar du på **menyn Mått** till vänster.

4. I fönstret **Mått** väljer du något av följande alternativ:

   1. **Tillgänglighet** - som visar *medelvärdet* av API-tillgänglighet under en tidsperiod.
   2. **Användning** - som visar hur användningen *räknas* för ditt konto.

      ![Fönstret Användningsmått för Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Därefter kan du välja *tidsintervallet* genom att klicka på **Senaste 24 timmarna (Automatisk)**. Som standard är tidsintervallet inställt på 24 timmar. När du har klickat ser du alla valbara tidsintervall. Du kan välja *tidsgranularitet* och välja att visa tiden som *lokal* eller *GMT* i samma listruta. Klicka på **Använd**.

    ![Tidsintervallet för Azure Maps-mått](media/how-to-view-api-usage/time-range.png)

6. När du har lagt till måttet kan du **lägga till filter** från de egenskaper som är relevanta för det måttet. Välj sedan värdet för den egenskap som du vill se återspeglas i diagrammet.

    ![Filter för användningsmått för Azure Maps](media/how-to-view-api-usage/filter.png)

7. Du kan också **använda delning** för ditt mått baserat på din valda måttegenskap. Det gör att diagrammet kan delas upp i flera diagram, för varje värde av den egenskapen. I följande bild motsvarar färgen på varje diagram egenskapsvärdet som visas längst ned i diagrammet.

    ![Azure Maps användningsmått dela](media/how-to-view-api-usage/splitting.png)

8. Du kan också observera flera mått i samma diagram, helt enkelt genom att klicka på knappen **Lägg till mått** överst.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps API:er som du vill spåra användning för:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK How-To](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK How-To](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API-dokumentation](https://docs.microsoft.com/rest/api/maps)
