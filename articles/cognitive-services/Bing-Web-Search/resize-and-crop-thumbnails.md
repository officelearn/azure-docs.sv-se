---
title: Ändra storlek på och beskära bildminiatyrer - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Några svar från Bing Search API:erna innehåller url:er till miniatyrbilder som visas av Bing, som du kan ändra storlek på och beskära, och kan innehålla frågeparametrar.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110633"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ändra storlek på och beskära miniatyrbilder

Några svar från Bing Search API:erna innehåller url:er till miniatyrbilder som visas av Bing, som du kan ändra storlek på och beskära, och kan innehålla frågeparametrar. Ett exempel:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Om du visar en delmängd av dessa miniatyrer kan du ange ett alternativ för att visa de återstående bilderna.

> [!NOTE]
> Se till att beskärning och storleksändring av miniatyrbilder ger ett sökscenario som respekterar tredje parts rättigheter, i enlighet med kraven för [användning och visning av](use-display-requirements.md)Bing Search.

## <a name="resize-a-thumbnail"></a>Ändra storlek på en miniatyr 

Om du vill ändra storlek på en miniatyr rekommenderar `w` Bing `h` att du bara anger en frågeparametrar (bredd) eller (höjd) i miniatyrens URL. Om du bara anger höjden eller bredden kan Bing behålla bildens ursprungliga aspekt. Ange bredd och höjd i pixlar. 

Om den ursprungliga miniatyren till exempel är 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Och du vill minska dess `w` storlek, ställa in parametern till ett nytt värde (till exempel) `336`och ta bort parametern: `h`

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Om du bara anger höjden eller bredden på en miniatyrbild behålls bildens ursprungliga proportioner. Om du anger båda parametrarna och proportionerna inte bibehålls, läggs Bing till vit utfyllnad på bildens kantlinje.

Om du till exempel ändrar storlek på en bild på 480x359 till 200x200 utan beskärning, innehåller hela bredden bilden, men höjden innehåller 25 pixlar vit utfyllnad längst upp och längst ned i bilden. Om bilden var 359x480, skulle vänster och höger kantlinje innehålla vit utfyllnad. Om du beskär bilden läggs inte vit utfyllnad till.  

Följande bild visar den ursprungliga storleken på en miniatyrbild (480x300).  
  
