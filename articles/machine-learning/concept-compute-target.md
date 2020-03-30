---
title: Vad är beräkningsmål
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270425"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Vad är beräkningsmål i Azure Machine Learning? 

Ett **beräkningsmål** är en angiven beräkningsresurs/-miljö där du kör ditt träningsskript eller är värd för din tjänstdistribution. Den här platsen kan vara din lokala dator eller en molnbaserad beräkningsresurs. Med hjälp av beräkningsmål gör det enkelt för dig att senare ändra din beräkningsmiljö utan att behöva ändra koden.  

I en typisk modellutvecklingslivscykel kan du:
1. Börja med att utveckla och experimentera på en liten mängd data. I det här skedet rekommenderar vi din lokala miljö (lokal dator eller molnbaserad virtuell dator) som beräkningsmål. 
2. Skala upp till större data eller gör distribuerad utbildning med hjälp av något av dessa [träningsberäkningsmål](#train).  
3. När din modell är klar distribuerar du den till en webbhotellsmiljö eller IoT-enhet med ett av dessa [beräkningsmål för distributionen](#deploy).

De beräkningsresurser som du använder för dina beräkningsmål är kopplade till en [arbetsyta](concept-workspace.md). Beräkningsresurser som inte är den lokala datorn delas av användare av arbetsytan.

## <a name="training-compute-targets"></a><a name="train"></a>Beräkningsmål för utbildning

Azure Machine Learning har varierande support för olika beräkningsresurser.  Du kan också koppla din egen beräkningsresurs, även om stödet för olika scenarier kan variera.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Läs mer om [hur du konfigurerar och använder ett beräkningsmål för modellutbildning](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Driftsättningsmål

Följande beräkningsresurser kan användas för att vara värd för din modelldistribution.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Lär dig [var och hur du distribuerar din modell till ett beräkningsmål](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning-beräkning (hanterad)

En hanterad beräkningsresurs skapas och hanteras av Azure Machine Learning. Den här beräkningen är optimerad för maskininlärningsarbetsbelastningar. Azure Machine Learning-beräkningskluster och [beräkningsinstanser](concept-compute-instance.md) är de enda hanterade beräkningarna. Ytterligare hanterade beräkningsresurser kan läggas till i framtiden.

Du kan skapa Azure Machine Learning-beräkningsinstanser (förhandsversion) eller beräkningskluster i:

| | Azure Machine Learning-studio | Azure Portal | SDK | Resource Manager-mall | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Beräkninsinstans | ja | ja | ja | ja |  |
| Beräkningskluster | ja | ja | ja | ja | ja |

När dessa beräkningsresurser skapas är de automatiskt en del av arbetsytan till skillnad från andra typer av beräkningsmål.

### <a name="compute-clusters"></a>Beräkna kluster

Du kan använda Azure Machine Learning-beräkningskluster för utbildning och batchinferencing (förhandsversion).  Med den här beräkningsresursen har du:

* Kluster med en eller flera noder
* Automatisk skalning varje gång du skickar in en körning 
* Automatisk klusterhantering och finplanering 
* Stöd för både CPU- och GPU-resurser



## <a name="unmanaged-compute"></a>Ohanterd beräkning

Ett ohanterat beräkningsmål hanteras *inte* av Azure Machine Learning. Du skapar den här typen av beräkningsmål utanför Azure Machine Learning och bifogar det sedan till din arbetsyta. Ohanterad beräkningsresurser kan kräva ytterligare steg för att du ska kunna underhålla eller förbättra prestanda för maskininlärningsarbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Konfigurera ett beräkningsmål för att träna din modell](how-to-set-up-training-targets.md)
* [Distribuera din modell till ett beräkningsmål](how-to-deploy-and-where.md)