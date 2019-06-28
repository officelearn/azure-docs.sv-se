---
title: 'MLOps: Hantera, distribuera och övervaka ML-modeller'
titleSuffix: Azure Machine Learning service
description: 'Lär dig hur du använder Azure Machine Learning-tjänsten för MLOps: distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning-tjänsten på den lokala datorn eller från andra källor.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 5cbb7f13214a86f528521fdeb1ffa1374ca813ef
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331711"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten

Läs mer om hur du använder Azure Machine Learning-tjänsten för att hantera livscykeln för dina modeller i den här artikeln. Azure Machine Learning använder en Machine Learning-åtgärder (MLOps)-metod, vilket förbättrar kvaliteten och konsekvenskontroll av lösningarna för maskininlärning. Azure Machine Learning-tjänsten innehåller följande MLOps funktioner:

* Integrering med Azure Pipelines. Definiera kontinuerlig integrering och distributionsarbetsflöden för dina modeller.
* Ett modell-register som underhåller flera versioner av tränade modeller.
* Modeller validering. Verifiera dina anpassade modeller och välj den bästa konfigurationen för att distribuera dem till produktion automatiskt.
* Distribuera dina modeller som en webbtjänst i molnet, lokalt eller på IoT Edge-enheter.
* Övervaka prestanda för din distribuerade modell, så att du kan få förbättringar i nästa version av modellen.

Titta på följande videoklipp för att få höra mer på koncepten bakom MLOps och hur de används Azure Machine Learning-tjänsten.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Integrering med Azure Pipelines

Du kan använda Azure Pipelines för att skapa en process för kontinuerlig integrering som tränar en modell. I ett vanligt scenario när inom Data Science kontrollerar en ändring i Git-lagringsplats för ett projekt startas Azure pipelinen en körning för utbildning. Resultaten av körningen kan sedan kontrolleras för att se prestandaegenskaper för den tränade modellen. Du kan också skapa en pipeline som distribuerar modellen som en webbtjänst.

Den [Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) gör det enklare att arbeta med Azure-Pipelines. Den tillhandahåller följande förbättringar till en Azure-pipeline:

* Möjliggör val av arbetsyta när du definierar en anslutning för tjänsten.
* Aktiverar release-pipelines som utlöses av tränade modeller som skapats i en pipeline för utbildning.

