---
title: Konvertera till indikatorvärden
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Konvertera till indikatorvärden i Azure Machine Learning för att konvertera kolumner som innehåller kategoriska värden till en serie binära indikatorkolumner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477671"
---
# <a name="convert-to-indicator-values"></a>Konvertera till indikatorvärden
I den här artikeln beskrivs en modul med Azure Machine Learning-designer.

Använd modulen **Konvertera till indikatorvärden** i Azure Machine Learning-designern för att konvertera kolumner som innehåller kategoriska värden till en serie binära indikatorkolumner.  

Den här modulen matar också ut en definition av den omvandling som används för att konvertera till indikatorvärden. Du kan återanvända den här omvandlingen på andra datauppsättningar som har samma schema med hjälp av modulen [Använd omvandling.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Konfigurera Konvertera till indikatorvärden

1.  Leta reda på **värdena Konvertera till indikator** och dra det till pipeline-utkastet. Du hittar den här modulen under kategorin **Dataomvandling.**
    > [!NOTE]
    > Du kan använda modulen [Redigera metadata](edit-metadata.md) före modulen Konvertera **till indiciatorvärden** för att markera målkolumnerna som kategoriska.

1. Anslut modulen **Konvertera till indikatorvärden** till den datauppsättning som innehåller de kolumner som du vill konvertera. 

1. Välj **Redigera kolumn** om du vill välja en eller flera kategoriska kolumner.

1. Markera alternativet **Skriv över kategoriska kolumner** om du **bara** vill mata ut de nya booleska kolumnerna. Som standard är det här alternativet inaktiverat.
    

    > [!TIP]
    >  Om du väljer alternativet att skriva över tas inte källkolumnen bort eller ändras. I stället genereras och presenteras de nya kolumnerna i utdatauppsättningen och källkolumnen förblir tillgänglig på arbetsytan. Om du behöver se de ursprungliga data kan du när som helst använda modulen [Lägg till kolumner](add-columns.md) för att lägga till källkolumnen igen.

1. Skicka pipelinen.

## <a name="results"></a>Resultat

Anta att du har en kolumn med poäng som anger om en server har en hög, medel eller låg sannolikhet för fel.  

| Server-ID | Felpoäng |
| --------- | ------------- |
| 10301     | Låg           |
| 10302     | Medel        |
| 10303     | Hög          |

När du använder **Konvertera till indikatorvärden**konverterar designern en enda kolumn med etiketter till flera kolumner som innehåller booleska värden:  

| Server-ID | Felpoäng - Låg | Felpoäng - Medel | Felpoäng - Hög |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Så här fungerar konverteringen:  

-   I kolumnen **Felpoäng** som beskriver risken finns det bara tre möjliga värden (Hög, Medel och Låg) och inga värden som saknas. Så exakt tre nya kolumner skapas.  

-   De nya indikatorkolumnerna namnges baserat på källkolumnens kolumnrubriker och värden med det här mönstret: * \<källkolumnen \<>- datavärde>*.  

-   Det bör finnas en 1 i exakt en indikatorkolumn och 0 i alla andra indikatorkolumner eftersom varje server bara kan ha en riskklassificering.  

Du kan nu använda de tre indikatorkolumnerna som funktioner i en maskininlärningsmodell.

Modulen returnerar två utgångar:

- **Resultatdatauppsättning**: En datauppsättning med konverterade indikatorvärden kolumner. Kolumner som inte valts för rengöring är också "passerade".
- **Omvandling av indikatorvärden**: En dataomvandling som används för att konvertera till indikatorvärden och som kan sparas på arbetsytan och tillämpas på nya data senare.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Använda en sparad indikatorvärdensåtgärd på nya data

Om du behöver upprepa åtgärder för indikatorvärden ofta kan du spara dina datamanipuleringssteg som en *transformering* för att återanvända den med samma datauppsättning. Detta är användbart om du ofta måste importera igen och sedan rensa data som har samma schema.

1. Lägg till modulen [Använd omvandling](apply-transformation.md) i pipelinen.

1. Lägg till den datauppsättning som du vill rensa och anslut datauppsättningen till den högra indataporten.

1. Expandera gruppen **Dataomvandling** i den vänstra rutan i designern. Leta reda på den sparade omvandlingen och dra den till pipelinen.

1. Anslut den sparade omvandlingen till den vänstra indataporten [i Apply Transformation](apply-transformation.md).

   När du använder en sparad omvandling kan du inte välja vilka kolumner som ska omformas. Detta beror på att omvandlingen har definierats och gäller automatiskt för de datatyper som anges i den ursprungliga åtgärden.

1. Skicka pipelinen.
 
## <a name="technical-notes"></a>Tekniska anmärkningar  

Det här avsnittet innehåller implementeringsinformation, tips och svar på vanliga frågor.

### <a name="usage-tips"></a>Tips för användning

-   Endast kolumner som är markerade som kategoriska kan konverteras till indikatorkolumner. Om följande fel visas är det troligt att en av kolumnerna du har valt inte är kategorisk:  

     Fel 0056: Kolumn \<med namnkolumnnamn> finns inte i en tillåten kategori.  

     Som standard hanteras de flesta strängkolumner som strängfunktioner, så du måste uttryckligen markera dem som kategoriska med hjälp av [Redigera metadata](edit-metadata.md).  

-   Det finns ingen gräns för hur många kolumner som du kan konvertera till indikatorkolumner. Eftersom varje kolumn med värden kan ge flera indikatorkolumner kanske du vill konvertera och granska bara några kolumner i taget.  

-   Om kolumnen innehåller värden som saknas skapas en separat indikatorkolumn för den saknade kategorin, med det här namnet: * \<källkolumnen>- Saknas*  

-   Om kolumnen som du konverterar till indikatorvärden innehåller tal måste den markeras som kategorisk som alla andra funktionskolumnen. När du har gjort det behandlas siffrorna som diskreta värden. Om du till exempel har en numerisk kolumn med MPG-värden på mellan 25 och 30, skapas en ny indikatorkolumn för varje diskret värde:  

    | Tillverkning       | Motorväg mpg -25 | Motorväg mpg -26 | Motorväg mpg -27 | Motorväg mpg -28 | Motorväg mpg -29 | Motorväg mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso Bilar | 0               | 0               | 0               | 0               | 0               | 1               |

- Undvik att lägga till för många dimensioner i datauppsättningen. Vi rekommenderar att du först kontrollerar antalet värden i kolumnen och lagerplats eller kvantifierar data på rätt sätt.  


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
