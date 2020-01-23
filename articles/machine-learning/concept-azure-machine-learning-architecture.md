---
title: Viktiga begrepp för arkitektur &
titleSuffix: Azure Machine Learning
description: Lär dig mer om arkitekturen, termer, koncept och arbets flöden som utgör Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: e3dcd9305a48b0d34f1bcbea64d6628efabc1235
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546069"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hur Azure Machine Learning fungerar: arkitektur och koncept

Lär dig mer om arkitekturen, begreppen och arbets flödet för Azure Machine Learning. Huvud komponenterna i tjänsten och det allmänna arbets flödet för att använda tjänsten visas i följande diagram:

![Azure Machine Learning arkitektur och arbets flöde](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Arbetsflöde

Arbets flödet för Machine Learning-modellen följer i allmänhet den här ordningen:

1. **Utbilda**
    + Utveckla utbildnings skript för maskin inlärning i **python** eller med den visuella designern.
    + Skapa och konfigurera en **beräkningsmålet**.
    + **Skicka skripten** till den konfigurerade beräkningsmål ska köras i den miljön. Under utbildningen kan skripten läsa från eller skriva till **data lager**. Och posterna för körningen sparas som **körs** i **arbets ytan** och grupperas under **experiment**.

1. **Paket** – när en tillfredsställande körning hittas registrerar du den sparade modellen i **modell registret**.

1. **Verifiera** - **fråga experimentet** efter loggade mått från aktuella och tidigare körningar. Om måtten inte indikerar ett önskat resultat går du tillbaka till steg 1 och itererar dina skript.

1. **Distribuera** – utveckla ett bedömnings skript som använder modellen och **distribuera modellen** som en **webb tjänst** i Azure, eller till en **IoT Edge enhet**.

1. **Övervaka** -Övervakare för **data avvikelser** mellan inlärnings data uppsättningen och data härlednings data för en distribuerad modell. Om det behövs går du tillbaka till steg 1 för att träna om modellen med nya tränings data.

## <a name="tools-for-azure-machine-learning"></a>Verktyg för Azure Machine Learning

Använd följande verktyg för Azure Machine Learning:

+  Interagera med tjänsten i valfri python-miljö med [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interagera med tjänsten i valfri R-miljö med [Azure Machine Learning SDK för R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatisera dina Machine Learning-aktiviteter med [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Använd [Azure Machine Learning designer (för hands version)](concept-designer.md) för att utföra arbets flödes stegen utan att skriva kod.


> [!NOTE]
> Även om den här artikeln definierar termer och begrepp som används av Azure Machine Learning definierar den inte termer och begrepp för Azure-plattformen. Mer information om terminologi för Azure-plattformen finns i [ord listan Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Ordlista
+ <a href="#activities">Aktivitet</a>
+ <a href="#compute-targets">Compute-mål</a>
+ <a href="#datasets-and-datastores">Data uppsättningar & data lager</a>
+ <a href="#endpoints">Slut punkter</a>
+ <a href="#environments">Miljöer</a>
+ [Kostnadsberäknare](#estimators)
+ <a href="#experiments">Experiment</a>
+ <a href="#github-tracking-and-integration">Git-spårning</a>
+ <a href="#iot-module-endpoints">IoT-moduler</a>
+ <a href="#logging">Loggning</a>
+ <a href="#ml-pipelines">ML pipelines</a>
+ <a href="#models">Modellerna</a>
+ <a href="#runs">Kör</a>
+ <a href="#run-configurations">Kör konfiguration</a>
+ <a href="#snapshots">Ögonblicks bild</a>
+ <a href="#training-scripts">Tränings skript</a>
+ <a href="#web-service-endpoint">Webb tjänster</a>
+ <a href="#workspaces">Platsen</a>

### <a name="activities"></a>Aktiviteter

En aktivitet representerar en långvarig åtgärd. Följande åtgärder är exempel på aktiviteter:

* Skapa eller ta bort ett beräkningsmål
* Köra ett skript på ett beräkningsmål

Aktiviteter kan ge aviseringar via SDK eller webb gränssnittet så att du enkelt kan övervaka förloppet för dessa åtgärder.

### <a name="compute-instance"></a>Beräknings instans (för hands version)

> [!NOTE]
> Beräknings instanser är endast tillgängliga för arbets ytor med en region i **norra centrala USA** eller **Storbritannien, södra**.
>Om din arbets yta finns i en annan region kan du fortsätta att skapa och använda en [virtuell dator](concept-compute-instance.md#notebookvm) i stället. 

En **Azure Machine Learning beräknings instans** (tidigare VM) är en fullständigt hanterad molnbaserad arbets station som innehåller flera verktyg och miljöer som är installerade för maskin inlärning. Beräknings instanser kan användas som beräknings mål för utbildnings-och inferencing-jobb. För stora aktiviteter är [Azure Machine Learning beräknings kluster](how-to-set-up-training-targets.md#amlcompute) med skalnings funktioner för flera noder ett bättre beräknings måls val.

Läs mer om [beräknings instanser](concept-compute-instance.md).

### <a name="compute-targets"></a>Beräkningsmål

Med ett [beräknings mål](concept-compute-target.md) kan du ange den beräknings resurs där du kör ditt utbildnings skript eller vara värd för tjänst distributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräknings resurs.

Lär dig mer om de [tillgängliga beräknings målen för utbildning och distribution](concept-compute-target.md).

### <a name="datasets-and-datastores"></a>Data uppsättningar och data lager

**Azure Machine Learning data uppsättningar** (för hands version) gör det enklare att komma åt och arbeta med dina data. Data uppsättningar hanterar data i olika scenarier, till exempel modell utbildning och skapande av pipelines. Med hjälp av Azure Machine Learning SDK kan du komma åt underliggande lagring, utforska data och hantera livs cykeln för olika definitioner av data uppsättningar.

Data uppsättningar tillhandahåller metoder för att arbeta med data i populära format, t. ex. genom att använda `from_delimited_files()` eller `to_pandas_dataframe()`.

Mer information finns i [skapa och registrera Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md).  Fler exempel på hur du använder data uppsättningar finns i [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Ett **data lager** är en lagrings abstraktion över ett Azure Storage-konto. Data lagret kan använda antingen en Azure Blob-behållare eller en Azure-filresurs som server dels lagringen. Varje arbets yta har ett standard-datalager och du kan registrera ytterligare data lager. Använd python SDK API eller Azure Machine Learning CLI för att lagra och hämta filer från data lagret.

### <a name="endpoints"></a>Slutpunkter

En slut punkt är en instansiering av din modell i antingen en webb tjänst som kan finnas i molnet eller en IoT-modul för integrerade enhets distributioner.

#### <a name="web-service-endpoint"></a>Webb tjänst slut punkt

När du distribuerar en modell som en webb tjänst kan slut punkten distribueras på Azure Container Instances, Azure Kubernetes-tjänsten eller FPGAs. Du skapar tjänsten från din modell, ditt skript och tillhör ande filer. De placeras i en bas behållar avbildning som innehåller körnings miljön för modellen. Avbildningen har en belastningsutjämnad HTTP-slutpunkt som tar emot Poäng begär Anden som skickas till webb tjänsten.

Azure hjälper dig att övervaka din webb tjänst genom att samla in Application Insights telemetri eller modellera telemetri, om du har valt att aktivera den här funktionen. Telemetridata är bara tillgängliga för dig och lagras i Application Insights-och lagrings konto instanser.

Om du har aktiverat automatisk skalning skalar Azure automatiskt distributionen.

Ett exempel på hur du distribuerar en modell som en webb tjänst finns [i Distribuera en bild klassificerings modell i Azure Container instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>IoT-modulens slut punkter

En distribuerad IoT module-slutpunkt är en Docker-behållare som innehåller din modell och tillhör ande skript eller program och eventuella ytterligare beroenden. Du distribuerar dessa moduler genom att använda Azure IoT Edge på gräns enheter.

Om du har aktiverat övervakning samlar Azure in telemetridata från modellen i Azure IoT Edge-modulen. Telemetridata är bara tillgängliga för dig och lagras i din lagrings konto instans.

Azure IoT Edge säkerställer att modulen körs och övervakar den enhet som är värd för den.

### <a name="environments"></a>Miljöer

Azure ML-miljöer används för att ange konfigurationen (Docker/python/Spark/osv.) som används för att skapa en miljö för förberedelse av data, modell utbildning och modell hantering. De är hanterade och versions bara entiteter inom din Azure Machine Learning arbets yta som möjliggör återskapande, gransknings bara och bärbara Machine Learning-arbetsflöden över olika beräknings mål.

Du kan använda ett miljö objekt i din lokala beräkning för att utveckla ditt utbildnings skript, återanvända samma miljö på Azure Machine Learning beräkning för modell utbildning i stor skala och till och med distribuera din modell med samma miljö. 

Lär dig [hur du skapar och hanterar en ÅTERANVÄNDBAR ml-miljö](how-to-use-environments.md) för utbildning och härledning.

### <a name="estimators"></a>Kostnadsberäknare

För att under lätta modell träningen med populära ramverk, gör klassen uppskattning att du enkelt kan skapa körnings konfigurationer. Du kan skapa och använda en generisk [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka utbildnings skript som använder valfritt ramverk för inlärning (till exempel scikit – lära).

För PyTorch-, TensorFlow-och kedje uppgifter-aktiviteter tillhandahåller Azure Machine Learning även de olika uppskattningarna [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) för att förenkla användningen av dessa ramverk.

Mer information finns i följande artiklar:

* [Träna ml-modeller med uppskattningar](how-to-train-ml-models.md).
* [Utbilda Pytorch djup inlärnings modeller i stor skala med Azure Machine Learning](how-to-train-pytorch.md).
* [Träna och registrera TensorFlow-modeller i stor skala med Azure Machine Learning](how-to-train-tensorflow.md).
* [Träna och registrera kedje modeller i skala med Azure Machine Learning](how-to-train-chainer.md).

### <a name="experiments"></a>Experiment

Ett experiment är en gruppering av många körningar från ett angivet skript. Det är alltid hör till en arbetsyta. När du skickar in en körning ska ange du ett namn på experiment. Information om körningen lagras under försöket. Om du skickar in en körning och anger ett experiment namn som inte finns, skapas ett nytt experiment med det nyligen angivna namnet automatiskt.

Ett exempel på hur du använder ett experiment finns i [Självstudier: träna din första modell](tutorial-1st-experiment-sdk-train.md).


### <a name="github-tracking-and-integration"></a>GitHub spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Detta fungerar med körningar som skickats med en uppskattning, ML-pipeline eller skript körning. Den fungerar även för körningar som skickats från SDK eller Machine Learning CLI.

Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Loggning

När du utvecklar din lösning använder du Azure Machine Learning python SDK i python-skriptet för att logga godtyckliga mått. Efter körningen frågar du måtten för att avgöra om körningen har genererat den modell som du vill distribuera.

### <a name="ml-pipelines"></a>ML-Pipelines

Du använder maskin inlärnings pipeliner för att skapa och hantera arbets flöden som häftar ihop Machine Learning-faser. En pipeline kan till exempel omfatta förberedelse av data, modell utbildning, modell distribution och steg för härledning/poängsättning. Varje fas kan omfatta flera steg, som kan köras obevakat i olika beräkningsmål. 

Pipeline-steg kan återanvändas och kan köras utan att köra efterföljande steg om utdata från det steget inte har ändrats. Du kan till exempel omträna en modell utan att köra kostsamma data förberedelse steg om data inte har ändrats. Pipelines gör det också möjligt för data experter att samar beta medan de arbetar med olika delar av ett Machine Learning-arbetsflöde.

Mer information om maskin inlärnings pipeliner med den här tjänsten finns i [pipelines och Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modeller

I sin enklaste är en modell en typ av kod som hämtar indata och utdata. Skapa en modell för maskininlärning innebär att välja en algoritm, att förse den med data och justering av hyperparametrar. Utbildning är en iterativ process som skapar en tränad modell, som kapslar in det modellen har lärt dig under utbildning.

En modell produceras av en körning i Azure Machine Learning. Du kan också använda en modell som har tränats utanför Azure Machine Learning. Du kan registrera en modell i en Azure Machine Learning-arbetsyta.

Azure Machine Learning är en Framework-oberoende. När du skapar en modell kan du använda alla populära ramverk för maskin inlärning, till exempel Scikit – lära, XGBoost, PyTorch, TensorFlow och kedjor.

Ett exempel på hur du tränar en modell med Scikit – lära och en uppskattningar finns i [Självstudier: träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

**Modell registret** håller reda på alla modeller i din Azure Machine Learning-arbetsyta.

Modeller identifieras av namn och version. Varje gången du registrerar en modell med samma namn som en befintlig, förutsätter registret att det är en ny version. Versionen ökar och den nya modellen registreras under samma namn.

När du registrerar modellen kan du ange ytterligare metadata-Taggar och sedan använda taggarna när du söker efter modeller.

> [!TIP]
> En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i Azure Machine Learning arbets ytan. Efter registreringen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Du kan inte ta bort en registrerad modell som används av en aktiv distribution.

Ett exempel på hur du registrerar en modell finns i [träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Körningar

En körning är en enskild körning av ett utbildnings skript. Azure Machine Learning registrerar alla körningar och lagrar följande information:

* Metadata om körning (tidstämpel, varaktighet och så vidare)
* Mått som loggas av ditt skript
* Utdatafiler som autosamlas in av experimentet eller som uttryckligen laddas upp av dig
* En ögonblicksbild av den katalog som innehåller dina skript innan körningen

Du skapar en körning när du skickar ett skript för att träna en modell. En körning kan ha noll eller flera underordnade körs. Körningen på den översta nivån kan till exempel ha två underordnade körningar, som var och en kan ha sin egen underordnade körning.

### <a name="run-configurations"></a>Köra konfigurationer

En körnings konfiguration är en uppsättning instruktioner som definierar hur ett skript ska köras i ett angivet beräknings mål. Konfigurationen innehåller en mängd olika beteende definitioner, till exempel om du vill använda en befintlig python-miljö eller om du vill använda en Conda miljö som bygger på en specifikation.

En körnings konfiguration kan sparas i en fil i den katalog som innehåller ditt utbildnings skript, eller så kan den konstrueras som ett InMemory-objekt och användas för att skicka in en körning.

För att till exempel köra konfigurationer, se [Välj och Använd ett beräknings mål för att träna din modell](how-to-set-up-training-targets.md).
### <a name="snapshots"></a>Ögonblicksbilder

När du skickar en körning komprimerar Azure Machine Learning den katalog som innehåller skriptet som en zip-fil och skickar den till beräknings målet. Zip-filen extraheras sedan och skriptet körs där. Azure Machine Learning lagrar också zip-filen som en ögonblicksbild som en del av den kör posten. Alla som har åtkomst till arbetsytan kan bläddra en kör post och ladda ned ögonblicksbilden.

> [!NOTE]
> Du kan förhindra att onödiga filer tas med i ögonblicks bilden genom att göra en IGNORE-fil (. gitignore eller. amlignore). Placera filen i ögonblicks bild katalogen och Lägg till de fil namn som ska ignoreras i den. Filen. amlignore använder samma [syntax och mönster som. gitignore-filen](https://git-scm.com/docs/gitignore). Om båda filerna finns får filen. amlignore företräde.

### <a name="training-scripts"></a>Utbildningsskript

För att träna en modell kan du ange den katalog som innehåller inlärningsskript och tillhörande filer. Du kan också ange ett experiment namn som används för att lagra information som samlas in under utbildningen. Under utbildningen kopieras hela katalogen till tränings miljön (Compute Target) och skriptet som anges av körnings konfigurationen startas. En ögonblicksbild av katalogen lagras också under experiment på arbetsytan.

Ett exempel finns i [Självstudier: träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="workspaces"></a>Arbetsytor

[Arbets ytan](concept-workspace.md) är den översta resursen för Azure Machine Learning. Det ger en central plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning. Du kan dela en arbets yta med andra. En detaljerad beskrivning av arbets ytor finns i [Vad är en Azure Machine Learning arbets yta?](concept-workspace.md).

### <a name="next-steps"></a>Nästa steg

För att komma igång med Azure Machine Learning, se:

* [Vad är Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)
* [Självstudie (del 1): träna en modell](tutorial-train-models-with-aml.md)
