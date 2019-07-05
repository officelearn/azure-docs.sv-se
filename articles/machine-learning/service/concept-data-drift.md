---
title: Data drift övervakning (förhandsversion)
titleSuffix: Azure Machine Learning service
description: Lär dig hur Azure Machine Learning-tjänsten övervakar för drift av data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442388"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Vad är data avviker övervakning (förhandsversion)?

Data drift har ändrats i fördelning av data. I samband med maskininlärning, kan tränade maskininlärningsmodeller prestanda försämrad förutsägelse på grund av drift. Övervaka drift mellan träningsdata och data som används för att göra förutsägelser kan identifiera prestandaproblem.

Machine learning-modeller är bara lika bra som de data som används för att träna dem. Distribuera modeller till produktion utan att övervaka dess prestanda kan leda till oidentifierade och skadliga konsekvenser. Med data drift övervakning, kan du identifiera och anpassa sig till data-drift. 

## <a name="when-to-monitor-for-data-drift"></a>När du ska övervaka för drift av data?

Vi kan övervaka mått är:

+ Omfattning för drift (Drift koefficienten)
+ Orsaken till drift (Drift bidrag av funktionen)
+ Avståndet mått (Wasserstein, energi, osv.)

Med den här övervakning, kan aviseringar eller åtgärder som ställas in när drift har identifierats och data Science kan undersöka orsaken till problemet. 

Om du tror att indata för din distribuerade modell kan ändras, bör du överväga att använda data drift identifiering.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Hur data drift övervakas i Azure Machine Learning-tjänsten

Med hjälp av **Azure Machine Learning-tjänsten**, data drift övervakas via datauppsättningar eller distributioner. För att övervaka data drift, har en baslinje-datauppsättning – vanligtvis utbildning datauppsättningen för en modell - angetts. En andra datauppsättning – vanligtvis modellen inkommande data som samlats in från en distribution – testas mot baslinje-datauppsättningen. Båda datauppsättningar är [profileras](how-to-explore-prepare-data.md#explore-with-summary-statistics) och indata till data avviker övervakningstjänsten. En machine learning-modell tränas att identifiera skillnader mellan de två datauppsättningarna. Modellens prestanda konverteras till koefficienten drift mäter omfattning för drift mellan de två datauppsättningarna. Med hjälp av [modellera interpretability](machine-learning-interpretability-explainability.md) de funktioner som bidragit till drift-koefficienten beräknas. Statistisk information om varje funktion spåras från profilen för en datauppsättning. 

## <a name="data-drift-metric-output"></a>Metrisk utdataporten för drift

Det finns flera sätt att visa mått för drift:

* Använd Jupyter-widgeten.
* Använd den `get_metrics()` funktion på någon `datadriftRun` objekt.
* Visa värdena i Azure portal på din modell

Följande mått har sparats i varje körning iteration för en aktivitet för drift av data:

|Mått|Beskrivning|
--|--|
wasserstein_distance|Statistisk avståndet som definierats för endimensionell numeriska distribution.|
energy_distance|Statistisk avståndet som definierats för endimensionell numeriska distribution.|
datadrift_coefficient|Kallades tidigare Matthews korrelationskoefficienten, ett reellt tal som sträcker sig från -1 till 1. I samband med drift, 0 indikerar inget drift och 1 anger maximal drift.|
datadrift_contribution|Funktionen vikten av funktioner som påverkar återställningstiden avviker.|

## <a name="next-steps"></a>Nästa steg

Visa exempel och lär dig att övervaka data drift:

+ [Lär dig att övervaka data drift på modeller som distribuerats via Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Prova att använda [Jupyter Notebook-exempel](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)