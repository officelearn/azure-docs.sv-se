---
title: 'Add Rows: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att använda modulen Lägg till rader i Azure Machine Learning-tjänsten för att sammanfoga två datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028762"
---
# <a name="add-rows-module"></a>Lägg till rader modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Du kan använda den här modulen för att sammanfoga två datauppsättningar. I sammanfoga raderna i den andra datauppsättningen har lagts till i slutet av den första datauppsättningen.  
  
Sammanslagning av rader är användbart i scenarier som följande:  
  
+ Du har genererat en serie av utvärdering statistik och du vill kombinera dem i en tabell för enklare rapportering.  
  
+ Du har arbetat med olika datauppsättningar och du vill kombinera datauppsättningar för att skapa en slutlig datauppsättningen.  

## <a name="how-to-use-add-rows"></a>Hur du använder Lägg till rader  

Om du vill sammanfoga rader från två datauppsättningar måste rader ha exakt samma schema. Det innebär att samma antal kolumner och samma typ av data i kolumnerna.

1.  Dra den **Lägg till rader** -modulen till ditt experiment, kan du hitta den under **Dataomvandling**i den **ändra** kategori.

2. Anslut datauppsättningarna till två indataportar. Den datauppsättning som du vill lägga till bör anslutas till den andra (höger). 
  
3.  Kör experimentet. Antalet rader i datauppsättningen för utdata ska vara lika med summan av raderna i båda indatauppsättningar.

    Om du lägger till samma datamängd både indata för den **Lägg till rader** modulen datauppsättningen är en dubblett. 

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 