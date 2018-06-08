---
title: Vad är en då FPGA? – Projekt Brainwave – Azure Machine Learning
description: Lär dig mer om att påskynda modeller och djupa neurala nätverk med FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: service
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 18b0ee143443ecf128bdfe57adb61cc22f95db87
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832592"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Vad är då FPGA och projektet Brainwave?

Den här artikeln innehåller en introduktion till fältet programmable gate matriser (då FPGA) och hur då FPGA är integrerat med Azure maskininlärning att ge realtidsskydd AI.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. Processorn och GPU ASIC

FPGAs innehåller en matris av programmerbara logiska block och en hierarki av omvandlingsbara anslutningar som tillåter block konfigureras på olika sätt efter tillverkning.

FPGAs innehåller en kombination av programmering och prestanda genom att jämföra till andra kretsar:

![Jämförelse mellan Azure Machine Learning då FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Enheter (CPU)** är allmänna processorer. CPU-prestanda är inte idealiskt för grafik och video bearbetning.
- **Grafikprocessorer (GPU)** erbjuder parallell bearbetning och är ett populärt alternativ för AI-beräkningar. Den parallell bearbetning med GPU resultat i snabbare avbildningen återgivning än processorer.
- **Programspecifika integrerade kretsar (ASIC)**, till exempel Google TensorFlow Processor enheter är anpassade kretsar. Även om dessa chip ger högsta effektivitet, är ASIC statiskt.
- **FPGAs**, till exempel de som finns på Azure, ger prestanda nära ASIC, men ger möjlighet att konfigureras senare.

FPGAs är nu i varje ny Azure-server. Microsoft använder redan FPGAs för Bing söka rangordnings djupa neurala nätverk (DNN)-utvärderingsversion och programvarudefinierade nätverksfunktioner (SDN) acceleration. Dessa då FPGA implementeringar minska svarstiden samtidigt som processorer för andra aktiviteter.

## <a name="project-brainwave-on-azure"></a>Projektet Brainwave på Azure

Projektet Brainwave är en maskinvaruarkitektur med som är utformad baserat på Intels då FPGA enheter och används för att påskynda realtid AI-beräkningar. Med den här ekonomiska arkitekturen då FPGA-aktiverade kan du köra en tränad neurala nätverket så snabbt som möjligt och med kortare svarstid. Projektet Brainwave kan parallelize före utbildade DNNs över FPGAs att skala upp din tjänst.

- Prestanda

    FPGAs gör det möjligt att uppnå låg latens för realtid inferencing begäranden. Batchbearbetning innebär att dela upp en begäran i mindre delar och mata dem till en processor att förbättra användning av maskinvara. Batchbearbetning är inte effektivt och kan orsaka svarstid. Brainwave kräver inte batchbearbetning; svarstiden är därför 10 gånger lägre jämfört med Processorn och GPU.

- Flexibilitet

    FPGAs kan konfigureras för olika typer av machine learning-modeller. Den här flexibiliteten gör det enklare att påskynda program baserat på den mest optimala numerisk precision och modellen i minnet som används.

    Nya machine learning tekniker utvecklas regelbundet och projektet Brainwave maskinvara design också utvecklas snabbt. Eftersom FPGAs är omvandlingsbara, är det möjligt att hålla dig informerad om kraven för förändras snabbt AI-algoritmer.

- Skala

    Microsoft Azure är världens största molnet investering i FPGAs. Du kan köra Brainwave på Azures skalbara infrastruktur.

En förhandsgranskning av projektet Brainwave integrerat med Azure Machine Learning är tillgänglig. Och en begränsad förhandsgranskning är också tillgänglig för att göra projektet Brainwave kant så att du kan dra nytta av att hastighet i företag och verksamhet.

I aktuella preview är Brainwave begränsad till TensorFlow distributionen och neural ResNet50-baserade nätverk på Intel då FPGA maskinvara för avbildningen klassificering och Igenkännande. Det finns planer för att stödja flera modeller i galleriet och andra ramverk.

Följande scenarier använder då FPGA i projektet Brainwave arkitektur:

- Automatiserad optical kontrollsystem. Se [realtid AI: Microsoft tillkännager förhandsgranskning av projektet Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Mark omfattar mappning. Se [hur du använder FPGAs för djup Learning härledning att utföra mark omfattar mappning på terabyte Flygfoto bilder](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-create-an-fpga-service"></a>Så här skapar du en då FPGA tjänst?

Övergripande flödet för att skapa en avbildning recognition tjänst i Azure med hjälp av ResNet50 som en featurizer är följande:

1. ResNet50 redan har distribuerats i Brainwave. Du kan skapa andra diagram (datum indata, klassificering och så vidare) med TensorFlow och definiera en pipeline (input -> featurize -> klassificera) med hjälp av en service definition json-fil. Komprimera definition och diagram till en zip-fil och ladda upp zip-filen till Azure Blob storage.
2. Registrera modellen med zip-filen i Blob storage Management API för Azure ML-modellen.
3. Distribuera tjänsten med registrerade modellen med hjälp av Azure ML-modell Management API.

En självstudiekurs finns [Azure ML maskinvara snabbare modeller Quickstart](https://github.com/Azure/aml-real-time-ai/blob/master/notebooks/resnet50/00_QuickStart.ipynb).

## <a name="start-using-fpga"></a>Börja använda då FPGA

Azure Machine Learning maskinvara snabbare modeller kan du distribuera utbildade DNN modeller till FPGAs i Azure-molnet. Om du vill komma igång, se:

- [Distribuera en modell som en webbtjänst på en då FPGA](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning maskinvara snabbare modeller som tillhandahålls av projektet Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Nästa steg

[Distribuera en modell som en webbtjänst på en då FPGA](how-to-deploy-fpga-web-service.md)

[Lär dig hur du installerar FPGA SDK](reference-fpga-package-overview.md)

[Storskaliga maskinvara: ML i skala ovanpå Azure + då FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[I Microsoft då FPGA-baserade konfigurerbara molnet (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft presenterar projektet Brainwave för realtid AI](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)