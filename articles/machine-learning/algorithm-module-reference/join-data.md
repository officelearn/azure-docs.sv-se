---
title: 'Gå med i data: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Join Join Data i Azure Machine Learning för att sammanfoga datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477586"
---
# <a name="join-data"></a>Koppla data

I den här artikeln beskrivs hur du använder modulen **Join Data** i Azure Machine Learning designer (förhandsversion) för att sammanfoga två datauppsättningar med hjälp av en anslutningsåtgärd i databasstil.  

## <a name="how-to-configure-join-data"></a>Konfigurera join-data

Om du vill utföra en koppling på två datauppsättningar bör de vara relaterade med en nyckelkolumn. Sammansatta nycklar med flera kolumner stöds också. 

1. Lägg till de datauppsättningar som du vill kombinera och dra sedan modulen **Anslut data** till pipelinen. 

    Du hittar modulen i kategorin **Dataomvandling** under **Manipulation**.

1. Anslut datauppsättningarna till modulen **Anslut data.** 
 
1. Välj **Starta kolumnväljare** om du vill välja nyckelkolumnerna. Kom ihåg att välja kolumner för både vänster och höger ingångar.

    För en enda nyckel:

    Välj en kolumn med en tangent för båda ingångarna.
    
    För en sammansatt nyckel:

    Markera alla nyckelkolumner från vänster ingång och höger ingång i samma ordning. **Modulen Koppla data** ansluter till tabellerna när alla nyckelkolumner matchar. Markera alternativet **Tillåt dubbletter och bevara kolumnordningen i markeringen** om kolumnordningen inte är densamma som den ursprungliga tabellen. 

    ![kolumnväljare](media/module/join-data-column-selector.png)


1. Välj alternativet **Matcha skiftläge** om du vill bevara skiftlägeskänsligheten i en textkolumnkoppling. 
   
1. Använd listrutan **Koppla typ** för att ange hur datauppsättningarna ska kombineras.  
  
    * **Inre koppling:** En *inre koppling* är den vanligaste kopplingsåtgärden. De kombinerade raderna returneras endast när värdena för nyckelkolumnerna matchar.  
  
    * **Vänster yttre koppling:** En *vänster yttre koppling* returnerar sammanfogade rader för alla rader från den vänstra tabellen. När en rad i den vänstra tabellen inte har några matchande rader i den högra tabellen innehåller den returnerade raden värden som saknas för alla kolumner som kommer från den högra tabellen. Du kan också ange ett ersättningsvärde för saknade värden.  
  
    * **Full yttre koppling:** En *fullständig yttre koppling* returnerar alla rader från den vänstra tabellen **(tabell1)** och från den högra tabellen (**tabell2**).  
  
         För var och en av raderna i endera tabellen som inte har några matchande rader i den andra, innehåller resultatet en rad som innehåller värden som saknas.  
  
    * **Vänster semi-join:** En *vänster halvkoppling* returnerar bara värdena från den vänstra tabellen när värdena i nyckelkolumnerna matchar.  

1. För alternativet **Behåll höger nyckelkolumner i den kopplade tabellen:**

    * Välj det här alternativet om du vill visa nycklarna från båda indatatabellerna.
    * Avmarkera om du bara vill returnera nyckelkolumnerna från den vänstra ingången.

1. Skicka pipelinen.

1. Om du vill visa resultatet högerklickar du på **Kopplingsdata** och väljer **Visualisera**.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 