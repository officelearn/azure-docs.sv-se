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
ms.openlocfilehash: 022e629469745fceb4fb9355cb6259a144dda222
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Djupgående utbildning för förutsägande Underhåll verkliga scenarier

Djupgående learning är en av de mest populära trenderna i machine learning och har många områden, inklusive-program:
- Protokollmekanismer utan drivrutiner bilar och robotics.
- Tal- och Igenkännande.
- finansiella prognoser.

Kallas djupa neurala nätverk (DNN) kan är dessa metoder inspirerat av enskilda neurons som ligger inom hjärna (biologiska neurala nätverk).

Effekten av utrustning oplanerade driftstopp kan vara skadliga för alla företag. Det är viktigt att behålla fältet utrustning som körs för att maximera användning och prestanda och minimera kostsamma, oplanerade driftavbrott. Tidig identifiering av problem kan hjälpa allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedjan processer. 

En strategi för förutsägande Underhåll (em) använder machine learning metoder för att avgöra tillståndet för utrustning förebyggande syfte Underhåll ska utföras för att undvika negativa datorn prestanda. PM, data som samlas in med tiden för att övervaka status för datorn och sedan analyseras för att hitta mönster för att förutsäga fel. [Lång kort sikt minne (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) nätverk är bra för den här inställningen, eftersom de har utformats för att lära sig från sekvenser av data.

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub-lagringsplatsen

Cortana Intelligence Gallery PM genomgång är en offentlig GitHub-lagringsplats ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) där du kan rapportera problem och avgifter.


## <a name="use-case-overview"></a>Använd case-översikt

