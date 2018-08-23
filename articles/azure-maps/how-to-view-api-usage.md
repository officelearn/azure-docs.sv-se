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
ms.openlocfilehash: e62d2ff1fdd6bc94244511a2de95c4268a58d6f9
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055584"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>Så här visar du Azure Maps-API-användning
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

    1. **Tillgänglighet**, som visar den *genomsnittlig* API tillgänglighet under en viss tidsperiod, eller 
    2. **Användning**, som visar hur användningen *antal* för ditt konto. 

    ![Fönstret för Azure Maps-mått](media/how-to-view-api-usage/portal-metrics.png)

5. När du har valt mått som du kan välja den *tidsintervall* genom att välja på den **senaste 12 timmarna (automatisk)** vilket är standardvärdet. Du kan också välja den *tidskornighet*, samt välja att visa tid som *lokala* eller *GMT* i samma listrutan. Klicka på **Verkställ**.

    ![Azure Maps mått tidsintervall](media/how-to-view-api-usage/time-range.png)
 
6. När du lägger till din mått, kan du sedan **Lägg till filter** bland egenskaperna som är relevanta för som mått och välj sedan värdet för egenskapen som du vill se diagrammet för. 

    ![Filter för Azure Maps-mått](media/how-to-view-api-usage/filter.png)

7. Du kan också **gäller dela** för dina mått baserat på ditt valda mått egenskapen. På så sätt kan grafen som ska delas upp i flera diagram, ett för varje värde för egenskapen. Till exempel i följande bild motsvarar färgen för varje diagram egenskapsvärdet visas längst ned i diagrammet.

    ![Azure Maps-mått dela](media/how-to-view-api-usage/splitting.png)
 
8. Du kan också notera flera mått på samma diagram genom att klicka på den **Lägg till mått** längst upp.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att spåra användningen av Azure Maps, du kan fortsätta att läsa mer om API: er som du använder med den:

* [Azure Maps REST API-dokumentation](https://docs.microsoft.com/rest/api/maps)
