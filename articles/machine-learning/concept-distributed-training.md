---
title: Vad är distribuerad utbildning?
titleSuffix: Azure Machine Learning
description: Läs mer om distribuerad utbildning och hur Azure Machine Learning stöder den.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385550"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Distribuerad utbildning med Azure Machine Learning

I den här artikeln får du lära dig mer om distribuerad utbildning och hur Azure Machine Learning stöder den för modeller med djupinlärning. 

I distribuerad utbildning delas arbetsbelastningen för att träna en modell upp och delas mellan flera miniprocessorer, så kallade arbetsnoder. Dessa arbetarnoder arbetar parallellt för att påskynda modellutbildningen. Distribuerad utbildning kan användas för traditionella ML-modeller, men är bättre lämpad för beräknings- och tidsintensiva uppgifter, som [djupinlärning](concept-deep-learning-vs-machine-learning.md) för träning av djupa neurala nätverk.

## <a name="deep-learning-and-distributed-training"></a>Djupinlärning och distribuerad utbildning 

Det finns två huvudtyper av distribuerad utbildning: [dataparallellism](#data-parallelism) och [modellparallellism](#model-parallelism). För distribuerad utbildning på djupinlärningsmodeller stöder [Azure Machine Learning SDK i Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) integrationer med populära ramverk, PyTorch och TensorFlow. Båda ramarna använder dataparallellism för distribuerad utbildning och kan utnyttja [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) för att optimera beräkningshastigheter. 

* [Distribuerad utbildning med PyTorch](how-to-train-pytorch.md#distributed-training)

* [Distribuerad utbildning med TensorFlow](how-to-train-tensorflow.md#distributed-training)

För ML-modeller som inte kräver distribuerad utbildning läser du [träna modeller med Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) för olika sätt att träna modeller med Python SDK.

## <a name="data-parallelism"></a>Data parallellism

Data parallellism är det lättast att genomföra av de två distribuerade utbildningsmetoder, och är tillräcklig för de flesta användningsfall.

I den här metoden är data indelade i partitioner, där antalet partitioner är lika med det totala antalet tillgängliga noder, i beräkningsklustret. Modellen kopieras i var och en av dessa arbetsnoder och varje arbetare arbetar på sin egen delmängd av data. Tänk på att varje nod måste ha kapacitet att stödja den modell som tränas, det vill vara modellen måste helt passa på varje nod.

Varje nod beräknar självständigt felen mellan dess förutsägelser för sina träningsprover och de märkta utgångarna. Varje nod uppdaterar i sin modell baserat på felen och måste meddela alla sina ändringar till de andra noderna för att uppdatera motsvarande modeller. Det innebär att arbetarnoderna måste synkronisera modellparametrarna, eller övertoningarna, i slutet av batchberäkningen för att säkerställa att de tränar en konsekvent modell. 

## <a name="model-parallelism"></a>Modell parallellism

I modellparallellism, även känd som nätverksparallellism, är modellen segmenterad i olika delar som kan köras samtidigt i olika noder, och var och en kommer att köras på samma data. Skalbarheten för den här metoden beror på graden av aktivitetssamallering av algoritmen, och det är mer komplext att implementera än dataparallellism. 

I modellparallellism behöver arbetarnoder bara synkronisera de delade parametrarna, vanligtvis en gång för varje steg framåt eller bakåt. Dessutom är större modeller inte ett problem eftersom varje nod fungerar på ett underavsnitt av modellen på samma träningsdata.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [konfigurerar träningsmiljöer](how-to-set-up-training-targets.md) med Python SDK.
* Ett tekniskt exempel finns i [referensarkitekturscenariot](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Tåg ML modeller med TensorFlow](how-to-train-tensorflow.md).
* [Tåg ML modeller med PyTorch](how-to-train-pytorch.md). 