---
title: Felsöka din modell
titleSuffix: ML Studio (classic) - Azure
description: Så här felar du fel som produceras av moduler för tågmodell och poängmodell i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218103"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Felsöka din modell i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

När du kör en modell kan du stöta på följande fel:

* [Tågmodellmodulen][train-model] ger ett fel 
* Modulen [Poängmodell][score-model] ger felaktiga resultat 

I den här artikeln förklaras potentiella orsaker till dessa fel.


## <a name="train-model-module-produces-an-error"></a>Train Model Module ger ett fel

![image1](./media/debug-models/train_model-1.png)

[Tågmodellmodulen][train-model] förväntar sig två ingångar:

1. Typ av maskininlärningsmodell från samlingen modeller som tillhandahålls av Azure Machine Learning Studio (klassisk).
2. Träningsdata med en angiven etikettkolumn som anger variabeln att förutsäga (de andra kolumnerna antas vara funktioner).

Den här modulen kan skapa ett fel i följande fall:

1. Kolumnen Etikett har angetts felaktigt. Detta kan inträffa om antingen mer än en kolumn är markerad som etikett eller om ett felaktigt kolumnindex är markerat. Det andra fallet skulle till exempel gälla om ett kolumnindex på 30 används med en indatauppsättning som bara har 25 kolumner.

2. Datauppsättningen innehåller inga funktionskolumner. Om indatauppsättningen till exempel bara har en kolumn, som är markerad som kolumnen Etikett, finns det inga funktioner som modellen kan byggas med. I det här fallet skapar modulen [Tågmodell][train-model] ett fel.

3. Indatauppsättningen (Funktioner eller Etikett) innehåller Oändlighet som ett värde.

## <a name="score-model-module-produces-incorrect-results"></a>Poängmodellmodul ger felaktiga resultat

![image2](./media/debug-models/train_test-2.png)

I ett typiskt utbildnings-/testexperiment för övervakad inlärning delar [modulen Dela data][split] den ursprungliga datauppsättningen i två delar: en del används för att träna modellen och en del används för att få hur bra den tränade modellen presterar. Den tränade modellen används sedan för att poängsätta testdata, varefter resultaten utvärderas för att bestämma modellens noggrannhet.

Modulen [Poängmodell][score-model] kräver två ingångar:

1. En tränad modellutgång från modulen [Tågmodell.][train-model]
2. En bedömningsdatauppsättning som skiljer sig från den datauppsättning som används för att träna modellen.

Det är möjligt att även om experimentet lyckas ger modulen [Poängmodell][score-model] felaktiga resultat. Flera scenarier kan orsaka att problemet inträffar:

1. Om den angivna etiketten är kategorisk och en regressionsmodell tränas på data, skapas en felaktig utdata av modulen [Poängmodell.][score-model] Detta beror på att regression kräver en kontinuerlig svarsvariabel. I detta fall skulle det vara mer lämpligt att använda en klassificeringsmodell. 

2. På samma sätt, om en klassificeringsmodell tränas på en datauppsättning med flyttalsnummer i kolumnen Etikett, kan det ge oönskade resultat. Detta beror på att klassificeringen kräver en diskret svarsvariabel som bara tillåter värden som sträcker sig över en begränsad och liten uppsättning klasser.

3. Om bedömningsdatauppsättningen inte innehåller alla funktioner som används för att träna modellen, skapar [poängmodellen][score-model] ett fel.

4. Om en rad i bedömningsdatauppsättningen innehåller ett värde som saknas eller ett oändligt värde för någon av dess funktioner, ger [poängmodellen][score-model] inte några utdata som motsvarar den raden.

5. [Poängmodellen][score-model] kan ge identiska utdata för alla rader i bedömningsdatauppsättningen. Detta kan till exempel inträffa när man försöker klassificera med hjälp av Beslutsskogar om det minsta antalet prover per bladnod väljs för att vara mer än antalet tillgängliga utbildningsexempel.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

