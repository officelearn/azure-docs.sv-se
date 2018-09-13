---
title: Djupinlärning för förutsägande Underhåll verkliga scenarier – Azure | Microsoft Docs
description: Lär dig mer om att replikera självstudien på djupinlärning för förutsägande Underhåll med Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 83e1f14db317f59ab2063a9d020adbdb6fe78e5f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650454"
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Djupinlärning för förutsägande Underhåll verkliga scenarier

Djupinlärning är en av de mest populära trenderna i machine learning och har program till många områden, inklusive:
- Förarlösa bilar och robotteknik.
- Tal- och taligenkänning.
- Finansiella prognostisering.

Även känt som djupa neurala nätverk (DNN), är dessa metoder inspirerat enskilda neurons som ligger inom hjärna (bioteknik neurala nätverk).

Följderna av oplanerade avbrott för utrustning kan vara skadliga för alla företag. Det är viktigt att hålla fältet utrustning som körs för att maximera användningen och prestanda och minimera dyra, oplanerade driftstopp. Tidig identifiering av problem kan allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedja processer. 

En strategi för förebyggande underhåll (em) använder machine learning-metoder för att fastställa villkoret utrustning utföra underhåll för att undvika negativa datorns prestanda. I PM, data som samlas in över tid för att övervaka status för datorn och sedan analyseras för att hitta mönster för att förutse problem. [Lång kort sikt minne (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) nätverk är attraktivt för den här inställningen eftersom de har skapats för att lära sig från sekvenser av data.

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence-galleriet GitHub-lagringsplats

Cortana Intelligence-galleriet för PM-självstudiekursen är en offentlig GitHub-lagringsplats ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) där du kan rapportera problem och göra bidrag.


## <a name="use-case-overview"></a>Översikt över användningsfall

