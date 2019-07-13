---
title: Ändra storlek på och Beskär miniatyrbilder - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du ändrar storlek på och Beskär miniatyrer som tillhandahålls av API: er för Bing-sökresultat.'
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867896"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ändra storlek på och Beskär miniatyrbilder

Några svar från Bing Search API: er inkluderar URL: er till miniatyrbilder som hanteras av Bing, där du kan ändra storlek och Beskär och kan innehålla Frågeparametrar. Exempel:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Om du visar en delmängd av dessa miniatyrer, anger du ett alternativ för att visa återstående bilder.

> [!NOTE]
> Se till att beskära och ändra storlek på miniatyrbilder ger ett Sök-scenario som respekterar tredje parts rättigheter som krävs av API: et för Bing Search [använder och visa kraven](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Ändra storlek på en miniatyrbild 

Om du vill ändra storlek på en miniatyrbild, Bing rekommenderar att du bara ange en den `w` (bredd) eller `h` (höjd) Frågeparametrar i URL: en för den miniatyrbilden. Ange endast höjd eller bredd kan Bing underhålla en bilds ursprungliga höjd. Ange bredd och höjd i bildpunkter. 

Till exempel, om den ursprungliga miniatyrbilden är 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Och du vill minska dess storlek genom att ange den `w` parametern till ett nytt värde (till exempel `336`), och ta bort den `h` parameter:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Om du anger bara höjd eller bredd i en miniatyr för bevaras en bilds ursprungliga proportionerna. Om du ange båda parametrarna och proportionerna inte bibehålls, Bing lägger till vit utfyllnad kantlinjen för avbildningen.

Om du ändrar storlek på en 480 x 359 avbildning till 200 x 200 utan beskärning full bredd innehåller avbildningen men höjden innehåller 25 bildpunkter vit utfyllnad högst upp och längst ned på bilden. Om avbildningen har 359 x 480, skulle de vänstra och högra kantlinjen innehåller vit utfyllnad. Om du Beskär bilden läggs inte vit utfyllnad.  

Följande bild visar den ursprungliga storleken på en miniatyrbild (480 x 300).  
  
![Liggande originalbilden](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Följande bild visar bildens storlek ska ändras till 200 x 200. Förhållandet underhålls och de övre och nedre gränser är utfyllnad med vitt (kantlinje här ingår att visa utfyllnaden).  
  
![Ändrade liggande bild](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Om du anger dimensioner som är större än en bilds ursprungliga Breddoch höjd, Bing till vit utfyllnad till vänster och övre gränser.  

## <a name="request-different-thumbnail-sizes"></a>Begär olika storlekar för miniatyr

Om du vill begära en annan miniatyrbilden storlek, ta bort alla frågeparametrar från den miniatyr-URL: en, förutom den `id` och `pid` parametrar. Lägg sedan till antingen den `&w` (bredd) eller `&h` (höjd) Frågeparametern med önskad avbildningens storlek i bildpunkter, men inte båda. Bing upprätthåller en bilds ursprungliga proportionerna. 

Om du vill öka bredden på den avbildning som angetts av ovanstående URL-Adressen till 165 bildpunkter, använder du följande URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Om du begär en avbildning som är större än en bilds ursprungliga storlek, lägger Bing till vit utfyllnad runt bilden efter behov. Till exempel om avbildningen ursprunglig storlek är 474 x 316 och du anger `&w` till 500, Bing returnerar en 500 x 333-avbildning. Den här avbildningen har 8,5 bildpunkter av vit utfyllnad längs övre och nedre kanten och 13 bildpunkter utfyllnad på vänster och höger kant.

Om du vill förhindra att Bing att lägga till vit utfyllnad om den begärda storleken är större än en bilds ursprungliga storlek, ange den `&p` frågeparameter till 0. Exempel: Om du inkluderar den `&p=0` ovan URL, Bing-parameter returnerar en 474 x 316 avbildning i stället för en 500 x 333 avbildning:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Om du anger både `&w` och `&h` frågeparametrar, Bing, skyddar bildens höjd-/ breddförhållandet och lägger till vit utfyllnad efter behov. Till exempel om avbildningen ursprungliga storlek är 474 x 316 och du anger parametrarna bredd och höjd till 200 x 200 (`&w=200&h=200`), Bing returnerar en avbildning som innehåller 33 bildpunkter vit utfyllnad på överkant och nederkant. Om du inkluderar den `&p` Frågeparametern Bing returnerar en 200 x 134 bild.

## <a name="crop-a-thumbnail"></a>Beskär en miniatyrbild 

Beskär en avbildning genom att inkludera den `c` (Beskär) frågeparameter. Du kan använda följande värden:
  
- `4` &mdash; Hemlig förhållande  
- `7` &mdash; Smart-förhållande  

### <a name="smart-ratio-cropping"></a>Smart beskärning förhållande

Om du begär förhållande Smart beskärning (genom att ange den `c` parameter `7`), Bing kommer beskära en bild från mitten av dess intresseregionen utåt, samtidigt som bildens höjd-/ breddförhållandet. Det intressanta området är del av avbildningen som Bing avgör innehåller de flesta import delar. Nedan visas ett exempel intresseregionen.  
  
![Intresseregionen](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Om du ändrar storlek på en bild och begära förhållande Smart beskärning, minskar Bing avbildningen till den begärda storleken samtidigt som proportionerna. Bing sedan Beskär bilden baserat på de ändrade dimensionerna. Till exempel om den ändrade bredden är mindre än eller lika med höjden, Beskär bilden till vänster och höger i mitten av det intressanta området i Bing. I annat fall kommer Bing beskära den längst upp och längst ned i mitten av det intressanta området.  
  
 
Nedan visas den avbildning som minskat till 200 x 200 med förhållandet Smart beskärning. Eftersom Bing mäter avbildningen från det övre vänstra hörnet, beskärs längst ned i bilden. 
  
![Liggande bilden beskärs 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Nedan visas den avbildning som minskat till 200 x 100 med förhållandet Smart beskärning. Eftersom Bing mäter avbildningen från det övre vänstra hörnet, beskärs längst ned i bilden. 
   
![Liggande bilden beskärs 200 × 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Nedan visas den avbildning som minskat till 100 x 200 med förhållandet Smart beskärning. Eftersom Bing mäter avbildningen från mitten, beskärs vänster och höger delar av bilden.
  
![Liggande bilden beskärs 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Om Bing inte kan avgöra avbildningens intresseregionen, ska användas av tjänsten hemlig förhållande beskärning.  

### <a name="blind-ratio-cropping"></a>Hemlig förhållande beskärning

Om du begär hemlig förhållande beskärning (genom att ange den `c` parameter `4`), Bing använder följande regler för att Beskär bilden.  
  
- Om `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, bilden, mätt från längst upp till vänster och beskärs längst ned på sidan.  
- Om `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, bilden, mätt från mitten och beskärs till vänster och höger.  

Nedan visas en stående-avbildning som är 225 x 300.  
  
![Direkt konsumtion originalbilden](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Nedan visas den avbildning som minskat till 200 x 200 med hemlig förhållande beskärning. Avbildningen mäts från det övre vänstra hörnet lett till längst ned i bilden beskärs.  
  
![Direkt konsumtion bilden beskärs 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Nedan visas den avbildning som minskat till 200 x 100 med hemlig förhållande beskärning. Avbildningen mäts från det övre vänstra hörnet lett till längst ned i bilden beskärs.  
  
![Direkt konsumtion bilden beskärs 200 × 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Nedan visas den avbildning som minskat till 100 x 200 med hemlig förhållande beskärning. Avbildningen mäts från center vilket resulterar i de vänstra och högra delarna av bilden beskärs.  
  
![Direkt konsumtion bilden beskärs 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Nästa steg

* [Vad är API: er för Bing Search?](bing-api-comparison.md)
* [Bing Search API använder och visa krav](use-display-requirements.md)