Den här kursen används ett exempel på simulerade flygplan motorn kör-till-felhändelser för att demonstrera förutsägande Underhåll modellera processen. Scenariot beskrivs i [förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Den huvudsakliga antaganden i den här inställningen är motorn progressivt försämras under sin livslängd. Nedbrytning kan identifieras i motorn sensor mått. PM försöker modellen relationen mellan ändringarna i värdena sensor och historiska fel. Modellen kan sedan förutsäga när och motorn fungerar inte i framtiden baserat på aktuell status för sensor mått.

Det här scenariot skapar ett LSTM nätverk för att förutsäga återstående livslängd (RUL) av flygplan motorer med hjälp av historiska sensor värden. Scenariot använder den [Keras](https://keras.io/) bibliotek med det [Tensorflow](https://www.tensorflow.org/) djup learning ramverk som motorn för beräkning. Scenariot tränar LSTM med en uppsättning motorer och testar nätverket på en uppsättning överblivna motorn.

## <a name="prerequisites"></a>Förutsättningar
- En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
- Azure Machine Learning arbetsstation med en arbetsyta skapas.
- För modellen operationalization: Azure Machine Learning Operationalization med en lokal distributionsmiljö ställer in, och en [konto i Azure Machine Learning modellen Management](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt genom att använda det här exemplet som en mall:

1. Öppna Machine Learning arbetsstationen.
2. På den **projekt** väljer  **+** , och välj sedan **nytt projekt**.
3. I den **Skapa nytt projekt** rutan Ange information för det nya projektet.
4. I den **Sök projektmallar** sökrutan, Skriv ”förutsägande underhåll” och välj den **djup Learning för scenariot med förutsägande Underhåll** mall.
5. Välj **Skapa**.

## <a name="prepare-the-notebook-server-computation-target"></a>Förbereda anteckningsboken server beräkning mål

Att köra på den lokala datorn från Machine Learning-arbetsstationen **filen** -menyn väljer du antingen **öppnar du kommandotolken** eller **öppna PowerShell CLI**. Gränssnittet CLI får du åtkomst till Azure-tjänster med hjälp av den `az` kommandon. Först logga in på ditt Azure-konto med kommandot:

```
az login
``` 

Det här kommandot ger en autentiseringsnyckel som ska användas med https:\\aka.ms\devicelogin URL. CLI väntar tills enheten inloggningen åtgärden returnerar och ger vissa anslutningsinformationen. Nästa, om du har en lokal [Docker](https://www.docker.com/get-docker) installation, Förbered lokala beräknings-miljö med kommandot:

```
az ml experiment prepare --target docker --run-configuration docker
```

Det är bättre att köra på en [Data vetenskap virtuell dator (DSVM) för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) för minne och diskutrymme. När DSVM har konfigurerats, Förbered fjärr Docker-miljö med följande två kommandon:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

När du är ansluten till den fjärranslutna dockerbehållare Förbered DSVM Docker beräknings-miljö med kommandot: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Öppna Jupyter-anteckningsbok servern från Machine Learning-arbetsstationen med Docker-beräkning miljön förberedd, **anteckningsböcker** fliken eller starta en webbläsare-baserad server med kommandot: 

```
az ml notebook start
```

Exempel-anteckningsböcker som lagras i katalogen kod. De bärbara datorerna är konfigurerade att köras efter varandra startar på den första (Code\1_data_ingestion.ipynb)-anteckningsboken. När du öppnar varje bärbar dator, uppmanas du att välja kernel för beräkning. Välj [projekt_namn] _Template [Connection_Name] kernel ska köras på den tidigare konfigurerade DSVM.

## <a name="data-description"></a>Beskrivning av data

Mallen använder tre datauppsättningar som indata i filerna PM_train.txt och PM_test.txt PM_truth.txt. 

- **Träna data**: flygplan motorn kör till fel data. Train-data (PM_train.txt) består av flera multivariate tidsserier med *växla* som tidsenheten. Den omfattar 21 sensoravläsningar för varje cykel. 

    - Varje tidsserier genereras från en annan modul av samma typ. Varje motor börjar med olika grader av inledande förslitning och en unik tillverkning variant. Den här informationen är okänd för användaren. 

    - I den här simulerade data antas motorn fungerar normalt i början av varje tidsserier. Den börjar försämras vid något tillfälle under drift cykler serie. Nedbrytning fortskrider och växer i storlek. 

    - När en fördefinierad tröskelvärde uppnås betraktas motorn som osäkra för ytterligare åtgärd. Den sista cykeln av varje tidsserier är felpunkt för som motorn.

- **Testa data**: flygplan motorn driftuppgifter, utan felhändelser som registreras. Testdata (PM_test.txt) har samma dataschemat som utbildning-data. Den enda skillnaden är att data inte anger när felet inträffar (senaste tidsperiod har *inte* representerar felpunkt). Det är inte känt hur många fler cykler den här motorn kan gälla innan det misslyckas.

- **Sanningen data**: informationen true återstående cykler för varje motor i tester data. Grunden sanningen data innehåller antalet återstående fungerande cykler för motorer i tester data.

## <a name="scenario-structure"></a>Scenario-struktur

Arbetsflöde för scenariot är uppdelat i tre steg och varje steg utförs i en Jupyter-anteckningsbok. Varje anteckningsbok producerar dataartifakter sparas lokalt för de bärbara datorerna.

### <a name="task-1-data-ingestion-and-preparation"></a>Uppgift 1: Datapåfyllning och förberedelse

Data införandet Jupyter Notebook i Code/1_data_ingestion_and_preparation.ipnyb läser in tre inkommande datauppsättningar i formatet Pandas DataFrame. Den bärbara datorn sedan förbereder data för modellering och har några inledande datavisualisering. De uppgifter som lagras lokalt i beräknings-kontexten för användning i Jupyter-anteckningsbok modellen byggnad.

### <a name="task-2-model-building-and-evaluation"></a>Uppgift 2: Modellskapandet och utvärdering

Modellen byggnad Jupyter Notebook i Code/2_model_building_and_evaluation.ipnyb läser datauppsättningar tåg och testa från disk och skapar ett LSTM nätverk för datauppsättning för träning. Modell-prestanda mäts i datamängden test. Den resulterande modellen serialiseras och lagras i den lokala beräkning kontexten för användning i aktiviteten operationalization.

### <a name="task-3-operationalization"></a>Uppgift 3: Operationalization

Operationalization Jupyter Notebook i Code/3_operationalization.ipnyb använder lagrade modellen för att skapa funktioner och schema för att anropa modellen på en Azure-baserad webbtjänst. Anteckningsboken testar funktionerna och sedan komprimerar tillgångarna till LSTM_o16n.zip-fil. Filen läses in på ditt Azure storage-behållare för distribution.

Filen LSTM_o16n.zip distribution innehåller följande artefakter:

- **webservices_conda.yaml**: definierar Python-paket som krävs för att köra LSTM modellen på distributionsmålet.  
- **service_schema.JSON**: definierar dataschemat som förväntas av LSTM-modell.   
- **lstmscore.PY**: definierar de funktioner som distributionsmålet körs att poängsätta nya data.    
- **modellstm.JSON**: definierar LSTM arkitektur. Funktionerna lstmscore.py läsa arkitektur och vikter till initiera modellen.
- **modellstm.H5**: definierar vikterna modellen.
- **test_service.PY**: ett testskript som anropar slutpunkten distribution med test dataposter. 
- **PM_test_files.pkl**: test_service.py-skriptet läser motorn för historiska data från filen PM_test_files.pkl och skickar webbtjänsten tillräckligt med cykler för LSTM att returnera en sannolikheten för fel motorn.

Anteckningsboken testar funktionerna med hjälp av modelldefinitionen innan det paket operationalization tillgångar för distribution. Instruktioner för att installera och testa webbtjänsten ingår i slutet av Code/3_operationalization.ipnyb anteckningsboken.

## <a name="conclusion"></a>Sammanfattning

Den här självstudiekursen innehåller ett enkelt scenario som använder sensor värden för att göra förutsägelser. Mer avancerade förutsägande Underhåll scenarier som den [förutsägande Underhåll Modeling guiden R anteckningsboken](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) kan använda flera datakällor, till exempel historiska Underhåll poster, felloggarna och funktioner på datorer. Ytterligare datakällor kan kräva olika behandling för användning med djup learning.


## <a name="references"></a>Referenser

Följande referens ger exempel på andra förutsägande Underhåll användningsfall för olika plattformar:

* [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll Modeling Guide med SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Förutsägande Underhåll verkliga scenarier](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>Nästa steg

Andra scenarier är tillgängliga i Machine Learning-arbetsstationen som visar ytterligare funktioner i produkten. 
