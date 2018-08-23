---
title: Intäktsklassificering - Team Data Science Process - Azure Machine Learning | Microsoft Docs
description: Hur du använder mallen Team Data Science Process för att skapa ett projekt i Azure Machine Learning som klassificerar inkomster från USA.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: edc3fc5e2a625a14bcb48b03f32cd99069a0ad53
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057109"
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Intäktsklassificering med Team Data Science Process (TDSP)-projekt

## <a name="introduction"></a>Introduktion

Standardisering av strukturen och dokumentation om data science projekt som är förankrad vid en etablerad [livscykeln för datavetenskap](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), är nyckeln till att underlätta effektivt samarbete i data science teams. Skapa Azure Machine Learning-projekt med den [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) mallen innehåller ett ramverk för sådana standardisering.

Vi hade tidigare gavs ut en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Men det var inte möjligt, fram till nu för att skapa en instans av TDSP struktur och mallar i ett verktyg för data science. Nu har vi aktiverat skapandet av Azure Machine Learning-projekt som instansieras med [TDSP struktur och dokumentation mallar för Azure Machine Learning](https://github.com/amlsamples/tdsp). Anvisningar om hur du använder TDSP struktur och mallar i Azure Machine Learning tillhandahålls [här](https://aka.ms/how-to-use-tdsp-in-aml). Här har ett exempel på hur en faktiska machine learning-projekt kan skapas med hjälp av TDSP struktur, fylls med projekt-specifik kod, artefakter och dokument, och körs i Azure Machine Learning.

## <a name="link-to-github-repository"></a>Länka till GitHub-lagringsplats
Vi ger en sammanfattning av dokumentationen [här](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) om exemplet. Mer omfattande dokumentation finns på webbplatsen GitHub.

### <a name="purpose"></a>Syfte
Det huvudsakliga syftet med det här exemplet är att visa hur du skapa en instans av och kör en machine learning-projekt med den [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) struktur och mallar i Azure Machine Learning. För detta ändamål använder vi den välkända [1994 oss insamlade data från UCI Machine Learning databasen](https://archive.ics.uci.edu/ml/datasets/adult). Modellering-uppgift är att förutsäga USA årliga inkomster klasser från oss insamlade information (till exempel ålder, RAS, utbildningsnivå, land ursprung, osv.)

### <a name="scope"></a>Omfång
 * Datagranskning, utbildning och distribution av en maskininlärningsmodell som göra förutsägelser problemet som beskrivs i det fallet översikt över. 
 * Körning av projekt i Azure Machine Learning med hjälp av Team Data Science Process TDSP ()-mall från Azure Machine Learning för det här projektet. För projektkörning och rapportering ska vi använda TDSP-livscykeln.
 * Driftsättning av lösningen direkt från Azure Machine Learning i Azure Container Services.

 Projektet visar flera funktioner i Azure Machine Learning, sådana TDSP struktur instansiering och användning, körning av kod i Jupyter-anteckningsböcker samt Python-filer och enkelt driftsättning i Azure Container Services med Docker och Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Livscykel för team Data Science Process (TDSP)
Se [Team Data Science Process (TDSP)-livscykeln](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Förutsättningar
### <a name="required-subscription-hardware-software"></a>Krävs: prenumeration, maskinvara, programvara
1. En Azure [prenumeration](https://azure.microsoft.com). Du kan få en [kostnadsfri prenumeration](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) att köra det här exemplet också.
2. En [Azure Data Science Virtual Machine (DSVM) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (VM-storlek: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)med 4 virtuella processorer och 14 Gb RAM-minne). Även om testas på en Azure-DSVM, är det troligt att arbeta i valfri Windows 10-dator.
3. Granska dokumentation om Azure Machine Learning och dess relaterade tjänster (se nedan för länkar).
4. Se till att du har installerat korrekt Azure Machine Learning genom den [installation snabbstartsguiden](../service/quickstart-installation.md).

Datauppsättningen för det här exemplet är från UCI ML databasen [[länk]](https://archive.ics.uci.edu/ml/datasets/adult). Den kommer från 1994 oss insamlade databasen och innehåller information om inventering och intäkter för ungefär 50 000 personer. Det här är strukturerade datauppsättning med numeriska och kategoriska funktioner och ett kategoriskt mål som består av två inkomst kategorier ('> 50 K' eller ' < = 50 K ”). 

### <a name="optional-version-control-repository"></a>Valfritt: Version källkontrollen
Om du vill spara och version ditt projekt och dess innehåll, du måste ha en version för källkontrollen där detta kan göras. Du kan ange plats för Git-lagringsplatsen när du skapar nytt projekt med TDSP-mall i Azure Machine Learning. Se [hur du använder Git i Azure Machine Learning](using-git-ml-project.md) för mer information.

### <a name="informational-about-azure-machine-learning"></a>Information: Om Azure Machine Learning
* [Vanliga frågor och svar – hur du kommer igång](frequently-asked-questions.md)
* [Översikt](../service/overview-what-is-azure-ml.md)
* [Installation](../service/quickstart-installation.md)
* [Körning](experimentation-service-configuration.md)
* [Använda TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Läsa och skriva filer](how-to-read-write-files.md)
* [Använda Git med Azure Machine Learning](using-git-ml-project.md)
* [Distribuera en ML-modell som en webbtjänst](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Skapa ett nytt workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök efter projektmallar** sökrutan skriver du ”inkomster klassificera USA – TDSP-projekt” och väljer mallen
5.  Klicka på **Skapa**

Om du anger en tom Git-lagringsplats plats under Skapa projekt (i respektive ruta) och sedan databasen fylls med projektstruktur och innehåll när projektet har skapats.

## <a name="use-case-overview"></a>Översikt över användningsfall
Problemet är att förstå hur socioekonomiska data som hämtats i oss insamlade kan hjälpa att förutsäga årliga från användare i USA. Baserat på typen inventering av funktioner, machine learning-uppgift är att förutsäga om inkomst en enskild är över 50 000 eller inte (binär klassificering aktiviteten).

## <a name="data-description"></a>Databeskrivning
Detaljerad information om data som finns i den [beskrivning](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) i cykeluthyrningsdata från databasen. 

Dessa data har extraherats från insamlade Bureau databasen finns på: https://www.census.gov/en.html. 


* Det finns totalt 48,842 instanser (före någon filtrering), blanda kontinuerliga och diskreta (träna = 32, 561, testa = 16, 281)
* Sannolikheten för etiketten ' > 50 K': 23.93% / 24.78% (utan obekanta faktorer)
* Sannolikheten för etiketten ' < = 50 K': 76.07% / 75.22% (utan obekanta faktorer)  

* **TARGET**: inkomst klassen ' > 50 K', ' < = 50 K ”. Dessa ersätts med 1 och 0 i förberedelsefasen för data.
* **FUNKTIONER**: ålder, arbete klass, utbildningsnivå, utbildningsnivå, RAS, kön, arbetstimmar per vecka osv.


## <a name="project-structure-execution-and-reporting"></a>Projektstruktur, körning och rapportering

### <a name="structure"></a>struktur
För det här projektet vi använder TDSP mappen struktur och dokumentation mallar (nedan), som följer den [TDSP livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projektet har skapats utifrån instruktionerna [här](https://aka.ms/how-to-use-tdsp-in-aml). När den är fylld med projektets kod och artefakter, strukturen ser ut så här (se projektstruktur boxed i rött i bilden nedan).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Körnings-
I det här exemplet vi köra kod i **lokala beräkningsmiljö**. Referera till Azure Machine Learning-dokument för mer information finns på [körning alternativ](experimentation-service-configuration.md).

Det är enkelt att köra ett pythonskript i en lokal Python-körning:

    az ml experiment submit -c local my_script.py

IPython notebook filer kan dubbelklickar på från projektstruktur till vänster i Användargränssnittet för Azure Machine Learning och köra i Jypyter Notebook-Server.


Den stegvisa arbetsflöden för datavetenskap var på följande sätt:

* [**Data förvärv och förståelse av**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Hämtade data i CSV-format från URL: er på UCI ML databasen [[länk]](https://archive.ics.uci.edu/ml/datasets/adult). Funktioner, mål och deras transformationer beskrivs i detalj i filen ProjectReport.md.

Kod för datainsamling och förstå finns i: / kod/01_data_acquisition_and_understanding.

Datagranskning utförs med hjälp av Python 3 [IDEAR (Interaktiv datautforskning och rapportering) verktyget](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) publiceras som en del av [TDSP uppsättning verktyg för datavetenskap](https://github.com/Azure/Azure-TDSP-Utilities). Det här verktyget hjälper till att generera standardiserad utforskning rapporter för data som innehåller funktioner för numeriska och kategoriska och mål. Information om hur verktyget Python 3 IDEAR användes finns nedan. 

Den slutliga utforskning rapporten finns [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). En vy över IDEAR rapporten visas nedan:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modellering**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Vi har skapat två modeller med 3-fold korsvalidering: elastisk Net och slumpmässiga skog. Vi använde [59 punkt sampling](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) för slumpmässiga grid sökning som en strategi för korsvalidering och datamodeller parametern optimering. Riktighet modeller har mätas med AUC (området under kurvan) på test-datauppsättning. 

Kod för modellering finns på: / kod/02_modeling.

AUC för både elastisk Net och slumpmässiga skog modeller har > 0.85. Vi sparar båda modellerna i pickled.pkl filer och utdata som ROC ritar för båda modellerna. AUC av slumpmässiga Skogsmodell var 0.92 och för elastiska Net modellen 0,90. Dessutom för modellen tolkning funktionen betydelse för slumpmässiga Skogsmodell är utdata i en .csv-fil och som beskrivs i en PDF-fil (20 främsta förebyggande funktioner endast).

ROC-kurvan för **slumpmässiga Skogsmodell** på test data visas nedan. Det var den modell som distribuerades:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Funktionen vikten (översta 20) av slumpmässiga Skogsmodell visas nedan. Den visar funktioner inhemska belopp, eduction, civilstånd, har högsta prioritet för funktionen.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Distribution**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Vi distribuerade slumpmässiga Skogsmodell som en webbtjänst på ett kluster i den [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Driftsättning miljön etablerar Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om processen för driftsättning [här](model-management-service-deploy.md). 

Kod för distributionen finns på: / kod/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Sista projektrapporten](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Information om var och en av ovanstående avsnitt finns i kompilerade sista projektrapporten [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Projektrapporten innehåller också ytterligare information om användningsfall, prestandamått för modellen, distribution och infrastruktur som projektet utvecklades och distribueras.

Projektrapport tillsammans med innehållet i hela projektmappen och version för källkontrollen kan levereras till klienten.


## <a name="conclusion"></a>Sammanfattning

I det här exemplet visar vi nu för att använda TDSP struktur och mallar i Azure Machine Learning. Via dokumentet och artefakt-mallar kan du:
1. Definiera korrekt syfte och omfånget för ett projekt
2. Skapa en projektgrupp distribuerade roller och ansvarsområden
3. Strukturera och utföra projekt enligt livscykelstadier för TDSP
4. Utveckla standardiserad rapporter som använder TDSP data science utilities (till exempel IDEAR data utforskning och visualisering rapporten).
5. Förbereda en slutlig data science-projektrapporten som kan levereras till en klient

Vi hoppas att du använder den här funktionen i Azure Machine Learning för att underlätta med standardiseringsorganisationen och samarbete inom data science teamen.

## <a name="next-steps"></a>Nästa steg

Se länkarna nedan för att komma igång:

[Hur du använder Team Data Science Process (TDSP) i Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[TDSP projektmall för Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[USA inkomst datauppsättningen från UCI ML-lagringsplats](https://archive.ics.uci.edu/ml/datasets/adult)