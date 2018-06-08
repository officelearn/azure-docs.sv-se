---
title: Python-paket för Azure Machine Learning
description: Läs mer om de python-paket som är tillgängliga för användare i Azure Machine Learning.
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7a8f9e9d757aaab04706dd3b7dc2b962b3a04130
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833275"
---
# <a name="python-packages-for-azure-machine-learning"></a>Python-paket för Azure Machine Learning

Lär dig mer om den egna Python-paket från Microsoft för Azure Machine Learning. Du kan använda dessa bibliotek och funktioner i kombination med andra paket med öppen källkod eller från tredje part, men att använda egna paket, din kod måste köras mot en tjänst eller på en dator som innehåller tolkarna Python.

Azure Machine Learning-paket är **Python pip-installeras tillägg för Azure Machine Learning** som gör att dataanalytiker och AI utvecklare att snabbt skapa och distribuera aktuell machine learning och djup learning modeller för olika domäner.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Azure ML-paketet för datorn Vision

Du kan skapa med Azure ML-paketet för datorn Vision, finjustera, och distribuera djup learning-modeller för avbildningen klassificering, objekt-identifiering och bild likhet.

Följande nästa felsökningssteg för det här paketet:
1. [Hämta](https://aka.ms/aml-packages/vision/download) paketet.
1. Läs den [installera docs](https://aka.ms/aml-packages/vision).
1. [Skapa och distribuera en vision datormodell](how-to-build-deploy-image-classification-models.md) med en Jupyter-anteckningsbok.
1. Utforska paketet [refererar dokumentationen](https://aka.ms/aml-packages/vision).

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Azure ML-paketet för Prognosticering

Med Azure ML-paketet för Prognosticering, kan du skapa och distribuera tidsserier prognosmodeller för finansiella och begäran prognoser scenarier.

Följande nästa felsökningssteg för det här paketet:
1. [Hämta](https://aka.ms/aml-packages/forecasting/download) paketet.
1. Läs den [installera docs](https://aka.ms/aml-packages/forecasting).
1. [Skapa och distribuera en prognosmodell](how-to-build-deploy-forecast-models.md) med en Jupyter-anteckningsbok.
1. Utforska paketet [refererar dokumentationen](https://aka.ms/aml-packages/forecasting).

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Azure ML-paketet för textanalys

Du kan skapa djup learning-modeller för text klassificering, kundentitet extrahering och word bädda in texten med Azure ML-paketet för textanalys.

Följande nästa felsökningssteg för det här paketet:
1. [Hämta](https://aka.ms/aml-packages/text/download) paketet.
1. Läs den [installera docs](https://aka.ms/aml-packages/text).
1. [Skapa och distribuera en modell för klassificering av text](how-to-build-deploy-text-classification-models.md) med en Jupyter-anteckningsbok.
1. Utforska paketet [refererar dokumentationen](https://aka.ms/aml-packages/text).

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (då FPGA acceleration)

Med Azure Machine Learning maskinvaruacceleration paketet, dataanalytiker och utvecklare AI kan featurize bilder med en quantized version ResNet 50 träna klassificerare baserat på dessa funktioner och sedan distribuera modeller till en [fältet programmable Gate-matriser (då FPGA)](concept-accelerate-with-fpgas.md) i Azure för mycket låg latens inferencing.

Följande nästa felsökningssteg för det här paketet:
1. [Hämta](https://aka.ms/aml-real-time-ai-package) paketet.
1. Läs den [installera docs](reference-fpga-package-overview.md).
1. [Distribuera en modell till en då FPGA](how-to-deploy-fpga-web-service.md).

