---
title: 'Beräkningsmål: träna och distribuera modeller'
titleSuffix: Azure Machine Learning service
description: Definiera var du vill att träna eller distribuera din modell med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806054"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Vad är beräkningsmål i Azure Machine Learning-tjänsten? 

En **beräkningsmålet** är en avsedda resurs/beräkningsmiljö där du kör dina utbildningsskript eller värden tjänstdistributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräkningsresurs. Med hjälp av compute mål gör det enkelt för dig att senare ändra din beräkningsmiljö utan att ändra koden.  

I en typisk modellen för säkerhetsutveckling kan du:
1. Börja med att utveckla och experimentera på en liten mängd data. I det här skedet rekommenderar vi din lokala miljö (lokal dator eller molnbaserad VM) som din beräkningsmål. 
2. Skala upp till större data eller distribueras utbildning med någon av dessa [utbildning beräkningsmål](#train).  
3. När din modell är klar kan du distribuera den till en webbplats som är värd för miljön eller IoT-enhet med en av dessa [distribution beräkningsmål](#deploy).

De beräkningsresurser du använder för din beräkningsmål som är kopplade till en [arbetsytan](concept-workspace.md). Compute-resurser än den lokala datorn som delas av användare i arbetsytan.

## <a name="train"></a> Beräkningsmål för utbildning

Azure Machine Learning-tjänsten har olika stöd för olika beräkningsresurser.  Du kan även ansluta dina egna beräkningsresurs, även om stöd för olika scenarier kan variera.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Läs mer om [ställa in och använda ett beräkningsmål för modellträning](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Distributionsmål

Följande beräkningsresurser kan användas som värd för din distribution av modeller.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Lär dig [där och hur du distribuerar din modell till en beräkningsmål](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning-beräkning (hanterad)

En hanterad beräkningsresurs skapas och hanteras av Azure Machine Learning-tjänsten. Den här beräkningen är optimerad för arbetsbelastningar för machine learning. Beräkning av Azure Machine Learning är den enda hantera databearbetning från och med 30 maj 2019. Ytterligare hanterade beräkningsresurser kan läggas till i framtiden.

Du kan använda beräkning av Azure Machine Learning för utbildning och för batch inferensjobb (förhandsversion).  Med den här beräkningsresurs har du:

* En eller flera node-kluster
* Skalar varje gång du skickar en körning 
* Automatisk klusterhantering och schemaläggning av jobb 
* Stöd för både CPU- och GPU-resurser

Du kan skapa beräkning av Azure Machine Learning-instanser i Azure portal, med SDK: N eller med CLI. När du skapade den ingår automatiskt i din arbetsyta till skillnad från andra typer av beräkningsmål.

## <a name="unmanaged-compute"></a>Ohanterade beräkning

En ohanterad beräkningsmål är *inte* hanteras av Azure Machine Learning-tjänsten. Du skapar den här typen av beräkningsmål utanför Azure Machine Learning och sedan koppla dem till din arbetsyta. Ohanterade beräkningsresurser kan kräva ytterligare steg för att underhålla eller förbättra prestanda för arbetsbelastningar för machine learning.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Konfigurera ett beräkningsmål för att träna din modell](how-to-set-up-training-targets.md)
* [Distribuera din modell till en beräkningsmål](how-to-deploy-and-where.md)