Den här självstudien används ett exempel på simulerade flygplan motorn för körning till fel händelser för att demonstrera förebyggande underhåll modellera processen. Scenariot beskrivs i [förebyggande underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Det huvudsakliga antagandet i den här inställningen är motorn progressivt sjunker under livslängden. Försämringen kan identifieras i motorn sensor mätning av faktisk användning. PM försöker modellera relationen mellan ändringarna i de här sensorvärdena och historiska felen. Modellen kan sedan förutsäga när och motorn fungerar inte i framtiden baserat på det aktuella tillståndet för sensor mätning av faktisk användning.

Det här scenariot skapar en LSTM nätverk för att förutsäga komponenternas livslängd (RUL) av flygplansmotorer med hjälp av historiska sensorvärdena. Scenariot använder den [Keras](https://keras.io/) bibliotek med den [Tensorflow](https://www.tensorflow.org/) ramverk för djupinlärning som en databearbetningsmotor. Scenariot träna LSTM med en uppsättning motorer och testar nätverket på en uppsättning överblivna motorn.

## <a name="prerequisites"></a>Förutsättningar
- En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
- Azure Machine Learning Workbench med en arbetsyta som skapats.
- För modellen driftsättning: Azure Machine Learning-driftsättning i en lokal distribution-miljö ställer in, och en [Azure Machine Learning-modellhantering konto](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:

1. Öppna Machine Learning Workbench.
2. På den **projekt** väljer **+**, och välj sedan **nytt projekt**.
3. I den **Skapa nytt projekt** fönstret anger du informationen för det nya projektet.
4. I den **Sök efter projektmallar** sökrutan skriver du ”förebyggande underhåll” och väljer den **Djupinlärning för förutsägande Underhållsscenariot** mall.
5. Välj **Skapa**.

## <a name="prepare-the-notebook-server-computation-target"></a>Förbered notebook server beräkning mål

Att köra på den lokala datorn från Machine Learning Workbench **filen** menyn väljer du antingen **öppna Kommandotolken** eller **öppna PowerShell CLI**. CLI-gränssnittet kan du komma åt dina Azure-tjänster med hjälp av den `az` kommandon. Logga först in på Azure-kontot med kommandot:

```
az login
``` 

Det här kommandot ger en autentiseringsnyckel som ska användas med https:\\aka.ms\devicelogin URL: en. CLI väntar tills enheten inloggningen åtgärden returnerar och vissa anslutningsinformation. Nästa, om du har en lokal [Docker](https://www.docker.com/get-docker) installation, förbereda lokala compute-miljö med kommandot:

```
az ml experiment prepare --target docker --run-configuration docker
```

Det är bättre att köra en [Data Science Virtual Machine (DSVM) för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) för minne och krav. När DSVM har konfigurerats kan du förbereda den fjärranslutna Docker-miljön med följande två kommandon:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

När du är ansluten till den fjärranslutna Docker-behållaren, förbereda beräkningsmiljö DSVM Docker med kommandot: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Med Docker beräkningsmiljö som förberetts, öppna Jupyter notebook-server från Machine Learning Workbench **anteckningsböcker** fliken eller starta en webbläsare-baserad server med kommandot: 

```
az ml notebook start
```

Exempel-anteckningsböcker lagras i katalogen kod. De bärbara datorerna är inställda som körs sekventiellt, från och med den första (Code\1_data_ingestion.ipynb)-anteckningsboken. När du öppnar varje bärbar dator, uppmanas du att välja beräknings-kernel. Välj [projekt_namn] _namn [Connection_Name] kernel ska köras på den tidigare konfigurerade DSVM.

## <a name="data-description"></a>Databeskrivning

Mallen använder tre datauppsättningar som indata i filerna PM_train.txt och PM_test.txt PM_truth.txt. 

- **Träna data**: flygplanet motor kör till Feldata. Träna data (PM_train.txt) består av flera multivarierad tidsserier med *migreringscykel* som tidsenheten. Den innehåller 21 sensoravläsningar för varje cykel. 

    - Varje tidsserie genereras från en annan modul av samma typ. Varje motor börjar med olika grader av inledande slitage och en unik tillverkning variant. Den här informationen är okänd för användaren. 

    - I den här simulerade data antas motorn vara fungerar normalt i början av varje tidsserie. Den börjar försämras vid en viss tidpunkt under serien med operativsystem cykler. Försämringen fortskrider och växer i storlek. 

    - När ett fördefinierat tröskelvärde har uppnåtts kan anses motorn osäkra för ytterligare åtgärd. Senaste cykel för varje tidsserie är felpunkt för vilken motorn.

- **Testa data**: flygplanet motorn driftuppgifter, utan felhändelser som registreras. Testdata (PM_test.txt) har samma dataschema som träningsdata. Den enda skillnaden är att data inte anger när felet inträffade (senaste tidsperiod har *inte* representerar felpunkt). Det är inte känt hur många fler cykler den här motorn kan senaste innan det misslyckas.

- **Sanningen data**: information om SANT återstående cykler för varje motorn i testdata. Grunden sanningen data visar antalet återstående fungerande cykler för motorerna i testdata.

## <a name="scenario-structure"></a>Scenario-struktur

Scenario-arbetsflödet är indelat i tre steg och varje steg utförs i en Jupyter-anteckningsbok. Varje notebook genererar data artefakter som sparas lokalt för användning i de bärbara datorerna.

### <a name="task-1-data-ingestion-and-preparation"></a>Uppgift 1: Förberedelse och inhämtning av Data

Data inmatning Jupyter Notebook i Code/1_data_ingestion_and_preparation.ipnyb läser in tre inkommande datauppsättningar i Pandas-DataFrame-format. Anteckningsboken sedan förbereder data för modellering och har vissa preliminär datavisualisering. De uppgifter som lagras lokalt till beräkningskontexten för användning i modellen skapa Jupyter-anteckningsboken.

### <a name="task-2-model-building-and-evaluation"></a>Uppgift 2: Modellskapandet och utvärdering

Modellen skapa Jupyter Notebook i Code/2_model_building_and_evaluation.ipnyb läser datauppsättningar träna och testa från disken och skapar ett LSTM nätverk för utbildning-datauppsättningen. Modellens prestanda mäts på test-datauppsättning. Den resulterande modellen serialiseras och lagras i den lokala beräkningskontexten för användning i aktiviteten driftsättningen.

### <a name="task-3-operationalization"></a>Uppgift 3: driftsättning

Driftsättning Jupyter Notebook i Code/3_operationalization.ipnyb använder lagrade modellen för att skapa funktioner och schema för att anropa modellen på en Azure-baserad webbtjänst. Anteckningsboken testar funktionerna och sedan komprimerar tillgångarna till LSTM_o16n.zip-fil. Filen läses in på ditt Azure storage-behållare för distribution.

Distributionsfilen LSTM_o16n.zip innehåller följande artefakter:

- **webservices_conda.yaml**: definierar de Python-paket som krävs för att köra LSTM modellen på mål-distribution.  
- **service_schema.JSON**: definierar dataschema som förväntas av LSTM modellen.   
- **lstmscore.PY**: definierar de funktioner som distributionsmålet körs för att poängsätta nya data.    
- **modellstm.JSON**: definierar LSTM-arkitekturen. Funktionerna lstmscore.py läsa arkitekturen och vikterna att initiera modellen.
- **modellstm.H5**: definierar modellen vikterna.
- **test_service.PY**: ett testskript som anropar slutpunkten för distribution med test dataposter. 
- **PM_test_files.pkl**: test_service.py skriptet läser historiska engine-data från filen PM_test_files.pkl och skickar webbtjänsten tillräckligt med cykler för LSTM att returnera en sannolikheten för motorfel.

Anteckningsboken testar funktionerna med hjälp av modelldefinitionen innan det paket driftsättning tillgångar för distribution. Anvisningar om hur du konfigurerar och testar webbtjänsten ingår i slutet av Code/3_operationalization.ipnyb anteckningsboken.

## <a name="conclusion"></a>Sammanfattning

Den här självstudien innehåller ett enkelt scenario där sensorvärdena används för att göra förutsägelser. Mer avancerade scenarier för förebyggande underhåll som den [förutsägande Underhåll modellering Guide R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) kan använda många datakällor, till exempel historiska Underhåll poster, felloggarna och funktioner för datorn. Ytterligare datakällor som kan kräva olika behandlingar ska användas med djupinlärning.


## <a name="references"></a>Referenser

Följande referens ger exempel på andra förebyggande underhåll användningsfall för olika plattformar:

* [Lösningsmallen för förebyggande underhåll](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förebyggande underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll modellering Guide med hjälp av SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande modellering Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förebyggande underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Verkliga scenarier för förebyggande underhåll](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>Nästa steg

Andra scenarier är tillgängliga i Machine Learning Workbench som visar hur du ytterligare funktioner i produkten. 
