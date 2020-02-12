---
title: Felsöka din modell
titleSuffix: ML Studio (classic) - Azure
description: Så här felsöker du fel som uppstår i modulerna träna modell och Poäng modell i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 320eba16511036df77dcdbb7ddb628eaa34b2450
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153561"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Felsöka din modell i Azure Machine Learning Studio (klassisk)

När du kör en modell kan du stöta på följande fel:

* modulen [träna modell][train-model] ger ett fel 
* modulen [Poäng modell][score-model] ger felaktiga resultat 

I den här artikeln förklaras möjliga orsaker till dessa fel.


## <a name="train-model-module-produces-an-error"></a>Träna modell genererar ett fel

![image1](./media/debug-models/train_model-1.png)

Modulen [träna modell][train-model] förväntar sig två indata:

1. Typ av Machine Learning-modell från den samling av modeller som tillhandahålls av Azure Machine Learning Studio (klassisk).
2. Tränings data med en angiven etikett kolumn som anger vilken variabel som ska förutsägas (de andra kolumnerna antas vara funktioner).

Den här modulen kan producera ett fel i följande fall:

1. Kolumnen etikett har angetts felaktigt. Detta kan inträffa om mer än en kolumn har markerats som etikett eller en felaktig kolumnindex har valts. Till exempel gäller det andra fallet om ett kolumn index på 30 används med en indata-datamängd som bara har 25 kolumner.

2. Datauppsättningen innehåller inte några kolumner i funktionen. Till exempel om datauppsättningen för indata har endast en kolumn som har markerats som kolumnen etikett, blir det inga funktioner med att bygga modellen. I det här fallet genererar modulen [träna modell][train-model] ett fel.

3. Datauppsättningen för indata (funktioner eller etikett) innehåller Infinity som ett-värde.

## <a name="score-model-module-produces-incorrect-results"></a>Modulen poängsätta modell ger felaktiga resultat

![image2](./media/debug-models/train_test-2.png)

I ett typiskt övnings-och testnings experiment för övervakad inlärning delar modulen [dela data][split] upp den ursprungliga data uppsättningen i två delar: en del används för att träna modellen och en del används för att visa hur väl den tränade modellen presterar. Den tränade modellen används sedan för att bedöma testdata, varefter resultaten utvärderas för att fastställa korrektheten i modellen.

Modulen [Poäng modell][score-model] kräver två indata:

1. En tränad modell utmatning från modulen [träna modell][train-model] .
2. En arbetsflödesbaserad datauppsättning som skiljer sig från den datauppsättning som används för att träna modellen.

Det är möjligt att även om experimentet lyckas, ger modulen [Poäng modell][score-model] felaktiga resultat. Det kan hända att det här problemet uppstår i flera scenarier:

1. Om den angivna etiketten är kategoriska och en Regressions modell tränas på data, skapas Felaktiga utdata av modulen [Poäng modell][score-model] . Det beror på att regression kräver en kontinuerlig svar-variabel. I så fall skulle det vara lämpligare att använda en modell för klassificering. 

2. Om en klassificering tränas på en datauppsättning med flyttal i kolumnen etikett, på samma sätt kan det ge oönskade resultat. Detta beror på att klassificeringen kräver en diskret variabel som bara tillåter värden som sträcker sig över en begränsad och liten uppsättning klasser.

3. Om poängsättnings data uppsättningen inte innehåller alla funktioner som används för att träna modellen, genererar [Poäng modellen][score-model] ett fel.

4. Om en rad i poängsättnings data uppsättningen innehåller ett saknat värde eller ett oändligt värde för någon av dess funktioner, genererar inte [Poäng modellen][score-model] några utdata som motsvarar den raden.

5. [Poäng modellen][score-model] kan producera identiska utdata för alla rader i poängsättnings data uppsättningen. Detta kan inträffa, till exempel vid klassificering med hjälp av beslut skogar om det minsta antalet prover per lövnod väljs vara fler än antalet utbildning exempel tillgängliga.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

