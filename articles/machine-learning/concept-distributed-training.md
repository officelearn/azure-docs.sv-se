---
title: Vad är distribuerad utbildning?
titleSuffix: Azure Machine Learning
description: Lär dig mer om distribuerad utbildning och hur Azure Machine Learning stöder det.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 62edee6a882191551ce2409646ea8b617576c059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651150"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Distribuerad utbildning med Azure Machine Learning

I den här artikeln lär du dig om distribuerad utbildning och hur Azure Machine Learning stöder den för djup inlärnings modeller. 

I distribuerad utbildning är arbets belastningen för att träna en modell att delas upp och delas mellan flera olika mini-processorer, som kallas arbetsnoder. Dessa arbetsnoder fungerar parallellt för att påskynda modell träningen. Distribuerad utbildning kan användas för traditionella ML-modeller, men passar bättre för beräknings-och tids krävande uppgifter, t. ex. [djup inlärning](concept-deep-learning-vs-machine-learning.md) för inlärnings djup neurala nätverk. 

## <a name="deep-learning-and-distributed-training"></a>Djup inlärning och distribuerad utbildning 

Det finns två huvudsakliga typer av distribuerade utbildningar: [data parallellitet](#data-parallelism) och [modell parallellitet](#model-parallelism). För distribuerad utbildning på djup inlärnings modeller stöder [Azure Machine Learning SDK i python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) integreringar med populära ramverk, PyTorch och TensorFlow. Båda ramverken använder data parallellitet för distribuerad utbildning och kan utnyttja [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) för att optimera beräknings hastigheter. 

* [Distribuerad utbildning med PyTorch](how-to-train-pytorch.md#distributed-training)

* [Distribuerad utbildning med TensorFlow](how-to-train-tensorflow.md#distributed-training)

För ML-modeller som inte kräver distribuerad utbildning, se [träna modeller med Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) för olika sätt att träna modeller med python SDK.

## <a name="data-parallelism"></a>Data parallellitet

Data Parallel är det enklaste sättet att implementera de två metoderna för distribuerad utbildning och är tillräckligt för de flesta användnings fall.

I den här metoden är data indelade i partitioner, där antalet partitioner motsvarar det totala antalet tillgängliga noder, i beräknings klustret. Modellen kopieras i var och en av dessa arbetsnoder och varje anställd arbetar på sin egen del av data. Tänk på att varje nod måste ha kapacitet att stödja den modell som har tränats, vilket är att modellen helt måste anpassas på varje nod. Följande diagram innehåller en visuell demonstration av den här metoden.

![Data-parallelation-Concept-diagram](./media/concept-distributed-training/distributed-training.svg)

Varje nod beräknar oberoende fel mellan sina förutsägelser för dess utbildnings exempel och etiketterade utdata. I sin tur uppdaterar varje nod sin modell baserat på felen och måste kommunicera alla ändringar i de andra noderna för att uppdatera deras motsvarande modeller. Det innebär att arbetsnoderna måste synkronisera modell parametrarna eller toningarna i slutet av batch-beräkningen för att se till att de tränar en konsekvent modell. 

## <a name="model-parallelism"></a>Modell parallellitet

I modell parallellitet, som även kallas nätverks parallellitet, segmenteras modellen i olika delar som kan köras samtidigt på olika noder, och var och en kommer att köras på samma data. Skalbarheten för den här metoden beror på parallellisering-algoritmens omfattning och är mer komplex att implementera än data parallellt. 

I modell parallellt behöver arbetsnoder bara synkronisera de delade parametrarna, vanligt vis en gång för varje steg framåt eller bakåt-spridning. Dessutom är större modeller inte ett problem eftersom varje nod fungerar på ett underavsnitt av modellen på samma tränings data.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använder beräknings mål för modell utbildning](how-to-set-up-training-targets.md) med python SDK.
* Ett tekniskt exempel finns i [referens arkitektur scenariot](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Träna ml-modeller med TensorFlow](how-to-train-tensorflow.md).
* [Träna ml-modeller med PyTorch](how-to-train-pytorch.md). 