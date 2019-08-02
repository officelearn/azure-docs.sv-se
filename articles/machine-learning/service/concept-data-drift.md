---
title: Övervakning av data drivgarn (för hands version)
titleSuffix: Azure Machine Learning service
description: Lär dig hur Azure Machine Learning-tjänsten kan övervaka data driften.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371080"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Vad är data avvikelse övervakning (för hands version)?

Data avvikelsen är ändringen av data fördelningen. I samband med Machine Learning kan tränade maskin inlärnings modeller uppleva försämrade förutsägelser på grund av driften. Att övervaka driften mellan tränings data och data som används för att göra förutsägelser kan identifiera prestanda problem.

Machine Learning-modeller är bara lika användbara som de data som används för att träna dem. Distribution av modeller till produktion utan övervakning av prestanda kan leda till oidentifierade och skadliga konsekvenser. Med data drifts övervakning kan du identifiera och anpassa till data avvikelser. 

## <a name="when-to-monitor-for-data-drift"></a>När ska jag övervaka för data avvikelse?

Mått som vi kan övervaka är:

+ Omfattning av drift (drivgarn)
+ Orsak till driften (drift bidrag efter funktion)
+ Avstånds mått (Wasserstein, energi osv.)

Med den här övervakningen på plats kan aviseringar eller åtgärder ställas in när driften identifieras och data expert kan undersöka orsaken till problemet. 

Om du tror att indata för din distribuerade modell kan ändras bör du överväga att använda data avkänning.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Så här övervakas data driften i Azure Machine Learnings tjänsten

Med hjälp av **Azure Machine Learning tjänsten**övervakas data drift genom data uppsättningar eller distributioner. För att övervaka data, en bas linje uppsättning – vanligt vis är inlärnings data uppsättningen för en modell-angiven. En andra data uppsättning – vanligt vis modell indata som samlas in från en distribution, testas mot bas linje data uppsättningen. Båda data uppsättningarna [](how-to-explore-prepare-data.md#explore-with-summary-statistics) är profilerade och indata för data tjänst övervaknings tjänsten. En maskin inlärnings modell är utbildad för att identifiera skillnader mellan de två data uppsättningarna. Modellens prestanda konverteras till drivgarn, som mäter storleken på driften mellan de två data uppsättningarna. Med [modell tolkning](machine-learning-interpretability-explainability.md) kan de funktioner som bidrog till drifts koefficienten beräknas. Statistisk information om varje funktion spåras från data uppsättnings profilen. 

## <a name="data-drift-metric-output"></a>Resultat av data avvikelse

Det finns flera sätt att Visa avvikelse mått:

* Använd widgeten [Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* Använd funktionen på valfritt `datadriftRun` objekt. `get_metrics()`
* Visa måtten i Azure Portal i din modell

Följande mått sparas i varje körnings iteration för en data avvikelse:

|Mått|Beskrivning|
--|--|
wasserstein_distance|Statistisk avstånd har definierats för en dimensionell numerisk distribution.|
energy_distance|Statistisk avstånd har definierats för en dimensionell numerisk distribution.|
datadrift_coefficient|Formellt Matthews korrelations-koefficient, ett reellt tal mellan-1 och 1. I samband med en drifts omgivning anger 0 ingen avvikelse och 1 anger maximal avvikelse.|
datadrift_contribution|Funktions betydelsen hos funktioner som bidrar till drift.|

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig att övervaka data genom att följa dessa:

+ [Lär dig hur du övervakar data drift på modeller som distribueras via Azure Kubernetes service (AKS)](how-to-monitor-data-drift.md)
+ Prova [Jupyter Notebook exempel](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)