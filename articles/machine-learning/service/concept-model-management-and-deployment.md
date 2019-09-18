---
title: 'MLOps: Hantera, distribuera & övervaka ML-modeller'
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du använder Azure Machine Learning för MLOps: distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning, på din lokala dator eller från andra källor.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 98a3102d47504b40a6b62eb329b508468947ca79
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035473"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning"></a>MLOps: Hantera, distribuera och övervaka modeller med Azure Machine Learning

I den här artikeln får du lära dig hur du använder Azure Machine Learning för att hantera livs cykeln för dina modeller. Azure Machine Learning använder en metod för Machine Learning åtgärder (MLOps) som förbättrar kvaliteten och konsekvensen för dina maskin inlärnings lösningar. 

Azure Machine Learning tillhandahåller följande MLOps-funktioner:

- **Distribuera ML-projekt från var som helst**
- **Övervaka ml-program för operativa och ml-relaterade problem** – jämför modell inmatningar mellan utbildning och härledning, utforska modell specifika mått och tillhandahålla övervakning och aviseringar på din ml-infrastruktur.
- **Samla in de data som krävs för att upprätta en Gransknings logg från slut punkt till slut punkt i ml**, inklusive vem som publicerar modeller, varför ändringar görs och när modeller har distribuerats eller använts i produktionen.
- **Automatisera livs cykeln från slut punkt till slut punkt med Azure Machine Learning-och Azure-DevOps** för att ofta uppdatera modeller, testa nya modeller och kontinuerligt distribuera nya ml-modeller tillsammans med andra program och tjänster.

Titta på följande video om du vill höra mer om begreppen bakom MLOps och hur de gäller för Azure Machine Learning.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Distribuera ML-projekt från var som helst

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Förvandla din utbildnings process till en återare pipeline
Använd ML-pipelines från Azure Machine Learning för att häfta ihop alla steg som ingår i din modell inlärnings process, från förberedelse av data till funktions extrahering för att justera en modell utvärdering.

