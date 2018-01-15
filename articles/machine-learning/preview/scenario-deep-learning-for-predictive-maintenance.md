---
title: "Djupgående utbildning för förutsägande Underhåll verkliga scenarier - Azure | Microsoft Docs"
description: "Lär dig mer om att replikera kursen djup inlärning för förebyggande underhåll med Azure Machine Learning-arbetsstationen."
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Djupgående utbildning för förutsägande Underhåll verkliga scenarier

Djupgående learning är en av de mest populära trenderna i machine learning. Djupgående learning används i många fält och program, inklusive protokollmekanismer utan drivrutiner bilar, tal-och image, robotics och ekonomi. Djupgående inlärning är en uppsättning algoritmer som inspirerat av form hjärna (biologiska neurala nätverk) och maskininlärning. Kognitiva forskare avser vanligtvis djup learning som artificiell neurala nätverk (ANNs).

Förutsägande Underhåll är också populära. I förutsägande Underhåll är många olika tekniker utformade för att avgöra tillståndet för utrustning och att förutsäga när Underhåll ska utföras. Några vanliga användningsområden för förutsägande Underhåll är felförutsägelse, fel diagnos (analys av grundorsaken), felidentifiering, fel typ klassificering och rekommendation av minskning eller underhåll åtgärder efter fel.

