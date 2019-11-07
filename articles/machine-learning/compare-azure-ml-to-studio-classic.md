---
title: Azure Machine Learning vs. ML Studio (klassisk)
description: Hur Azure Machine Learning skiljer sig från ML Studio (klassisk)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: dae708a417a1bf8ca4b118f218e353a28e5675c1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684967"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Hur Azure Machine Learning skiljer sig från ML Studio (klassisk)

I den här artikeln jämförs funktioner, funktioner och gränssnitt för Azure Machine Learning till ML Studio (klassisk). 

## <a name="about-ml-studio-classic"></a>Om ML Studio (klassisk)
[Ml Studio (klassisk)](/studio/what-is-ml-studio.md) är en gemensam, dra och släpp-visuell arbets yta där du kan bygga, testa och distribuera maskin inlärnings lösningar utan att behöva skriva kod. Den använder förinställda och förkonfigurerade Machine Learning-algoritmer och data hanterings moduler samt en patentskyddad beräknings plattform.

## <a name="about-azure-machine-learning"></a>Om Azure Machine Learning

Under tiden tillhandahåller [Azure Machine Learning](/service/overview-what-is-azure-ml.md) både ett webb gränssnitt som kallas designer (för hands version) **och** flera SDK: er och CLI för att snabbt kunna utveckla data, träna och distribuera maskin inlärnings modeller. Med Azure Machine Learning du skala, stöd för flera ramverk, avancerade ML-funktioner som automatiserad maskin inlärning och pipeline-support.

Azure Machine Learning designer har en liknande dra-och-släpp-upplevelse till Studio (klassisk). Men till skillnad från den tillverkarspecifika beräknings plattformen i Studio (klassisk) använder designern dina egna beräknings resurser, är skalbara och är helt integrerade i Azure Machine Learning.  

> [!TIP]
> Kunder som för närvarande använder eller utvärderar Machine Learning Studio (klassisk) uppmanas att prova [Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (för hands version), vilket ger dra och släpp ml-moduler __samt__ skalbarhet, versions kontroll och företags säkerhet.

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>Jämförelse: Azure Machine Learning vs. ML Studio (klassisk)

Här är en snabb jämförelse.

||  Azure Machine Learning designer|Studio (klassisk) |
|---| --- | --- |
||Designern är i för hands version, Azure Machine Learning är GA|Allmänt tillgänglig (GA) | 
|Dra och släpp-gränssnitt| Ja | Ja|
|Olika| Skala med beräknings mål|Skala (10 GB inlärnings data gräns) | 
|Moduler för gränssnitt|  Många populära moduler|Mycket |
|Inlärnings mål| AML Compute (GPU/CPU)|Patentskyddat Compute Target, endast CPU|
|Inferencing Compute-mål| Azure Kubernetes service för real tids härledning <br/>AML-beräkning för batch-härledning|Eget webb tjänst format, inte anpassningsbart | 
|ML pipeline| Pipeline-redigering <br/> Publicerad pipeline <br/> Pipeline-slutpunkt <br/> [Läs mer om ML-pipeline](service/concept-ml-pipelines.md)|Stöds inte | 
|ML-OPS| Konfigurerbar distribution, modell och pipeline-version|Grundläggande modell hantering och distribution | 
|Modell| Standardformat beror på utbildnings jobbet|Patentskyddat, icke-portabelt format.| 
|Automatisk modell utbildning|Ännu inte i designern, men möjligt via gränssnittet och SDK: er.| Nej | 

## <a name="get-started-with-azure-machine-learning-designer"></a>Kom igång med Azure Machine Learning designer

Följande resurser kan hjälpa dig att komma igång med Azure Machine Learning

- Läs [Azure Machine Learning översikt](service/tutorial-first-experiment-automated-ml.md) 

- [Skapa din första designer-pipeline](service/tutorial-designer-automobile-price-train-score.md) för att förutsäga de automatiska priserna.


![Exempel på Azure Machine Learning designer](service/media/concept-ml-pipelines/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Nästa steg

Förutom dra-n-släpp-funktionerna i designern har Azure Machine Learning andra tillgängliga verktyg:  
  + [Använd python-anteckningsböcker för att träna & distribuera ML-modeller](./service/tutorial-1st-experiment-sdk-setup.md)
  + [Använd R-markdown för att träna & distribuera ML-modeller](./service/tutorial-1st-r-experiment.md) 
  + [Använd automatisk maskin inlärning för att träna & distribuera ML-modeller](./service/tutorial-designer-automobile-price-train-score.md) 
  + [Använda Machine Learning CLI för att träna och distribuera en modell](./service/tutorial-train-deploy-model-cli.md)

