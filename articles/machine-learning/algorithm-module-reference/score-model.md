---
title: 'Poängsätta modell: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen poängsätta modell i Azure Machine Learning-tjänsten för att generera förutsägelser med en tränad klassificerings- eller regressionsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029272"
---
# <a name="score-model-module"></a>Modulen Poängsätta modell

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att generera förutsägelser med en tränad klassificerings- eller regressionsmodell modell.

## <a name="how-to-use"></a>Hur du ska använda detta

1. Lägg till den **Poängmodell** modulen i experimentet.

2. Koppla en tränad modell och en datauppsättning som innehåller nya indata. 

    Data ska vara i ett format som är kompatibel med typen av tränade modellen som du använder. Schemat för datauppsättningen för indata ska också i allmänhet matcha schemat för data som används för att träna modellen.

3. Kör experimentet.

## <a name="results"></a>Resultat

När du har genererat en uppsättning resultat med hjälp av [Poängmodell](./score-model.md):

+ Att generera en uppsättning mått som används för att utvärdera modellens precision (prestanda).  Du kan ansluta poängsatta datauppsättningen till [utvärdera modell](./evaluate-model.md), 
+ Högerklicka på modulen och välj **visualisera** att se ett exempel på resultatet.
+ Spara resultaten i en datauppsättning.

Poäng eller förutsagt värde kan vara i olika format, beroende på modell- och dina indata:

- För klassificering modeller [Poängmodell](./score-model.md) matar ut ett prognostiserat värde för klassen samt sannolikheten för förutsägelsevärdet.
- För regressionsmodeller, [Poängmodell](./score-model.md) genererar bara det förväntade numeriska värdet.
- För avbildningsklassificeringsmodeller vara poängen klassen på objekt i bilden eller ett booleskt värde som anger om en viss funktion hittades.

## <a name="publish-scores-as-a-web-service"></a>Publicera massor som en webbtjänst

Ett vanligt användningsområde för bedömning är att returnerar utdata som en del av en förutsägbar webbtjänst. Mer information finns i den här självstudiekursen om hur du skapar en webbtjänst som är baserat på ett experiment i Azure Machine Learning:


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 