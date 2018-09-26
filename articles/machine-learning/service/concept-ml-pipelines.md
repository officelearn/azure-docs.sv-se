---
title: Skapa machine learning pipelines – Azure Machine Learning-tjänst
description: Läs mer om de machine learning pipelines som du kan skapa med Azure Machine Learning-SDK för Python och fördelarna med att använda pipelines i den här artikeln. Machine learning (ML) pipelines som används av datatekniker att bygga, optimera och hantera sina arbetsflöden för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: e1181d07ed51b1edf5da68d759d871703bd1540f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162788"
---
# <a name="pipelines-and-azure-machine-learning"></a>Pipelines och Azure Machine Learning

Läs mer om de machine learning pipelines som du kan skapa med Azure Machine Learning-SDK för Python och fördelarna med att använda pipelines i den här artikeln.

## <a name="what-are-machine-learning-pipelines"></a>Vad är machine learning pipelines?

Machine learning (ML) pipelines som används av datatekniker att bygga, optimera och hantera sina arbetsflöden för maskininlärning. En typisk pipeline omfattar en sekvens med steg som täcker följande områden:

+ Förberedelse av data, till exempel normalizations och omvandlingar
+ Modellera utbildning, till exempel hyper parametern justering och validering
+ Distribution av modeller och utvärdering  

I följande diagram visas en exempel-pipeline:

[ ![Machine learning pipelines i Azure Machine Learning-tjänsten](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Varför ska man skapa pipelines med Azure Machine Learning?

Den [Azure Machine Learning-SDK för Python](#the-python-sdk-for-pipelines) kan användas för att skapa ML pipelines samt att skicka och spåra individuella pipelinekörningar.

Du kan optimera ditt arbetsflöde med enkelhet, hastighet, portabilitet och återanvändning med pipelines. När du skapar pipelines med Azure Machine Learning kan du fokusera på dina kunskaper bäst &mdash; maskininlärning &mdash; istället för infrastruktur.

Med hjälp av steg gör det möjligt att köra bara de steg som du måste du justera och testa ditt arbetsflöde. Ett steg är en beräkningsenhet i pipelinen. I diagrammet ovan visas uppgiften att förbereda data kan omfatta många steg, inklusive men begränsat inte till, normalisering, transformation, verifiering och funktionalisering.

När är utformat för pipelinen, är det ofta mer finjustera runt loopen utbildning för pipelinen. När du kör en pipeline, kör hoppar till de steg som måste köras igen, till exempel en uppdaterad inlärningsskript och hoppar över vad som inte har ändrats. Samma paradigmet gäller oförändrade skript som används för körningen av steget. 

Med Azure Machine Learning, kan du använda olika verktyg och ramverk som Microsoft Cognitive Toolkit eller TensorFlow för varje steg i din pipeline. Azure koordinater mellan de olika [beräkningsmål](concept-azure-machine-learning-architecture.md) du använder så att dina mellanliggande data kan delas med de underordnade beräkningsmål enkelt. 

## <a name="key-advantages"></a>Viktiga fördelar

Viktiga fördelar för att skapa pipelines för din machine learning-arbetsflöden är:

|Stor fördel|Beskrivning|
|:-------:|-----------|
|**Obevakad&nbsp;körs**|Schemalägga några steg för att köra parallellt eller i följd i en tillförlitlig och obevakat sätt. Sedan dataförberedelser och modellering kan senast dagar eller veckor, du kan nu fokusera på andra uppgifter medan din pipeline körs. |
|**Blandade och skilda beräkning**|Använda flera pipelines som på ett tillförlitligt sätt samordnas i heterogena och skalbar beräkningar och lagringsutrymmen. Enskilda pipeline-stegen kan köras på olika beräkning som mål, till exempel HDInsight, GPU Data Science virtuella datorer och Databricks, för att effektivisera användning av tillgängliga beräkningsalternativ.|
|**Återanvändningsmöjligheter**|Pipelines kan vara templatized för specifika scenarier, till exempel träna och batchbedömning.  De kan aktiveras från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultatet sökvägar som du iterera använda pipelines SDK att uttryckligen ge ett namn och version dina data datakällor, indata och utdata samt hantera skript och data separat för ökad produktivitet.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK för pipelines

Använda Python för att skapa ML-pipelines. SDK: N för Azure Machine Learning erbjuder tvingande konstruktioner för ordningsföljd och parallellisera stegen i dina pipelines när inget databeroende finns. Du kan interagera med den i Jupyter-anteckningsböcker eller i en annan önskade IDE. 

Du kan med hjälp av deklarativa databeroenden, för att optimera dina uppgifter. SDK innehåller ett ramverk med färdiga moduler för vanliga uppgifter, som data transfer, compute target skapande och modellera publicering. Ramverket kan utökas för att modellera egna konventioner genom att implementera anpassade steg som är återanvändbara för pipelines.

Pipelines kan sparas som en mall och kan distribueras till en REST-slutpunkt så att du kan schemalägga jobb i batch-bedömning eller omtränings.

Kolla in den [Python SDK referensdokument för pipelines](http://aka.ms/aml-sdk) och anteckningsboken i nästa avsnitt för att se hur du skapar dina egna.

## <a name="example-notebooks"></a>Exempel-anteckningsböcker
 
Följande anteckningsboken visar pipelines med Azure Machine Learning: `pipeline/pipeline-batch-scoring.ipynb`.
 
Hämta dessa anteckningsböcker:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
