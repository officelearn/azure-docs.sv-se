---
title: 'Poäng modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Poäng modell i Azure Machine Learning för att generera förutsägelser med hjälp av en tränad klassificerings-eller Regressions modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 27838aae1d829598bed44a6c16f7a1d38a95b727
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314104"
---
# <a name="score-model-module"></a>Modulen Poängsätta modell

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att generera förutsägelser med hjälp av en utbildad klassificerings-eller Regressions modell.

## <a name="how-to-use"></a>Hur du ska använda detta

1. Lägg till modulen **Poäng modell** i din pipeline.

2. Koppla en utbildad modell och en data uppsättning som innehåller nya indata. 

    Data ska vara i ett format som är kompatibelt med den typ av tränad modell som du använder. Schemat för data uppsättningen för indata ska också ofta matcha schemat för de data som används för att träna modellen.

3. Köra en pipeline.

## <a name="results"></a>Resultat

När du har genererat en uppsättning poäng med [Poäng modell](./score-model.md):

+ För att generera en uppsättning mått som används för att utvärdera modellens precision (prestanda).  Du kan ansluta den returnerade data uppsättningen för att [utvärdera modellen](./evaluate-model.md), 
+ Högerklicka på modulen och välj **visualisera** för att se ett exempel på resultatet.
+ Spara resultatet till en data uppsättning.

Poängen eller det förväntade värdet kan ha många olika format, beroende på modell och indata:

- För klassificerings modeller utvärderar [Poäng modellen](./score-model.md) ett förutsägande värde för klassen, samt sannolikheten för det förväntade värdet.
- För Regressions modeller genererar [Poäng modellen](./score-model.md) bara det förväntade numeriska värdet.
- För bild klassificerings modeller kan poängen vara objekt klassen i bilden, eller ett booleskt värde som anger om en viss funktion hittades.

## <a name="publish-scores-as-a-web-service"></a>Publicera resultat som en webb tjänst

En vanlig användning av poängsättning är att returnera utdata som en del av en förutsägbar webb tjänst. Mer information finns i den här självstudien om hur du skapar en webb tjänst baserat på en pipeline i Azure Machine Learning:

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 