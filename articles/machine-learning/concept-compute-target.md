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
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270425"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Vad är beräknings mål i Azure Machine Learning? 

Ett **beräknings mål** är en viss beräknings resurs/miljö där du kör ditt utbildnings skript eller är värd för tjänst distributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräknings resurs. Genom att använda beräknings mål kan du enkelt ändra beräknings miljön utan att behöva ändra koden.  

I en typisk modell utvecklings livs cykel kan du:
1. Börja med att utveckla och experimentera med en liten mängd data. I det här skedet rekommenderar vi din lokala miljö (lokal dator eller molnbaserad virtuell dator) som beräknings mål. 
2. Skala upp till större data eller distribuera utbildning med någon av dessa [inlärnings mål](#train).  
3. När modellen är klar distribuerar du den till en webb värd miljö eller IoT-enhet med någon av dessa [distributions mål](#deploy).

De beräknings resurser som du använder för beräknings målen är kopplade till en [arbets yta](concept-workspace.md). Andra beräknings resurser än den lokala datorn delas av användare av arbets ytan.

## <a name="train"></a>Inlärnings mål

Azure Machine Learning har varierande stöd för olika beräknings resurser.  Du kan också koppla din egen beräknings resurs, även om stöd för olika scenarier kan variera.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Läs mer om hur du [konfigurerar och använder ett beräknings mål för modell träning](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Distributions mål

Följande beräknings resurser kan användas som värd för modell distributionen.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Lär dig [hur och hur du distribuerar din modell till ett beräknings mål](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (hanterad)

En hanterad beräknings resurs skapas och hanteras av Azure Machine Learning. Den här beräkningen är optimerad för Machine Learning-arbetsbelastningar. Azure Machine Learning beräknings kluster och [beräknings instanser](concept-compute-instance.md) är de enda hanterade beräkningarna. Ytterligare hanterade beräknings resurser kan läggas till i framtiden.

Du kan skapa Azure Machine Learning beräknings instanser (för hands version) eller beräknings kluster i:

| | Azure Machine Learning-studio | Azure Portal | SDK | Resource Manager-mall | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Beräkninsinstans | ja | ja | ja | ja |  |
| Beräkningskluster | ja | ja | ja | ja | ja |

När du skapar de här beräknings resurserna sker en del av din arbets yta automatiskt till skillnad från andra typer av beräknings mål.

### <a name="compute-clusters"></a>Beräknings kluster

Du kan använda Azure Machine Learning beräknings kluster för utbildning och för batch-inferencing (för hands version).  Med den här beräknings resursen har du:

* Kluster med en eller flera noder
* Autoskalar varje gången du skickar en körning 
* Automatisk kluster hantering och schemaläggning av jobb 
* Stöd för både CPU-och GPU-resurser



## <a name="unmanaged-compute"></a>Ohanterad beräkning

Ett ohanterat beräknings mål hanteras *inte* av Azure Machine Learning. Du skapar den här typen av beräknings mål utanför Azure Machine Learning och kopplar den sedan till din arbets yta. Ohanterade beräknings resurser kan kräva ytterligare åtgärder för att underhålla eller förbättra prestanda för Machine Learning-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Konfigurera ett beräknings mål för att träna din modell](how-to-set-up-training-targets.md)
* [Distribuera din modell till ett beräknings mål](how-to-deploy-and-where.md)