---
title: 'MLOps: ML modellhantering'
titleSuffix: Azure Machine Learning
description: 'Lär dig mer om modellhantering med Azure Machine Learning (MLOps). Distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 40e335ee0377c560b15a588269cbdb39cdebca82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477365"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Modellhantering, distribution och övervakning med Azure Machine Learning

I den här artikeln får du lära dig mer om hur du använder Azure Machine Learning för att hantera livscykeln för dina modeller. Azure Machine Learning använder en MPP-metod (Machine Learning Operations). MLOps förbättrar kvaliteten och konsekvensen i dina maskininlärningslösningar. 

## <a name="what-is-mlops"></a>Vad är MLOps?

Machine Learning Operations (MLOps) baseras på [DevOps](https://azure.microsoft.com/overview/what-is-devops/) principer och metoder som ökar effektiviteten i arbetsflöden. Till exempel kontinuerlig integrering, leverans och distribution. MLOps tillämpar dessa huvudmän på maskininlärningsprocessen, med målet att:

* Snabbare experiment och utveckling av modeller
* Snabbare användning av modeller i produktion
* Kvalitetssäkring

Azure Machine Learning innehåller följande MLOps-funktioner:

- **Skapa reproducerbara ML-pipelines**. Med Machine Learning-pipelines kan du definiera repeterbara och återanvändbara steg för dina processer för förberedelse, utbildning och bedömning av data.
- **Skapa återanvändbara programvarumiljöer** för utbildning och distribution av modeller.
- **Registrera, paketera och distribuera modeller var som helst**. Du kan också spåra associerade metadata som krävs för att använda modellen.
- **Samla in styrningsdata för ml-livscykeln från slut-till-slut**. Den loggade informationen kan inkludera vem som publicerar modeller, varför ändringar gjordes och när modeller har distribuerats eller använts i produktionen.
- **Meddela och avisera om händelser i ML-livscykeln**. Till exempel slutförande av experiment, modellregistrering, modelldistribution och identifiering av datadrift.
- **Övervaka ML-program för drifts- och ML-relaterade problem**. Jämför modellindata mellan utbildning och slutledning, utforska modellspecifika mätvärden och tillhandahålla övervakning och aviseringar på ml-infrastrukturen.
- **Automatisera ml-livscykeln från sluten till med Azure Machine Learning och Azure Pipelines**. Med hjälp av pipelines kan du ofta uppdatera modeller, testa nya modeller och kontinuerligt rulla ut nya ML-modeller tillsammans med dina andra program och tjänster.

## <a name="create-reproducible-ml-pipelines"></a>Skapa reproducerbara ML-pipelines

Använd ML-pipelines från Azure Machine Learning för att sy ihop alla steg som ingår i din modellutbildningsprocess.

En ML-pipeline kan innehålla steg från dataförberedelse till funktionsextrahering till hyperparameterjustering till modellutvärdering. Mer information finns i [ML-pipelines](concept-ml-pipelines.md).

Om du använder [Designern](concept-designer.md) för att skapa dina ML-pipelines kan du när som helst klicka på **"..."** längst upp till höger på designersidan och sedan välja **Klona**. Klona din pipeline kan du iterera din pipeline design utan att förlora dina gamla versioner.  

## <a name="create-reusable-software-environments"></a>Skapa återanvändbara programvarumiljöer

Med Azure Machine Learning-miljöer kan du spåra och återskapa dina projekts programvaruberoenden när de utvecklas. Med miljöer kan du se till att byggen kan reproduceras utan manuella programvarukonfigurationer.

Miljöer beskriver pip- och Conda-beroenden för dina projekt och kan användas för både utbildning och distribution av modeller. Mer information finns i [Vad är Azure Machine Learning-miljöer](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrera, paketera och distribuera modeller var som helst

### <a name="register-and-track-ml-models"></a>Registrera och spåra ML-modeller

Med modellregistrering kan du lagra och modellera dina modeller i Azure-molnet på arbetsytan. Modellregistret gör det enkelt att organisera och hålla reda på dina tränade modeller.

> [!TIP]
> En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enda modell på arbetsytan Azure Machine Learning. Efter registreringen kan du sedan hämta eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Registrerade modeller identifieras med namn och version. Varje gång du registrerar en modell med ett namn som redan finns ökar versionsnumret. Ytterligare metadatataggar kan tillhandahållas under registreringen. Dessa taggar används sedan när du söker efter en modell. Azure Machine Learning stöder alla modeller som kan läsas in med Python 3.5.2 eller senare.

> [!TIP]
> Du kan också registrera modeller som tränas utanför Azure Machine Learning.

Du kan inte ta bort en registrerad modell som används i en aktiv distribution.
Mer information finns i avsnittet registermodell [i Distribuera modeller](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Profilmodeller

Azure Machine Learning kan hjälpa dig att förstå cpu- och minneskraven för tjänsten som skapas när du distribuerar din modell. Profilering testar tjänsten som kör din modell och returnerar information som CPU-användning, minnesanvändning och svarssvarstid. Det ger också en CPU och minne rekommendation baserat på resursanvändningen.
Mer information finns i avsnittet profilering [i Distribuera modeller](how-to-deploy-and-where.md#profilemodel).

### <a name="package-and-debug-models"></a>Paket- och felsökningsmodeller

Innan du distribuerar en modell i produktion paketeras den till en Docker-avbildning. I de flesta fall sker bildskapande automatiskt i bakgrunden under distributionen. Du kan ange bilden manuellt.

Om du stöter på problem med distributionen kan du distribuera i din lokala utvecklingsmiljö för felsökning och felsökning.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md#registermodel) och [felsökningsdistributioner](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Konvertera och optimera modeller

Om du konverterar din modell till [Open Neural Network Exchange](https://onnx.ai) (ONNX) kan prestanda förbättras. I genomsnitt kan konvertering till ONNX ge en 2x prestandaökning.

Mer information om ONNX med Azure Machine Learning finns i artikeln [Skapa och påskynda ML-modeller.](concept-onnx.md)

### <a name="use-models"></a>Använda modeller

Utbildade maskininlärningsmodeller distribueras som webbtjänster i molnet eller lokalt. Du kan också distribuera modeller till Azure IoT Edge-enheter. Distributioner använder CPU, GPU eller fältprogrammerbara grindmatriser (FPGA) för inferencing. Du kan också använda modeller från Power BI.

När du använder en modell som webbtjänst eller IoT Edge-enhet anger du följande objekt:

* De modeller som används för att poängsätta data som skickas till tjänsten/enheten.
* Ett startskript. Det här skriptet accepterar begäranden, använder modellen/arna för att poängsätta data och returnera ett svar.
* En Azure Machine Learning-miljö som beskriver pip- och Conda-beroenden som krävs av modellens och inmatningsskriptet.
* Alla ytterligare tillgångar som text, data etc. som krävs av modellen/post- och inmatningsskriptet.

Du anger också konfigurationen av måldistributionsplattformen. Till exempel den virtuella datorns familjetyp, tillgängligt minne och antal kärnor vid distribution till Azure Kubernetes Service.

När avbildningen skapas läggs även komponenter som krävs av Azure Machine Learning till. Till exempel resurser som behövs för att köra webbtjänsten och interagera med IoT Edge.

#### <a name="batch-scoring"></a>Batchbedömning
Batchbedömning stöds via ML-pipelines. Mer information finns i [Batch-förutsägelser om stordata](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Webbtjänster i realtid

Du kan använda dina modeller i **webbtjänster** med följande beräkningsmål:

* Azure Container-instans
* Azure Kubernetes Service
* Lokal utvecklingsmiljö

Om du vill distribuera modellen som en webbtjänst måste du ange följande:

* Modellen eller ensemblen av modeller.
* Beroenden som krävs för att använda modellen. Ett skript som accepterar begäranden och anropar modellen, conda-beroenden osv.
* Distributionskonfiguration som beskriver hur och var modellen ska distribueras.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge-enheter

Du kan använda modeller med IoT-enheter via **Azure IoT Edge-moduler**. IoT Edge-moduler distribueras till en maskinvaruenhet, vilket möjliggör slutledning eller modellbedömning på enheten.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analys

Microsoft Power BI stöder användning av maskininlärningsmodeller för dataanalys. Mer information finns [i Azure Machine Learning-integrering i Power BI (förhandsversion).](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Samla in de styrningsdata som krävs för att samla in ml-livscykeln från sluten till

Azure ML ger dig möjlighet att spåra granskningsspåret från på till sluten tid för alla dina ML-tillgångar. Mer specifikt:

- Azure ML [integreras med Git](how-to-set-up-training-targets.md#gitintegration) för att spåra information om vilken databas /gren/genomföra koden som kom från.
- [Azure ML-datauppsättningar](how-to-create-register-datasets.md) hjälper dig att spåra, profilera och versionsdata. 
- Azure ML Run-historik lagrar en ögonblicksbild av koden, data och beräkningar som används för att träna en modell.
- Azure ML-modellregistret samlar in alla metadata som är associerade med din modell (som experimenterar tränade den, där den distribueras, om dess distributioner är felfria).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Meddela, automatisera och varna om händelser i ML-livscykeln
Azure ML publicerar viktiga händelser till Azure EventGrid, som kan användas för att meddela och automatisera händelser i ML-livscykeln. Mer information finns i [det här dokumentet](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Övervaka för operativa & ML-problem

Övervakning gör att du kan förstå vilka data som skickas till din modell och de förutsägelser som returneras.

Den här informationen hjälper dig att förstå hur din modell används. Insamlade indata kan också vara användbara för att utbilda framtida versioner av modellen.

Mer information finns i [Så här aktiverar du insamling av modelldata](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Omskola din modell på nya data

Ofta vill du uppdatera din modell, eller till och med träna om den från grunden, när du får ny information. Ibland är det en förväntad del av domänen att ta emot nya data. Andra gånger, som diskuteras i [Identifiera data drift (förhandsvisning) på datauppsättningar](how-to-monitor-datasets.md), modellprestanda kan försämras inför sådana saker som ändringar i en viss sensor, naturliga data förändringar såsom säsongsbetonade effekter, eller funktioner som skiftar i deras förhållande till andra funktioner. 

Det finns inget universellt svar på "Hur vet jag om jag ska omskola mig?" men Azure ML-händelse- och övervakningsverktyg som tidigare diskuterats är bra utgångspunkter för automatisering. När du har bestämt dig för att omskola dig bör du: 

- Förbehandla dina data med hjälp av en repeterbar, automatiserad process
- Träna din nya modell
- Jämför utgångarna för din nya modell med den gamla modellens
- Använd fördefinierade villkor för att välja om du vill ersätta den gamla modellen 

Ett tema i ovanstående steg är att din omskolning ska automatiseras, inte ad hoc. [Azure Machine Learning-pipelines](concept-ml-pipelines.md) är ett bra svar för att skapa arbetsflöden som rör förberedelse, utbildning, validering och distribution av data. Läs [Omskolningsmodeller med Azure Machine Learning designer (förhandsversion)](how-to-retrain-designer.md) för att se hur pipelines och Azure Machine Learning-designern passar in i ett omskolningsscenario. 

## <a name="automate-the-ml-lifecycle"></a>Automatisera ML-livscykeln 

Du kan använda GitHub och Azure Pipelines för att skapa en kontinuerlig integrationsprocess som tränar en modell. I ett typiskt scenario startar Azure Pipeline en utbildningskörning när en datavetenskapsman kontrollerar en ändring i Git-repo för ett projekt. Resultaten av körningen kan sedan inspekteras för att se prestandaegenskaperna hos den tränade modellen. Du kan också skapa en pipeline som distribuerar modellen som en webbtjänst.

[Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) gör det enklare att arbeta med Azure Pipelines. Det innehåller följande förbättringar av Azure Pipelines:

* Aktiverar val av arbetsyta när en tjänstanslutning definieras.
* Gör att versionspipelor kan utlösas av utbildade modeller som skapats i en träningspipeline.

Mer information om hur du använder Azure Pipelines med Azure Machine Learning finns i följande länkar:

* [Kontinuerlig integrering och distribution av ML-modeller med Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps-databas.](https://aka.ms/mlops)
* [Azure Machine Learning MLOpsPython-databas.](https://github.com/Microsoft/MLOpspython)

Du kan också använda Azure Data Factory för att skapa en datainmatningspipeline som förbereder data för användning med utbildning. Mer information finns [i Pipeline för datainmatning](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Nästa steg

Läs mer genom att läsa och utforska följande resurser:

+ [Så här & var du kan distribuera modeller](how-to-deploy-and-where.md) med Azure Machine Learning

+ [Självstudiekurs: Distribuera en bildklassificeringsmodell i ACI](tutorial-deploy-models-with-aml.md).

+ [Heltäckande MLOps-exempel repo](https://github.com/microsoft/MLOps)

+ [CI/CD av ML-modeller med Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Skapa klienter som [använder en distribuerad modell](how-to-consume-web-service.md)

+ [Maskininlärning i skala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI-referensarkitekturer & metodtips rep](https://github.com/microsoft/AI)
