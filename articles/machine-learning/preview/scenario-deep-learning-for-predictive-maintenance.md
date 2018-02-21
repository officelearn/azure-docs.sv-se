---
title: "Djupgående utbildning för förutsägande Underhåll verkliga scenarier - Azure | Microsoft Docs"
description: "Lär dig mer om att replikera kursen djup inlärning för förebyggande underhåll med Azure Machine Learning-arbetsstationen."
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 7d4fe98b5c45767fb06391218e80789fc0c96a3b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>Djup learning för förutsägande Underhåll verkligt scenario.

Djupgående learning är en av de mest populära trenderna i machine learning med program på många områden, inklusive:
- protokollmekanismer utan drivrutiner bilar och robotics
- tal-och
- finansiella prognoser.

Även känd som djupa Neurala nätverk (DNN), är dessa metoder inspirerat av enskilda neurons inom hjärna (biologiska neurala nätverk).

Effekten av utrustning oplanerade driftstopp kan vara skadliga för alla företag. Det är viktigt att behålla fältet utrustning som körs för att maximera användning och prestanda och minimera kostsamma, oplanerade driftavbrott. Tidig identifiering av problem kan hjälpa allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedjan processer. 

En strategi för förutsägande Underhåll (em) använder machine learning metoder för att avgöra tillståndet för utrustning förebyggande syfte Underhåll ska utföras för att undvika negativa datorn prestanda. Data som samlas in med tiden för att övervaka status för datorn är i PM, analyser för att hitta mönster som kan användas för att förutsäga fel. [Lång kort sikt minne (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) nätverk är bra för den här inställningen, eftersom de har utformats för att lära sig från sekvenser av data.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Följande är en länk till offentliga GitHub-lagringsplatsen för problemet rapporter och bidrag: [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

## <a name="use-case-overview"></a>Använd case-översikt

Den här kursen används ett exempel på simulerade flygplan motorn kör-till-felhändelser för att demonstrera förutsägande Underhåll modellera processen. Scenariot beskrivs i [förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)

Den huvudsakliga antaganden i den här inställningen är motorn progressivt försämras under sin livslängd. Nedbrytning kan identifieras i motorn sensor mått. PM försöker modellen relationen mellan ändringarna i värdena sensor och historiska fel. Modellen kan sedan förutsäga när och motorn fungerar inte i framtiden baserat på aktuell status för sensor mått.

Det här scenariot skapar ett LSTM nätverk för att förutsäga återstående livslängd (RUL) av flygplan motorer med historiska sensor värden. Med hjälp av [Keras](https://keras.io/) med [Tensorflow](https://www.tensorflow.org/) djup learning framework som en beräkningsmotor scenariot tränar LSTM med en uppsättning motorer och testa nätverk på en uppsättning överblivna motorn.

## <a name="prerequisites"></a>Förutsättningar
- En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
- Azure Machine Learning arbetsstation med en arbetsyta skapas.
- För modellen operationalization: Azure Machine Learning Operationalization, med en lokal distributionsmiljö ställer in, och en [konto i Azure Machine Learning modellen Management](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt genom att använda det här exemplet som en mall:

1. Öppna Machine Learning arbetsstationen.
2. På den **projekt** väljer  **+** , och välj sedan **nytt projekt**.
3. I den **Skapa nytt projekt** rutan Ange information för det nya projektet.
4. I den **Sök projektmallar** sökrutan, Skriv ”förutsägande underhåll” och välj den **djup Learning för scenariot med förutsägande Underhåll** mall.
5. Klicka på den **skapa** knappen

## <a name="prepare-the-notebook-server-computation-target"></a>Förbereda anteckningsboken server beräkning mål

Att köra på den lokala datorn från AML arbetsstationen `File` -menyn väljer du antingen den `Open Command Prompt` eller `Open PowerShell CLI`. Gränssnittet CLI får du åtkomst till Azure-tjänster med hjälp av den `az` kommandon. Först och logga in på ditt Azure-konto med kommandot:

```
az login
``` 

Det här kommandot ger en autentiseringsnyckel som ska användas med den `https:\\aka.ms\devicelogin` URL. CLI väntar tills enheten inloggningen åtgärden returnerar och ger vissa anslutningsinformationen. Nästa, om du har en lokal [docker](https://www.docker.com/get-docker) installera, förbereder lokala beräknings-miljön med följande kommandon:

```
az ml experiment prepare --target docker --run-configuration docker
```

Det är bättre att köra på en [datavetenskap virtuell dator för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) för minne och diskutrymme. När du har konfigurerat DSVM Förbered fjärr docker-miljö med följande två kommandon:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

När du är ansluten till den fjärranslutna dockerbehållare förbereda DSVM docker beräkning miljö med: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Med docker compute-miljö har förberetts, öppna Jupyter-anteckningsbok server antingen inom fliken AML arbetsstationen bärbara datorer eller starta en webbläsarbaserad server med: 
```
az ml notebook start
```

Exempel-anteckningsböcker som lagras i den `Code` directory. De bärbara datorerna är konfigurerade att köras efter varandra startar på först (`Code\1_data_ingestion.ipynb`) bärbar dator. När du öppnar varje bärbar dator uppmanas att välja kernel för beräkning. Välj den `[Project_Name]_Template [Connection_Name]` kernel ska köras på den tidigare konfigurerade DSVM.

## <a name="data-description"></a>Beskrivning av data

Mallen använder tre datauppsättningar som indata i filer **PM_train.txt**, **PM_test.txt**, och **PM_truth.txt**. 

-  **Träna data**: flygplan motorn kör till fel data. Train-data (PM_train.txt) består av flera multivariate tidsserier med *växla* som tidsenheten. Den omfattar 21 sensoravläsningar för varje cykel. 

    - Varje tidsserier genereras från en annan modul av samma typ. Varje motor börjar med olika grader av inledande förslitning och en unik tillverkning variant. Den här informationen är okänd för användaren. 

    - I den här simulerade data antas motorn fungerar normalt i början av varje tidsserier. Den börjar försämras vid något tillfälle under drift cykler serie. Nedbrytning fortskrider och växer i storlek. 

    - När en fördefinierad tröskelvärde uppnås betraktas motorn som osäkra för ytterligare åtgärd. Den sista cykeln av varje tidsserier är felpunkt för som motorn.

-   **Testa data**: flygplan motorn driftuppgifter, utan felhändelser som registreras. Testdata (PM_test.txt) har samma dataschemat som utbildning-data. Den enda skillnaden är att data inte anger när felet inträffar (senaste tidsperiod har *inte* representerar felpunkt). Det är inte känt hur många fler cykler den här motorn kan gälla innan det misslyckas.

- **Sanningen data**: informationen true återstående cykler för varje motor i tester data. Grunden sanningen data innehåller antalet återstående fungerande cykler för motorer i tester data.

## <a name="scenario-structure"></a>Scenario-struktur

Arbetsflöde för scenariot är uppdelat i tre steg, var de körs i en Jupyter-anteckningsböcker. Varje anteckningsbok ger dataartifakter sparas lokalt för användning i följande datorer: 

### <a name="task-1-data-ingestion-and-preparation"></a>Uppgift 1: Datapåfyllning och förberedelse

Data införandet Jupyter Notebook i `Code/1_data_ingestion_and_preparation.ipnyb` läser in tre inkommande datauppsättningar i Pandas dataformat ram. Därefter förbereder data för modellering och har vissa preliminär datavisualisering. De uppgifter som lagras lokala beräknings-kontexten för användning i modellen byggnad anteckningsboken.

### <a name="task-2-model-building-and-evaluation"></a>Uppgift 2: Modellskapandet och utvärdering

Modellen byggnad Jupyter Notebook i `Code/2_model_building_and_evaluation.ipnyb` läser datauppsättningar tåg och testa från disk och skapar ett LSTM nätverk datauppsättning för träning. Modell-prestanda mäts på test-uppsättningen. Den resulterande modellen serialiseras och lagras i den lokala beräkning kontexten för användning i aktiviteten operationalization.

### <a name="task-3-operationalization"></a>Uppgift 3: Operationalization

Operationalization Jupyter Notebook i `Code/3_operationalization.ipnyb` använder lagrade modellen för att skapa funktioner och schema för att anropa modellen på en Azure-baserad webbtjänst. Anteckningsboken testar funktionerna och komprimerar tillgångar i den `LSTM_o16n.zip` fil som har lästs in på ditt Azure storage-behållare för distribution.

Den `LSTM_o16n.zip` distributionsfilen innehåller följande artefakter:

- `webservices_conda.yaml` definierar python-paket som krävs för att köra LSTM modellen på distributionsmålet.  
- `service_schema.json` definierar dataschemat förväntades av LSTM modellen.     
- `lstmscore.py` definierar de funktioner som distributionsmålet körs att poängsätta nya data.    
- `modellstm.json` definierar LSTM arkitektur. Funktionerna lstmscore.py läsa arkitektur och vikter till initiera modellen.
- `modellstm.h5` definierar vikterna modellen.
- `test_service.py` Ett testskript som anropar slutpunkten distribution med test dataposter. 
- `PM_test_files.pkl` Den `test_service.py` skriptet läser motorn för historiska data från den `PM_test_files.pkl` filen och skickar webservice tillräckligt med cykler för LSTM att returnera en sannolikheten för fel motorn.

Anteckningsboken testar funktioner med hjälp av modelldefinitionen innan det paket operationalization tillgångar för distribution. Instruktioner för att installera och testa webbtjänsten som ingår i slutet av den `Code/3_operationalization.ipnyb` bärbar dator.

## <a name="conclusion"></a>Sammanfattning

Den här kursen används ett enkelt scenario som använder sensor värden för att göra förutsägelser. Mer avancerade scenarier för förutsägande underhåll, som den [förutsägande Underhåll Modeling guiden R anteckningsboken](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), kan använda flera datakällor, till exempel historiska Underhåll poster, felloggar, och datorn funktioner. Ytterligare datakällor kan kräva olika behandling som ska användas med djup learning.


## <a name="references"></a>Referenser

Det finns andra förutsägande Underhåll Använd case-exempel på en mängd olika plattformar:

* [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll Modeling Guide med SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

* [Verkligt scenario med förutsägande Underhåll](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>Nästa steg

Det finns många andra exempelscenarier Azure Machine Learning-arbetsstationen som visar ytterligare funktioner i produkten. 