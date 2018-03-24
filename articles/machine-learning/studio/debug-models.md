---
title: Felsöka din modell i Azure Machine Learning | Microsoft Docs
description: Så här felsöker du fel som genereras av modulerna Träningsmodell och Poängmodell i Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.openlocfilehash: bca5ebbda2d46270cc0a996df5fc46f201931343
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="debug-your-model-in-azure-machine-learning"></a>Felsöka din modell i Azure Machine Learning

Den här artikeln beskrivs möjliga orsaker till något av följande två fel kan uppstå när du kör en modell:

* den [Träningsmodell] [ train-model] modulen genererar ett fel 
* den [Poängmodell] [ score-model] modulen ger felaktiga resultat 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Modulen för Train-modell genererar ett fel

![image1](./media/debug-models/train_model-1.png)

Den [Träningsmodell] [ train-model] modulen förväntar två indata:

1. Typ av maskininlärningsmodell för insamling av modeller som tillhandahålls av Azure Machine Learning.
2. Utbildning data med en angiven etikett-kolumn som anger variabeln för att förutsäga (de andra kolumnerna antas vara funktioner).

Den här modulen kan producera ett fel i följande fall:

1. Kolumnen etikett har angetts felaktigt. Detta kan inträffa om mer än en kolumn har markerats som etikett eller en felaktig kolumnindex är markerad. Det andra fallet skulle till exempel gälla om ett index på 30 används med en inkommande datamängd som har bara 25 kolumner.

2. Dataset innehåller inte några kolumner i funktionen. Till exempel om den inkommande datamängden har endast en kolumn som har markerats som kolumnen etikett, är det inga funktioner som att skapa modellen. I det här fallet den [Träningsmodell] [ train-model] modulen genererar ett fel.

3. Inkommande datauppsättningen (funktioner eller etikett) innehåller oändligt som ett-värde.

## <a name="score-model-module-produces-incorrect-results"></a>Modulen poängsätta modell ger felaktiga resultat

![image2](./media/debug-models/train_test-2.png)

I en typisk utbildning/testning experiment för övervakad inlärning av [dela Data] [ split] modulen delas den ursprungliga datauppsättningen i två delar: en del används för att träna modellen och en del används för att poängsätta hur väl utför den tränade modellen. Den tränade modellen används sedan för att samla in testdata, efter vilken resultaten utvärderas för att fastställa korrektheten i modellen.

Den [Poängmodell] [ score-model] modulen kräver två indata:

1. En tränad modell utdata från den [Träningsmodell] [ train-model] modul.
2. En bedömningsprofil datamängd som skiljer sig från den datamängd som används för att träna modellen.

Det är möjligt att även om försöket lyckas den [Poängmodell] [ score-model] modulen ger felaktiga resultat. Flera scenarier kan orsaka detta ske:

1. Om den angivna etiketten är kategoriska och en regressionsmodell tränats på data, felaktig utdata skulle genereras av den [Poängmodell] [ score-model] modul. Det beror på att regression kräver en kontinuerlig svar-variabel. I så fall skulle vara mer lämpligt att använda en modell för klassificering. 

2. På samma sätt kan kan en klassificering modell tränas på en datamängd med flyttal i kolumnen etikett, det ge oönskade resultat. Det beror på att klassificering kräver en diskret svar-variabel som endast tillåter att värden intervallet över en begränsad och vanligtvis något liten uppsättning klasser.

3. Om bedömningsprofil datamängden inte innehåller alla funktioner som används för att träna modellen, den [Poängmodell] [ score-model] genererar ett fel.

4. Om en rad i bedömningsprofil datamängden innehåller ett saknat värde eller ett oändligt värde för någon av dess funktioner i [Poängmodell] [ score-model] kommer inte producerar några utdata som motsvarar den raden.

5. Den [Poängmodell] [ score-model] kan ge identiska utdata för alla rader i dataset bedömningsprofil. Detta kan exempelvis inträffa när försök klassificering med beslut skogar om det minsta antalet prov per lövnod väljs vara fler än antalet utbildning exempel tillgängliga.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

