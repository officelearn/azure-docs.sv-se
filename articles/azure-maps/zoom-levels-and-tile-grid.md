---
title: Zooma nivåer och panelen rutnät i Azure Maps | Microsoft Docs
description: Läs mer om zoomningsnivåer och panelen rutnät i Azure Maps
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 330333569f094fe3cec7f73ee3b20107ec70f5b5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Zoomningsnivåer och rutnät
Azure Maps använder sfäriska Mercator-projektion koordinatsystem (EPSG: 3857).

Världen är uppdelat i kvadratisk paneler. Rendera (rasterbilder) har 19 zoomningsnivåer, numrerade 0 till 18. Rendera (vektor) har 21 zoomningsnivåer, numrerade 0 och 20. På zoomning nivå 0 hela världen får plats på en enda sida vid sida:

![World sida vid sida](./media/zoom-levels-and-tile-grid/world0.png)

Zoomningsnivån 1 använder fyra paneler för att återge världen: en 2 x 2 kvadratisk

![World panelen längst upp till vänster](./media/zoom-levels-and-tile-grid/world1a.png)     ![World panelen längst upp till höger](./media/zoom-levels-and-tile-grid/world1c.png) 

![World panelen längst ned till vänster](./media/zoom-levels-and-tile-grid/world1b.png)     ![World panelen nedre högra hörnet](./media/zoom-levels-and-tile-grid/world1d.png) 


Varje efterföljande zoomningsnivån quad-dividerar paneler för den föregående, skapa ett rutnät med 2<sup>Zooma</sup> x 2<sup>Zooma</sup>. Zoomningsnivån 20 är ett rutnät 2<sup>20</sup> x 2<sup>20</sup>, eller 1,048,576 x 1,048,576 paneler (109,951,162,778 paneler totalt).

Följande tabell innehåller fullständig lista över värden för zoomningsnivåer:

|zoomningsnivån|mätare/pixel|mätare/sida vid sida|
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
|18|0,6|152.8|
|19|0,3|76.4|
|20|0,15|38.2|

Paneler anropas av zoom-nivå och x och y-koordinaterna som motsvarar på panelen position i rutnätet för att zoomningsnivån.

När du fastställer vilka zoomnivå för att använda, Kom ihåg att varje plats i en fast position på dess sida vid sida. Detta innebär att antalet brickor som behövs för att visa en viss expanse för område som är beroende av specifika placeringen av zoomning rutnätet i världen. Till exempel om det finns två pekar 900 meter, den *kan* endast ta tre sammanfattningar som ska visas en väg mellan dem i zoomningsnivån 17. Om western till höger om dess panelen och eastern punkt till vänster om dess sida vid sida, kan det ta fyra paneler:

![Demo zoomningsnivån](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

När zoomnivån bestäms x och y värden kan beräknas. Övre vänstra panelen i varje zoomning rutnät är x = 0, y = 0; den nedre högra panelen är på x = 2<sup>Zooma -1</sup>, y = 2<sup>zoom-1</sup>.

Här är zoomning rutnätet för zoomningsnivån 1:

![Zooma rutnät för zoomningsnivån 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
