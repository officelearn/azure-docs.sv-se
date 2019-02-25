---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753792"
---
Vissa Bing-svar inkluderar URL: er till miniatyrbilder som hanteras av Bing. Du kan ändra storlek på och Beskär miniatyrbilder. 

> [!NOTE]
> Kontrollera storleken och beskärning på miniatyrbilden som ger ett Sök-scenario och respekterar tredje parts rättigheter som krävs av Bing Search API-användning och visa kraven.


Inkludera frågan bredd (b) om du vill ändra storlek på en bild, parameter, h (höjd) fråga eller båda i URL: en för den miniatyrbilden. Ange bredd och höjd i bildpunkter. Exempel:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Om du anger bara bredden eller endast Frågeparametern höjd, upprätthåller Bing bildens höjd-/ breddförhållandet. Om du anger både bredd och höjd och du inte ha en bilds ursprungliga proportionerna, Bing lägger till vit utfyllnad kantlinjen för avbildningen. Om du ändrar storlek på en 480 x 359 avbildning till 200 x 200 utan beskärning full bredd innehåller bilden men höjden innehåller 25 bildpunkter vit utfyllnad högst upp och längst ned på bilden. Samma skulle vara fallet om avbildningen har 359 x 480 utom vänstra och högra kantlinjen innehåller vit utfyllnad. Om du Beskär bilden läggs inte vit utfyllnad.  

 
Följande bild visar den ursprungliga storleken på en miniatyrbild (480 x 300).  
  
![Liggande originalbilden](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Följande bild visar bildens storlek ska ändras till 200 x 200. Förhållandet underhålls och de övre och nedre gränser är utfyllnad med vitt (svart kantlinjen ingår att visa utfyllnaden).  
  
![Ändrade liggande bild](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Om du anger dimensioner som är större än en bilds ursprungliga Breddoch höjd utfyllnad avbildningen med vitt på den vänstra och övre kantlinjen.  
  
Beskär en avbildning genom att inkludera c (Beskär) frågeparameter. Här följer möjliga värden som du kan ange.  
  
- 4&mdash;förhållande med nedsatt syn  
- 7&mdash;förhållande för smartkort  
  
Om du begär förhållande Smart beskärning (c = 7), bilden beskärs från mitten av avbildningens intresseregionen passiv samtidigt bildens höjd-/ breddförhållandet. Det intressanta området är del av avbildningen som Bing avgör innehåller de flesta import delar. Nedan visas ett exempel intresseregionen.  
  
![Intresseregionen](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Om du ändrar storlek på en bild och begära förhållande Smart beskärning reduceras avbildningen till den begärda storleken samtidigt som proportionerna. Bilden beskärs sedan baserat på de ändrade dimensionerna. Till exempel om den ändrade bredden är mindre än eller lika med höjden, beskärs bilden till vänster och höger i mitten av det intressanta området. I annat fall beskärs bilden längst upp och längst ned i mitten av det intressanta området.  
  
 
Nedan visas den avbildning som minskat till 200 x 200 med förhållandet Smart beskärning.  
  
![Liggande bilden beskärs 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Nedan visas den avbildning som minskat till 200 x 100 med förhållandet Smart beskärning.  
   
![Liggande bilden beskärs 200 × 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Nedan visas den avbildning som minskat till 100 x 200 med förhållandet Smart beskärning.  
  
![Liggande bilden beskärs 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Om Bing inte kan avgöra avbildningens intresseregionen, använder Bing hemlig förhållande beskärning.  
  
Om du begär hemlig förhållande beskärning (c = 4), Bing använder följande regler för att Beskär bilden.  
  
- Om (ursprungliga bredd / ursprungliga bildens höjd) < (begärt bredd / begärt bildens höjd) avbildningen mäts från övre vänstra hörnet och beskärs längst ned på sidan.  
- Om (ursprungliga bredd / ursprungliga bildens höjd) > (begärt bredd / begärt bildens höjd) avbildningen mätt från center och beskärs till vänster och höger.  



Nedan visas en stående-avbildning som är 225 x 300.  
  
![Direkt konsumtion originalbilden](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Nedan visas den avbildning som minskat till 200 x 200 med hemlig förhållande beskärning. Avbildningen mäts från det övre vänstra hörnet, vilket resulterar i längst ned i bilden beskärs.  
  
![Direkt konsumtion bilden beskärs 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Nedan visas den avbildning som minskat till 200 x 100 med hemlig förhållande beskärning. Avbildningen mäts från det övre vänstra hörnet, vilket resulterar i längst ned i bilden beskärs.  
  
![Direkt konsumtion bilden beskärs 200 × 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Nedan visas den avbildning som minskat till 100 x 200 med hemlig förhållande beskärning. Avbildningen mäts från center vilket resulterar i de vänstra och högra delarna av bilden beskärs.  
  
![Direkt konsumtion bilden beskärs 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

