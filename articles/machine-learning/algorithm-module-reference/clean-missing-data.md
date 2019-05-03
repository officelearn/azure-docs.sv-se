---
title: 'Rensa Data som saknas: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Rensa Data som saknas i Azure Machine Learning-tjänsten för att ta bort, ersätta eller härleda värden som saknas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028792"
---
# <a name="clean-missing-data-module"></a>Rensa Data som saknas-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen om du vill ta bort, ersätta eller härleda värden som saknas. 

Dataexperter Kontrollera ofta data för saknade värden och utföra olika åtgärder för att korrigera data eller infoga nya värden. Målet med sådana rengöringsband åtgärder är att förhindra problem på grund av saknade data som kan uppstå när träna en modell. 

Den här modulen stöder flera typ av åtgärder för ”Rensa” saknade värden, inklusive:

+ Ersätt värden som saknas med platshållare, medel eller andra värde
+ Helt ta bort rader och kolumner som har värden som saknas
+ Härledning av värden som baseras på statistiska metoder


Med den här modulen ändras inte datauppsättningen för källan. Istället skapas en ny datauppsättning i din arbetsyta som du kan använda i efterföljande arbetsflödet. Du kan också spara nya, rensade datauppsättningen för återanvändning.

Den här modulen returnerar också en definition av omvandling används för att rensa värden som saknas. Du kan återanvända den här omvandlingen andra datauppsättningar som har samma schema med hjälp av den [gäller omvandling](./apply-transformation.md) modulen.  

## <a name="how-to-use-clean-missing-data"></a>Hur du använder Rensa Data som saknas

Den här modulen kan du definiera en rengöringsåtgärden. Du kan också spara rengöringsåtgärden så att du kan använda den senare för nya data. Se följande länkar för en beskrivning av hur du skapar och sparar en process som rengöringsband: 
 
+ Ersätt värden som saknas
  
+ Att tillämpa en rengöringsband omvandling till nya data
 
> [!IMPORTANT]
> Metoden rengöringsband som du använder för att hantera värden som saknas kan avsevärt påverka resultatet. Vi rekommenderar att du experimentera med olika metoder. Överväg att båda anledningen för användning av en viss metod och resultatets kvalitet.

### <a name="replace-missing-values"></a>Ersätt värden som saknas  

Varje gång som du har utfört den [Rensa Data som saknas](./clean-missing-data.md) modul till en datauppsättning med samma rengöringsåtgärden tillämpas på alla kolumner som du väljer. Om du vill rensa olika kolumner med olika metoder kan du därför använda separata instanser av modulen.

1.  Lägg till den [Rensa Data som saknas](./clean-missing-data.md) modul till ditt experiment, och anslut den datauppsättning som har värden som saknas.  
  
2.  För **kolumner som ska rensas bort**, Välj de kolumner som innehåller de värden som saknas och som du vill ändra. Du kan välja flera kolumner, men du måste använda samma metod som ersättning i alla valda kolumner. Därför vanligtvis måste du rensa strängkolumner och numeriska kolumner separat.

    Till exempel vill söka efter saknade värden i alla numeriska kolumner:

    1. Öppna kolumnväljaren och välj **med regler**.
    2. För **börjar med**väljer **nr kolumner**.

        Du kan också starta med alla kolumner och utesluta kolumner. Inledningsvis är regler visas inte om du först på **alla kolumner**, men du kan klicka på **nr kolumner** och klicka sedan på **alla kolumner** igen för att börja med alla kolumner och sedan filtrera ut (exkludera) kolumner baserat på namn, datatyp, eller index för kolumner.

    3. För **inkludera**väljer **kolumntyp** från listrutan och välj sedan **numeriska**, eller en mer specifik numerisk typ. 
  
    Valfri rengöras eller ersättning metod som du väljer måste tillämpas på **alla** kolumner i urvalet. Om data i valfri kolumn är inte kompatibel med den angivna åtgärden modulen returnerar ett fel och stoppar experimentet.
  
3.  För **minsta saknas värdet förhållandet**, ange det minsta antalet värden som saknas och som krävs för åtgärden som ska utföras.  
  
    Du använder det här alternativet i kombination med **maximalt värde förhållande som saknas** och definierar de villkor som ett rengöringsband åtgärden utförs på datauppsättningen. Om det finns för många eller för få rader som saknar värden, det går inte att utföra åtgärden. 
  
    Det tal du anger representerar den **förhållande** värden som saknas på alla värdena i kolumnen. Som standard den **minsta saknas värdet förhållandet** egenskapen till 0. Det innebär att värden som saknas rensas även om det finns bara ett värde som saknas. 

    > [!WARNING]
    > Det här villkoret måste uppfyllas av varje kolumn för den angivna åtgärden tillämpas. Anta exempelvis att du har valt tre kolumner och sedan ange den minsta kontrastförhållande på saknade värden till.2 (20%), men endast en kolumn har faktiskt 20% värden som saknas. I det här fallet gäller Rensningsåtgärden endast för kolumnen med över 20% saknar värden. Därför kan är de andra kolumnerna oförändrade.
    > 
    > Om du är osäker om saknade värden har ändrats, markerar du alternativet **generera saknas indikator kolumnen**. En kolumn läggs till i datauppsättningen som visar huruvida varje kolumn uppfyller de angivna villkoren för de minsta och största intervall.  
  
