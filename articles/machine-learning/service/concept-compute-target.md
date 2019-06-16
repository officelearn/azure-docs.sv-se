---
title: Beräkningsmål
titleSuffix: Azure Machine Learning service
description: Beräkningsmål kan du ange beräkningsresursen där du kör dina utbildningsskript eller värden tjänstdistributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräkningsresurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755357"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Vad är ett beräkningsmål i Azure Machine Learning-tjänsten? 

Beräkningsmål kan du ange beräkningsresursen där du kör dina utbildningsskript eller värden tjänstdistributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräkningsresurs.

Beräkningsmål gör det enkelt att ändra din beräkningsmiljö utan att ändra koden.  En typisk modellen för säkerhetsutveckling:

* Börja med en liten mängd data dev/experiment. I det här skedet bör du använda en lokal miljö. Den lokala datorn eller en molnbaserad VM.
* Skala upp din utbildning på större datauppsättningar eller distribueras utbildning med någon av de [utbildning mål](#train).  
* Distribuera till flera web värdmiljöer eller IoT-enheter med någon av de [distributionskanaler](#deploy).

De beräkningsresurser du använder för din beräkningsmål som är kopplade till en [arbetsytan](concept-workspace.md). Compute-resurser än den lokala datorn som delas av användare i arbetsytan.

## <a name="train"></a> Mål för utbildning

Azure Machine Learning-tjänsten har olika stöd för olika beräkningsresurser.  Du kan även bifoga dina egna beräkningsresurs, även om stöd för olika scenarier kan variera som beskrivs nedan:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Distributionsmål

Följande beräkningsresurser kan användas som värd för din distribution av modeller.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Hanterad databearbetning

En hanterad beräkningsresurs skapas och hanteras av Azure Machine Learning-tjänsten. Den här beräkningen är optimerad för arbetsbelastningar för machine learning. Beräkning av Azure Machine Learning är den enda hantera databearbetning från och med 30 maj 2019. Ytterligare hanterade beräkningsresurser kan läggas till i framtiden.

### <a name="amlcompute"></a> Azure Machine Learning-beräkning

Du kan använda beräkning av Azure Machine Learning för utbildning och för batch inferensjobb (förhandsversion).  Med den här beräkningsresurs har du:

* En eller flera node-kluster
* Skalar varje gång du skickar en körning 
* Automatisk klusterhantering och schemaläggning av jobb 
* Stöd för både CPU- och GPU-resurser

Du kan skapa beräkning av Azure Machine Learning-instanser med något av följande:

* Azure Portal
* The Azure Machine Learning SDK
* Azure CLI

Alla andra beräkningsresurser måste skapas utanför arbetsytan och sedan ansluts till den.

## <a name="unmanaged-compute"></a>Ohanterade beräkning

En ohanterad beräkningsresurs är *inte* hanteras av Azure Machine Learning-tjänsten. Du skapar den här typen av beräkning utanför Azure Machine Learning och sedan koppla dem till din arbetsyta. Ohanterade beräkningsresurser kan kräva ytterligare steg för att underhålla eller förbättra prestanda för arbetsbelastningar för machine learning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera beräkningsmål för modellträning](how-to-set-up-training-targets.md)
* [Distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md)