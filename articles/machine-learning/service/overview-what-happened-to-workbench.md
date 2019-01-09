---
title: Vad hände med Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Läs mer om vad som hände med Machine Learning Workbench-programmet, vad som har ändrats i tjänsten Azure Machine Learning Service och vad supporttidslinjen är.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: ff6b61874363bbc869bd509174e58640a2487f56
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811315"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>Vad hände med Machine Learning Workbench i tjänsten Azure Machine Learning Service?

Azure Machine Learning Workbench-programmet och vissa andra tidiga funktioner togs bort och ersattes i versionen från september 2018 för att frigöra plats för en förbättrad [arkitektur](concept-azure-machine-learning-architecture.md). För att förbättra upplevelsen innehåller versionen många viktiga uppdateringar som baseras på feedback från kunder. Grundläggande funktioner från experimentkörningar till modelldistribution har inte förändrats. Men du kan nu använda den robusta <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> och [Azure CLI](reference-azure-machine-learning-cli.md) för att utföra dina maskininlärningsaktiviteter och pipelines.  

I den här artikeln får lära du dig om vad som ändrats och hur det påverkar ditt befintliga arbete med Azure Machine Learning Workbench och dess API:er.

## <a name="what-changed"></a>Vad har ändrats?

Den senaste versionen av Azure Machine Learning Service innehåller följande funktioner:
+ En [förenklad Azure-resursmodell](concept-azure-machine-learning-architecture.md).
+ Ett [nytt användargränssnitt för portalen](how-to-track-experiments.md) för att hantera experiment och beräkningsmål.
+ Ett nytt och mer omfattande Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>.
+ Det nya, utökade [Azure CLI-tillägget](reference-azure-machine-learning-cli.md) för maskininlärning.

