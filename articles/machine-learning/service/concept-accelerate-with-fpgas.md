---
title: Vad är en FPGA? – Project Brainwave – Azure Machine Learning
description: Lär dig att påskynda modeller och djupa neurala nätverk med FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646772"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Vad är FPGA och Project Brainwave?

Den här artikeln innehåller en introduktion till fältet-programmable gate matriser (FPGA) och hur FPGA är integrerat med Azure machine learning för att tillhandahålla AI i realtid.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. CPU, GPU och ASIC

FPGA innehåller en matris av programmerbara logiska block och en hierarki av omkonfigurerbara anslutningar som gör att de block som ska konfigureras på olika sätt när tillverkning.

FPGA innehåller en kombination av programmering och jämförs med prestanda för andra kretsar:

![Jämförelse mellan Azure Machine Learning FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Enheter (CPU)** är allmänna processorer. CPU-prestanda är inte idealiskt för grafik och video bearbetning.
- **Grafikprocessorer (GPU)** ger parallell bearbetning och är ett populärt alternativ för AI-beräkningar. Den parallell bearbetning med GPU: er resulterar i snabbare bildåtergivning än processorer.
- **Programspecifika integrerade kretsar (ASIC)**, till exempel Googles TensorFlow Processor enheter är anpassade kretsar. Dessa kretsar ger högsta möjliga effektivitet, är ASICs oflexibel.
- **FPGA**, till exempel de som är tillgängliga på Azure, ger prestanda nära ASIC, men erbjuder flexibilitet konfigureras senare.

FPGA är nu i varje ny Azure-server. Microsoft använder redan FPGA för Bing-sökresultat rangordna djupa neurala nätverk (DNN)-utvärderingsversion och programvarudefinierade nätverk (SDN)-acceleration. Dessa FPGA-implementeringar minska svarstiden samtidigt som processorer för andra aktiviteter.

## <a name="project-brainwave-on-azure"></a>Project Brainwave på Azure

Project Brainwave är en maskinvaruarkitektur med som är utformad baserat på Intels FPGA enheter och används för att påskynda i realtid AI-beräkningar. Med den här ekonomiskt arkitekturen FPGA-aktiverade, kan du köra en tränad neuralt nätverk så snabbt som möjligt och med kortare svarstider. Project Brainwave kan parallellisera förtränade dnn: er i FPGA att skala ut din tjänst.

- Prestanda

    FPGA gör det möjligt att uppnå med låg latens för i realtid inferensjobb begäranden. Batchbearbetning innebär att dela upp en begäran i mindre delar och skicka dem till en processor att förbättra maskinvaruutnyttjandet. Batchbearbetning är inte effektiva och kan orsaka svarstid. Brainwave kräver inte batchbearbetning; svarstiden är därför 10 gånger lägre jämfört med processor- och GPU.

- Flexibilitet

    FPGA kan konfigureras för olika typer av machine learning-modeller. Den här flexibiliteten gör det enklare att påskynda de programmen baserat på den mest optimala numerisk precision och modellen i minnet som används.

    Nya machine learning-teknik håller på att utvecklas med jämna mellanrum och även Project Brainwave maskinvara design utvecklas snabbt. Eftersom FPGA är omkonfigurerbara, är det möjligt att hålla dig uppdaterad med kraven för snabbt föränderliga AI-algoritmer.

- Skala

    Microsoft Azure är världens största molninvestering i FPGA. Du kan köra Brainwave i Azures skala infrastruktur.

En förhandsversion av Project Brainwave integrerat med Azure Machine Learning är tillgänglig. Och en begränsad förhandsversion är också tillgänglig så att Project Brainwave till gränsen så att du kan dra nytta av den hastigheten i dina egna företag och verksamhet.

Brainwave är begränsad till TensorFlow-distribution och ResNet50-baserade neurala nätverk på Intel FPGA maskinvara för klassificering av avbildning och igenkänning av aktuella förhandsversion. Det finns planer på att stöd för flera modeller i galleriet och andra ramverk.

Följande scenarier använder FPGA i Project Brainwave arkitektur:

- Automatiserad optiska kontrollsystem. Se [Real-time AI: Microsoft presenterar en förhandsversion av Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Mark cover-mappning. Se [hur du använder FPGA för Djupinlärning Inferens att utföra mark Cover mappning på terabyte Flygfoto avbildningar](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Hur du distribuerar en webbtjänst till en FPGA?

Övergripande flöde för att skapa en avbildning av tjänst i Azure med ResNet50 som en upplärda är följande:

1. ResNet50 redan har distribuerats i Brainwave. Du kan skapa andra diagram (datum indata, klassificering och så vidare) med TensorFlow och definierar en pipeline (indata -> förtränade -> klassificera) med hjälp av en service definition json-fil. Komprimera definitions- och diagram till en zip-fil och ladda upp zip-filen till Azure Blob storage.
2. Registrera modellen med Azure ML-modellen Management-API med zip-filen i Blob storage.
3. Distribuera tjänsten med den registrerade modellen med hjälp av Azure ML-modellen Management-API.

Mer information om den här processen i artikeln [distribuera en modell som en webbtjänst på en FPGA med Azure Machine Learning](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Börja använda FPGA

Azure Machine Learning maskinvara Accelerated modeller kan du distribuera anpassade DNN-modeller till FPGA i Azure-molnet. Kom igång, se:

- [Distribuera en modell som en webbtjänst på en FPGA](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning-maskinvara Accelerated modeller som drivs av Project Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Nästa steg

[Distribuera en modell som en webbtjänst på en FPGA](how-to-deploy-fpga-web-service.md)

[Lär dig att installera FPGA SDK](reference-fpga-package-overview.md)

[Hyperskala maskinvara: ML skalenligt ovanpå Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[Inom Microsoft FPGA-baserat konfigurerbart moln (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft presenterar Project Brainwave för AI i realtid](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)