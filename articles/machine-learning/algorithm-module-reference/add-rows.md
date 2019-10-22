---
title: 'Lägg till rader: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Lägg till rader i Azure Machine Learning-tjänsten för att sammanfoga två data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c8d8b6a873ee17d1658b0cb47de830848b215b89
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693237"
---
# <a name="add-rows-module"></a>Lägg till Rows-modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att sammanfoga två data uppsättningar. I sammanfogningen läggs raderna i den andra data uppsättningen till i slutet av den första data mängden.  
  
Sammanfogning av rader är användbart i scenarier som dessa:  
  
+ Du har genererat en serie utvärderings statistik och vill kombinera dem till en tabell för enklare rapportering.  
  
+ Du har arbetat med olika data uppsättningar och du vill kombinera data uppsättningarna för att skapa en slutgiltig data uppsättning.  

## <a name="how-to-use-add-rows"></a>Så här använder du Lägg till rader  

Om du vill sammanfoga rader från två data uppsättningar måste raderna ha exakt samma schema. Det innebär att samma antal kolumner och samma typ av data i kolumnerna.

1.  Dra modulen **Lägg till rader** till din pipeline. du hittar den under **datatransformering**i kategorin för **manipulering** .

2. Anslut data uppsättningarna till de två portarna för indata. Den data uppsättning som du vill lägga till ska vara ansluten till den andra (högra) porten. 
  
3.  Köra en pipeline. Antalet rader i data uppsättningen för utdata ska vara lika med summan av raderna i båda data uppsättningarna.

    Om du lägger till samma data uppsättning i båda indata för modulen **Lägg till rader** dupliceras data uppsättningen. 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 