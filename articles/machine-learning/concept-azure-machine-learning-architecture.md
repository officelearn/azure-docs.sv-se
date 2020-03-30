---
title: Arkitektur & nyckelbegrepp
titleSuffix: Azure Machine Learning
description: Lär dig mer om arkitektur, termer, koncept och arbetsflöden som utgör Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f779781eee81bf85f6420e5bae6b0feb62680b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064177"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Så här fungerar Azure Machine Learning: Arkitektur och begrepp

Lär dig mer om arkitektur, koncept och arbetsflöde för Azure Machine Learning. Huvudkomponenterna i tjänsten och det allmänna arbetsflödet för att använda tjänsten visas i följande diagram:

![Azure Machine Learning arkitektur och arbetsflöde](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Arbetsflöde

Arbetsflödet för maskininlärningsmodell följer i allmänhet den här sekvensen:

1. **Tåg**
    + Utveckla maskininlärningsskript i **Python,** **R**eller med den visuella designern.
    + Skapa och konfigurera ett **beräkningsmål**.
    + **Skicka skripten** till ett konfigurerat beräkningsmål som ska köras i den miljön. Under träningen kan skripten läsa från eller skriva till **datalager**. Loggar och utdata som produceras under träningen sparas som **körs** på **arbetsytan** och grupperas under **experiment**.

1. **Paket** - När en tillfredsställande körning har hittats registrerar du den kvarstående modellen i **modellregistret**.

1. **Validera** - **Fråga experimentet** för loggade mått från aktuella och tidigare körningar. Om måtten inte anger ett önskat resultat, loop tillbaka till steg 1 och iterera på dina skript.

1. **Distribuera** - Utveckla ett bedömningsskript som använder modellen och **Distribuera modellen** som en **webbtjänst** i Azure eller till en **IoT Edge-enhet**.

1. **Monitor** - Övervaka för **datadrift** mellan träningsdatauppsättningen och inferensdata för en distribuerad modell. Vid behov, slinga tillbaka till steg 1 för att träna om modellen med nya träningsdata.

## <a name="tools-for-azure-machine-learning"></a>Verktyg för Azure Machine Learning

Använd de här verktygen för Azure Machine Learning:

+  Interagera med tjänsten i alla Python-miljöer med [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interagera med tjänsten i alla R-miljöer med [Azure Machine Learning SDK för R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatisera dina maskininlärningsaktiviteter med [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Använd [Azure Machine Learning designer (förhandsversion)](concept-designer.md) för att utföra arbetsflödesstegen utan att skriva kod.


> [!NOTE]
> Även om den här artikeln definierar termer och begrepp som används av Azure Machine Learning, definierar den inte termer och begrepp för Azure-plattformen. Mer information om Azure-plattformsterminologi finns i [Microsoft Azure-ordlistan](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Ordlista

* [Aktivitet](#activities)
* [Arbetsyta](#workspaces)
    * [Experiment](#experiments)
        * [Köra](#runs) 
            * [Kör konfiguration](#run-configurations)
            * [Ögonblicksbild](#snapshots)
            * [Git spårning](#github-tracking-and-integration)
            * [Loggning](#logging)
    * [ML-pipelines](#ml-pipelines)
    * [Modeller](#models)
        * [Miljöer](#environments)
        * [Utbildningsskript](#training-scripts)
        * [Skattningar](#estimators)
    * [Slutpunkter](#endpoints)
        * [Webbtjänst](#web-service-endpoint)
        * [IoT-moduler](#iot-module-endpoints)
    * [Datauppsättning & datalager](#datasets-and-datastores)
    * [Beräkningsmål](#compute-targets)

### <a name="activities"></a>Aktiviteter

En aktivitet representerar en tidskrävande åtgärd. Följande åtgärder är exempel på aktiviteter:

* Skapa eller ta bort ett beräkningsmål
* Köra ett skript på ett beräkningsmål

Aktiviteter kan ge meddelanden via SDK eller webbgränssnittet så att du enkelt kan övervaka förloppet för dessa åtgärder.

### <a name="workspaces"></a>Arbetsytor

[Arbetsytan](concept-workspace.md) är den resurs på den högsta nivån för Azure Machine Learning. Det ger en centraliserad plats att arbeta med alla artefakter du skapar när du använder Azure Machine Learning. Du kan dela en arbetsyta med andra. En detaljerad beskrivning av arbetsytor finns i [Vad är en Azure Machine Learning-arbetsyta?](concept-workspace.md)

### <a name="experiments"></a>Experiment

Ett experiment är en gruppering av många körningar från ett angivet skript. Den tillhör alltid en arbetsyta. När du skickar en körning anger du ett experimentnamn. Information för körningen lagras under det experimentet. Om du skickar en körning och anger ett experimentnamn som inte finns skapas ett nytt experiment med det nyligen angivna namnet automatiskt.

Ett exempel på hur du använder ett experiment finns i [Självstudiekurs: Träna din första modell](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Körningar

En körning är en enda körning av ett utbildningsskript. Ett experiment innehåller vanligtvis flera körningar.

Azure Machine Learning registrerar alla körningar och lagrar följande information i experimentet:

* Metadata om körningen (tidsstämpel, varaktighet och så vidare)
* Mått som loggas av skriptet
* Utdatafiler som automatiskt samlas in av experimentet eller som uttryckligen laddas upp av dig
* En ögonblicksbild av katalogen som innehåller skripten, före körningen

Du skapar en körning när du skickar in ett skript för att träna en modell. En körning kan ha noll eller fler underordnade körningar. Den översta körningen kan till exempel ha två underordnade körningar, som var och en kan ha en egen underordnad körning.

### <a name="run-configurations"></a>Kör konfigurationer

En körningskonfiguration är en uppsättning instruktioner som definierar hur ett skript ska köras i ett angivet beräkningsmål. Konfigurationen innehåller en bred uppsättning beteendedefinitioner, till exempel om du vill använda en befintlig Python-miljö eller använda en Conda-miljö som är byggd utifrån en specifikation.

En körningskonfiguration kan sparas i en fil i katalogen som innehåller utbildningsskriptet, eller så kan den skapas som ett in-memory-objekt och användas för att skicka en körning.

Kör till exempel konfigurationer, se [Välj och använda ett beräkningsmål för att träna din modell](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Ögonblicksbilder

När du skickar en körning komprimerar Azure Machine Learning katalogen som innehåller skriptet som en zip-fil och skickar den till beräkningsmålet. Zip-filen extraheras sedan och skriptet körs där. Azure Machine Learning lagrar även zip-filen som en ögonblicksbild som en del av körningsposten. Alla som har åtkomst till arbetsytan kan bläddra i en körningspost och hämta ögonblicksbilden.

> [!NOTE]
> Om du vill förhindra att onödiga filer inkluderas i ögonblicksbilden gör du en ignorerad fil (.gitignore eller .amlignore). Placera den här filen i katalogen Ögonblicksbild och lägg till filnamnen som ska ignoreras i den. .amlignore-filen använder samma [syntax och mönster som .gitignore-filen](https://git-scm.com/docs/gitignore). Om det finns båda filerna har .amlignore-filen företräde.

### <a name="github-tracking-and-integration"></a>GitHub-spårning och -integration

När du startar en utbildningskörning där källkatalogen är en lokal Git-databas lagras information om databasen i körningshistoriken. Detta fungerar med körningar som skickas med en uppskattning, ML-pipeline eller skriptkörning. Det fungerar också för körningar som skickats från SDK eller Machine Learning CLI.

Mer information finns i [Git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Loggning

När du utvecklar din lösning använder du Azure Machine Learning Python SDK i python-skriptet för att logga godtyckliga mått. Efter körningen frågar du måtten för att avgöra om körningen har producerat den modell som du vill distribuera.

### <a name="ml-pipelines"></a>ML Rörledningar

Du använder machine learning-pipelines för att skapa och hantera arbetsflöden som syr ihop maskininlärningsfaser. En pipeline kan till exempel innehålla dataförberedelser, modellutbildning, modelldistribution och inferens-/bedömningsfaser. Varje fas kan omfatta flera steg, som var och en kan köras obevakad i olika beräkningsmål. 

Pipeline-stegen kan återanvändas och kan köras utan att köra de föregående stegen igen om utdata för dessa steg inte har ändrats. Du kan till exempel träna om en modell utan att köra om kostsamma dataförberedelsesteg om data inte har ändrats. Pipelines gör det också möjligt för dataforskare att samarbeta när de arbetar med separata områden i ett arbetsflöde för maskininlärning.

Mer information om pipelines för maskininlärning med den här tjänsten finns i [Pipelines och Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modeller

I sin enklaste, en modell är en bit kod som tar en ingång och producerar utdata. Att skapa en maskininlärningsmodell innebär att välja en algoritm, förse den med data och justera hyperparametrar. Utbildning är en iterativ process som producerar en utbildad modell, som sammanfattar vad modellen lärt sig under utbildningsprocessen.

En modell produceras av en körning i Azure Machine Learning. Du kan också använda en modell som tränas utanför Azure Machine Learning. Du kan registrera en modell på en Azure Machine Learning-arbetsyta.

Azure Machine Learning är ramoberoende. När du skapar en modell kan du använda alla populära ramverk för maskininlärning, till exempel Scikit-learn, XGBoost, PyTorch, TensorFlow och Chainer.

Ett exempel på utbildning av en modell med Scikit-learn och en estimator finns i [Självstudiekurs: Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

**Modellregistret** håller reda på alla modeller i din Azure Machine Learning-arbetsyta.

Modeller identifieras med namn och version. Varje gång du registrerar en modell med samma namn som en befintlig, antar registret att det är en ny version. Versionen ökas och den nya modellen registreras under samma namn.

När du registrerar modellen kan du ange ytterligare metadatataggar och sedan använda taggarna när du söker efter modeller.

> [!TIP]
> En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enda modell på arbetsytan Azure Machine Learning. Efter registreringen kan du sedan hämta eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Du kan inte ta bort en registrerad modell som används av en aktiv distribution.

Ett exempel på hur du registrerar en modell finns i [Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Miljöer

Azure ML-miljöer används för att ange konfigurationen (Docker / Python / Spark / etc.) som används för att skapa en reproducerbar miljö för dataförberedelse, modellutbildning och modellvisning. De hanteras och versionförs enheter inom din Azure Machine Learning-arbetsyta som aktiverar reproducerbara, granskningsbara och bärbara machine learning-arbetsflöden över olika beräkningsmål.

Du kan använda ett miljöobjekt på din lokala beräkning för att utveckla ditt träningsskript, återanvända samma miljö på Azure Machine Learning Compute för modellutbildning i stor skala och till och med distribuera din modell med samma miljö. 

Lär dig hur du [skapar och hanterar en återanvändbar ML-miljö](how-to-use-environments.md) för träning och slutledning.

### <a name="training-scripts"></a>Utbildningsskript

Om du vill träna en modell anger du katalogen som innehåller utbildningsskriptet och tillhörande filer. Du anger också ett experimentnamn som används för att lagra information som samlas in under träningen. Under träningen kopieras hela katalogen till träningsmiljön (beräkningsmål) och skriptet som anges av körningskonfigurationen startas. En ögonblicksbild av katalogen lagras också under experimentet på arbetsytan.

Ett exempel finns i [Självstudiekurs: Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Skattningar

För att underlätta modellutbildning med populära ramverk kan du med uppskattningsklassen enkelt konstruera körningskonfigurationer. Du kan skapa och använda en allmän [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka in utbildningsskript som använder alla inlärningsramverk som du väljer (till exempel scikit-learn).

För uppgifter i PyTorch, TensorFlow och Chainer tillhandahåller Azure Machine Learning även respektive [PyTorch-,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow-](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och [Chainer-skattuppskattningar](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) för att förenkla med hjälp av dessa ramverk.

Mer information finns i följande artiklar:

* [Tåg ML modeller med skattställare](how-to-train-ml-models.md).
* [Träna Pytorch-djupinlärningsmodeller i stor skala med Azure Machine Learning](how-to-train-pytorch.md).
* [Träna och registrera TensorFlow-modeller i stor skala med Azure Machine Learning](how-to-train-tensorflow.md).
* [Träna och registrera Chainer-modeller i stor skala med Azure Machine Learning](how-to-train-chainer.md).

### <a name="endpoints"></a>Slutpunkter

En slutpunkt är en instansiering av din modell till antingen en webbtjänst som kan finnas i molnet eller en IoT-modul för integrerade enhetsdistributioner.

#### <a name="web-service-endpoint"></a>Slutpunkt för webbtjänsten

När du distribuerar en modell som en webbtjänst kan slutpunkten distribueras på Azure Container Instances, Azure Kubernetes Service eller FPGA. Du skapar tjänsten från dina modell-, skript- och associerade filer. Dessa placeras i en basbehållarevseende som innehåller körningsmiljön för modellen. Avbildningen har en belastningsbalanserad HTTP-slutpunkt som tar emot bedömningsbegäranden som skickas till webbtjänsten.

Azure hjälper dig att övervaka din webbtjänst genom att samla in Program Insights telemetri eller modelltelemetri, om du har valt att aktivera den här funktionen. Telemetridata är endast tillgängliga för dig och lagras i dina Application Insights- och lagringskontoinstanser.

Om du har aktiverat automatisk skalning skalar Azure automatiskt distributionen.

Ett exempel på att distribuera en modell som en webbtjänst finns i [Distribuera en avbildningsklassificeringsmodell i Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Slutpunkter för IoT-modul

En distribuerad IoT-modulslutpunkt är en Docker-behållare som innehåller din modell och associerade skript eller program och eventuella ytterligare beroenden. Du distribuerar dessa moduler med hjälp av Azure IoT Edge på kantenheter.

Om du har aktiverat övervakning samlar Azure in telemetridata från modellen i Azure IoT Edge-modulen. Telemetridata är endast tillgängliga för dig och lagras i din lagringskontoinstans.

Azure IoT Edge säkerställer att din modul körs och övervakar enheten som är värd för den.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Beräkningsinstans (förhandsgranskning)

En **Azure Machine Learning-beräkningsinstans** (tidigare virtuell dator för bärbara datorer) är en fullständigt hanterad molnbaserad arbetsstation som innehåller flera verktyg och miljöer installerade för maskininlärning. Beräkningsinstanser kan användas som beräkningsmål för utbildning och inferencing-jobb. För stora uppgifter är [Azure Machine Learning-beräkningskluster](how-to-set-up-training-targets.md#amlcompute) med skalningsfunktioner med flera noder ett bättre val av beräkningsmål.

Läs mer om [beräkningsinstanser](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Dataset och datalager

**Azure Machine Learning Datasets** (förhandsversion) gör det enklare att komma åt och arbeta med dina data. Datauppsättningar hanterar data i olika scenarier, till exempel modellutbildning och skapande av pipeline. Med Hjälp av Azure Machine Learning SDK kan du komma åt underliggande lagring, utforska data och hantera livscykeln för olika datauppsättningsdefinitioner.

Datauppsättningar ger metoder för att arbeta med data `from_delimited_files()` `to_pandas_dataframe()`i populära format, till exempel använda eller .

Mer information finns i [Skapa och registrera Azure Machine Learning Datasets](how-to-create-register-datasets.md).  Fler exempel med Datauppsättningar finns i [exempelböckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Ett **datalager** är en lagringsabstraktion över ett Azure-lagringskonto. Datastore kan använda antingen en Azure blob-behållare eller en Azure-filresurs som backend-lagring. Varje arbetsyta har ett standarddatalager och du kan registrera ytterligare datalager. Använd Python SDK API eller Azure Machine Learning CLI för att lagra och hämta filer från databutiken.

### <a name="compute-targets"></a>Beräkningsmål

Med ett [beräkningsmål](concept-compute-target.md) kan du ange beräkningsresursen där du kör ditt träningsskript eller är värd för tjänstdistributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräkningsresurs.

Läs mer om tillgängliga [beräkningsmål för utbildning och distribution](concept-compute-target.md).

### <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure Machine Learning finns i:

* [Vad är Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)
* [Handledning (del 1): Träna en modell](tutorial-train-models-with-aml.md)
