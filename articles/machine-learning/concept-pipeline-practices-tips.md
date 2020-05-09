---
title: Iterera och utvecklas maskin inlärnings pipeliner
titleSuffix: Azure Machine Learning
description: Mönster, metoder och tips för snabb utveckling
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858193"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Iterera och utvecklas maskin inlärnings pipeliner

Azure Machine Learning pipelines ger ett effektivt sätt att modularize koden, återanvända resultat och optimera dina beräknings resurser. Här följer några praktiska tips och metoder för att arbeta med pipeliner.

## <a name="how-do-you-get-started-with-pipelines"></a>Hur kommer du igång med pipelines?

Det finns flera alternativ för att komma igång om du är nybörjare på pipelines:

* Om du får bästa möjliga genom att läsa och återskapa konstruktions processen kan artikeln [skapa och köra maskin inlärnings pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) passar bra 
* Om du gillar att lära dig interaktivt i en Jupyter Notebook, som utvecklats i [Azure Machine Learning pipelines: komma igång](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) Notebook kan vara rätt för dig
* Om du föredrar en kod för första gången är det en bra utgångs punkt att klona [Azure Machine Learning pipelines demo](https://github.com/microsoft/aml-pipelines-demo) lagrings platsen

## <a name="how-do-you-modularize-pipeline-code"></a>Hur modularize du pipeline-kod? 

Moduler och- `ModuleStep` klassen ger dig en bra möjlighet att MODULARIZE din ml-kod. Det måste dock vara kvar i åtanke att det är mycket dyrare att flytta mellan pipeline-steg än ett funktions anrop. Frågan du behöver fråga inte så mycket "är dessa funktioner och data konceptuellt annorlunda än de som finns i det här avsnittet?" men "vill jag att dessa funktioner och data ska utvecklas separat?" eller "är den här beräkningen kostsam och kan jag återanvända sina utdata?" Mer information finns i thisn'tebook [How to Create module, ModuleVersion och use the i en pipeline med ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Som tidigare nämnts är det ofta en sådan möjlighet att separera förberedelse av data från utbildning. Ibland är förberedelse av data komplexa och tids krävande att du kan dela upp processen i separata pipeline-steg. Andra möjligheter är testning och analys efter utbildning. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Hur effektiviserar du pipelinen? 

Vanliga tekniker för att snabbt iterera pipelines är: 

- Klona pipelinen (göra en kopia) och kör pipelinen igen snabbt
- Hålla beräknings instansen igång så att du undviker start tiden
- Om du konfigurerar data och steg för att tillåta åter användning kan pipelinen hoppa över att beräkna om oförändrade data

När du snabbt vill iterera kan du klona din pipeline, göra en pipeline och köra pipelinen igen. En annan praktisk teknik är om du håller din beräkning varm, kommer du inte att debiteras kostnaden för att snurra upp den nya beräkningen. Om du ställer in steget för att tillåta åter användning av resultatet av en körning kommer den upprepade körningen att återanvända resultat när det är möjligt (när det inte finns några ändringar i stegen).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Hur samarbetar jag med ML-pipeliner? 

Separata pipeliner är naturliga linjer som du kan använda för att dela arbets insats. Flera utvecklare eller till och med flera team kan arbeta med olika steg, så länge som de data och argument som flödar mellan stegen är överenskomna. 

Under aktiv utveckling kan du hämta `PipelineRun` och `StepRun` köra resultat från arbets ytan, använda dessa objekt för att hämta slutgiltiga och mellanliggande utdata och använda dessa artefakter för ditt eget modulära arbete.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Använda pipelines för att testa tekniker i isolering

Verkliga ML-lösningar omfattar vanligt vis avsevärd anpassning av varje steg. Rå data måste ofta förberedas på något sätt: filtrerad, transformerad och förhöjd. Inlärnings processerna kan ha flera möjliga arkitekturer och, för djup inlärning, många möjliga variationer för lager storlekar och aktiverings funktioner. Även om du har en konsekvent arkitektur kan du skapa en betydande WINS-sökning.

Förutom verktyg som AutoML och [automatiserad sökning](how-to-tune-hyperparameters.md)i [AutoML](concept-automated-ml.md) , kan pipelines vara ett viktigt verktyg för test lösningar i/B. Om du har flera varianter av stegen för pipelinen är det enkelt att generera separata körningar med sina variationer: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

