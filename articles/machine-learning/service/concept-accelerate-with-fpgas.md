---
title: Vad är en FPGA och Project Brainwave? – Azure Machine Learning-tjänsten
description: Lär dig att påskynda modeller och djupa neurala nätverk med FPGA på Azure. Den här artikeln innehåller en introduktion till fältet-programmable gate matriser (FPGA) och hur Azure Machine Learning-tjänsten tillhandahåller i realtid artificiell intelligens (AI) när du distribuerar din modell till en Azure-FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: 411beacd65915c30338ab415b095acc1a0c8cbe6
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238863"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Vad är FPGA och Project Brainwave?

Den här artikeln innehåller en introduktion till fältet-programmable gate matriser (FPGA) och hur Azure Machine Learning-tjänsten tillhandahåller i realtid artificiell intelligens (AI) när du distribuerar din modell till en Azure-FPGA.

FPGA innehåller en matris av programmerbara logiska block och en hierarki av omkonfigurerbara sammanbinder. Den sammanbinder Tillåt rutorna konfigureras i olika sätt efter tillverkning. FPGA ger en kombination av programmering och prestanda jämfört med andra kretsar.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. CPU, GPU och ASIC

![Azure Machine Learning-tjänsten FPGA jämförelse](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processor||Beskrivning|
|---|:-------:|------|
|Programspecifika integrerade kretsar|ASICs|Anpassade kanaler, till exempel Googles TensorFlow Processor enheter (TPU), ger högsta möjliga effektivitet. De kan inte konfigureras när dina behov ändras.|
|Fältet-programmable gate-matriser|FPGA|FPGA, till exempel de som är tillgängliga på Azure, ger prestanda nära ASICs, men är flexibla och omkonfigurerbara över tid för att implementera logik som är nya.|
|Bearbetningsenheter för grafik|GPU: er|Ett populärt alternativ för AI-beräkningar som erbjuder parallellbearbetning funktioner som gör det snabbare på bildåtergivning än processorer.|
|Enheter|CPU:er|Allmänna processorer vars prestanda inte är idealiskt för grafik och video bearbetning.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave på Azure

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) är Microsofts ekonomiskt maskinvaruarkitektur baserat på Intels FPGA enheter att dataexperter och utvecklare som använder för att påskynda i realtid AI-beräkningar.  Den här FPGA-aktiverade arkitekturen erbjuder **prestanda**, **flexibilitet**, och **skala** och är tillgänglig på Azure.

**FPGA gör det möjligt att uppnå med låg latens för i realtid inferensjobb begäranden.** Batchbearbetning innebär att samla in en stor mängd data och skicka den till en processor att förbättra maskinvaruutnyttjandet. Batchbearbetning kan orsaka svarstid eftersom mer data som behöver bearbetas, men det kan förbättra dataflödet. Project Brainwave implementeringar av neurala bearbetningsenheter kräver inte batchbearbetning; Därför kan fördröjningen vara många gånger lägre jämfört med processor- och GPU.

### <a name="reconfigurable-power"></a>Omkonfigurerbara power
**FPGA kan konfigureras för olika typer av machine learning-modeller.** Den här flexibiliteten gör det enklare att påskynda de programmen baserat på den mest optimala numerisk precision och modellen i minnet som används.

Nya machine learning-teknik håller på att utvecklas med jämna mellanrum och även Project Brainwave maskinvara design utvecklas snabbt. Eftersom FPGA är omkonfigurerbara, är det möjligt att hålla dig uppdaterad med kraven för snabbt föränderliga AI-algoritmer.

### <a name="whats-supported-on-azure"></a>Vad som stöds på Azure
**Microsoft Azure är världens största molninvestering i FPGA.** Du kan köra Project Brainwave i Azures skala infrastruktur.

Idag, stöder Project Brainwave:
+ Bild klassificering och igenkänning av scenarier
+ TensorFlow-distribution
+ Dnn: er: ResNet-50, ResNet 152, VGG-16, SSD-VGG och DenseNet 121
+ Intel FPGA maskinvara 

Med den här FPGA-aktiverade maskinvaruarkitektur tränade neurala nätverk sker snabbt och kortare svarstider. Project Brainwave kan parallellisera förtränade djupa neurala nätverk (DNN) över FPGA att skala ut din tjänst. De dnn: er kan tränas före, som en djupgående upplärda för överföring learning eller finjusterat med uppdaterade vikterna.

### <a name="scenarios-and-applications"></a>Scenarier och program

Project Brainwave är integrerad med Azure Machine Learning. Microsoft använder FPGA för DNN-utvärdering, Bing search rangordning och programvara definierats networking (SDN)-acceleration för att minska svarstiden samtidigt som processorer för andra aktiviteter.

Följande scenarier använder FPGA i Project Brainwave arkitektur:
+ [Automatiserad optiska kontrollsystem](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mark cover mappning](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Distribuera till FPGA på Azure

Här är arbetsflödet för att skapa en avbildning av tjänst i Azure med stöds dnn: er som en upplärda för distribution på Azure FPGA:

1. Använd Azure Machine Learning-SDK för Python för att skapa en tjänstdefinition av som är en fil som beskriver en pipeline med diagram (indata, upplärda och klassificerare) baserat på TensorFlow. Distributionskommandot automatiskt komprimera definitions- och diagram i en ZIP-fil och ladda upp ZIP-filen till Azure Blob storage.  DNN redan har distribuerats på Project Brainwave ska köras på FPGA.

1. Registrera modellen med hjälp av SDK med ZIP-filen i Azure Blob storage.

1. Distribuera tjänsten med den registrerade modellen med hjälp av SDK.

Du kan börja distribuera den anpassade DNN modeller som FPGA i Azure i molnet med den här artikeln **”[distribuera en modell som en webbtjänst på en FPGA](how-to-deploy-fpga-web-service.md)”**.


## <a name="next-steps"></a>Nästa steg

Kolla in dessa videor och bloggar:

+ [Hyperskala maskinvara: ML skalenligt ovanpå Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Inom Microsoft FPGA-baserat konfigurerbart moln (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave för i realtid AI: projektets startsida](https://www.microsoft.com/research/project/project-brainwave/)
