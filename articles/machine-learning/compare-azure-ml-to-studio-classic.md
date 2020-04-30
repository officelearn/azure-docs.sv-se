---
title: Azure Machine Learning vs. Machine Learning Studio (klassisk)
description: Vad är skillnaden mellan Azure Machine Learning och Machine Learning Studio (klassisk)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371843"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klassisk)

I den här artikeln får du lära dig skillnaden mellan Azure Machine Learning och Machine Learning Studio (klassisk). 

Azure Machine Learning tillhandahåller python-och R **-SDK:** erna "dra och släpp"-designern för att bygga och distribuera maskin inlärnings modeller. Studio (klassisk) erbjuder bara en fristående dra-och-släpp-upplevelse.

Vi rekommenderar att nya användare väljer Azure Machine Learning för det bredaste utbudet av maskin inlärnings verktyg.

## <a name="quick-comparison"></a>Snabb jämförelse

I följande tabell sammanfattas några av de viktigaste skillnaderna mellan Azure Machine Learning och Studio (klassisk):

| | Machine Learning Studio (klassisk) | Azure Machine Learning |
|---| --- | --- |
| Dra och släpp gränssnitt | Stöds | Stödd [Azure Machine Learning designer (för hands version)](concept-designer.md) | 
| Experiment | Skalbar (10 GB inlärnings data gräns) | Skala med beräknings mål |
| Inlärnings mål | Patentskyddat beräknings mål, endast CPU-stöd | Brett utbud av anpassningsbara [inlärnings mål](concept-compute-target.md#train). Innehåller stöd för GPU och CPU | 
| Beräknings mål för distribution | Eget webb tjänst format, inte anpassningsbart | Brett utbud av anpassningsbara [beräknings mål för distribution](concept-compute-target.md#deploy). Innehåller stöd för GPU och CPU |
| ML pipeline | Stöds inte | Bygg flexibla, modulära [pipelines](concept-ml-pipelines.md) för att automatisera arbets flöden |
| MLOps | Grundläggande modell hantering och distribution | Enhets version (modell, data, arbets flöden), automatisering av arbets flöde, integrering med CICD-verktyg [och mycket annat](concept-model-management-and-deployment.md) |
| Modell format | Eget format, endast Studio (klassiskt) | Flera format som stöds beroende på utbildnings jobb typ |
| Automatisk modell inlärning och inställning för att justera dem |  Stöds inte | [Stöds i SDK-och visualiserings arbets ytan](concept-automated-ml.md) | 
| Data avkänning | Stöds inte | [Stöds i SDK och visuell arbets yta](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrera från Machine Learning Studio (klassisk)

För närvarande finns det inget sätt att migrera Studio (klassiska) till gångar till Azure Machine Learning designer (för hands version). Aktuella Studio-användare kan fortsätta att använda sina Machine Learning-tillgångar. Vi rekommenderar dock att alla användare överväger att använda designern, vilket ger en välbekant upplevelse för att dra och släppa med förbättrat arbets flöde **plus** skalbarhet, versions kontroll och företags säkerhet.

## <a name="get-started-with-azure-machine-learning"></a>Kom igång med Azure Machine Learning

Följande resurser kan hjälpa dig att komma igång med Azure Machine Learning. 

- Läs [Azure Machine Learning översikt](overview-what-is-azure-ml.md).

- Skapa ditt [första experiment med python SDK](tutorial-1st-experiment-sdk-setup.md).

- [Skapa din första designer-pipeline](tutorial-designer-automobile-price-train-score.md) för att förutsäga de automatiska priserna.

![Exempel på Azure Machine Learning designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Nästa steg

Förutom dra och släpp-funktionerna i designern har Azure Machine Learning andra tillgängliga verktyg:  
  + [Använd python-anteckningsböcker för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd R-markdown för att träna & distribuera ML-modeller](tutorial-1st-r-experiment.md) 
  + [Använd automatisk maskin inlärning för att träna & distribuera ML-modeller](tutorial-first-experiment-automated-ml.md)  
  + [Använda Machine Learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

