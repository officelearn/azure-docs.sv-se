---
title: Azure Machine Learning vs. Machine Learning Studio (klassisk)
description: Hur Azure Machine Learning skiljer sig från Machine Learning Studio (klassisk)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 064786de1f25c3d185984534b9a8fc61602826ee
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311469"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Hur Azure Machine Learning skiljer sig från Machine Learning Studio (klassisk)

I den här artikeln jämförs funktioner, funktioner och gränssnitt för Azure Machine Learning till Machine Learning Studio (klassisk). 

## <a name="about-machine-learning-studio-classic"></a>Om Machine Learning Studio (klassisk)
[Machine Learning Studio (klassisk)](studio/what-is-ml-studio.md) är en gemensam, dra och släpp-visuell arbets yta där du kan bygga, testa och distribuera maskin inlärnings lösningar utan att behöva skriva kod. Den använder förinställda och förkonfigurerade Machine Learning-algoritmer och data hanterings moduler samt en patentskyddad beräknings plattform.

## <a name="about-azure-machine-learning"></a>Om Azure Machine Learning

Under tiden tillhandahåller [Azure Machine Learning](overview-what-is-azure-ml.md) både ett webb gränssnitt som kallas designer **och** flera SDK: er och CLI för att snabbt kunna utveckla data, träna och distribuera maskin inlärnings modeller. Med Azure Machine Learning du skala, stöd för flera ramverk, avancerade ML-funktioner som automatiserad maskin inlärning och pipeline-support.

Azure Machine Learning designer har en liknande dra-och-släpp-upplevelse till Studio (klassisk). Men till skillnad från den tillverkarspecifika beräknings plattformen i Studio (klassisk) använder designern dina egna beräknings resurser, är skalbara och är helt integrerade i Azure Machine Learning.  

> [!TIP]
> Kunder som för närvarande använder eller utvärderar Machine Learning Studio (klassisk) uppmanas att prova [Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/concept-designer), som tillhandahåller dra och släpp ml-moduler, __samt__ skalbarhet, versions kontroll och företags säkerhet.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Jämförelse: Azure Machine Learning vs. Machine Learning Studio (klassisk)

Här är en snabb jämförelse.

||  Azure Machine Learning designer|Studio (klassisk) |
|---| --- | --- | 
|Dra och släpp-gränssnitt| Ja | Ja|
|Experiment| Skala med beräknings mål|Skala (10 GB inlärnings data gräns) | 
|Moduler för gränssnitt| [Många populära moduler](algorithm-module-reference/module-reference.md) | Mycket |
|Inlärnings mål| AML Compute (GPU/CPU)|Patentskyddat Compute Target, endast CPU|
|Inferencing Compute-mål| Azure Kubernetes service för real tids härledning <br/>AML-beräkning för batch-härledning|Eget webb tjänst format, inte anpassningsbart | 
|ML pipeline| Pipeline-redigering <br/> Publicerad pipeline <br/> Pipeline-slutpunkt <br/> [Läs mer om ML-pipeline](concept-ml-pipelines.md)|Stöds inte | 
|ML-OPS| Konfigurerbar distribution, modell och pipeline-version|Grundläggande modell hantering och distribution | 
|Modell| Standardformat beror på utbildnings jobbet|Patentskyddat, icke-portabelt format.| 
|Automatisk modell utbildning|Ännu inte i designern, men möjligt via gränssnittet och SDK: er.| Inga | 

## <a name="get-started-with-azure-machine-learning"></a>Kom igång med Azure Machine Learning

Följande resurser kan hjälpa dig att komma igång med Azure Machine Learning

- Läs [Azure Machine Learning översikt](tutorial-first-experiment-automated-ml.md) 

- [Skapa din första designer-pipeline](tutorial-designer-automobile-price-train-score.md) för att förutsäga de automatiska priserna.

![Exempel på Azure Machine Learning designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Nästa steg

Förutom dra och släpp-funktionerna i designern har Azure Machine Learning andra tillgängliga verktyg:  
  + [Använd python-anteckningsböcker för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd R-markdown för att träna & distribuera ML-modeller](tutorial-1st-r-experiment.md) 
  + [Använd automatisk maskin inlärning för att träna & distribuera ML-modeller](tutorial-designer-automobile-price-train-score.md) 
  + [Använda Machine Learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

