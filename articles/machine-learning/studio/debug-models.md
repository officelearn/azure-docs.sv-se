---
title: Felsöka din modell
titleSuffix: Azure Machine Learning Studio
description: Så här felsöker du fel som genereras av modulerna Träningsmodell och Poängmodell i Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 9c505262030e5b5aa13b8d221cf1e39c4a9c7833
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090771"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Felsöka din modell i Azure Machine Learning Studio

När du kör en modell kan stöta du på följande fel:

* den [Träningsmodell] [ train-model] modulen genererar ett fel 
* den [Poängmodell] [ score-model] modulen ger felaktiga resultat 

Den här artikeln beskrivs möjliga orsaker till att de här felen.


## <a name="train-model-module-produces-an-error"></a>Träna modell genererar ett fel

![image1](./media/debug-models/train_model-1.png)

Den [Träningsmodell] [ train-model] modulen förväntar sig två indata:

1. Typ av machine learning-modell från samlingen av modeller som tillhandahålls av Azure Machine Learning Studio.
2. Träningsdata med en angiven etikett-kolumn som anger variabeln för att förutsäga (de andra kolumnerna antas vara funktioner).

Den här modulen kan producera ett fel i följande fall:

1. Kolumnen etikett har angetts felaktigt. Detta kan inträffa om mer än en kolumn har markerats som etikett eller en felaktig kolumnindex har valts. Det andra fallet skulle till exempel gälla om ett index på 30 används med en indatauppsättning som har bara 25 kolumner.

2. Datauppsättningen innehåller inte några kolumner i funktionen. Till exempel om datauppsättningen för indata har endast en kolumn som har markerats som kolumnen etikett, blir det inga funktioner med att bygga modellen. I det här fallet den [Träningsmodell] [ train-model] modulen genererar ett fel.

3. Datauppsättningen för indata (funktioner eller etikett) innehåller Infinity som ett-värde.

## <a name="score-model-module-produces-incorrect-results"></a>Modulen poängsätta modell ger felaktiga resultat

![image2](./media/debug-models/train_test-2.png)

I en typisk träning och testning experiment för övervakad inlärning den [dela Data] [ split] modulen dividerar den ursprungliga datauppsättningen i två delar: en del används för att träna modellen och en del används för att bedöma hur bra utför den tränade modellen. Den tränade modellen används sedan för att bedöma testdata, varefter resultaten utvärderas för att fastställa korrektheten i modellen.

Den [Poängmodell] [ score-model] modulen kräver två indata:

1. En tränad utdata från den [Träningsmodell] [ train-model] modulen.
2. En arbetsflödesbaserad datauppsättning som skiljer sig från den datauppsättning som används för att träna modellen.

Det är möjligt att även om försöket lyckas, den [Poängmodell] [ score-model] modulen ger felaktiga resultat. Flera scenarier kan orsaka det här problemet att detta inträffar:

1. Om den angivna etiketten är kategoriska och en regressionsmodellen ska tränas på data, felaktig utdata skulle genereras av den [Poängmodell] [ score-model] modulen. Det beror på att regression kräver en kontinuerlig svar-variabel. I så fall skulle det vara lämpligare att använda en modell för klassificering. 

2. Om en klassificering tränas på en datauppsättning med flyttal i kolumnen etikett, på samma sätt kan det ge oönskade resultat. Det beror på att klassificering kräver en diskret svar-variabel som endast tillåter värden som intervall för över en begränsad och små, uppsättning klasser.

3. Om bedömnings datauppsättningen inte innehåller alla funktioner som används för att öva med modellen, de [Poängmodell] [ score-model] genererar ett fel.

4. Om en rad i bedömnings datauppsättningen innehåller saknade värden eller ett oändligt värde för någon av dess funktioner i [Poängmodell] [ score-model] inte producerar några utdata som motsvarar den raden.

5. Den [Poängmodell] [ score-model] kan ge identiska utdata för alla rader i bedömnings datauppsättningen. Detta kan inträffa, till exempel vid klassificering med hjälp av beslut skogar om det minsta antalet prover per lövnod väljs vara fler än antalet utbildning exempel tillgängliga.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

