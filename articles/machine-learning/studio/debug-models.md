---
title: Felsöka din modell
titleSuffix: ML Studio (classic) Azure
description: Så här felsöker du fel som uppstår i modulerna träna modell och Poäng modell i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 0ab7f041da63731706742547a53df47462bc584a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619447"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Felsöka din modell i Azure Machine Learning Studio (klassisk)

När du kör en modell kan du stöta på följande fel:

* modulen [träna modell][train-model] ger ett fel 
* modulen [Poäng modell][score-model] ger felaktiga resultat 

I den här artikeln förklaras möjliga orsaker till dessa fel.


## <a name="train-model-module-produces-an-error"></a>Träna modell module genererar ett fel

![image1](./media/debug-models/train_model-1.png)

Modulen [träna modell][train-model] förväntar sig två indata:

1. Typ av Machine Learning-modell från den samling av modeller som tillhandahålls av Azure Machine Learning Studio (klassisk).
2. Tränings data med en angiven etikett kolumn som anger vilken variabel som ska förutsägas (de andra kolumnerna antas vara funktioner).

Den här modulen kan generera ett fel i följande fall:

1. Etikett kolumnen har angetts felaktigt. Detta kan inträffa om antingen fler än en kolumn har marker ATS som etikett eller felaktigt kolumn index har valts. Till exempel gäller det andra fallet om ett kolumn index på 30 används med en indata-datamängd som bara har 25 kolumner.

2. Data uppsättningen innehåller inte några funktions kolumner. Om till exempel indata-datauppsättningen bara har en kolumn, som har marker ATS som etikett-kolumnen, finns det inga funktioner för att bygga modellen. I det här fallet genererar modulen [träna modell][train-model] ett fel.

3. Data uppsättningen för indata (funktioner eller etikett) innehåller oändligt som ett värde.

## <a name="score-model-module-produces-incorrect-results"></a>Score modell-modulen ger felaktiga resultat

![image2](./media/debug-models/train_test-2.png)

I ett typiskt övnings-och testnings experiment för övervakad inlärning delar modulen [dela data][split] upp den ursprungliga data uppsättningen i två delar: en del används för att träna modellen och en del används för att visa hur väl den tränade modellen presterar. Den tränade modellen används sedan för att Visa test data, varefter resultaten utvärderas för att fastställa modellens noggrannhet.

Modulen [Poäng modell][score-model] kräver två indata:

1. En tränad modell utmatning från modulen [träna modell][train-model] .
2. En poäng data uppsättning som skiljer sig från den data uppsättning som används för att träna modellen.

Det är möjligt att även om experimentet lyckas, ger modulen [Poäng modell][score-model] felaktiga resultat. Det kan hända att det här problemet uppstår i flera scenarier:

1. Om den angivna etiketten är kategoriska och en Regressions modell tränas på data, skapas Felaktiga utdata av modulen [Poäng modell][score-model] . Detta beror på att regression kräver en variabel med kontinuerlig respons. I det här fallet skulle det vara mer lämpligt att använda en klassificerings modell. 

2. På samma sätt kan det uppstå oönskade resultat om en klassificerings modell tränas på en data uppsättning med flytt ALS nummer i kolumnen etikett. Detta beror på att klassificeringen kräver en diskret variabel som bara tillåter värden som sträcker sig över en begränsad och liten uppsättning klasser.

3. Om poängsättnings data uppsättningen inte innehåller alla funktioner som används för att träna modellen, genererar [Poäng modellen][score-model] ett fel.

4. Om en rad i poängsättnings data uppsättningen innehåller ett saknat värde eller ett oändligt värde för någon av dess funktioner, genererar inte [Poäng modellen][score-model] några utdata som motsvarar den raden.

5. [Poäng modellen][score-model] kan producera identiska utdata för alla rader i poängsättnings data uppsättningen. Detta kan inträffa, till exempel vid försök att klassificera med hjälp av besluts skogar om det minsta antalet sampel per lövnod väljs som ska vara mer än antalet tillgängliga utbildnings exempel.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

