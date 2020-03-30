---
title: 'Rensa data som saknas: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Rensa data som saknas i Azure Machine Learning för att ta bort, ersätta eller dra slutsatser om saknade värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477705"
---
# <a name="clean-missing-data-module"></a>Rensa datamodul som saknas

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att ta bort, ersätta eller dra slutsatsen saknade värden. 

Dataexperter kontrollerar ofta data för saknade värden och utför sedan olika åtgärder för att åtgärda data eller infoga nya värden. Målet med sådana rengöringsåtgärder är att förhindra problem som orsakas av saknade data som kan uppstå vid utbildning av en modell. 

Den här modulen stöder flera typer av åtgärder för att "rengöra" saknade värden, inklusive:

+ Ersätta värden som saknas med en platshållare, medelvärde eller annat värde
+ Helt ta bort rader och kolumner som saknar värden
+ Dra slutsatser om värden baserade på statistiska metoder


Om du använder den här modulen ändras inte källdatauppsättningen. I stället skapas en ny datauppsättning på arbetsytan som du kan använda i det efterföljande arbetsflödet. Du kan också spara den nya, rensade datauppsättningen för återanvändning.

Den här modulen matar också ut en definition av omvandlingen som används för att rengöra de saknade värdena. Du kan återanvända den här omvandlingen på andra datauppsättningar som har samma schema med hjälp av modulen [Använd omvandling.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Så här använder du data som saknas

Med den här modulen kan du definiera en rengöring. Du kan också spara rengöringen så att du kan använda den senare på nya data. Se följande avsnitt i hur du skapar och sparar en rengöringsprocess: 
 
+ [Så här ersätter du saknade värden](#replace-missing-values)
  
+ [Så här tillämpar du en rengöringstransformation på nya data](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Den rengöringsmetod som du använder för att hantera saknade värden kan påverka dina resultat dramatiskt. Vi rekommenderar att du experimenterar med olika metoder. Tänk på både motiveringen för användning av en viss metod och kvaliteten på resultaten.

### <a name="replace-missing-values"></a>Ersätta saknade värden  

Varje gång du använder modulen [Rensa data som saknas](./clean-missing-data.md) på en uppsättning data tillämpas samma rensningsåtgärd på alla kolumner som du väljer. Om du behöver rensa olika kolumner med olika metoder använder du därför separata instanser av modulen.

1.  Lägg till modulen [Rensa data som saknas](./clean-missing-data.md) i pipelinen och anslut datauppsättningen som saknar värden.  
  
2.  För **kolumner som ska rensas**väljer du de kolumner som innehåller de värden som saknas som du vill ändra. Du kan välja flera kolumner, men du måste använda samma ersättningsmetod i alla markerade kolumner. Därför måste du vanligtvis rensa strängkolumner och numeriska kolumner separat.

    Om du till exempel vill söka efter saknade värden i alla numeriska kolumner:

    1. Välj modulen **Rensa data som saknas** och klicka på **Redigera kolumn** på modulens högra panel.

    3. För **Inkludera**väljer du **Kolumntyper** i listrutan och väljer sedan **Numeriskt**. 
  
    Alla rengörings- eller ersättningsmetoder som du väljer måste gälla för **alla** kolumner i markeringen. Om data i en kolumn är inkompatibla med den angivna åtgärden returnerar modulen ett fel och stoppar pipelinen.
  
3.  För **Minsta värdekvot som saknas**anger du det minsta antal värden som saknas som krävs för att åtgärden ska utföras.  
  
    Du använder det här alternativet i kombination med **maximalt värdeförhållande som saknas** för att definiera under vilka förhållanden en rensning utförs på datauppsättningen. Om det finns för många eller för få rader som saknar värden kan åtgärden inte utföras. 
  
    Talet du anger representerar **förhållandet mellan** saknade värden och alla värden i kolumnen. Som standard är egenskapen **Minsta saknade värdekvot** inställd på 0. Detta innebär att saknade värden rensas även om det bara finns ett värde som saknas. 

    > [!WARNING]
    > Detta villkor måste uppfyllas av varje kolumn för att den angivna åtgärden ska kunna tillämpas. Anta till exempel att du har markerat tre kolumner och sedan ange det minsta förhållandet mellan saknade värden till 0,2 (20 %), men bara en kolumn har faktiskt 20 % saknade värden. I det här fallet skulle rensningsåtgärden endast gälla kolumnen med över 20 % saknade värden. Därför skulle de andra kolumnerna vara oförändrade.
    > 
    > Om du är osäker på om värden som saknas har ändrats väljer du alternativet **Generera kolumn för indikatorkolumn för saknade värdeindikatorer**. En kolumn läggs till i datauppsättningen för att ange om varje kolumn uppfyllde de angivna kriterierna för minimi- och maximiintervallen.  
  
4. För **Maximalt värdeförhållande som saknas**anger du det maximala antalet värden som saknas för den operation som ska utföras.   
  
    Du kanske till exempel bara vill utföra värdeersättning som saknas om 30 % eller färre rader innehåller värden som saknas, men lämnar värdena i det första fallet om mer än 30 % av raderna har saknade värden.  
  
    Du definierar talet som förhållandet mellan saknade värden och alla värden i kolumnen. Som standard är det **maximala saknade värdeförhållandet** inställt på 1. Detta innebär att saknade värden rensas även om 100 % av värdena i kolumnen saknas.  
  
   
  
5. För **rengöringsläge**väljer du ett av följande alternativ för att ersätta eller ta bort värden som saknas:  
  
  
    + **Anpassat ersättningsvärde**: Använd det här alternativet om du vill ange ett platshållarvärde (till exempel ett 0 eller NA) som gäller för alla värden som saknas. Det värde som du anger som ersättning måste vara kompatibelt med kolumnens datatyp.
  
    + **Ersätt med medelvärdet**: Beräknar kolumnmedelvärdet och använder medelvärdet som ersättningsvärde för varje saknat värde i kolumnen.  
  
        Gäller endast kolumner som har datatyper för Heltal, Dubbel eller Boolean.  
  
    + **Ersätt med median:** Beräknar kolumnmediavärdet och använder medianvärdet som ersättning för eventuella saknade värden i kolumnen.  
  
        Gäller endast kolumner som har Heltals- eller Double-datatyper. 
  
    + **Ersätt med läge**: Beräknar läget för kolumnen och använder läget som ersättningsvärde för varje värde som saknas i kolumnen.  
  
        Gäller för kolumner som har Datatyper för Heltal, Dubbel, Boolean eller Kategorisk. 
  
    + **Ta bort hela raden**: Tar helt bort alla rader i datauppsättningen som har ett eller flera värden som saknas. Detta är användbart om det saknade värdet kan anses vara slumpmässigt saknas.  
  
    + **Ta bort hela kolumnen**: Tar helt bort alla kolumner i datauppsättningen som har ett eller flera värden som saknas.  
  
    
  
6. Alternativet **Ersättningsvärde** är tillgängligt om du har valt alternativet, **Anpassat ersättningsvärde**. Skriv ett nytt värde som ska användas som ersättningsvärde för alla värden som saknas i kolumnen.  
  
    Observera att du bara kan använda det här alternativet i kolumner som har Heltal, Dubbel, Boolean eller Sträng.
  
7. **Generera indikatorkolumn för saknade värdeindikatorer:** Välj det här alternativet om du vill mata ut någon indikation på om värdena i kolumnen uppfyllde kriterierna för att rensa värden saknas. Det här alternativet är särskilt användbart när du ställer in en ny rengöring och vill se till att den fungerar som den är utformad.
  
8. Skicka pipelinen.

### <a name="results"></a>Resultat

Modulen returnerar två utgångar:  

-   **Rensad datauppsättning:** En datauppsättning som består av de markerade kolumnerna, med saknade värden som hanteras enligt angivna, tillsammans med en indikatorkolumn, om du har valt det alternativet.  

    Kolumner som inte valts för rengöring är också "passerade".  
  
-  **Rengöringsomvandling**: En dataomvandling som används för rengöring, som kan sparas på arbetsytan och tillämpas på nya data senare.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Använda en sparad rengöring på nya data  

Om du behöver upprepa rengöringsoperationer ofta rekommenderar vi att du sparar receptet för datarensning som en *transformering*för att återanvända med samma datauppsättning. Det är särskilt användbart att spara en rensningsomvandling om du ofta måste importera och sedan rensa data som har samma schema.  
      
1.  Lägg till modulen [Använd omvandling](./apply-transformation.md) i pipelinen.  
  
2.  Lägg till den datauppsättning som du vill rensa och anslut datauppsättningen till den högra indataporten.  
  
3.  Expandera gruppen **Transformeringar** i designerns vänstra fönsterruta. Leta reda på den sparade omvandlingen och dra den till pipelinen.  

4.  Anslut den sparade omvandlingen till den vänstra indataporten [i Apply Transformation](./apply-transformation.md). 

    När du använder en sparad omvandling kan du inte markera de kolumner som omvandlingen tillämpas på. Det beror på att omvandlingen redan har definierats och gäller automatiskt för de kolumner som anges i den ursprungliga åtgärden.

    Anta dock att du har skapat en omformning för en delmängd av numeriska kolumner. Du kan använda den här omvandlingen på en datauppsättning av blandade kolumntyper utan att skapa ett fel, eftersom de saknade värdena bara ändras i de matchande numeriska kolumnerna.

6.  Skicka pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 