Mer information finns i [ml pipelines](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Registrera och spåra ML-modeller

Med modell registreringen kan du lagra och version av dina modeller i Azure-molnet i din arbets yta. Med modell registret kan du enkelt organisera och hålla koll på dina utbildade modeller.

> [!TIP]
> En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i Azure Machine Learning arbets ytan. Efter registreringen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.
 
Registrerade modeller identifieras med namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig ökas registret versionen. Du kan också ange ytterligare metadatataggar under registreringen som kan användas när du söker för modeller. Azure Machine Learning stöder alla modeller som kan läsas in med python-3.5.2 eller högre.

> [!TIP]
> Du kan också registrera modeller som har tränats utanför Azure Machine Learning.

Du kan inte ta bort en registrerad modell som används i en aktiv distribution.
Mer information finns i avsnittet registrera modell i [Distribuera modeller](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Paket-och fel söknings modeller

Innan du distribuerar en modell till produktion paketeras den i en Docker-avbildning. I de flesta fall sker bild skapande automatiskt i bakgrunden under distributionen. För avancerade scenarier kan du manuellt ange avbildningen.

Om du stöter på problem med distributionen kan du distribuera i din lokala utvecklings miljö för fel sökning och fel sökning.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md#registermodel) och [Felsöka distributioner](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Validera och profilera modeller

Azure Machine Learning kan använda profilering för att fastställa de idealiska processor-och minnes inställningar som ska användas när du distribuerar din modell. Modell valideringen sker som en del av den här processen, med hjälp av data som du anger för profilerings processen.

### <a name="convert-and-optimize-models"></a>Omvandla och optimera modeller

Att konvertera din modell till [Open neurala Network Exchange](https://onnx.ai) (ONNX) kan förbättra prestandan. I genomsnitt kan konvertering till ONNX ge en prestanda ökning på 2 gånger.

Mer information om ONNX med Azure Machine Learning finns i artikeln [skapa och påskynda ml-modeller](concept-onnx.md) .

### <a name="use-models"></a>Använda modeller

Utbildade maskin inlärnings modeller kan distribueras som webb tjänster i molnet eller lokalt i din utvecklings miljö. Du kan också distribuera modeller till Azure IoT Edge enheter. Distributioner kan använda CPU-, GPU-eller FPGA (Field-programmerbara grind mat ris) för inferencing. Du kan också använda modeller från Power BI.

När du använder en modell som en webb tjänst eller IoT Edge enhet kan du ange följande objekt:

* Den eller de modeller som används för att räkna upp data som skickas till tjänsten/enheten.
* Ett Entry-skript. Det här skriptet accepterar begär Anden, använder modeller för att räkna data och returnera ett svar.
* En Conda miljö fil som beskriver de beroenden som krävs av modell (er) och registrerings skriptet.
* Ytterligare till gångar, till exempel text, data osv. som krävs av modell (er) och registrerings skript.

Dessa till gångar paketeras i en Docker-avbildning och distribueras som en webb tjänst eller IoT Edge modul.

Alternativt kan du använda följande parametrar för att ytterligare finjustera distributionen:

* Aktivera GPU: Används för att aktivera GPU-stöd i Docker-avbildningen. Avbildningen måste användas på Microsoft Azure tjänster som Azure Container Instances, Azure Kubernetes service, Azure Machine Learning Compute eller Azure Virtual Machines.
* Ytterligare steg i Docker-filen: En fil som innehåller ytterligare Docker-steg att köra när du skapar Docker-avbildningen.
* Bas avbildning: En anpassad bild som ska användas som bas avbildning. Om du inte använder en anpassad avbildning tillhandahålls bas avbildningen av Azure Machine Learning.

Du anger också konfigurationen för mål distributions plattformen. Till exempel typ av VM-familj, tillgängligt minne och antal kärnor vid distribution till Azure Kubernetes-tjänsten.

När avbildningen skapas, läggs även komponenter som krävs av Azure Machine Learning. Till exempel till gångar som behövs för att köra webb tjänsten och interagera med IoT Edge.

> [!NOTE]
> Du kan inte ändra eller ändra webb servern eller IoT Edge komponenter som används i Docker-avbildningen. Azure Machine Learning använder en webb Server konfiguration och IoT Edge komponenter som testas och stöds av Microsoft.

#### <a name="web-service"></a>Webbtjänst

Du kan använda dina modeller i **webb tjänster** med följande beräknings mål:

* Azure Container-instans
* Azure Kubernetes Service
* Lokal utvecklings miljö

Om du vill distribuera modellen som en webb tjänst måste du tillhandahålla följande objekt:

* Modellernas eller ensemblens modell.
* Beroenden som krävs för att använda modellen. Till exempel ett skript som accepterar begär Anden och anropar-modellen, Conda-beroenden osv.
* Distributions konfiguration som beskriver hur och var modellen ska distribueras.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge-enheter

Du kan använda modeller med IoT-enheter via **Azure IoT Edge moduler**. IoT Edge moduler distribueras till en maskin varu enhet, vilket möjliggör en härledning eller modell beräkning på enheten.

Mer information finns i [Distribuera modeller](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI stöder användning av Machine Learning-modeller för data analys. Mer information finns i [Azure Machine Learning integration i Power BI (för hands version)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Övervaka ML-program för operativa och ML-relaterade problem

Med övervakning kan du förstå vilka data som skickas till din modell och vilka förutsägelser som returneras.

Den här informationen hjälper dig att förstå hur modellen används. Insamlade indata kan också vara användbara i träna framtida versioner av modellen.

Mer information finns i [så här aktiverar du modell data insamling](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Avbilda en slut punkt till slut punkt för den ML-livs cykeln

Azure ML ger dig möjlighet att spåra gransknings historiken från slut punkt till slut punkt för alla dina ML-tillgångar. Närmare bestämt:

- Azure ML [integreras med git](how-to-set-up-training-targets.md#gitintegration) för att spåra information som lagrings plats/Branch/bekräfta din kod kommer från.
- Med [Azure ml-datauppsättningar](how-to-create-register-datasets.md) kan du spåra och versions data.
- I Azure ML-körnings historik lagras en ögonblicks bild av koden, data och beräkning som används för att träna en modell.
- Azure ML Model-registret fångar alla metadata som är associerade med din modell (vilket experiment tränade den, där den distribueras, om distributionerna är felfria).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatisera livs cykeln för slut punkt till slut punkt ML 

Du kan använda GitHub och Azure-pipelines för att skapa en kontinuerlig integrerings process som tågen en modell. När en data expert kontrollerar en ändring i git-lagrings platsen för ett projekt i ett typiskt scenario startar Azure pipelinen en tränings körning. Resultaten av körningen kan sedan kontrol leras för att se prestanda egenskaperna för den tränade modellen. Du kan också skapa en pipeline som distribuerar modellen som en webb tjänst.

Med [Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) blir det enklare att arbeta med Azure-pipeliner. Den ger följande förbättringar av Azure-pipeliner:

* Aktiverar val av arbets yta när du definierar en tjänst anslutning.
* Gör att lanserings pipelines kan utlösas av utbildade modeller som skapats i en utbildnings pipeline.

Mer information om hur du använder Azure-pipeliner med Azure Machine Learning finns i artikeln [kontinuerlig integrering och distribution av ml-modeller med Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning) och [Azure Machine Learning MLOps](https://aka.ms/mlops) -lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du kan distribuera modeller](how-to-deploy-and-where.md) med Azure Machine Learning. Ett exempel på distribution finns i [Självstudier: Distribuera en modell för bild klassificering i](tutorial-deploy-models-with-aml.md)Azure Container instances.

Lär dig hur du skapar [kontinuerlig integrering och distribution av ml-modeller med Azure-pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Lär dig hur du skapar klient program och tjänster som [använder en modell som distribueras som en webb tjänst](how-to-consume-web-service.md).
