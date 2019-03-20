---
title: 'Maskininlärning i molnet: Arkitektur för Användarvillkor'
titleSuffix: Azure Machine Learning service
description: Läs mer om arkitekturen, termer och begrepp som utgör Azure Machine Learning-tjänsten. Här beskrivs också hur det allmänna arbetsflödet för att använda tjänsten och Azure-tjänster som används av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1640b1cdb9410f33f6556667f36aafcfe575a082
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080330"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Så här fungerar Azure Machine Learning-tjänsten: Arkitektur och begrepp

Den här artikeln beskrivs arkitekturen och begrepp för Azure Machine Learning-tjänsten. De viktigaste komponenterna i tjänsten och det allmänna arbetsflödet för att använda tjänsten visas i följande diagram:

[![Azure Machine Learning-service-arkitektur och arbetsflöde](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Arbetsflödet följer vanligtvis den här sekvensen:

1. Utveckla maskininlärning utbildning skript i **Python**.
1. Skapa och konfigurera en **beräkningsmålet**.
1. **Skicka skripten** till den konfigurerade beräkningsmål ska köras i den miljön. Vid träning, skripten kan läsa från eller skriva till **datalager**. Posterna i körningen sparas som **körs** i den **arbetsytan** och grupperade under **experiment**.
1. **Fråga experimentet** för loggade mått från de aktuella och tidigare körningarna. Om mått som inte visar önskat utfall ska köras i slinga att gå tillbaka till steg 1 och iterera på dina skript.
1. När du har hittat ett tillfredsställande kör registrera beständiga modellen i den **modellen registret**.
1. Utveckla ett bedömningsskript.
1. **Skapa en avbildning** och registrera den i den **avbildningsregister**.
1. **Distribuera avbildningen** som en **webbtjänsten** i Azure.


> [!NOTE]
> Även om den här artikeln definierar termer och begrepp som används av Azure Machine Learning-tjänsten, anger inte termer och begrepp för Azure-plattformen. Läs mer om Azure-plattformen terminologi, den [Microsoft Azures ordlista](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Arbetsyta

Arbetsytan är den översta resursen för Azure Machine Learning-tjänsten. Det ger en centraliserad plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.

Arbetsytan och ser en lista över beräkningsmål som du kan använda för att träna din modell. Det håller också en historik över träningskörningar, inklusive loggar, mått, utdata och en ögonblicksbild av dina skript. Du kan använda den här informationen för att avgöra vilka kör utbildning ger den bästa modellen.

Du kan registrera modeller med arbetsytan. Du använder en modell som är registrerade och bedömnings-skript för att skapa en avbildning. Du kan sedan distribuera avbildningen till Azure Container Instances, Azure Kubernetes Service eller till en fält-programmable gate (FPGA) som en REST-baserade HTTP-slutpunkt. Du kan också distribuera avbildningen till en Azure IoT Edge-enhet som en modul.

Du kan skapa flera arbetsytor och varje arbetsyta kan delas av flera personer. När du delar en arbetsyta, kan du styra åtkomsten till den genom att tilldela användarna följande roller:

* Ägare
* Deltagare
* Läsare

När du skapar en ny arbetsyta skapas automatiskt flera Azure-resurser som används av arbetsytan:

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registrerar docker-behållare som du använder vid träning och när du distribuerar en modell.
* [Azure-lagringskonto](https://azure.microsoft.com/services/storage/): Används som standard-datalager för arbetsytan.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Butiker övervakningsinformation om dina modeller.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Butiker hemligheter som används av compute mål och annan känslig information som behövs av arbetsytan.

> [!NOTE]
> Förutom att skapa nya versioner, kan du också använda befintliga Azure-tjänster.

En taxonomi för arbetsytan illustreras i följande diagram:

[![Arbetsytan taxonomi](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="experiment"></a>Experiment

Ett experiment är en gruppering av många körs från ett angivet skript. Det är alltid hör till en arbetsyta. När du skickar in en körning ska ange du ett namn på experiment. Information om körningen lagras under försöket. Om du skickar en körning och ange ett namn på experiment som inte finns, skapas automatiskt ett nytt experiment med det nyss angivna namnet.

Ett exempel på hur du använder ett experiment finns i [snabbstarten: Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).

## <a name="model"></a>Modell

I sin enklaste är en modell en typ av kod som hämtar indata och utdata. Skapa en modell för maskininlärning innebär att välja en algoritm, att förse den med data och justering av hyperparametrar. Utbildning är en iterativ process som skapar en tränad modell, som kapslar in det modellen har lärt dig under utbildning.

En modell produceras av en körning i Azure Machine Learning. Du kan också använda en modell som har tränats utanför Azure Machine Learning. Du kan registrera en modell i en arbetsyta för Azure Machine Learning-tjänsten.

Azure Machine Learning-tjänsten är framework oberoende. När du skapar en modell kan använda du alla populära machine learning-ramverk, som lär du dig Scikit, XGBoost, PyTorch, TensorFlow, Chainer och Microsoft Cognitive Toolkit (kallades tidigare CNTK).

Ett exempel för att träna en modell finns i [snabbstarten: Skapa en arbetsyta för Machine Learning-tjänsten](quickstart-get-started.md).

### <a name="model-registry"></a>Modell-registret

Modellen registret håller reda på alla modeller i din arbetsyta för Azure Machine Learning-tjänsten.

Modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig förutsätter registret att det är en ny version. Versionen ökas och den nya modellen är registrerad med samma namn.

När du registrerar modellen kan du ange ytterligare metadatataggar och sedan använda taggar när du söker efter modeller.

Du kan inte ta bort modeller som används av en avbildning.

Ett exempel med att registrera en modell finns i [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run-configuration"></a>Köra konfiguration

En kör konfiguration är en uppsättning instruktioner som definierar hur ett skript ska köras i en angiven beräkningsmål. Konfigurationen innehåller en bred uppsättning beteende definitioner, till exempel om du vill använda en befintlig Python-miljö eller använda en Conda-miljö som är byggd från en specifikation.

En kör konfiguration kan vara kvar i en fil på den katalog som innehåller utbildning skriptet eller den kan konstrueras som ett objekt i minnet och används för att skicka en körning.

Till exempel kör konfigurationer, se [Använd beräkningsmål träna din modell](how-to-set-up-training-targets.md).

## <a name="datastore"></a>Datalager

Ett datalager är en abstraktion av lagring via ett Azure storage-konto. Datalagringen kan använda en Azure blob-behållare eller en Azure-filresurs som backend-lagring. Varje arbetsyta har ett standard-datalager, och du kan registrera ytterligare datalager.

Använda Python SDK-API eller Azure Machine Learning CLI för att lagra och hämta filer från databasen.

## <a name="compute-target"></a>Beräkningsmål

Beräkningsmål är beräkningsresursen som används för att köra skriptet utbildning eller vara värd för tjänstdistributionen av. Beräkningsmål som stöds är:

| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Den lokala datorn | ✓ | &nbsp; |
| Azure Machine Learning-beräkning | ✓ | &nbsp; |
| En virtuell Linux-dator i Azure</br>(till exempel Data Science Virtual Machine) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Apache Spark för HDInsight | ✓ | &nbsp; |
| Azure Container Instances | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Field-programmable gate array) | &nbsp; | ✓ |

Beräkningsmål är kopplade till en arbetsyta. Compute mål än den lokala datorn som delas av användare i arbetsytan.

### <a name="managed-and-unmanaged-compute-targets"></a>Hanterade och ohanterade beräkningsmål

* **Hanterade**: Beräkningsmål som skapas och hanteras av Azure Machine Learning-tjänsten. Dessa beräkningsalternativ mål är optimerade för machine learning-arbetsbelastningar. Azure Machine Learning-instanser är den enda hanterade beräkningsmål från och med 4 December 2018. Ytterligare hanterade beräkningsmål kan läggas till i framtiden.

    Du kan skapa machine learning beräkningsinstanser direkt via arbetsytan med hjälp av Azure-portalen, Azure Machine Learning SDK eller Azure CLI. Alla andra beräkningsmål måste skapas utanför arbetsytan och sedan ansluts till den.

* **Ohanterade**: Beräkningsmål som är *inte* hanteras av Azure Machine Learning-tjänsten. Du kan behöva skapa dem utanför Azure Machine Learning och sedan koppla dem till din arbetsyta före användning. Ohanterade beräkningsmål kan kräva ytterligare steg för att underhålla eller förbättra prestanda för arbetsbelastningar för machine learning.

Information om att välja beräkningsmål för träning, finns i [Använd beräkningsmål träna din modell](how-to-set-up-training-targets.md).

Information om att välja beräkningsmål för distribution finns i den [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

## <a name="training-script"></a>Inlärningsskript

För att träna en modell kan du ange den katalog som innehåller inlärningsskript och tillhörande filer. Du kan även ange ett namn på experiment, som används för att lagra information som samlas in under utbildning. Hela katalogen kopieras till miljön (beräkningsmål) vid träning, och skriptet som anges av körningskonfigurationen har startats. En ögonblicksbild av katalogen lagras också under experiment på arbetsytan.

Ett exempel finns i [skapa en arbetsyta med Python](quickstart-get-started.md).

## <a name="run"></a>Kör

En körning är en post som innehåller följande information:

* Metadata om körningen (tidsstämpel, varaktighet och så vidare)
* Mått som loggas av skriptet
* Utdatafilerna som autocollected av experimentet eller uttryckligen överförts av dig
* En ögonblicksbild av den katalog som innehåller dina skript innan körningen

Du kan skapa en körning när du skickar in ett skript för att träna en modell. En körning kan ha noll eller flera underordnade körs. Den översta körningen kan till exempel ha två underordnade körningar, som kan ha sin egen underordnade kör.

Ett exempel med att visa körningar som genereras av träna en modell finns i [snabbstarten: Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).

## <a name="snapshot"></a>Ögonblicksbild

När du skickar en körning komprimerar katalogen som innehåller skriptet som en zip-fil och skickar dem till beräkningsmål-i Azure Machine Learning. Zip-filen hämtas sedan och skriptet körs det. Azure Machine Learning lagrar också zip-filen som en ögonblicksbild som en del av den kör posten. Alla som har åtkomst till arbetsytan kan bläddra en kör post och ladda ned ögonblicksbilden.

## <a name="activity"></a>Aktivitet

En aktivitet representerar en långvarig åtgärd. Följande åtgärder är exempel på aktiviteter:

* Skapa eller ta bort ett beräkningsmål
* Köra ett skript på ett beräkningsmål

Aktiviteter kan ge meddelanden via SDK eller webbläsaren så att du enkelt kan övervaka förloppet för dessa åtgärder.

## <a name="image"></a>Bild

Bilder är ett sätt att distribuera en modell, tillsammans med alla komponenter måste du använda modellen på ett tillförlitligt sätt. En avbildning innehåller följande objekt:

* En modell.
* En arbetsflödesbaserad skript eller program. Du kan använda skript för att skicka indata till modellen och returnera utdata från modellen.
* De beroenden som krävs av modellen eller bedömnings-skript eller program. Du kan exempelvis innehålla en miljö Conda-fil som listar beroendena för Python-paketet.

Azure Machine Learning kan skapa två typer av bilder:

* **FPGA bild**: Används när du distribuerar till en fält-programmable gate array i Azure.
* **Docker-avbildning**: Används när du distribuerar till beräkningsmål än FPGA. Exempel är Azure Container Instances och Azure Kubernetes Service.

Ett exempel för att skapa en avbildning finns i [distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Avbildningsregister

Avbildningsregister håller reda på avbildningar som skapats från dina modeller. Du kan ange ytterligare metadatataggar när du skapar avbildningen. Metadatataggar lagras som bild-registret, och du kan skicka frågor mot dem för att hitta din avbildning.

## <a name="deployment"></a>Distribution

En distribution är en instansiering bildens i antingen en webbtjänst som kan användas i molnet eller en IoT-modul för integrerad enhet distributioner.

### <a name="web-service"></a>Webbtjänst

En distribuerad webbtjänst kan använda Azure Container Instances, Azure Kubernetes Service eller FPGA. Du har skapat tjänsten från en avbildning som innehåller din modell, skript och tillhörande filer. Avbildningen har en Utjämning av nätverksbelastning, HTTP-slutpunkt som tar emot bedömnings förfrågningar som skickas till webbtjänsten.

Azure hjälper dig att övervaka din distribution av webbtjänster genom att samla in Application Insights telemetry eller modell telemetri om du har valt att aktivera den här funktionen. Dessa data är tillgänglig endast för dig och lagras i dina Application Insights och storage-konto instanser.

Om du har aktiverat automatisk skalning, skalar Azure automatiskt din distribution.

Ett exempel för att distribuera en modell som en webbtjänst finns i [distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>IoT-modul

En distribuerad IoT-modul är en Docker-behållare som innehåller din modell och associerade skript eller program och eventuella ytterligare beroenden. Du kan distribuera dessa moduler med hjälp av Azure IoT Edge på Edge-enheter.

Om du har aktiverat övervakning, Azure att samlar in telemetridata från modellen i Azure IoT Edge-modul. Dessa data är tillgänglig för dig och lagras i din instans av storage-konto.

Azure IoT Edge säkerställer att din modul körs och det övervakar den enhet som är värd för den.

## <a name="pipeline"></a>Pipeline

Använder du machine learning pipelines för att skapa och hantera arbetsflöden som sätta ihop machine learning faser. En pipeline kan till exempel innehålla förberedelse av data, modellträning, distribution av modeller och inferensjobb faser. Varje fas kan omfatta flera steg, som kan köras obevakat i olika beräkningsmål.

Mer information om machine learning pipelines med den här tjänsten finns i [Pipelines och Azure Machine Learning](concept-ml-pipelines.md).

## <a name="logging"></a>Loggning

När du utvecklar din lösning kan du använda Azure Machine Learning Python SDK i ditt Python-skript för att logga godtyckligt mått. När du kör, fråga om mått för att avgöra om körningen har producerat modellen som du vill distribuera.

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Machine Learning-tjänsten, se:

* [Vad är Azure Machine Learning Service?](overview-what-is-azure-ml.md)
* [Snabbstart: Skapa en arbetsyta med Python](quickstart-get-started.md)
* [Självstudie: Träna en modell](tutorial-train-models-with-aml.md)
* [Skapa en arbetsyta med en resource manager-mall](how-to-create-workspace-template.md)