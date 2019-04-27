---
title: Så här visar du Azure Maps-API-användning | Microsoft Docs
description: Lär dig hur du visar mått för dina Azure Maps-API-anrop i portalen.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: d14088ed940ab83be29756a26f8612704bb9aebd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770168"
---
# <a name="view-azure-maps-api-usage"></a>Visa Azure Maps-API-användning

Den här artikeln visar hur du visar användningsstatistik för API för Azure Maps-konto i den [portal](https://portal.azure.com). Mått som visas i praktiskt diagramformat längs en anpassningsbar varaktighet.

## <a name="view-metric-snapshot"></a>Visa mått ögonblicksbild

Du kan se vissa vanliga mått på den **översikt** för Maps-konto. Den visar för närvarande *förfrågningarna*, *Totalt antal fel*, och *tillgänglighet* över valbara varaktighet.

![Översikt över Azure Maps-mått](media/how-to-view-api-usage/portal-overview.png)

Fortsätta till nästa avsnitt om du vill anpassa dessa diagram för din specifika analys.

## <a name="view-detailed-metrics"></a>Visa detaljerade mätvärden

1. Logga in på Azure-prenumerationen i den [portal](https://portal.azure.com).

2. Klicka på den **alla resurser** menyn på vänster sida för konfigurationsobjektet och navigera till din *Azure Maps-konto*.

3. När ditt Maps-konto är öppet klickar du på den **mått** på vänstra menyn.

4. På den **mått** fönstret, välja mellan något av följande:

   1. **Tillgänglighet** – som visar den *genomsnittlig* API tillgänglighet under en viss tidsperiod.
   2. **Användning** – som visar hur användningen *antal* för ditt konto.

      ![Fönstret för Azure Maps-mått](media/how-to-view-api-usage/portal-metrics.png)

5. Du kan sedan välja den *tidsintervall* genom att klicka på **senaste 24 timmarna (automatisk)**. Tidsintervallet anges som standard till 24 timmar. När du klickar på, visas alla valbara tidsintervall. Du kan välja den *tidskornighet* och väljer att visa tiden som *lokala* eller *GMT* i samma listrutan. Klicka på **Verkställ**.

    ![Azure Maps mått tidsintervall](media/how-to-view-api-usage/time-range.png)

6. När du lägger till din mått, kan du sedan **Lägg till filter** bland egenskaperna som är relevanta för som mått och välj sedan värdet för egenskapen som du vill se diagrammet för.

    ![Filter för Azure Maps-mått](media/how-to-view-api-usage/filter.png)

7. Du kan också **gäller dela** för dina mått baserat på ditt valda mått egenskapen. På så sätt kan grafen som ska delas upp i flera diagram, ett för varje värde för egenskapen. I följande bild motsvarar färgen för varje diagram egenskapsvärdet visas längst ned i diagrammet.

    ![Azure Maps-mått dela](media/how-to-view-api-usage/splitting.png)

8. Du kan också notera flera mått på samma diagram genom att klicka på den **Lägg till mått** längst upp.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps API: er du vill spåra användning för:

> [!div class="nextstepaction"]
> [Azure Maps REST API-dokumentation](https://docs.microsoft.com/rest/api/maps)
