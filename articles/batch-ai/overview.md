---
title: Azure Batch AI-tjänsten – AI-träning | Microsoft Docs
description: Lär dig mer om hur du använder den hanterade Azure Batch AI-tjänsten för att träna artificiell intelligens (AI) och andra maskininlärningsmodeller på kluster med GPU:er och CPU:er.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052707"
---
# <a name="what-is-azure-batch-ai"></a>Vad är Azure Batch AI?

Azure Batch AI är en hanterad tjänst som hjälper datatekniker och AI-forskare att träna och testa maskininlärnings- och AI-modeller i skala i Azure – utan att behöva hantera komplex infrastruktur. Beskriv beräkningsresurser, jobben som du vill köra, var du vill lagra modellens indata och utdata, så tar Batch AI hand om resten.

Du kan antingen använda Batch AI fristående eller för att utföra modellträning som en del av ett större utvecklingsarbetsflöde:

* Använd Batch AI fristående för att träna, testa, och batchbedöma modeller för maskininlärning och AI på kluster av [GPU:er](../virtual-machines/linux/sizes-gpu.md) eller CPU:er. 

* Rikta dig mot ett Batch AI-kluster i ett arbetsflöde med [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) eller andra [plattformsverktyg för Azure AI](https://azure.microsoft.com/overview/ai-platform/). Azure ML ger bra förutsättningar för förberedelse av data, experimentering och jobbhistorik. Azure ML kan även paketera en tränad modell i en container och distribuera en modell för inferens eller IoT-enheter.  

## <a name="train-machine-learning-and-ai-models"></a>Träna modeller för maskininlärning och AI

Använd Batch AI för att träna modeller för maskininlärning, neurala djupnätverk (djupinlärning) och andra AI-lösningar. Batch AI har inbyggt stöd för populära bibliotek och ramverk för AI med öppen källkod, inklusive [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) och [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK).

När du har identifierat ditt problemområde och förberett dina data arbetar du interaktivt med Batch AI för att testa modellidéer. När du sedan är redo att experimentera i stor skala startar du jobb på flera GPU:er med MPI eller andra kommunikationsbibliotek och kör flera experiment parallellt.

Batch AI hjälper dig träna modeller i stor skala på flera olika sätt. Exempel: 

|  |  |
|---------|---------|
| **Distribuera träning**<br/>![Distribuerad träning](./media/overview/distributed-training.png)  | Skala upp träningen för ett enskilt jobb på många nätverksanslutna GPU:er för att träna större nätverk med stora mängder data.|
| **Experimentera**<br/>![Experimentering](./media/overview/experimentation.png) | Skala ut träning med flera jobb. Kör parametersvep för att testa nya idéer eller justera hyperparametrar för att optimera precision och prestanda. |
| **Kör parallellt**![Parallellkörning](./media/overview/parallel-execution.png) | Träna eller bedöm många modeller samtidigt som körs parallellt i ett serverbestånd för att slutföra jobb snabbare.|

När du har tränat en modell kan du använda Batch AI för att testa modellen, om det inte utgjorde en del av dina träningsskript, eller utföra batchbedömning.

## <a name="how-it-works"></a>Hur det fungerar

Använda Batch AI-SDK:er, kommandoradsskript eller Azure-portalen för att hantera beräkningsresurser och schemalägga jobb för träning och testning av AI: 

* **Etablera och skala kluster av virtuella datorer** – Välj antalet noder (virtuella datorer) och välj en GPU-aktiverad eller annan storlek för den virtuella datorn som uppfyller dina behov. Skala automatiskt eller manuellt upp eller ner antalet noder så att du bara använder resurser vid behov. 

* **Hantera beroenden och containrar** – Som standard kör virtuella datorer för Batch AI-kluster Linux-avbildningar. De har förinstallerade beroenden för att köra containerbaserade träningsramverk antingen på GPU:er eller CPU:er. Använd anpassade avbildningar eller kör skript vid start för ytterligare konfiguration.

* **Distribuera data** – Välj ett eller flera lagringsalternativ för att hantera inkommande data och utdata från skript och jobb: Azure Files, Azure Blob Storage eller en hanterad NFS-server. Batch AI har även stöd för anpassade lagringslösningar inklusive parallella filsystem med höga prestanda. Montera filsystem för lagring till klusternoderna och jobbcontainrarna med enkla konfigurationsfiler.

* **Schemalägg jobb** – Skicka jobb till en prioritetsbaserad jobbkö för att dela klusterresurser och utnyttja fördelarna med virtuella datorer med låg prioritet och reserverade instanser.

* **Hantera fel** – Övervaka jobbstatus och starta om jobb vid fel på den virtuella datorn under potentiellt långvariga jobb.

* **Samla in resultat** – Få enkel åtkomst till utdataloggar, Stdout, Stderr och tränade modeller. Konfigurera dina Batch AI-jobb för att skicka utdata direkt till monterad lagring.

Batch AI har som Azure-tjänst stöd för vanliga verktyg, till exempel rollbaserad åtkomstkontroll (RBAC) och virtuella Azure-nätverk för säkerhet.  

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Batch AI-resurserna](resource-concepts.md) för att träna en modell för maskininlärning eller AI.

* Kom igång och [träna en exempelmodell för djupinlärning](quickstart-tensorflow-training-cli.md) med Batch AI.

* Kolla in exempel på [träningsrecept](https://github.com/Azure/BatchAI/blob/master/recipes) för populära AI-ramverk.
