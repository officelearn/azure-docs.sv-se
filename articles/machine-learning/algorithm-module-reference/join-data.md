---
title: 'Anslut till Data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder kopplingen koppla samman Data modul i Azure Machine Learning-tjänsten för att slå samman datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518019"
---
# <a name="join-data"></a>Koppla data

Den här artikeln beskriver hur du använder den **koppla samman Data** modul i Azure Machine Learning-tjänsten visuella gränssnittet att slå samman två datauppsättningar som använder en join-åtgärd för databas-format.  

## <a name="how-to-configure-join-data"></a>Så här konfigurerar du ansluta till Data

Om du vill utföra en koppling på två datauppsättningar, bör de relaterade genom en nyckelkolumn. Sammansatta nycklar med hjälp av flera kolumner stöds också. 

1. Lägg till de datauppsättningar som du vill kombinera och dra sedan den **ansluta Data** modulen i experimentet. 

    Du kan hitta modulen i den **Dataomvandling** kategorin under **manipulering av**.

1. Ansluta datauppsättningarna kan den **ansluta Data** modulen. 
 
1. Välj **starta kolumnväljaren** välja viktiga kolumner. Kom ihåg att välja kolumner för både vänster och höger indata.

    För en enda nyckel:

    Välj en enda nyckelkolumn för både indata.
    
    För en sammansatt nyckel:

    Välj alla nyckelkolumner indata vänstra och högra indatasporten i samma ordning. Den **koppla samman Data** modulen ansluts till tabellerna när alla kolumner matchar. Markera alternativet **tillåter dubbletter och bevara kolumnordning i val av** om kolumnordningen inte är samma som den ursprungliga tabellen. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Välj den **matcha gemener/versaler** om du vill bevara skiftlägeskänslighet på en kolumn text-koppling. 
   
1. Använd den **kopplingstyp** lista för att ange hur datauppsättningarna som ska kombineras.  
  
    * **Inre koppling**: En *inre koppling* är den vanligaste join-åtgärden. De kombinerade raderna returneras endast när värdena i nyckelkolumnerna som matchar.  
  
    * **Vänster yttre koppling**: En *vänster yttre koppling* returnerar ansluten rader för alla rader från den vänstra tabellen. När en rad i den vänstra tabellen har inga matchande rader i den högra tabellen, innehåller den returnerade raden värden som saknas för alla kolumner som kommer från den högra tabellen. Du kan också ange ett ersättningsvärde för värden som saknas.  
  
    * **Fullständig yttre koppling**: En *fullständig yttre koppling* returnerar alla rader från den vänstra tabellen (**tabell1**) och i den högra tabellen (**tabell2**).  
  
         Resultatet innehåller en rad som innehåller värden som saknas för var och en av de rader i antingen tabellen som innehåller inga matchande rader i den andra.  
  
    * **Left semikolonavgränsade Join**: En *left join delvis* returnerar endast värden från den vänstra tabellen när värdena i nyckelkolumnerna som matchar.  

1. För alternativet **Tänk kopplad tabell direkt nyckelkolumner**:

    * Välj det här alternativet om du vill visa alla nycklarna från båda tabeller.
    * Avmarkera att endast returnera nyckelkolumnerna från den vänstra indataporten.

1. Kör experimentet, eller väljer du modulen koppla samman Data och valt **kör valda** att utföra kopplingen.

1. Om du vill visa resultatet genom att högerklicka på den **ansluta Data** > **resultat datauppsättning** > **visualisera**.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 