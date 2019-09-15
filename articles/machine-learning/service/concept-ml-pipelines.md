---
title: Vad är ML-pipelines
titleSuffix: Azure Machine Learning service
description: Läs mer om de machine learning pipelines som du kan skapa med Azure Machine Learning-SDK för Python och fördelarna med att använda pipelines i den här artikeln. Machine learning (ML) pipelines som används av datatekniker att bygga, optimera och hantera sina arbetsflöden för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2547ca3af3039af53cb5fd5b244669b9152c21b3
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993291"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Vad är ML pipelines i Azure Machine Learning service?

Lär dig mer om de pipelines i Machine Learning som du kan bygga och hantera med Azure Machine Learning-tjänsten. 

Med machine learning (ML) pipelines, dataexperter, datatekniker och IT-proffs kan samarbeta med stegen som ingår i:
+ Förberedelse av data, till exempel normalizations och omvandlingar
+ Modellträning
+ Modell-utvärdering
+ Distribution

Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md).

![Maskin inlärnings pipeliner i Azure Machine Learning-tjänsten](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Vilken Azure pipeline-teknik ska jag använda?

Azure-molnet tillhandahåller flera andra pipelines, var och en med olika ändamål. I följande tabell visas de olika pipelinen och vad de används för:

| Pipeline | Vad läget gör | Kanoniskt rör |
| ---- | ---- | ---- |
| Azure Machine Learning pipelines | Definierar återanvändbara Machine Learning-arbetsflöden som kan användas som en mall för dina maskin inlärnings scenarier. | Data > modell |
| [Azure Data Factory-pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Grupperar data flyttnings-, omvandlings-och kontroll aktiviteter som krävs för att utföra en uppgift.  | Data-> data |
| [Azure-pipeline](https://azure.microsoft.com/services/devops/pipelines/) | Kontinuerlig integrering och leverans av ditt program till alla plattformar/moln  | Code-> App/Service |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Varför ska man skapa pipelines med Azure Machine Learning?

Maskin inlärnings pipeliner optimerar arbets flödet med hastighet, portabilitet och åter användning så att du kan fokusera på din expertis, maskin inlärning i stället för infrastruktur och automatisering.

Pipelines skapas utifrån flera **steg**, som är distinkta beräknings enheter i pipelinen. Varje steg kan köras oberoende och använda isolerade beräknings resurser.
Oberoende steg gör det möjligt för flera data forskare att arbeta med samma pipeline samtidigt utan beskattnings beräknings resurser och gör det också enkelt att använda olika beräknings typer/storlekar för varje steg.

Efter att pipelinen har utformats finns det ofta mer fin justering av pipelinens inlärnings slinga. När du kör en pipeline igen fortsätter körningen till de distinkta stegen som måste köras igen, till exempel ett uppdaterat utbildnings skript och hoppar över vad som inte har ändrats. Samma paradigmet gäller oförändrade skript som används för körningen av steget. Den här åter användnings funktionen hjälper till att undvika att köra kostsamma och tids krävande steg som data inmatning och omvandling om underliggande data inte har ändrats.

Med Azure Machine Learning kan du använda olika verktyg och ramverk, till exempel PyTorch eller TensorFlow, för varje steg i din pipeline. Azure-koordinater mellan de olika [beräknings målen](concept-azure-machine-learning-architecture.md) som du använder, så att dina mellanliggande data kan delas med de underordnade beräknings målen enkelt.

Du kan [spåra måtten för dina pipelines experiment](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) direkt i Azure Portal eller i [landnings sidan för din arbets yta (för hands version)](https://ml.azure.com). När en pipeline har publicerats kan du konfigurera en REST-slutpunkt, vilket gör att du kan köra pipelinen igen från valfri plattform eller stack.

## <a name="key-advantages"></a>Viktiga fördelar

De främsta fördelarna med att använda pipeliner för dina Machine Learning-arbetsflöden är:

|Stor fördel|Beskrivning|
|:-------:|-----------|
|**Obevakad&nbsp;körs**|Schemalägg steg som ska köras parallellt eller i följd på ett tillförlitligt och obevakat sätt. Förberedelse av data och modellering kan senaste dagar eller veckor, och pipelines gör att du kan fokusera på andra uppgifter medan processen körs. |
|**Heterogena-beräkning**|Använd flera pipelines som är tillförlitligt koordinerade över heterogena och skalbara beräknings resurser och lagrings platser. Effektiv användning av tillgängliga beräknings resurser genom att köra enskilda pipeline-steg för olika beräknings mål, till exempel HDInsight, GPU: ar för data vetenskap och Databricks.|
|**Återanvändningsmöjligheter**|Skapa pipeline-mallar för vissa scenarier, till exempel omskolning och batch-poäng. Utlös publicerade pipelines från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultat Sök vägar när du itererar, använder du pipelines SDK för att explicit namnge och version av data källor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|
|**Samarbete**|Pipelines gör det möjligt för data experter att samar beta över alla områden i design processen för maskin inlärning, samtidigt som de kan arbeta med pipeline-steg samtidigt.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK för pipelines

[Använd python SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) för att skapa dina ml-pipeliner i dina önskade Integrated Development Environment (IDE) eller Jupyter-anteckningsböcker. SDK: N för Azure Machine Learning erbjuder tvingande konstruktioner för ordningsföljd och parallellisera stegen i dina pipelines när inget databeroende finns. 

Du kan med hjälp av deklarativa databeroenden, för att optimera dina uppgifter. SDK innehåller ett ramverk av förbyggda moduler för vanliga uppgifter, till exempel data överföring och modell publicering. Du kan utöka ramverket för att modellera dina egna konventioner genom att implementera anpassade steg återanvändbara över pipeliner. Du kan också hantera beräknings mål och lagrings resurser direkt från SDK: n.

Spara dina pipelines som mallar och distribuera dem till en REST-slutpunkt för batch-poäng eller återträna jobb.

Det finns två python-paket för pipelines med Azure Machine Learning: [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och [azureml-pipeline – steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Kom igång snabbt genom att använda en av de fördefinierade modulerna, till exempel:

* Köra Python-skript i ett steg med [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)
* Överföra data mellan lagrings alternativ med [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)
* Skapa ett AutoML pipeline-steg med [AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep)

## <a name="next-steps"></a>Nästa steg

+ Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md).

+ Lär dig hur du [kör batch-förutsägelser på stora data](tutorial-pipeline-batch-scoring-classification.md).

+ Se [SDK-referens dokument för pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py).

+ Prova Jupyter Notebooks som demonstrerar [Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Lär dig hur du [Kör antecknings böcker för att utforska den här tjänsten](samples-notebooks.md).
