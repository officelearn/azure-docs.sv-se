---
title: 'MLOps: ML modell hantering'
titleSuffix: Azure Machine Learning
description: 'Lär dig mer om modell hantering med Azure Machine Learning (MLOps). Distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 02/21/2020
ms.custom: seodec18
ms.openlocfilehash: 6671b9c83ab71b4a92fe36d647e5a4e4d781154e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096178"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: modell hantering, distribution och övervakning med Azure Machine Learning

I den här artikeln får du lära dig hur du använder Azure Machine Learning för att hantera livs cykeln för dina modeller. Azure Machine Learning använder en metod för Machine Learning åtgärder (MLOps). MLOps förbättrar kvaliteten och konsekvensen för dina Machine Learning-lösningar. 

## <a name="what-is-mlops"></a>Vad är MLOps?

Machine Learning åtgärder (MLOps) baseras på [DevOps](https://azure.microsoft.com/overview/what-is-devops/) principer och metoder som ökar effektiviteten i arbets flöden. Till exempel kontinuerlig integrering, leverans och distribution. MLOps tillämpar dessa huvud objekt i Machine Learning-processen, med målet att:

* Snabbare experimentering och utveckling av modeller
* Snabbare distribution av modeller till produktion
* Kvalitets garanti

Azure Machine Learning tillhandahåller följande MLOps-funktioner:

- **Skapa reproducerade ml-pipeliner**. Med Machine Learning pipelines kan du definiera upprepnings bara och återanvändbara steg för förberedelse av data, utbildnings-och bedömnings processer.
- **Skapa åter användnings bara program miljöer** för utbildning och distribution av modeller.
- **Registrera, paketera och distribuera modeller var som helst**. Du kan också spåra associerade metadata som krävs för att använda modellen.
- **Fånga in styrnings data för slut punkt till slut punkt i ml-livscykeln**. Den loggade informationen kan inkludera vem som publicerar modeller, varför ändringar gjorts och när modeller distribuerades eller användes i produktionen.
- **Meddela och Avisera händelser i ml-livscykel**. Till exempel experimentering, modell registrering, modell distribution och data avkänning.
- **Övervaka ml-program för operativa och ml-relaterade problem**. Jämför modell inmatningar mellan utbildning och härledning, utforska modellbaserade mått och tillhandahålla övervakning och aviseringar på din ML-infrastruktur.
- **Automatisera livs cykeln från slut punkt till slut punkt med Azure Machine Learning och Azure-pipeliner**. Med hjälp av pipeliner kan du ofta uppdatera modeller, testa nya modeller och kontinuerligt distribuera nya ML-modeller tillsammans med andra program och tjänster.

## <a name="create-reproducible-ml-pipelines"></a>Skapa reproducerade ML-pipeliner

Använd ML-pipelines från Azure Machine Learning för att sammanfoga alla steg som ingår i din modell inlärnings process.

En ML-pipeline kan innehålla steg från förberedelse av data till funktions extrahering för att justera en modell utvärdering. Mer information finns i [ml pipelines](concept-ml-pipelines.md).

Om du använder [designern](concept-designer.md) för att skapa en ml-pipeline kan du när som helst klicka på **"..."** längst upp till höger på design sidan och sedan välja **klona**. Genom att klona din pipeline kan du iterera din pipeline-design utan att förlora dina gamla versioner.  

## <a name="create-reusable-software-environments"></a>Skapa åter användnings bara program miljöer

Med Azure Machine Learning miljöer kan du spåra och återskapa dina projekts program varu beroenden när de utvecklas. Med miljöer kan du se till att build-versioner kan reproduceras utan manuella program varu konfigurationer.

Miljöer beskriver pip-och Conda-beroenden för dina projekt och kan användas för både utbildning och distribution av modeller. Mer information finns i [Vad är Azure Machine Learning miljöer](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrera, paketera och distribuera modeller var som helst

### <a name="register-and-track-ml-models"></a>Registrera och spåra ML-modeller

Med modell registreringen kan du lagra och version av dina modeller i Azure-molnet i din arbets yta. Med modell registret kan du enkelt organisera och hålla koll på dina utbildade modeller.

> [!TIP]
> En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i Azure Machine Learning arbets ytan. Efter registreringen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Registrerade modeller identifieras med namn och version. Varje gång du registrerar en modell med ett namn som redan finns ökar versionsnumret. Ytterligare metadata-taggar kan anges under registreringen. Taggarna används sedan för att söka efter en modell. Azure Machine Learning stöder alla modeller som kan läsas in med python-3.5.2 eller högre.

> [!TIP]
> Du kan också registrera modeller som har tränats utanför Azure Machine Learning.

Du kan inte ta bort en registrerad modell som används i en aktiv distribution.
Mer information finns i avsnittet registrera modell i [Distribuera modeller](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Paket-och fel söknings modeller

Innan du distribuerar en modell till produktion paketeras den i en Docker-avbildning. I de flesta fall sker bild skapande automatiskt i bakgrunden under distributionen. Du kan ange avbildningen manuellt.

Om du stöter på problem med distributionen kan du distribuera i din lokala utvecklings miljö för fel sökning och fel sökning.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md#registermodel) och [Felsöka distributioner](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Omvandla och optimera modeller

Att konvertera din modell till [Open neurala Network Exchange](https://onnx.ai) (ONNX) kan förbättra prestandan. I genomsnitt kan konvertering till ONNX ge en prestanda ökning på 2 gånger.

Mer information om ONNX med Azure Machine Learning finns i artikeln [skapa och påskynda ml-modeller](concept-onnx.md) .

### <a name="use-models"></a>Använda modeller

Tränade Machine Learning-modeller distribueras som webb tjänster i molnet eller lokalt. Du kan också distribuera modeller till Azure IoT Edge enheter. I distributioner används CPU-, GPU-eller Field-programmerbara Gate-matriser (FPGA) för inferencing. Du kan också använda modeller från Power BI.

När du använder en modell som en webb tjänst eller IoT Edge enhet kan du ange följande objekt:

* Den eller de modeller som används för att räkna upp data som skickas till tjänsten/enheten.
* Ett Entry-skript. Det här skriptet accepterar begär Anden, använder modeller för att räkna data och returnera ett svar.
* En Azure Machine Learning miljö som beskriver pip-och Conda-beroenden som krävs av modell (er) och Entry-skript.
* Ytterligare till gångar, till exempel text, data osv. som krävs av modell (er) och registrerings skript.

Du anger också konfigurationen för mål distributions plattformen. Till exempel typ av VM-familj, tillgängligt minne och antal kärnor vid distribution till Azure Kubernetes-tjänsten.

När avbildningen skapas, läggs även komponenter som krävs av Azure Machine Learning. Till exempel till gångar som behövs för att köra webb tjänsten och interagera med IoT Edge.

#### <a name="batch-scoring"></a>Batchbedömning
Batch-Poäng stöds via ML-pipeliner. Mer information finns i [batch-förutsägelser för Big data](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Real tids webb tjänster

Du kan använda dina modeller i **webb tjänster** med följande beräknings mål:

* Azure Container-instans
* Azure Kubernetes Service
* Lokal utvecklings miljö

Om du vill distribuera modellen som en webb tjänst måste du tillhandahålla följande objekt:

* Modellernas eller ensemblens modell.
* Beroenden som krävs för att använda modellen. Till exempel ett skript som accepterar begär Anden och anropar-modellen, Conda-beroenden osv.
* Distributions konfiguration som beskriver hur och var modellen ska distribueras.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge enheter

Du kan använda modeller med IoT-enheter via **Azure IoT Edge moduler**. IoT Edge moduler distribueras till en maskin varu enhet, vilket möjliggör en härledning eller modell beräkning på enheten.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI stöder användning av Machine Learning-modeller för data analys. Mer information finns i [Azure Machine Learning integration i Power BI (för hands version)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Avbilda de styrnings data som krävs för att samla in den slutliga ML-livs cykeln

Azure ML ger dig möjlighet att spåra gransknings historiken från slut punkt till slut punkt för alla dina ML-tillgångar. Mer specifikt:

- Azure ML [integreras med git](how-to-set-up-training-targets.md#gitintegration) för att spåra information om vilken lagrings plats/Branch/bekräfta din kod kommer från.
- Med [Azure ml-datauppsättningar](how-to-create-register-datasets.md) kan du spåra, profilera och versions data. 
- I Azure ML-körnings historik lagras en ögonblicks bild av koden, data och beräkningarna som används för att träna en modell.
- Azure ML Model-registret fångar alla metadata som är associerade med din modell (vilket experiment tränade den, där den distribueras, om distributionerna är felfria).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Meddela, automatisera och Avisera händelser i ML-livscykel
Azure ML publicerar viktiga händelser till Azure-EventGrid, som kan användas för att meddela och automatisera händelser i ML-livscykel. Mer information finns i [det här dokumentet](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Övervakare för drift & ML-problem

Med övervakning kan du förstå vilka data som skickas till din modell och vilka förutsägelser som returneras.

Den här informationen hjälper dig att förstå hur modellen används. Insamlade indata kan också vara användbara i träna framtida versioner av modellen.

Mer information finns i [så här aktiverar du modell data insamling](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Träna modellen på nya data

Ofta vill du uppdatera din modell eller till och med återträna den från grunden, när du får ny information. Ibland är att ta emot nya data en förväntad del av domänen. Andra gånger, som vi beskrivit i [identifiera data drift (för hands version) på data uppsättningar](how-to-monitor-datasets.md), kan modell prestanda försämras i sådana saker som förändringar i en viss sensor, naturliga data förändringar, till exempel säsongs effekter eller funktioner som går i relation till andra funktioner. 

Det finns inget universellt svar på "Hur gör jag för att veta om jag borde träna?" Men Azure ML event och övervaknings verktyg som tidigare diskuterats är väl utgångs punkter för Automation. När du har bestämt dig för att träna bör du: 

- Förbearbeta dina data med en upprepnings bar, automatiserad process
- Träna din nya modell
- Jämför utmatningarna för den nya modellen till dem i din gamla modell
- Använd fördefinierade villkor för att välja om du vill ersätta din gamla modell 

Ett tema med ovanstående steg är att omskolningen ska vara automatiserad, inte ad hoc. [Azure Machine Learning pipelines](concept-ml-pipelines.md) är ett utmärkt svar för att skapa arbets flöden som rör förberedelse av data, utbildning, validering och distribution. Läs [träna modeller med Azure Machine Learning designer (för hands version)](how-to-retrain-designer.md) om du vill se hur pipelines och Azure Machine Learning designer passar i ett omskolnings scenario. 

## <a name="automate-the-ml-lifecycle"></a>Automatisera ML-livscykeln 

Du kan använda GitHub och Azure-pipelines för att skapa en kontinuerlig integrerings process som tågen en modell. När en data expert kontrollerar en ändring i git-lagrings platsen för ett projekt i ett typiskt scenario startar Azure pipelinen en tränings körning. Resultaten av körningen kan sedan kontrol leras för att se prestanda egenskaperna för den tränade modellen. Du kan också skapa en pipeline som distribuerar modellen som en webb tjänst.

Med [Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) blir det enklare att arbeta med Azure-pipeliner. Den ger följande förbättringar av Azure-pipeliner:

* Aktiverar val av arbets yta när du definierar en tjänst anslutning.
* Gör att lanserings pipelines kan utlösas av utbildade modeller som skapats i en utbildnings pipeline.

Mer information om hur du använder Azure-pipeliner med Azure Machine Learning finns i artikeln [kontinuerlig integrering och distribution av ml-modeller med Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning) och [Azure Machine Learning MLOps](https://aka.ms/mlops) -lagringsplatsen.

Du kan också använda Azure Data Factory för att skapa en pipeline för data inmatning som förbereder data för användning med träning. Mer information finns i [pipeline för data inmatning](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer genom att läsa och utforska följande resurser:

+ [Hur & du distribuerar modeller](how-to-deploy-and-where.md) med Azure Machine Learning

+ [Självstudie: Distribuera en bild klassificerings modell i Aci](tutorial-deploy-models-with-aml.md).

+ [Lagrings platsen för MLOps-exempel från slut punkt till slut punkt](https://github.com/microsoft/MLOps)

+ [CI/CD med ML-modeller med Azure-pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Skapa klienter som [använder en distribuerad modell](how-to-consume-web-service.md)

+ [Maskininlärning i skala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Referens arkitekturer för Azure AI & Best Practices rep](https://github.com/microsoft/AI)