![Arkivbild.](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Följande bild visar bilden storleksas till 200x200. Bildförhållandet bibehålls och de övre och nedre kanterna är vadderade med vitt (den svarta gränsen här ingår för att visa stoppningen).  
  
![Storleksad landskapsbild](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Om du anger dimensioner som är större än bildens ursprungliga bredd och höjd, lägger Bing till vit utfyllnad till vänster och övre kantlinjer.  

## <a name="request-different-thumbnail-sizes"></a>Begär olika miniatyrstorlekar

Om du vill begära en annan miniatyrbildsstorlek tar du `id` bort `pid` alla frågeparametrar från miniatyrens URL utom parametrarna och. Lägg sedan `&w` till frågeparametern (bredd) eller `&h` (höjd) med önskad bildstorlek i pixlar, men inte båda. Bing kommer att behålla bildens ursprungliga bildförhållande. 

Om du vill öka bredden på bilden som anges av ovanstående URL till 165 pixlar använder du följande URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Om du begär en bild som är större än bildens ursprungliga storlek lägger Bing till vit utfyllnad runt bilden efter behov. Om bildens ursprungliga storlek till exempel är 474x316 och du ställer in på `&w` 500 returnerar Bing en bild på 500x333. Den här bilden har 8,5 pixlar vit utfyllnad längs de övre och nedre kanterna och 13 pixlar stoppning på vänster och höger kant.

Om du vill förhindra att Bing lägger till vit utfyllnad om `&p` den begärda storleken är större än bildens ursprungliga storlek anger du frågeparametern till 0. Om du till exempel `&p=0` inkluderar parametern i ovanstående URL returnerar Bing en bild på 474x316 i stället för en 500x333-bild:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Om du `&w` anger `&h` båda och frågeparametrar behåller Bing bildens proportioner och lägger till vit utfyllnad efter behov. Om bildens ursprungliga storlek till exempel är 474x316 och du ställer in bredd- och`&w=200&h=200`höjdparametrarna till 200x200 ( ) returnerar Bing en bild som innehåller 33 pixlar vit utfyllnad överst och längst ned. Om du `&p` inkluderar frågeparametern returnerar Bing en bild på 200x134.

## <a name="crop-a-thumbnail"></a>Beskära en miniatyrbild 

Om du vill beskära en bild inkluderar du frågeparametern `c` (beskära). Du kan använda följande värden:
  
- `4`&mdash; Blind ratio  
- `7`&mdash; Smart Ratio (smart kvot)  

### <a name="smart-ratio-cropping"></a>Beskärning av smart ratio

Om du begär beskärning av `c` smart `7`ratio (genom att ställa in parametern till ) beskärs en bild från mitten av dess intresseområde utåt, samtidigt som bildens proportioner bibehålls. Den region av intresse är det område av bilden som Bing bestämmer innehåller flest importdelar. Följande visar ett exempel region av intresse.  
  
![Region av intresse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Om du ändrar storlek på en bild och begär smart ratio-beskärning minskar Bing bilden till den begärda storleken samtidigt som proportionerna bibehålls. Bing beskär sedan bilden baserat på de storleksdimensionerade dimensionerna. Om den storleksägde bredden till exempel är mindre än eller lika med höjden, beskär bing bilden till vänster och höger om mitten av den region av intresse. Annars kommer Bing beskära den till toppen och botten av mitten av regionen av intresse.  
  
 
Följande visar bilden reducerad till 200x200 med Smart Ratio beskärning. Eftersom Bing mäter bilden från det övre vänstra hörnet beskärs den nedre delen av bilden. 
  
![Landskapsbild beskuren till 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Följande visar bilden reducerad till 200x100 med Smart Ratio beskärning. Eftersom Bing mäter bilden från det övre vänstra hörnet beskärs den nedre delen av bilden. 
   
![Landskapsbild beskuren till 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Följande visar bilden reducerad till 100x200 med Smart Ratio beskärning. Eftersom Bing mäter bilden från mitten beskärs bildens vänstra och högra delar.
  
![Landskapsbild beskuren till 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Om Bing inte kan avgöra bildens region av intresse, kommer tjänsten att använda blind ratio beskärning.  

### <a name="blind-ratio-cropping"></a>Beskärning av blinda förhållanden

Om du begär beskärning av `c` blinda `4`förhållanden (genom att ställa in parametern till ) använder Bing följande regler för att beskära bilden.  
  
- Om `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`mäts bilden från det övre vänstra hörnet och beskärs längst ned.  
- Om `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`mäts bilden från mitten och beskärs till vänster och höger.  

Följande visar en stående bild som är 225x300.  
  
![Original solrosbild](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Följande visar bilden reducerad till 200x200 med blind ratio beskärning. Bilden mäts från det övre vänstra hörnet, vilket resulterar i att den nedre delen av bilden beskärs.  
  
![Solrosbild beskuren till 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Följande visar bilden reducerad till 200x100 med blind ratio beskärning. Bilden mäts från det övre vänstra hörnet, vilket resulterar i att den nedre delen av bilden beskärs.  
  
![Solrosbild beskuren till 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Följande visar bilden reducerad till 100x200 med blind ratio beskärning. Bilden mäts från mitten vilket resulterar i att bildens vänstra och högra delar beskärs.  
  
![Solrosbild beskuren till 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Nästa steg

* [Vilka är Bing Search API:er?](bing-api-comparison.md)
* [Användnings- och visningskrav för API:er för Bing-sökresultat](use-display-requirements.md)