[Arkitekturen](concept-azure-machine-learning-architecture.md) har gjorts om för enklare användning. I stället för flera Azure-resurser och konton behöver du bara en [Azure Machine Learning-tjänstarbetsyta](concept-azure-machine-learning-architecture.md#workspace). Du kan skapa arbetsytor snabbt i [Azure-portalen](quickstart-get-started.md). Genom att använda en arbetsyta kan flera användare lagra tränings- och distributionsberäkningsmål, modellexperiment, Docker-avbildningar, distribuerade modeller och så vidare.

Det finns nya förbättrade CLI- och SDK-klienter i den aktuella versionen, men själva Workbench-skrivbordsprogrammet är inaktuellt. Nu kan du övervaka dina experiment i [instrumentpanelen för arbetsytor i Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Använd instrumentpanelen för att hämta din experimenthistorik, hantera beräkningsmål som är kopplade till din arbetsyta, hantera modeller och Docker-avbildningar och även distribuera webbtjänster.

## <a name="how-do-i-migrate"></a>Hur migrerar jag?

De flesta av de artefakter som skapades i den tidigare versionen av Azure Machine Learning Service lagras i din egen lokala lagring eller molnlagring. Dessa artefakter försvinner aldrig. Om du vill migrera behöver du registrera artefakterna igen med den uppdaterade Azure Machine Learning-tjänsten. Lär dig vad du kan migrera och hur det går till i den här [migreringsartikeln](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Supporttidslinje

Du kan fortfarande använda dina konton för Machine Learning-experimentering och -modellhantering och Machine Learning Workbench-programmet efter september 2018. Stödet för följande resurser tas bort progressivt under tre till fyra månader efter lanseringen. Du hittar fortfarande dokumentation för de gamla funktionerna i [avsnittet Resurser](../desktop-workbench/tutorial-classifying-iris-part-1.md) längst ned i innehållsförteckningen.

|Tillbakadragningsfas|Information som stöd för äldre funktioner|
|:---:|----------------|
|4 december 2018|Möjligheten att skapa konton för Azure Machine Learning-experimentering och -modellhantering på Azure Portal och från CLI har upphört. Möjligheten att skapa Machine Learning-beräkningsmiljöer från CLI har också upphört. Om du har ett befintligt konto fortsätter CLI och Machine Learning Workbench för skrivbord att fungera i den här fasen.|
|9 januari 2019|Stöd för allt annat upphör vid det här datumet. Exempel är återstående API:er och Machine Learning Workbench för skrivbord.|

[Börja migrera](how-to-migrate.md) i dag. Alla nya funktioner är tillgängliga med hjälp av vår nya <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md) och [portalen](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Vad händer med körhistorik?

Körhistorik förblir tillgänglig ett tag. När du är redo att flytta till den uppdaterade versionen av Azure Machine Learning Service kan du exportera denna körhistorik om du vill behålla en kopia.

Körhistorik kallas **experiment** i den aktuella versionen. Du kan samla in modellens experiment och utforska dem med hjälp av SDK, CLI eller Azure Portal.

Portalens instrumentpanel för arbetsytor stöds endast i webbläsarna Microsoft Edge, Chrome och Firefox:

[ ![Onlineportalen](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Går det fortfarande att förbereda data?

Dina befintliga dataförberedelsefiler kan inte överföras till den senaste versionen eftersom vi inte längre har Machine Learning Workbench. Du kan dock fortfarande förbereda dina data för modellering.  

Med datamängder oavsett storlek kan du använda [Azure Machine Learning-SDK för dataförberedelse](http://aka.ms/data-prep-sdk) för att snabbt förbereda dina data före modellering genom att skriva Python-kod. 

Du kan följa [den här självstudien](tutorial-data-prep.md) för att lära dig mer om hur du använder Azure Machine Learning SDK för dataförberedelse.

## <a name="will-projects-persist"></a>Kommer projekt att bevaras?

Du förlorar inte någon kod eller något arbete. I den äldre versionen är projekt molnentiteter med en lokal katalog. I den senaste versionen kopplar du lokala kataloger till Azure Machine Learning Service-arbetsytan med hjälp av en lokal konfigurationsfil. Se ett [diagram över den senaste arkitekturen](concept-azure-machine-learning-architecture.md).

Mycket av projektinnehållet finns redan på den lokala datorn. Så du behöver bara skapa en konfigurationsfil i denna katalog och referera till den i koden för att ansluta till din arbetsyta. Lär dig hur du [migrerar dina befintliga projekt](how-to-migrate.md#projects).

Lär dig hur du kommer igång i [Python med huvudsaklig SDK](quickstart-create-workspace-with-python.md) med hjälp av [Azure Portal](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>Vad händer med mina registrerade modeller och bilder?
 
De modeller som du registrerade i ditt gamla modellregister måste migreras till din nya arbetsyta om du vill fortsätta att använda dem. För att migrera dina modeller [hämtar du modellerna och registrerar dem](how-to-migrate.md) i den nya arbetsytan. 

Bilder som du skapade i ditt gamla avbildningsregister måste återskapas i den nya arbetsytan om du vill fortsätta att använda dem. Du kan återskapa dessa bilder genom att följa avsnittet [Konfigurera och skapa avbildning](how-to-deploy-and-where.md#configureimage). 

## <a name="what-about-deployed-web-services"></a>Vad händer med distribuerade webbtjänster?

Modeller som du distribuerade som webbtjänster med hjälp av ditt Machine Learning-modellhanteringskonto fungerar så länge det finns stöd för Azure Container Service. Dessa webbtjänster fungerar även när stödet upphör för Machine Learning-modellhanteringskonton. När stödet för det gamla CLI upphör att gälla upphör dock även möjligheten att hantera de webbtjänsterna.

I den nyare versionen distribueras modeller som webbtjänster till Azure Container Instances eller Azure Kubernetes Service-kluster (AKS). Du kan även distribuera till FPGA och till Azure IoT Edge. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md). Utan att behöva ändra några av dina bedömningsfiler, beroenden och scheman kan du distribuera om dina modeller med hjälp av den nya SDK:n eller CLI. 

## <a name="what-about-the-old-sdk-and-cli"></a>Vad gäller för den gamla SDK:n och CLI?

Ja, de fortsätter att fungera fram till januari. Se föregående [tidslinje](#timeline). Vi rekommenderar att du börjar skapa dina nya experiment och modeller med den senaste SDK:n eller CLI.

Genom att använda den nya Python SDK:n i den senaste versionen kan du interagera med Azure Machine Learning Service i alla Python-miljöer. Lär dig hur du installerar den senast <a href="https://aka.ms/aml-sdk" target="_blank">SDK:n</a>. Du kan också använda det uppdaterade [Azure Machine Learning-tillägget](reference-azure-machine-learning-cli.md) med den omfattande uppsättningen `az ml`-kommandon för att interagera med tjänsten i valfri kommandoradmiljö, inklusive Azure Cloud Shell.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Vad händer med Azure Machine Learning för Visual Studio Code?

I den senaste versionen har Azure Machine Learning för Visual Studio Code utökats och förbättrats för att fungera med följande nya funktioner.

[ ![Azure Machine Learning för Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Vad gäller för domänpaket?

Domänpaketen för [visuellt innehåll, textanalys och prognostisering](../desktop-workbench/reference-python-package-overview.md) kan inte användas med den senaste versionen av Azure Machine Learning. Du kan dock fortfarande skapa och träna modeller för visuellt innehåll, textanalys och prognostisering med den senaste Azure Machine Learning Python-<a href="https://aka.ms/aml-sdk" target="_blank">SDK:n</a>. Om du vill lära dig mer om att migrera befintliga modeller som skapats med visuellt innehåll-, textanalys- och prognostiseringspaket, kontakta[AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [senaste arkitekturen för Azure Machine Learning Service](concept-azure-machine-learning-architecture.md). Testa någon av snabbstarterna eller självstudierna:

* [Vad är Azure Machine Learning Service?](overview-what-is-azure-ml.md)
* [Snabbstart: Skapa en arbetsyta med Python](quickstart-get-started.md)
* [Självstudie: Träna en modell](tutorial-train-models-with-aml.md)
