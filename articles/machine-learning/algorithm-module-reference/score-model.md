---
title: 'Poängmodell: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder poängmodellmodulen i Azure Machine Learning för att generera förutsägelser med hjälp av en tränad klassificering eller regressionsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364176"
---
# <a name="score-model-module"></a>Modulen Poängsätta modell

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att generera förutsägelser med hjälp av en tränad klassificering eller regressionsmodell.

## <a name="how-to-use"></a>Hur du ska använda detta

1. Lägg till **modulen Poängmodell** i pipelinen.

2. Koppla en tränad modell och en datauppsättning som innehåller nya indata. 

    Data bör vara i ett format som är kompatibelt med den typ av tränad modell du använder. Schemat för indatauppsättningen bör också i allmänhet matcha schemat för de data som används för att träna modellen.

3. Skicka pipelinen.

## <a name="results"></a>Resultat

När du har genererat en uppsättning poäng med [poängmodell:](./score-model.md)

+ Om du vill generera en uppsättning mått som används för att utvärdera modellens noggrannhet (prestanda) kan du ansluta den poängsatta datauppsättningen till [Utvärdera modell](./evaluate-model.md), 
+ Högerklicka på modulen och välj **Visualisera** om du vill se ett exempel på resultatet.
<!-- + To Save the results to a dataset. -->

Poängen, eller det förväntade värdet, kan vara i många olika format, beroende på modell och indata:

- För klassificeringsmodeller ger [poängmodell](./score-model.md) ut ett förväntat värde för klassen samt sannolikheten för det förväntade värdet.
- För regressionsmodeller genererar [poängmodellen](./score-model.md) bara det förväntade numeriska värdet.


## <a name="publish-scores-as-a-web-service"></a>Publicera poäng som en webbtjänst

En vanlig användning av poängsättning är att returnera utdata som en del av en prediktiv webbtjänst. Mer information finns i [den här självstudien](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) om hur du distribuerar en slutpunkt i realtid baserat på en pipeline i Azure Machine Learning-designern.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 