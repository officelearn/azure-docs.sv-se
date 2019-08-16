---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "67868981"
---
Vissa Bing-svar innehåller URL: er till miniatyr bilder som hanteras av Bing. Du kan ändra storlek och beskära miniatyr bilderna. 

> [!NOTE]
> Kontrol lera att miniatyrens storlek och beskärning ger ett Sök scenario och respektera tredje parts rättigheter, enligt kraven i Bing-sökning API-användning och visnings krav.


Om du vill ändra storlek på en bild tar du med Frågeparametern w (bredd), h (height) eller båda i miniatyrens URL. Ange bredd och höjd i bild punkter. Exempel:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Om du bara anger bredden eller bara parametern height behåller Bing bildens höjd-förhållande. Om du anger både bredd och höjd och du inte behåller bildens ursprungliga höjd-breddförhållandet, lägger Bing till en vit utfyllnad i bildens kant. Om du till exempel ändrar storlek på en 480x359-bild till 200x200 utan beskärning, innehåller den fullständiga bredden bilden, men höjden innehåller 25 pixlar med vit utfyllnad överst och längst ned i bilden. Detsamma gäller om bilden var 359x480, förutom den vänstra och högra kant linjen skulle innehålla vit utfyllnad. Om du beskär bilden läggs inte vit utfyllnad.  

 
Följande bild visar den ursprungliga storleken på en miniatyr bild (480x300).  
  
![Ursprunglig liggande bild](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Följande bild visar bild storleken ändrat till 200x200. Höjd-breddförhållandet upprätthålls och de övre och nedre kant linjerna fylls med vitt (den svarta kant linjen ingår för att Visa utfyllnaden).  
  
![Ändra storlek på liggande bild](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Om du anger mått som är större än bildens ursprungliga bredd och höjd, fylls bilden i med vitt i den vänstra och övre kant linjen.  
  
Om du vill beskära en bild inkluderar du Frågeparametern c (beskärning). Följande är de möjliga värden som du kan ange.  
  
- 4&mdash;blind kvot  
- 7&mdash;Smart kvot  
  
Om du begär en smart kvot beskärning (c = 7) beskärs bilden från mitten av bildens region för att utåt samtidigt som bildens proportioner bibehålls. Intresse området är det område i bilden som Bing avgör innehåller de flesta import delar. Nedan visas ett exempel på ett intresse område.  
  
![Intresse region](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Om du ändrar storlek på en bild och begär Smart kvot beskärning, minskas bilden till den begärda storleken samtidigt som proportionerna bibehålls. Bilden beskärs sedan baserat på dimensioner som ändrats. Om till exempel den storleksändrade bredden är mindre än eller lika med höjden beskärs bilden till vänster och höger om intresse området. Annars beskärs bilden längst upp och längst ned i mitten av intresse regionen.  
  
 
Nedan visas bilden som är reducerad till 200x200 med hjälp av Smart grad-beskärning.  
  
![Liggande bild beskuren till 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Nedan visas bilden som är reducerad till 200x100 med hjälp av Smart grad-beskärning.  
   
![Liggande bild beskuren till 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Nedan visas bilden som är reducerad till 100x200 med hjälp av Smart grad-beskärning.  
  
![Liggande bild beskuren till 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Om Bing inte kan avgöra bildens intresse område, använder Bing blind grade-beskärning.  
  
Om du begär överanvändning av hemliga kvot (c = 4) använder Bing följande regler för att beskära bilden.  
  
- Om (ursprunglig bild bredd/ursprunglig bildhöjd) < (begärd bild bredd/bild höjd) mäts bilden från det övre vänstra hörnet och beskärs längst ned.  
- Om (ursprunglig bild bredd/ursprunglig bildhöjd) > (begärd bild bredd/begärd bildhöjd), mäts bilden från mitten och beskärs till vänster och höger.  



Nedan visas en stående bild som är 225x300.  
  
![Ursprunglig sol ros bild](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Nedan visas bilden som är reducerad till 200x200 med beskärning av blinda tal. Bilden mäts från det övre vänstra hörnet som visas i den nedre delen av bilden som beskärs.  
  
![Sol ros bild beskuren till 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Nedan visas bilden som är reducerad till 200x100 med beskärning av blinda tal. Bilden mäts från det övre vänstra hörnet som visas i den nedre delen av bilden som beskärs.  
  
![Sol ros bild beskuren till 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Nedan visas bilden som är reducerad till 100x200 med beskärning av blinda tal. Bilden mäts från mitten som skapas i den vänstra och högra delen av bilden som beskärs.  
  
![Sol ros bild beskuren till 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

