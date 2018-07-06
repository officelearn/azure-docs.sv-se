---
title: Python-paket för Azure Machine Learning
description: Läs mer om de python-paket som är tillgängliga för Azure Machine Learning-användare.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 36322c5fde7c992faa830fe272971b53fbf10211
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872484"
---
# <a name="python-packages-for-azure-machine-learning"></a>Python-paket för Azure Machine Learning

Läs mer om de egna Python-paket från Microsoft för Azure Machine Learning. Du kan använda dessa bibliotek och funktioner i kombination med andra paket med öppen källkod eller från tredje part, men att använda egna paket, din kod måste köras mot en tjänst eller på en dator som innehåller tolkarna Python.

Azure Machine Learning-paket är **Python-pip-installation tillägg för Azure Machine Learning** som gör att dataanalytiker och AI-utvecklare att snabbt bygga och distribuera mycket exakta machine learning och djupinlärning modeller för olika domäner.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Azure ML-paket för visuellt innehåll

Med Azure ML-paket för visuellt innehåll, kan du bygga, finjustera, och distribuera modeller för djup maskininlärning för klassificering av avbildning, objekt-identifiering och avbildning likhet.

Prova de här nästa steg för det här paketet:
1. [Ladda ned](https://aka.ms/aml-packages/vision/download) paketet.
1. Läs den [installera docs](https://aka.ms/aml-packages/vision).
1. [Skapa och distribuera en modellen för visuellt innehåll](how-to-build-deploy-image-classification-models.md) med en Jupyter-anteckningsbok.
1. Utforska paketet [referensdokumentation](https://aka.ms/aml-packages/vision).

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Azure ML-paketet för prognostisering

Med Azure ML-paketet för prognostisering, kan du skapa och distribuera tidsserier prognosmodeller för finansiella och efterfrågeprognostisering scenarier.

Prova de här nästa steg för det här paketet:
1. [Ladda ned](https://aka.ms/aml-packages/forecasting/download) paketet.
1. Läs den [installera docs](https://aka.ms/aml-packages/forecasting).
1. [Skapa och distribuera en prognosmodell](how-to-build-deploy-forecast-models.md) med en Jupyter-anteckningsbok.
1. Utforska paketet [referensdokumentation](https://aka.ms/aml-packages/forecasting).

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Azure ML-paketet för textanalys

Du kan skapa modeller för djupinlärning för text klassificering, extrahering av anpassad entitet och word bädda in texten med Azure ML paket för textanalys.

Prova de här nästa steg för det här paketet:
1. [Ladda ned](https://aka.ms/aml-packages/text/download) paketet.
1. Läs den [installera docs](https://aka.ms/aml-packages/text).
1. [Skapa och distribuera en modell för klassificering av text](how-to-build-deploy-text-classification-models.md) med en Jupyter-anteckningsbok.
1. Utforska paketet [referensdokumentation](https://aka.ms/aml-packages/text).

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (FPGA acceleration)

Träna klassificerare som baseras på de här funktionerna med maskinvaruacceleration för Azure Machine Learning-paket, datatekniker och AI-utvecklare kan förtränade bilder med en quantized version av ResNet-50, och sedan distribuera modeller till en [fältet programmerbart Gate-matriser (FPGA)](concept-accelerate-with-fpgas.md) på Azure för extremt låg latens inferensjobb.

Prova de här nästa steg för det här paketet:
1. [Ladda ned](https://aka.ms/aml-real-time-ai-package) paketet.
1. Läs den [installera docs](reference-fpga-package-overview.md).
1. [Distribuera en modell till en FPGA](how-to-deploy-fpga-web-service.md).