I scenarier med förutsägande Underhåll samlas data över tid för att övervaka status för utrustning. Målet är att hitta mönster som hjälper dig att förutsäga och slutligen förhindra fel. Med hjälp av [lång kort sikt minne (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) nätverk är en djup learning metod som är särskilt tilltalande i förutsägande underhåll. Det är bra på att lära sekvenser LSTM nätverk. Tid series-data kan användas för att gå tillbaka till längre tid att upptäcka fel mönster.

I den här självstudiekursen kommer vi bygga en LSTM nätverk för datauppsättningen och scenario som beskrivs i [förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). Vi kan använda nätverket för att förutsäga flygplan motorer återstående livslängd. Mallen använder simulerade flygplan sensor värden för att förutsäga när ett flygplan motorn misslyckas i framtiden. Med den här förutsägelse kan Underhåll planeras i förväg för att förhindra fel.

Den här kursen använder den [Keras](https://keras.io/) djup bibliotek och Microsoft kognitiva Toolkit [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) som en serverdel.

Offentliga GitHub-lagret med exemplen i den här självstudien är på [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Använd case-översikt

Den här kursen används ett exempel på simulerade flygplan motorn kör-till-felhändelser för att demonstrera förutsägande Underhåll modellera processen. 

Det underförstådda antagandet om modellering data som beskrivs här är att tillgången har ett progressing omvandlings-mönster. Mönstret avspeglas i tillgångens sensor mått. Genom att undersöka sensor resursvärden över tid Läs maskininlärningsalgoritmen relationen mellan sensor-värden, ändringar i sensor värden och historiska fel. Den här relationen används för att förutsäga fel i framtiden. 

Vi rekommenderar att du undersöker dataformatet och slutföra alla tre stegen för mallen innan du ersätta exempeldata med din egen datum.

## <a name="prerequisites"></a>Förutsättningar

- En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
- Azure Machine Learning arbetsstation med en arbetsyta skapas.
- För modellen operationalization: Azure Machine Learning Operationalization, med en lokal distributionsmiljö ställer in, och en [konto i Azure Machine Learning modellen Management](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt genom att använda det här exemplet som en mall:

1. Öppna Machine Learning arbetsstationen.
2. På den **projekt** väljer  **+** , och välj sedan **nytt projekt**.
3. I den **Skapa nytt projekt** rutan Ange information för det nya projektet.
4. I den **Sök projektmallar** sökrutan, ange **förutsägande Underhåll**, och välj sedan mallen.
5. Välj **Skapa**.

## <a name="prepare-the-notebook-server-computation-target"></a>Förbereda anteckningsboken server beräkning mål

På den lokala datorn på Machine Learning-arbetsstationen **filen** -menyn väljer du antingen **öppnar du kommandotolken** eller **öppna PowerShell**. I kommandotolksfönstret alternativet som du väljer att köra följande kommandon:

`az ml experiment prepare --target docker --run-configuration docker`

Vi rekommenderar att du kör anteckningsboken-server på en standard som DS4_V2 [Data vetenskap virtuell dator (DSVM) för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu). Efter DSVM konfigureras, kör följande kommandon för att förbereda Docker-avbildningar:

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Öppna servern Jupyter-anteckningsbok med Docker-bilder är förberedd. Om du vill öppna Jupyter-anteckningsbok server antingen gå till Machine Learning-arbetsstationen **anteckningsböcker** fliken eller starta en webbläsare-baserad server:`az ml notebook start`

Anteckningsböcker som lagras i katalogen koden i Jupyter-miljö. Kör dessa anteckningsböcker sekventiellt som numrerade, från och med Code\1_data_ingestion_and_preparation.ipynb.

Välj kernel för att matcha dina [projekt_namn] _Template [connection_name] och välj sedan **ange Kernel**.

## <a name="data-description"></a>Beskrivning av data

Mallen använder tre datauppsättningar som indata i filerna PM_train.txt och PM_test.txt PM_truth.txt.

-  **Träna data**: flygplan motorn kör till fel data. Train-data (PM_train.txt) består av flera multivariate tidsserier med *växla* som tidsenheten. Den omfattar 21 sensoravläsningar för varje cykel. 

    Varje tidsserier antas kan genereras från en annan modul av samma typ. Varje motor förutsätts för att starta med olika typer av inledande förslitning och produktion variation. Den här informationen är okänd för användaren. 

    I den här simulerade data antas motorn fungerar normalt i början av varje tidsserier. Den börjar försämras vid något tillfälle under drift cykler serie. Nedbrytning fortskrider och växer i storlek. 

    När en fördefinierad tröskelvärde uppnås betraktas motorn som osäkra för ytterligare åtgärd. Den sista cykeln i varje tidsserier kan ses felpunkt motsvarande motorns.

-   **Testa data**: flygplan motorn driftuppgifter, utan felhändelser som registreras. Testdata (PM_test.txt) har samma dataschemat som utbildning-data. Den enda skillnaden är att data inte anger när felet inträffar (senaste tidsperiod har *inte* representerar felpunkt). Det är inte känt hur många fler cykler den här motorn kan gälla innan det misslyckas.

- **Sanningen data**: informationen true återstående cykler för varje motor i tester data. Grunden sanningen data innehåller antalet återstående fungerande cykler för motorer i tester data.

## <a name="scenario-structure"></a>Scenario-struktur

Innehållet i scenariot är tillgängligt på [GitHub-lagringsplatsen] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

I databasen beskrivs en [viktigt] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md)-fil processer från förbereder data bygger upp och operationalizing modellen. Tre Jupyter-anteckningsböcker är tillgängliga i mappen [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) i databasen. 

Därefter beskriver vi steg för steg-scenariot arbetsflödet.

### <a name="task-1-data-ingestion-and-preparation"></a>Uppgift 1: Datapåfyllning och förberedelse

Data införandet Jupyter Notebook i Code/1_data_ingestion_and_preparation.ipnyb läser in tre inkommande datauppsättningar i Pandas dataframe format. Sedan den förbereder data för modellering och har några inledande datavisualisering. Data sedan omvandlas till PySpark-format och lagras i ett Azure Blob storage-behållare i Azure-prenumerationen för användning i nästa modellering uppgift.

### <a name="task-2-model-building-and-evaluation"></a>Uppgift 2: Modellskapandet och utvärdering

Modellen byggnad Jupyter Notebook i Code/2_model_building_and_evaluation.ipnyb läser PySpark träna och testa datauppsättningar från Blob storage. Sedan bygger ett LSTM nätverk med datauppsättningar utbildning. Modell-prestanda mäts på test-uppsättningen. Den resulterande modellen serialiseras och lagras i den lokala beräkning kontexten för användning i aktiviteten operationalization.

### <a name="task-3-operationalization"></a>Uppgift 3: Operationalization

Operationalization Jupyter Notebook i Code/3_operationalization.ipnyb använder lagrade modellen för att skapa funktioner och schema som krävs för att anropa modellen på en Azure-baserad webbtjänst. Anteckningsboken testar funktionerna och sänds (komprimerar) operationalization tillgångar i en .zip-fil.

Den komprimerade filen innehåller följande filer:

- **modellstm.JSON**: definitionsfilen schemat för distributionen. 
- **lstmscore.PY**: den **init()** och **run()** funktioner som krävs av Azure webbtjänsten.
- **lstm.Model**: katalogen model definition.

Anteckningsboken testar funktionerna med hjälp av modelldefinitionen innan det paket operationalization tillgångar för distribution. Instruktioner för distribution ingår i slutet av den bärbara datorn.


## <a name="conclusion"></a>Sammanfattning

Den här kursen används ett enkelt scenario i vilken endast en källa (sensor värden) som används för att göra förutsägelser. För mer avancerade scenarier med förutsägande underhåll som den [förutsägande Underhåll Modeling guiden R anteckningsboken](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), flera datakällor kan användas. Andra datakällor kan innehålla historiska Underhåll poster, felloggarna och datorn och operatorn funktioner. Ytterligare datakällor kan kräva olika typer av behandling som ska användas i djup learning nätverk. Det är också viktigt att justera modeller för rätt parametrar, t.ex. för fönsterstorlek. 

Du kan redigera relevanta delar av det här scenariot och Använd olika problem scenarier som som beskrivs i den [förutsägande Underhåll Modeling guiden](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1), vilket innebär att flera andra datakällor.


## <a name="references"></a>Referenser

- [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