4. För **maximalt värde förhållande som saknas**, ange det maximala antalet värden som saknas som kan finnas för åtgärden som ska utföras.   
  
    Du kanske exempelvis vill utföra saknas värdet ersättningen om 30% eller färre rader innehåller värden som saknas, men lämna värden som-är om mer än 30% av rader har värden som saknas.  
  
    Du kan ange hur många som förhållandet mellan värden som saknas på alla värdena i kolumnen. Som standard den **maximalt värde förhållande som saknas** har angetts till 1. Det innebär att värden som saknas rensas även om 100% av värdena i kolumnen saknas.  
  
   
  
5. För **rensning läge**, väljer du något av följande alternativ för att ersätta eller ta bort saknas värden:  
  
  
    + **Anpassade ersättningsvärde**: Använd det här alternativet om du vill ange ett platshållarvärde (t.ex 0 eller NA) som gäller för alla värden som saknas. Det värde som du anger som en ersättning måste vara kompatibel med datatypen för kolumnen.
  
    + **Ersätt med medelvärdet**: Beräknar medelvärdet för kolumnen och använder medelvärdet som ersättningsvärdet för varje saknas värde i kolumnen.  
  
        Gäller enbart för kolumner som har Integer, Double eller booleskt datatyper.  
  
    + **Ersätt med median**: Beräknar kolumnen medianvärdet och använder medianvärdet som ersättningen för alla saknade värden i kolumnen.  
  
        Gäller enbart för kolumner som har heltal eller dubbel datatyper. 
  
    + **Ersätt med läget**: Beräknar läge för kolumnen och använder läget som ersättningsvärdet för varje saknas värde i kolumnen.  
  
        Gäller för kolumner som har Integer, Double, booleskt värde eller Kategoriskt datatyper. 
  
    + **Ta bort hela raden**: Fullständigt tar bort alla rader i den datauppsättning som har en eller flera av de värden som saknas. Detta är användbart om värden som saknas kan anses slumpmässigt saknas.  
  
    + **Ta bort hela kolumnen**: Fullständigt tar bort en kolumn i datauppsättningen som har en eller flera av de värden som saknas.  
  
    
  
6. Alternativet **ersättningsvärdet** är tillgänglig om du har valt alternativet **värde för anpassat ersättningen**. Ange ett nytt värde som används som ersättningsvärdet för alla värden som saknas i kolumnen.  
  
    Observera att du kan använda det här alternativet endast i kolumner som har datatyper Integer, Double, booleskt värde eller datum. För datumkolumner, ersättningsvärdet kan också anges som du antalet intervall om 100 nanosekunder sedan 1/1/0001 12:00 A.M.  
  
7. **Generera saknas indikator kolumnen**: Välj det här alternativet om du vill spara någon indikation på om värdena i kolumnen uppfylls kriterierna för saknade värden rensning. Det här alternativet är särskilt användbart när du hur du konfigurerar en ny rengöringsåtgärden och vill kontrollera att den fungerar som avsett.
  
8. Kör experimentet.

### <a name="results"></a>Resultat

Modulen returnerar två utdata:  

-   **Rensade datauppsättningen**: En datauppsättning som består av de markerade kolumnerna med värden som saknas hanteras enligt vad som anges tillsammans med en kolumn för indikatorn, om du har valt det alternativet.  

    Kolumner som inte valts för rensning också skickas ”via”.  
  
-  **Rensa omvandling**: En Dataomvandling används för rensning, som kan sparas på din arbetsyta och tillämpas på nya data senare.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Använda en sparad rengöringsåtgärden nya data  

Om du behöver upprepa rengöringsband åtgärder ofta, rekommenderar vi att du sparar din recept för Datarensning som en *transformera*, för att återanvända med samma datamängd. Sparar en transformation som rengöringsband är särskilt användbart om du måste importera ofta på nytt och sedan rensa data som har samma schema.  
      
1.  Lägg till den [gäller omvandling](./apply-transformation.md) modulen i experimentet.  
  
2.  Lägg till den datauppsättning som du vill rensa och Anslut datauppsättningen till den högra indataporten.  
  
3.  Expandera den **omvandlar** i den vänstra rutan i gränssnittet. Leta upp den sparade omvandlingen och dra det till experimentet.  
  
4.  Anslut den sparade omvandlingen till den vänstra indataporten för [gäller omvandling](./apply-transformation.md). 

    När du använder en sparad omvandling så kan du markera de kolumner som används som omvandlingen. Det beror omvandlingen redan har definierats och gäller automatiskt för de kolumner som anges i den ursprungliga åtgärden.

    Anta dock att du har skapat en omvandling på en delmängd av numeriska kolumner. Du kan använda den här omvandlingen till en datauppsättning för blandade kolumntyper utan att ett fel eftersom dessa värden ändras endast i de matchande numeriska kolumnerna.

6.  Kör experimentet.  

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 