---
title: Azure Machine Learning vs Machine Learning Studio (klassisk)
description: Vad är skillnaden mellan Azure Machine Learning och Machine Learning Studio (klassisk)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371843"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klassisk)

I den här artikeln får du lära dig skillnaden mellan Azure Machine Learning och Machine Learning Studio (klassisk). 

Azure Machine Learning tillhandahåller Python- och R-SDK:er **och** "dra-och-släpp-designern" för att skapa och distribuera maskininlärningsmodeller. Studio (klassisk) erbjuder bara en fristående dra-och-släpp-upplevelse.

Vi rekommenderar att nya användare väljer Azure Machine Learning för det bredaste utbudet av avancerade verktyg för maskininlärning.

## <a name="quick-comparison"></a>Snabb jämförelse

I följande tabell sammanfattas några av de viktigaste skillnaderna mellan Azure Machine Learning och Studio (klassisk):

| | Machine Learning Studio (klassisk) | Azure Machine Learning |
|---| --- | --- |
| Dra och släpp-gränssnitt | Stöds | Stöds - [Azure Machine Learning designer (förhandsversion)](concept-designer.md) | 
| Experiment | Skalbar (10 GB utbildningsdatagräns) | Skala med beräkningsmål |
| Beräkningsmål för utbildning | Proprietär beräkningsmål, CPU-stöd endast | Brett utbud av anpassningsbara [utbildning beräkningsmål](concept-compute-target.md#train). Inkluderar GPU- och CPU-stöd | 
| Beräkningsmål för distribution | Proprietär webbtjänstformat, inte anpassningsbart | Brett utbud av anpassningsbara [beräkningsmål för distribution](concept-compute-target.md#deploy). Inkluderar GPU- och CPU-stöd |
| ML Rörledning | Stöds inte | Skapa flexibla, modulära [pipelines](concept-ml-pipelines.md) för att automatisera arbetsflöden |
| MLOps (på ett sätt) | Grundläggande modellhantering och distribution | Entitetsversionering (modell, data, arbetsflöden), automatisering av arbetsflöden, integrering med CICD-verktyg [med mera](concept-model-management-and-deployment.md) |
| Modellformat | Proprietärt format, Studio (klassisk) endast | Flera format som stöds beroende på utbildningsjobbtyp |
| Automatiserad modellträning och hyperparameterjustering |  Stöds inte | [Stöds i SDK och den visuella arbetsytan](concept-automated-ml.md) | 
| Identifiering av datadrift | Stöds inte | [Stöds i SDK och visuell arbetsyta](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrera från Machine Learning Studio (klassisk)

För närvarande finns det inget sätt att migrera Studio (klassiska) tillgångar till Azure Machine Learning designer (förhandsversion). Aktuella Studio-användare (klassiska) kan fortsätta att använda sina maskininlärningsresurser. Vi uppmuntrar dock alla användare att överväga att använda designern, vilket ger en välbekant dra-och-släpp-upplevelse med förbättrat arbetsflöde **plus** skalbarhet, versionskontroll och företagssäkerhet.

## <a name="get-started-with-azure-machine-learning"></a>Komma igång med Azure Machine Learning

Följande resurser kan hjälpa dig att komma igång med Azure Machine Learning. 

- Läs [översikten över Azure Machine Learning](overview-what-is-azure-ml.md).

- Skapa ditt [första experiment med Python SDK](tutorial-1st-experiment-sdk-setup.md).

- [Skapa din första designerpipeline](tutorial-designer-automobile-price-train-score.md) för att förutsäga automatiska priser.

![Exempel på Azure Machine Learning-designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Nästa steg

Förutom dra-och-släpp-funktionerna i designern har Azure Machine Learning andra verktyg tillgängliga:  
  + [Använda Python-anteckningsböcker för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd R Markdown för att träna & distribuera ML-modeller](tutorial-1st-r-experiment.md) 
  + [Använd automatiserad maskininlärning för att träna & distribuera ML-modeller](tutorial-first-experiment-automated-ml.md)  
  + [Använd machine learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