Mer information om hur du använder Azure-Pipelines med Azure Machine Learning finns det [kontinuerlig integrering och distribution av ML-modeller med Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) artikeln och [Azure Machine Learning-tjänsten MLOps](https://aka.ms/mlops) lagringsplats.

## <a name="convert-and-optimize-models"></a>Konvertera och optimering av modeller

Konvertera din modell till [öppna Neural Network Exchange](https://onnx.ai) (ONNX) kan du förbättra prestandan. Konvertera till ONNX väsentliga i genomsnitt en 2 x prestandaökning.

Mer information om ONNX med Azure Machine Learning-tjänsten finns i den [skapa och påskynda ML-modeller](concept-onnx.md) artikeln.

## <a name="register-models"></a>Registrera modeller

Modellen kan du lagra och version dina modeller i Azure-molnet, på din arbetsyta. Modellen registret gör det enkelt att ordna och Håll koll på dina tränade modeller.

> [!TIP]
> Registrerade modellen är en logisk behållare för en eller flera filer som utgör din modell. Exempelvis om du har en modell som lagras i flera filer kan registrera du dem som en enda modell i Azure Machine Learning-arbetsyta. Efter registreringen, kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som registrerades.
 
Registrerade modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig ökas registret versionen. Du kan också ange ytterligare metadatataggar under registreringen som kan användas när du söker för modeller. Azure Machine Learning-tjänsten har stöd för alla modeller som kan vara inlästa med hjälp av Python 3.5.2 eller högre.

> [!TIP]
> Du kan också registrera modeller tränas utanför Azure Machine Learning-tjänsten.

Du kan inte ta bort en registrerade modellen som används i en aktiv distribution.

Mer information finns i avsnittet registrera modellen i [distribuera modeller](how-to-deploy-and-where.md#registermodel).

Ett exempel med att registrera en modell som lagras i pickle-format finns i [självstudien: Träna en modell för klassificering av bild](tutorial-deploy-models-with-aml.md).

## <a name="package-and-debug-models"></a>Paket och felsöka modeller

Innan du distribuerar en modell i produktionen, är det paketeras i en Docker-avbildning. I de flesta fall sker skapa avbildningar automatiskt i bakgrunden under distributionen. Du kan manuellt ange avbildningen för avancerade scenarier.

Om du stöter på problem med distributionen, kan du distribuera på din lokala utvecklingsmiljö för felsökning.

Mer information finns i [distribuera modeller](how-to-deploy-and-where.md#registermodel) och [felsökning distributioner](how-to-troubleshoot-deployment.md).

## <a name="validate-and-profile-models"></a>Validera och profilera modeller

Azure Machine Learning-tjänsten kan använda profilering för att fastställa perfekt processor- och inställningarna som ska användas när du distribuerar din modell. Modell-verifieringen sker som en del av den här processen med hjälp av data som du anger för att Profileringen.

## <a name="use-models"></a>Använd modeller

Tränade machine learning-modeller kan distribueras som webbtjänster i molnet eller lokalt i din utvecklingsmiljö. Du kan också distribuera modeller till Azure IoT Edge-enheter. Distributioner kan använda CPU, GPU eller fält-programmable gate matriser (FPGA) för inferensjobb. Du kan också använda modeller från Power BI.

När du använder en modell som en webbtjänst eller IoT Edge-enhet kan ange du följande:

* Modeller som används för att bedöma de data som skickats till tjänsten/enheten.
* En post-skript. Det här skriptet tar emot förfrågningar, använder modeller för att använda till och returnerar något svar.
* En conda miljö-fil som beskriver alla beroenden som krävs av modeller och post-skriptet.
* Eventuella ytterligare tillgångar som till exempel text, data, etc. som krävs av skriptet modell(er) och posten.

Dessa tillgångar är paketeras i en Docker-avbildning och distribueras som en webbtjänst eller IoT Edge-modul.

Du kan även använda följande parametrar för att ytterligare finjustera distributionen:

* Aktivera GPU: Används för att aktivera stöd för GPU i Docker-avbildningen. Avbildningen måste användas på Microsoft Azure-tjänster som Azure Container Instances, Azure Kubernetes Service, beräkning av Azure Machine Learning eller Azure Virtual Machines.
* Steg för extra docker-fil: En fil som innehåller ytterligare Docker-åtgärder för att köra när du skapar Docker-avbildningen.
* Källavbildning: En anpassad avbildning som ska användas som basavbildningen. Om du inte använder en anpassad avbildning kommer basavbildningen från Azure Machine Learning-tjänsten.

Du kan också ange konfigurationen för målplattformen för distribution. Till exempel den virtuella datorn familj, minne, och antalet kärnor när du distribuerar till Azure Kubernetes Service.

När avbildningen har skapats läggs även komponenter som krävs av Azure Machine Learning-tjänsten. Till exempel tillgångar som behövs för att köra webbtjänsten och interagera med IoT Edge.

> [!NOTE]
> Du kan inte ändra eller ändra webbserver eller IoT Edge-komponenter som används i Docker-avbildningen. Azure Machine Learning-tjänsten använder en webbserverns konfiguration och IoT Edge-komponenter som testats och stöds av Microsoft.

### <a name="web-service"></a>Webbtjänst

Du kan använda dina modeller i **webbtjänster** compute med följande mål:

* Azure Container-instans
* Azure Kubernetes Service
* Lokal utvecklingsmiljö

För att distribuera modellen som en webbtjänst, måste du ange följande:

* Modellen eller ensemble av modeller.
* Beroenden som krävs för att använda modellen. Till exempel ett skript som tar emot förfrågningar och anropar modell, conda-beroenden, osv.
* Distributionskonfiguration som beskriver hur och var att distribuera modellen.

Mer information finns i [distribuera modeller](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>IoT Edge-enheter


Du kan använda modeller med IoT-enheter via **Azure IoT Edge-moduler**. IoT Edge-moduler distribueras till en maskinvaruenhet som möjliggör inferens eller modell bedömning på enheten.

Mer information finns i [distribuera modeller](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI stöder användningen av machine learning-modeller för dataanalys. Mer information finns i [Azure Machine Learning-integrering i Power BI (förhandsgranskning)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="monitor-and-collect-data"></a>Övervaka och samla in data

Övervakning kan du förstå vilka data som skickas till din modell och förutsägelser som returneras.

Den här informationen hjälper dig att förstå hur din modell används. Insamlade inkommande data kan också vara användbara i utbildning framtida versioner av modellen.

Mer information finns i [hur du aktiverar insamling av modelldata](how-to-enable-data-collection.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du kan distribuera modeller](how-to-deploy-and-where.md) med Azure Machine Learning-tjänsten. Ett exempel på distribution finns i [självstudien: Distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

Lär dig hur du skapar [kontinuerlig integrering och distribution av ML-modeller med Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Lär dig hur du skapar klienten program och tjänster som [Använd en modell som distribueras som en webbtjänst](how-to-consume-web-service.md).
