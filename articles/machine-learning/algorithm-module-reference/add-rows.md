---
title: 'Lägga till rader: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Lägg till rader i Azure Machine Learning för att sammanfoga två datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477739"
---
# <a name="add-rows-module"></a>Lägg till modul för rader

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att sammanfoga två datauppsättningar. I sammanfogning läggs raderna för den andra datauppsättningen till i slutet av den första datauppsättningen.  
  
Sammanfogning av rader är användbart i scenarier som dessa:  
  
+ Du har genererat en serie utvärderingsstatistik och du vill kombinera dem i en tabell för enklare rapportering.  
  
+ Du har arbetat med olika datauppsättningar och du vill kombinera datauppsättningarna för att skapa en slutlig datauppsättning.  

## <a name="how-to-use-add-rows"></a>Så här använder du Lägg till rader  

Om du vill sammanfoga rader från två datauppsättningar måste raderna ha exakt samma schema. Det innebär samma antal kolumner och samma typ av data i kolumnerna.

1.  Dra modulen **Lägg till rader** till pipelinen, du hittar den under **Dataomvandling**.

2. Anslut datauppsättningarna till de två indataportarna. Den datauppsättning som du vill lägga till ska vara ansluten till den andra (högra) porten. 
  
3.  Skicka pipelinen. Antalet rader i utdatauppsättningen bör vara lika med summan av raderna för båda indatauppsättningarna.

    Om du lägger till samma datauppsättning i båda ingångarna i modulen **Lägg till rader** dupliceras datauppsättningen. 

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 