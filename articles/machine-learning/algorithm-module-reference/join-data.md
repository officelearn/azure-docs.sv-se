---
title: 'Koppla data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Anslut till data i Azure Machine Learning-tjänsten för att sammanfoga data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: 5fdb822a445e8a62cd36e6dd05aa218b9b41ce9e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128742"
---
# <a name="join-data"></a>Koppla data

Den här artikeln beskriver hur du använder modulen **koppla data** i det visuella gränssnittet i Azure Machine Learning tjänsten för att slå samman två data uppsättningar med en kopplings åtgärd i databas format.  

## <a name="how-to-configure-join-data"></a>Så här konfigurerar du kopplings data

Om du vill utföra en koppling på två data uppsättningar ska de relateras av en nyckel kolumn. Sammansatta nycklar med flera kolumner stöds också. 

1. Lägg till de data uppsättningar som du vill kombinera och dra sedan modulen **koppla data** till experimentet. 

    Du hittar modulen i kategorin **data omvandling** under **manipulation**.

1. Anslut data uppsättningarna till modulen **koppla data** . 
 
1. Välj **Starta kolumn väljaren** för att välja nyckel kolumn (er). Kom ihåg att välja kolumner för både vänster och höger indata.

    För en enskild nyckel:

    Välj en enskild nyckel kolumn för båda indata.
    
    För en sammansatt nyckel:

    Välj alla nyckel kolumner från vänster inmatad och höger indatatyp i samma ordning. Modulen **koppla data** kommer att koppla tabellerna när alla nyckel kolumner matchar. Markera alternativet **Tillåt dubbletter och behåll kolumn ordning i markering** om kolumn ordningen inte är densamma som den ursprungliga tabellen. 

    ![kolumn-väljare](media/module/join-data-column-selector.png)


1. Välj alternativet **Matcha gemener/versal** om du vill bevara Skift läges känslighet för en text kolumn koppling. 
   
1. Använd List rutan **kopplings typ** för att ange hur data uppsättningarna ska kombineras.  
  
    * **Inre koppling**: En *inre koppling* är den vanligaste kopplings åtgärden. Den returnerar de kombinerade raderna endast när värdena för nyckel kolumnerna matchar.  
  
    * **Vänster yttre koppling**: En *vänster yttre koppling* returnerar kopplade rader för alla rader från den vänstra tabellen. När en rad i den vänstra tabellen inte har några matchande rader i den högra tabellen, innehåller den returnerade raden värden som saknas för alla kolumner som kommer från den högra tabellen. Du kan också ange ett ersättnings värde för saknade värden.  
  
    * **Fullständig yttre koppling**: En *fullständig yttre koppling* returnerar alla rader från den vänstra tabellen (**TABLE1**) och från den högra tabellen (**tabell2**).  
  
         För varje rad i en tabell som inte har några matchande rader i den andra, innehåller resultatet en rad som innehåller värden som saknas.  
  
    * **Vänster halv koppling**: En *vänster halv koppling* returnerar bara värdena från den vänstra tabellen när värdena för nyckel kolumnerna matchar.  

1. För alternativet **Behåll rätt nyckel kolumner i en sammanfogad tabell**:

    * Välj det här alternativet om du vill visa nycklarna från båda ingångs tabellerna.
    * Avmarkera för att endast returnera nyckel kolumnerna från den vänstra indatatypen.

1. Kör experimentet eller Välj modulen koppla data och vald **körning vald** för att utföra kopplingen.

1. Om du vill visa resultaten högerklickar du på **data** > **uppsättnings** > visualiseringen kopplings resultat.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 