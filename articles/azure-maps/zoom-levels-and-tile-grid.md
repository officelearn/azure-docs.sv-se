---
title: Zoomningsnivåer och rutnät i Azure Maps | Microsoft Docs
description: Lär dig zoomningsnivåer och rutnät i Azure Maps
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8eae5f258eaa899dc60e1e1cc066241bcff54970
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494096"
---
# <a name="zoom-levels-and-tile-grid"></a>Zoomningsnivåer och rutnät
Azure Maps använder sfärisk Mercator-projektion koordinatsystem (EPSG: 3857).

Världen är uppdelad i kvadratisk paneler. Rendering (Raster) har 21 zoomningsnivåer numrerade 0 och 20. Rendering (vektor) har 23 zoomningsnivåer numrerade 0 till och med 22. På zoomning nivå 0 i hela världen som får plats på en panel:

![World panel](./media/zoom-levels-and-tile-grid/world0.png)

Zoomnivå 1 använder fyra paneler för att återge världen: en 2 × 2 kvadratisk

![Världen ikonen längst upp till vänster](./media/zoom-levels-and-tile-grid/world1a.png)     ![Världen ikonen längst upp till höger](./media/zoom-levels-and-tile-grid/world1c.png) 

![World panel i nederkanten till vänster](./media/zoom-levels-and-tile-grid/world1b.png)     ![World panel nederkanten till höger](./media/zoom-levels-and-tile-grid/world1d.png) 


Varje efterföljande zoomningsnivån quad-dividerar startmenyn för det tidigare, skapar ett rutnät med 2<sup>Zooma</sup> x 2<sup>Zooma</sup>. Zoomningsnivån 22 är ett rutnät 2<sup>22</sup> x 2<sup>22</sup>, eller 4,194,304 x 4,194,304 paneler (17,592,186,044,416 panelerna totalt).

Följande tabell innehåller listan innehåller värden för zoomningsnivåer:

|Zoomnivå|Taxor/pixel|Taxor/sida vid sida|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0,075|19.1|
|22|0.0375|9.55|

Paneler anropas av zoomning-nivå och x och y-koordinater som motsvarar på panelen position i rutnätet för den zoomnivån.

När du bestämmer vilka zoomnivå för att använda, Kom ihåg att varje plats är i en fast position på dess panel. Det innebär att antalet paneler som behövs för att visa en viss expanse av territorium är beroende av specifika placeringen av zoomning rutnätet i hela världen. Till exempel om det finns två pekar 900 meter, den *kan* bara tar tre brickor att visa en väg mellan dem i zoomningsnivån 17. Om western redan är till höger om dess panel och östra peka till vänster på dess panel, kan det ta fyra paneler:

![Zoomningsnivån demo](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

När zoomningsnivån bestäms x och y värden kan beräknas. Den övre vänstra panelen i varje rutnät för zoomning är x = 0, y = 0. den nedre högra panelen är på x = 2<sup>Zooma -1</sup>, y = 2<sup>Zooma-1</sup>.

Här är zoomning rutnätet för zoomnivå 1:

![Rutnätet för zoomning för zoomnivå 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
