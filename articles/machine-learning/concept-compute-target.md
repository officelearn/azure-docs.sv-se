---
title: Vad är beräknings mål
titleSuffix: Azure Machine Learning
description: Definiera var du vill träna eller distribuera din modell med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: ed65d69c18f2dbcd53324fe3cc18af8c51c546b2
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780121"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Vad är beräknings mål i Azure Machine Learning? 

Ett **beräknings mål** är en viss beräknings resurs/miljö där du kör ditt utbildnings skript eller är värd för tjänst distributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräknings resurs. Genom att använda beräknings mål kan du enkelt ändra beräknings miljön utan att behöva ändra koden.  

I en typisk modell utvecklings livs cykel kan du:
1. Börja med att utveckla och experimentera med en liten mängd data. I det här skedet rekommenderar vi din lokala miljö (lokal dator eller molnbaserad virtuell dator) som beräknings mål. 
2. Skala upp till större data eller distribuera utbildning med någon av dessa [inlärnings mål](#train).  
3. När modellen är klar distribuerar du den till en webb värd miljö eller IoT-enhet med någon av dessa [distributions mål](#deploy).

De beräknings resurser som du använder för beräknings målen är kopplade till en [arbets yta](concept-workspace.md). Andra beräknings resurser än den lokala datorn delas av användare av arbets ytan.

## <a name="training-compute-targets"></a><a name="train"></a>Inlärnings mål

Azure Machine Learning har varierande stöd för olika beräknings resurser.  Du kan också koppla din egen beräknings resurs, även om stöd för olika scenarier kan variera.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Läs mer om hur du [konfigurerar och använder ett beräknings mål för modell träning](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Distributions mål

Följande beräknings resurser kan användas som värd för modell distributionen.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Lär dig [hur och hur du distribuerar din modell till ett beräknings mål](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (hanterad)

En hanterad beräknings resurs skapas och hanteras av Azure Machine Learning. Den här beräkningen är optimerad för Machine Learning-arbetsbelastningar. Azure Machine Learning beräknings kluster och [beräknings instanser](concept-compute-instance.md) är de enda hanterade beräkningarna. Ytterligare hanterade beräknings resurser kan läggas till i framtiden.

Du kan skapa Azure Machine Learning beräknings instanser (för hands version) eller beräknings kluster från:
* Azure Machine Learning-studio
* Azure Portal
* Python SDK- [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) och [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) -klasser
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Resource Manager-mall

Du kan också skapa beräknings kluster med [Machine Learning-tillägget för Azure CLI](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training).

När du skapar dessa beräknings resurser automatiskt en del av din arbets yta, till skillnad från andra typer av beräknings mål.

### <a name="compute-clusters"></a>Beräknings kluster

Du kan använda Azure Machine Learning beräknings kluster för utbildning och för batch-inferencing (för hands version).  Med den här beräknings resursen har du:

* Kluster med en eller flera noder
* Automatisk skalning varje gången du skickar en körning 
* Automatisk kluster hantering och schemaläggning av jobb 
* Stöd för både CPU-och GPU-resurser

### <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

När du väljer en Node-storlek för en hanterad beräknings resurs i Azure Machine Learning kan du välja mellan Välj VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för Linux och Windows för olika arbets belastningar. Mer information om de olika [VM-typerna och-storlekarna](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)hittar du här.

Det finns några undantag och begränsningar för att välja en VM-storlek:
* En del VM-serien stöds inte i Azure Machine Learning.
* En del VM-serien är begränsad. Om du vill använda en begränsad serie kontaktar du supporten och begär en kvot ökning för serien. Information om hur du kontaktar support finns i [Support alternativ för Azure](https://azure.microsoft.com/support/options/)

I följande tabell finns mer information om vilka serier och begränsningar som stöds. 

| **VM-serien som stöds**  | **Begränsningar** |
|------------|------------|
| D | Inga |
| Dv2 | Inga |  
| DSv2 | Inga |  
| FSv2 | Inga |  
| M | Godkännande krävs |
| NC | Inga |    
| NCsv2 | Godkännande krävs |
| NCsv3 | Godkännande krävs |  
| NDs | Godkännande krävs |
| NDv2 | Godkännande krävs |
| NV | Inga |
| NVv3 | Godkännande krävs | 


Även om Azure Machine Learning stöder dessa VM-serier är de inte tillgängliga i alla Azure-regioner. Du kan kontrol lera om det finns tillgängliga VM-serier här: [produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Ohanterad beräkning

Ett ohanterat beräknings mål hanteras *inte* av Azure Machine Learning. Du skapar den här typen av beräknings mål utanför Azure Machine Learning och kopplar den sedan till din arbets yta. Ohanterade beräknings resurser kan kräva ytterligare åtgärder för att underhålla eller förbättra prestanda för Machine Learning-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Konfigurera ett beräknings mål för att träna din modell](how-to-set-up-training-targets.md)
* [Distribuera din modell till ett beräknings mål](how-to-deploy-and-where.md)
