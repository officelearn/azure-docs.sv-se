---
title: Hur fungerar Azure Machine Learning-tjänsten?
description: Läs mer om arkitekturen, termer och begrepp som utgör Azure Machine Learning-tjänsten. Här beskrivs också hur det allmänna arbetsflödet för att använda tjänsten och Azure-tjänster som används av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 10/24/2018
ms.openlocfilehash: b00f72c987b6ce8c44796bd036af670ec39fa7a6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093640"
---
# <a name="how-the-azure-machine-learning-service-works-architecture-and-concepts"></a>Hur fungerar Azure Machine Learning-tjänsten: arkitektur och begrepp

Det här dokumentet beskriver arkitekturen och begrepp för Azure Machine Learning-tjänsten. Följande diagram visas de viktigaste komponenterna i tjänsten och visar det allmänna arbetsflödet när du använder tjänsten: 

[![Azure Machine Learning-tjänsten arkitektur och arbetsflöde](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Arbetsflödet följer vanligtvis dessa steg:

1. Utveckla maskininlärning utbildning skript i __Python__.
1. Skapa och konfigurera en __beräkningsmålet__.
1. __Skicka skripten__ till den konfigurerade beräkningsmål ska köras i den miljön. Vid träning, lagrar beräkningsmål kör poster till en __datalager__. Det poster sparas i en __experimentera__.
1. __Fråga experimentet__ för loggade mått från de aktuella och tidigare körningarna. Om mått som inte anger ett önskat utfall kan köras i slinga att gå tillbaka till steg 1 och iterera på dina skript.
1. När ett tillfredsställande kör hittas, registrera beständiga modellen i den __modellen registret__.
1. Utveckla ett bedömningsskript.
1. __Skapa en avbildning__ och registrera den i den __avbildningsregister__. 
1. __Distribuera avbildningen__ som en __webbtjänsten__ i Azure.


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> Även om det här dokumentet definierar termer och begrepp som används av Azure Machine Learning, anger inte termer och begrepp för Azure-plattformen. Mer information om terminologi för Azure-plattformen finns i den [Microsoft Azures ordlista](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Arbetsyta

Arbetsytan är den översta resursen för Azure Machine Learning-tjänsten. Det ger en centraliserad plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.

Arbetsytan och ser en lista över beräkningsmål som kan användas för att träna din modell. Det håller också en historik över träningskörningar, inklusive loggar, mått, utdata och en ögonblicksbild av dina skript. Den här informationen används för att avgöra vilka kör utbildning ger den bästa modellen.

Modeller är registrerade med arbetsytan. Registrerade modellen och bedömnings skript används för att skapa en avbildning. Avbildningen kan sedan distribueras till Azure Container Instances, Azure Kubernetes Service eller till en fält-programmable gate (FPGA) som en REST-baserade HTTP-slutpunkt. Det kan också distribueras till en Azure IoT Edge-enhet som en modul.

Du kan skapa flera arbetsytor och varje arbetsyta kan delas av flera personer. När du delar en arbetsyta, styra åtkomst till arbetsytan genom att tilldela användarna följande roller:

* Ägare
* Deltagare
* Läsare

När du skapar en ny arbetsyta skapas automatiskt flera Azure-resurser som används av arbetsytan:

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -registrerar docker-behållare som används vid träning och när du distribuerar en modell.
* [Azure Storage](https://azure.microsoft.com/services/storage/) – används som standard-datalager för arbetsytan.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) – butiker övervakningsinformation om dina modeller.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) – butiker hemligheter som används av beräkningsmål och annan känslig information som krävs av arbetsytan.

> [!NOTE]
> Du kan också använda befintliga Azure-tjänster i stället för att skapa nya versioner. 

Följande diagram är en taxonomi för arbetsytan:

[![Arbetsytan taxonomi](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>Modell

I sin enklaste är en modell en typ av kod som hämtar indata och utdata. Skapa en modell för maskininlärning innebär att välja en algoritm, att förse den med data och justering av hyperparametrar. Utbildning är en iterativ process som skapar en tränad modell, som kapslar in det modellen har lärt dig under utbildning.

En modell produceras av en körning i Azure Machine Learning. Du kan också använda en modell tränas utanför Azure Machine Learning. En modell kan registreras under en arbetsyta för Azure Machine Learning-tjänsten.

Azure Machine Learning-tjänsten är framework oberoende. Du kan använda valfritt populära machine learning-ramverk när du skapar en modell, till exempel scikit-Läs xgboost, PyTorch, TensorFlow, Chainer och CNTK.

Ett exempel för att träna en modell finns i den [Snabbstart: skapa en machine learning-arbetsyta](quickstart-get-started.md) dokumentet.

### <a name="model-registry"></a>Modell-registret

Modellen registret håller reda på alla modeller i din arbetsyta för Azure Machine Learning-tjänsten. 

Modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig förutsätter registret att det är en ny version. Versionen ökas och den nya modellen är registrerad under namnet.

Du kan ange ytterligare metadatataggar när du registrera modellen och sedan använda dessa taggar när du söker efter modeller.

Du kan inte ta bort modeller som används av en avbildning.

Ett exempel med att registrera en modell finns i den [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentet.

## <a name="image"></a>Bild

Bilder är ett sätt att distribuera en modell, tillsammans med alla komponenter som krävs för att använda modellen på ett tillförlitligt sätt. En avbildning innehåller följande objekt:

* En modell.
* En arbetsflödesbaserad skript eller program. Det här skriptet används för att skicka indata till modellen och returnerar resultatet av modellen.
* Beroenden som krävs av modellen eller bedömnings skriptprogram.  Du kan exempelvis innehålla en miljö Conda-fil som listar beroendena för Python-paketet.

Det finns två typer av avbildningar som kan skapas med Azure Machine Learning:

* FPGA avbildning: används för att distribuera till en fält-programmable gate i Azure-molnet.
* Docker-avbildning: används när du distribuerar för att beräkna mål än FPGA. Till exempel Azure Container Instances och Azure Kubernetes Service.

Ett exempel för att skapa en avbildning finns i den [distribuera en modell för klassificering av avbildning i Azure Container Instance](tutorial-deploy-models-with-aml.md) dokumentet.

### <a name="image-registry"></a>Avbildningsregister

Avbildningsregister håller reda på bilder som skapats från dina modeller. Du kan ange ytterligare metadatataggar när du skapar avbildningen. Metadatataggar lagras som bild-registret och kan efterfrågas för att hitta din avbildning.

## <a name="deployment"></a>Distribution

En distribution är en instansiering bildens i antingen en webbtjänst som kan finnas i molnet eller en IoT-modul för integrerad enhet distributioner. 

### <a name="web-service"></a>Webbtjänst

En distribuerad webbtjänst kan använda Azure Container Instances, Azure Kubernetes Service eller fält-programmable gate matriser (FPGA).
Tjänsten har skapats från en avbildning som innehåller din modell, skript och tillhörande filer. Avbildningen har en Utjämning av nätverksbelastning, HTTP-slutpunkt som tar emot bedömnings begäranden som skickas till webbtjänsten.

Azure hjälper dig att övervaka din distribution av webbtjänster genom att samla in telemetri med Application Insights och/eller modell telemetri om du har valt att aktivera den här funktionen. Dessa data är endast tillgänglig för dig och lagras i dina Application Insights och storage-konto instanser.

Om du har aktiverat automatisk skalning kan skala Azure automatiskt din distribution.

Ett exempel för att distribuera en modell som en webbtjänst finns i den [distribuera en modell för klassificering av avbildning i Azure Container Instance](tutorial-deploy-models-with-aml.md) dokumentet.

### <a name="iot-module"></a>IoT-modul

En distribuerad IoT-modul är en Docker-behållare som innehåller din modell och associerade skript eller program och eventuella ytterligare beroenden. Dessa moduler distribueras med hjälp av Azure IoT Edge på edge-enheter. 

Om du har aktiverat övervakning, Azure att samlar in telemetridata från modellen i Azure IoT Edge-modul. Dessa data är endast tillgänglig för dig och lagras i din instans av storage-konto.

Azure IoT Edge säkerställer att din modul körs och övervaka den enhet som är värd för den.

## <a name="datastore"></a>Datalager

Ett datalager är en abstraktion av storage via Azure Storage-kontot. Datalagringen kan använda en Azure blob-behållare eller en Azure-filresurs som backend-lagring. Varje arbetsyta har ett standard-datalager, och du kan registrera dig ytterligare datalager. 

Använda Python SDK API eller Azure Machine Learning CLI för att lagra och hämta filer från databasen. 

## <a name="run"></a>Kör

En körning är en post som innehåller följande information:

* Metadata om körningen (tidsstämpel, varaktighet osv.)
* Mått som loggats av skriptet
* Skapa filer som samlas in automatiskt av experimentet eller uttryckligen överförda av dig.
* En ögonblicksbild av den katalog som innehåller dina skript innan körningen

En körning skapas när du skickar in ett skript för att träna en modell. En körning kan ha noll eller flera underordnade körs. Så det översta kör kan ha två underordnade körningar, som kan ha körs sina egna underordnade.

Ett exempel på Visa körs producerade genom att träna en modell finns i den [Snabbstart: Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) dokumentet.

## <a name="experiment"></a>Experiment

Ett experiment är en gruppering av många körs från ett givet skript. Det är alltid hör till en arbetsyta. När du skickar in en körning ska ange du ett namn på experiment. Information om körningen lagras under försöket. Om du skickar en körning och ange ett namn på experiment som inte finns, skapas automatiskt ett nytt experiment med det namnet.

Ett exempel på hur du använder ett experiment finns i den [Snabbstart: Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) dokumentet.


## <a name="pipelines"></a>Pipelines

Pipelines används för att skapa och hantera arbetsflöden som sadelhäftning tillsammans maskininlärning (ML) faser, till exempel dataförberedelser, modellinlärning, distribution av modeller och inferensjobb. Varje fas kan omfatta flera steg, som kan köras obevakat i olika beräkningsmål.

Mer information om machine learning pipelines med den här tjänsten finns i artikeln [Pipelines och Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Beräkningsmål

Beräkningsmål är beräkningsresursen som används för att köra skriptet utbildning eller vara värd för din distribution av webbtjänster. Beräkningsmål som stöds är: 

* Den lokala datorn
* En virtuell Linux-dator i Azure (till exempel Data Science Virtual Machine)
* Azure Batch AI-kluster
* Apache Spark för HDInsight
* Azure Container-instans
* Azure Kubernetes Service

Beräkningsmål är kopplade till en arbetsyta. Compute mål än den lokala datorn som delas av användare i arbetsytan.

De flesta compute mål kan skapas direkt via arbetsytan med hjälp av Azure-portalen, Azure Machine Learning SDK eller Azure CLI. Om du har beräkningsmål som har skapats av en annan process (till exempel Azure portal eller Azure CLI) kan du lägga till (Anslut) dem till din arbetsyta. Vissa compute mål måste skapas utanför arbetsytan och sedan ansluts.

Information om att välja beräkningsmål för träning, finns det [Använd beräkningsmål träna din modell](how-to-set-up-training-targets.md) dokumentet.

Information om att välja beräkningsmål för distribution finns i den [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md) dokumentet.

## <a name="run-configuration"></a>Köra konfiguration

En kör konfiguration är en uppsättning instruktioner som definierar hur ett skript ska köras i en viss beräkningsmål. Den innehåller en bred uppsättning beteende definitioner, till exempel om du vill använda en befintlig Python-miljö eller använda en Conda-miljö som bygger på specifikationen.

En kör konfiguration kan vara kvar i en fil på den katalog som innehåller skriptet utbildning, eller skapas som ett objekt i minnet och används för att skicka en körning.

Till exempel kör konfigurationer, finns i den [Använd beräkningsmål träna din modell](how-to-set-up-training-targets.md) dokumentet.

## <a name="training-script"></a>Inlärningsskript

För att träna en modell kan du ange den katalog som innehåller inlärningsskript och tillhörande filer. Du kan även ange ett namn på experiment, som används för att lagra information som samlas in under utbildningen. Hela katalogen kopieras till miljön (beräkningsmål) vid träning, och skriptet som anges av körningskonfigurationen har startats. En ögonblicksbild av katalogen lagras också under experiment på arbetsytan.

Ett exempel på hur du använder skript för att träna en modell finns i [skapa en arbetsyta med Python](quickstart-get-started.md)

## <a name="logging"></a>Loggning

När du utvecklar din lösning, använda Azure Machine Learning Python SDK i ditt Python-skript för att logga godtyckligt mått. När du kör, fråga om mått för att avgöra om körningen produceras modellen som du vill distribuera. 

## <a name="snapshot"></a>Ögonblicksbild

När du skickar en körning komprimerar katalogen som innehåller skriptet som en zip-fil och skickar dem till beräkningsmål-i Azure Machine Learning. ZIP-filen expanderas sedan och skriptet körs det. Azure Machine Learning lagrar också zip-filen som en ögonblicksbild som en del av den kör posten. Alla som har åtkomst till arbetsytan kan bläddra en kör post och ladda ned ögonblicksbilden.

## <a name="activity"></a>Aktivitet

En aktivitet representerar en långvarig åtgärd. Följande åtgärder är exempel på aktiviteter:

* Skapa eller ta bort ett beräkningsmål
* Köra ett skript på ett beräkningsmål

Aktiviteter kan ge meddelanden från SDK eller Webbgränssnittet så att du enkelt kan övervaka förloppet för dessa åtgärder.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar för att komma igång med Azure Machine Learning:

* [Vad är Azure Machine Learning-tjänsten?](overview-what-is-azure-ml.md)
* [Snabbstart: Skapa en arbetsyta med Python](quickstart-get-started.md)
* [Självstudie: Träna en modell](tutorial-train-models-with-aml.md)
