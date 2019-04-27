---
title: Vad är FPGA och Project Brainwave?
titleSuffix: Azure Machine Learning service
description: Lär dig att påskynda modeller och djupa neurala nätverk med FPGA på Azure. Den här artikeln innehåller en introduktion till fältet-programmable gate matriser (FPGA) och hur Azure Machine Learning-tjänsten tillhandahåller i realtid artificiell intelligens (AI) när du distribuerar din modell till en Azure-FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 1/10/2019
ms.custom: seodec18
ms.openlocfilehash: 716de6d491be753c5c32c0a2774404140010f72c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821215"
---
# <a name="what-are-fpgas-and-project-brainwave"></a>Vad är FPGA och Project Brainwave?

Den här artikeln innehåller en introduktion till fältet-programmable gate matriser (FPGA) och hur Azure Machine Learning-tjänsten tillhandahåller i realtid artificiell intelligens (AI) när du distribuerar din modell till en Azure-FPGA.

FPGA innehåller en matris av programmerbara logiska block och en hierarki av omkonfigurerbara anslutningar. Den sammanbinder Tillåt rutorna konfigureras på olika sätt när tillverkning. FPGA ange jämfört med andra kretsar, en kombination av programmering och prestanda.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. CPU, GPU och ASIC

Följande diagram och tabell som visar hur FPGA jämfört med andra processorer.

![Diagram över Azure Machine Learning-tjänsten FPGA jämförelse](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processor||Beskrivning|
|---|:-------:|------|
|Programspecifika integrerade kretsar|ASICs|Anpassade kanaler, till exempel Googles TensorFlow Processor enheter (TPU), ger högsta möjliga effektivitet. De kan inte konfigureras när dina behov ändras.|
|Fältet-programmable gate-matriser|FPGA:er|FPGA, till exempel de som är tillgängliga på Azure, ger prestanda nära ASICs. De är också flexibelt och omkonfigurerbara över tid, för att implementera logik som är nya.|
|Bearbetningsenheter för grafik|GPU: er|Ett populärt alternativ för AI-beräkningar. GPU: er erbjuder funktioner för parallell bearbetning, vilket gör det snabbare på bildåtergivning än processorer.|
|Enheter|CPU:er|Allmänna processorer, som inte är perfekt för grafik och bearbetning av livevideo.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave på Azure

[Project Brainwave](https://www.microsoft.com/en-us/research/project/project-brainwave/) är en maskinvaruarkitektur från Microsoft. Den är baserad på Intels FPGA enheter, vilka dataexperter och utvecklare använder för att påskynda i realtid AI-beräkningar. Den här arkitekturen för FPGA-aktiverade erbjuder prestanda, flexibilitet och skalbarhet och är tillgängliga på Azure.

FPGA gör det möjligt att uppnå med låg latens för i realtid inferensjobb begäranden. Asynkrona begäranden (batchbearbetning) behövs inte. Batchbearbetning kan orsaka svarstid, eftersom mer data som behöver bearbetas. Project Brainwave implementeringar av neurala bearbetningsenheter kräver inte batchbearbetning; fördröjningen kan därför många gånger lägre jämfört med processor- och GPU-processorer.

### <a name="reconfigurable-power"></a>Omkonfigurerbara power
Du kan konfigurera om FPGA för olika typer av machine learning-modeller. Den här flexibiliteten gör det enklare att påskynda de programmen baserat på den mest optimala numerisk precision och modellen i minnet som används. Eftersom FPGA är omkonfigurerbara kan du Håll dig uppdaterad med kraven för att snabbt förändrade AI-algoritmer.

### <a name="whats-supported-on-azure"></a>Vad som stöds på Azure
Microsoft Azure är världens största molninvestering i FPGA. Du kan köra Project Brainwave i Azures skala infrastruktur.

Idag, stöder Project Brainwave:
+ Bild klassificering och igenkänning av scenarier
+ TensorFlow-distribution
+ Dnn: er: ResNet-50, ResNet 152, VGG-16, SSD-VGG och DenseNet 121
+ Intel FPGA maskinvara 

Med den här FPGA-aktiverade maskinvaruarkitektur tränade neurala nätverk sker snabbt och kortare svarstider. Project Brainwave kan parallellisera förtränade djupa neurala nätverk (DNN) över FPGA att skala ut din tjänst. De dnn: er kan tränas före, som en djupgående upplärda för överföring learning eller finjusterat med uppdaterade vikterna.

### <a name="scenarios-and-applications"></a>Scenarier och program

Project Brainwave är integrerad med Azure Machine Learning. Microsoft använder FPGA för DNN-utvärdering, Bing search rangordning och programvara definierats networking (SDN)-acceleration för att minska svarstiden, samtidigt som processorer för andra aktiviteter.

Följande scenarier använder FPGA i Project Brainwave arkitektur:
+ [Automatiserad optiska kontrollsystem](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mark cover mappning](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Distribuera till FPGA på Azure

För att skapa en avbildning av tjänst i Azure, kan du använda stöds dnn: er som en upplärda för distribution på Azure FPGA:

1. Använd den [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk) att skapa en tjänstdefinition. En tjänstdefinition är en fil som beskriver en pipeline med diagram (indata, upplärda och klassificerare) baserat på TensorFlow. Distributionskommandot automatiskt komprimerar definitions- och diagram till en ZIP-fil och laddar upp ZIP-filen till Azure Blob storage. DNN redan har distribuerats på Project Brainwave ska köras på FPGA.

1. Registrera modellen med hjälp av SDK: N med ZIP-filen i Azure Blob storage.

1. Distribuera tjänsten med den registrerade modellen med hjälp av SDK.

Kom igång med att distribuera anpassade DNN-modeller till FPGA i Azure-molnet, se [distribuera en modell som en webbtjänst på en FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Nästa steg

Kolla in dessa videor och bloggar:

+ [Hyperskala maskinvara: ML skalenligt ovanpå Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Inom Microsoft FPGA-baserat konfigurerbart moln (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave för i realtid AI: projektets startsida](https://www.microsoft.com/research/project/project-brainwave/)
