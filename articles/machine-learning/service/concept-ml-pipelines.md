---
title: 'Pipelines: optimera Machine Learning-arbetsflöden'
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
ms.openlocfilehash: a9965dbbca939f566048312af921061a188ee50d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884249"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>Bygg återanvändbara ML-pipeliner i Azure Machine Learning-tjänsten

I den här artikeln lär du dig om de pipelines i Machine Learning som du kan bygga med Azure Machine Learning SDK för python och fördelarna med att använda pipeliner.

## <a name="what-are-machine-learning-pipelines"></a>Vad är machine learning pipelines?

Med machine learning (ML) pipelines, dataexperter, datatekniker och IT-proffs kan samarbeta med stegen som ingår i:
+ Förberedelse av data, till exempel normalizations och omvandlingar
+ Modellträning
+ Modell-utvärdering
+ Distribution

I följande diagram visas ett exempel på en pipeline-process:

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

Du kan använda [Azure Machine Learning SDK för python](#the-python-sdk-for-pipelines) för att skapa ml-pipelines, samt för att skicka och spåra enskilda pipeline-körningar.

Du kan optimera ditt arbetsflöde med enkelhet, hastighet, portabilitet och återanvändning med pipelines. När du skapar pipeliner med Azure Machine Learning kan du fokusera på din expertis, maskin inlärning i stället för infrastruktur och automatisering.

Pipelines skapas utifrån flera **steg**, som är distinkta beräknings enheter i pipelinen. Varje steg kan köras oberoende och använda isolerade beräknings resurser. Detta gör det möjligt för flera data forskare att arbeta med samma pipeline samtidigt utan beskattnings beräknings resurser och gör det också enkelt att använda olika beräknings typer/storlekar för varje steg.

Efter att pipelinen har utformats finns det ofta mer fin justering av pipelinens inlärnings slinga. När du kör en pipeline igen fortsätter körningen till de distinkta stegen som måste köras igen, till exempel ett uppdaterat utbildnings skript och hoppar över vad som inte har ändrats. Samma paradigmet gäller oförändrade skript som används för körningen av steget. Den här funktionen hjälper till att undvika att köra kostsamma och tids krävande steg som data inmatning och omvandling om underliggande data inte har ändrats.

Med Azure Machine Learning kan du använda olika verktyg och ramverk, till exempel PyTorch eller TensorFlow, för varje steg i din pipeline. Azure-koordinater mellan de olika [beräknings målen](concept-azure-machine-learning-architecture.md) som du använder, så att dina mellanliggande data kan delas med de underordnade beräknings målen enkelt.

Du kan [spåra mått för din pipeline-experiment](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) direkt i Azure-portalen. När en pipeline har publicerats kan du konfigurera en REST-slutpunkt som gör att du kan köra pipelinen igen från vilken plattform eller stack som helst.

## <a name="key-advantages"></a>Viktiga fördelar

De främsta fördelarna med att använda pipeliner för dina Machine Learning-arbetsflöden är:

|Stor fördel|Beskrivning|
|:-------:|-----------|
|**Obevakad&nbsp;körs**|Schemalägg steg som ska köras parallellt eller i följd på ett tillförlitligt och obevakat sätt. Förberedelse av data och modellering kan senaste dagar eller veckor, och pipelines gör att du kan fokusera på andra uppgifter medan processen körs. |
|**Heterogena-beräkning**|Använd flera pipelines som är tillförlitligt koordinerade över heterogena och skalbara beräknings resurser och lagrings platser. Kör enskilda pipeline-steg på olika beräknings mål, till exempel HDInsight, GPU: ar för data vetenskap och Databricks. Detta ger effektiv användning av tillgängliga beräknings alternativ.|
|**Återanvändningsmöjligheter**|Skapa pipeline-mallar för vissa scenarier, till exempel omskolning och batch-poäng. Utlös publicerade pipelines från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultat Sök vägar när du itererar, använder du pipelines SDK för att explicit namnge och version av data källor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|
|**Samarbete**|Pipelines gör det möjligt för data experter att samar beta över alla områden i design processen för maskin inlärning, samtidigt som de kan arbeta med pipeline-steg samtidigt.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK för pipelines

Använda Python för att skapa ML-pipelines. SDK: N för Azure Machine Learning erbjuder tvingande konstruktioner för ordningsföljd och parallellisera stegen i dina pipelines när inget databeroende finns. Du kan interagera med den i Jupyter-anteckningsböcker eller i en annan önskad IDE.

Du kan med hjälp av deklarativa databeroenden, för att optimera dina uppgifter. SDK innehåller ett ramverk av förbyggda moduler för vanliga uppgifter, till exempel data överföring och modell publicering. Du kan utöka ramverket till att modellera dina egna konventioner genom att implementera anpassade steg som kan återanvändas över pipeliner. Du kan också hantera beräknings mål och lagrings resurser direkt från SDK: n.

Du kan spara pipelines som mallar och distribuera dem till en REST-slutpunkt så att du kan schemalägga batch-poäng eller återträna jobb.

Information om hur du skapar egna finns i [referens dokument för python SDK för pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och antecknings boken i nästa avsnitt.

## <a name="example-notebooks"></a>Exempel-anteckningsböcker

Följande anteckningsböcker demonstrera pipelines med Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md).
