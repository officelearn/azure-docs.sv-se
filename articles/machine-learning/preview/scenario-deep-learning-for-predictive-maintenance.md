---
title: "Djup Learning för Scenario med förutsägande Underhåll verkliga - Azure | Microsoft Docs"
description: "Det här dokumentet beskriver hur du replikerar djup Learning förutsägande Underhåll självstudiekurs med Azure Machine Learning arbetsstationen"
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 8997e38a81ed848c9e052ab08566ffc99560ed86
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>Djup learning för förutsägande Underhåll verkligt scenario

Djupgående learning är en av de mest populära trenderna i machine learning utrymme Nuförtiden och det finns många fält och program där det står ut, till exempel protokollmekanismer utan drivrutiner bilar, tal-och image, robotics och ekonomi. Djupgående learning är en uppsättning algoritmer som inspirerat av vår hjärna (biologiska neurala nätverk) form och machine learning och kognitiva forskare refererar vanligtvis till den som artificiell Neurala nätverk (ANN).

Förutsägande Underhåll är också ett mycket populär område där många olika tekniker som är utformade för att avgöra tillståndet för utrustning för att förutsäga när Underhåll ska utföras. Förutsägande Underhåll omfattar en mängd ämnen, inklusive men inte begränsat till: felförutsägelse, fel diagnos (Rotorsaksanalys), felidentifiering, fel typ klassificering och rekommendation av minskning eller underhåll åtgärder efter ett fel uppstod.

I scenarier med förutsägande Underhåll samlas data över tid för att övervaka status för utrustning med slutgiltiga målet att identifiera mönster att förutsäga fel. Bland djup learning metoder, [lång kort sikt minne (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) nätverk är särskilt tilltalande till domänen förutsägande Underhåll på att de är mycket bra på att lära sekvenser. Detta lämpar sig för sina program med tiden series-data genom att göra det möjligt att gå tillbaka för längre tid för att upptäcka fel mönster.

I den här självstudiekursen kommer vi skapa ett LSTM nätverk för datauppsättningen och scenario som beskrivs i [förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3) att förutsäga återstående flygplan motorer livslängd. Sammanfattningsvis använder mallen simulerade flygplan sensor värden för att förutsäga när ett flygplan motorn misslyckas i framtiden så att Underhåll kan planeras i förväg.

Den här kursen använder [keras](https://keras.io/) djup learning bibliotek med Microsoft kognitiva Toolkit [CNTK](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras) som backend.

## <a name="link-to-the-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen galleri 

Följande är en länk till den offentliga GitHub-lagringsplatsen: [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)

## <a name="use-case-overview"></a>Använd case-översikt

Den här kursen används ett exempel på simulerade flygplan motorn kör-till-felhändelser för att demonstrera förutsägande Underhåll modellera processen. Det underförstådda antagandet om modellerar data som görs under har tillgången intressanta ett progressing mönster försämring avspeglas i tillgångens sensor mått. Genom att undersöka sensor resursvärden över tid Läs maskininlärningsalgoritmen relationen mellan sensor värdena och ändringar i sensor värden som tidigare fel för att förutsäga fel i framtiden. Vi rekommenderar att undersöka dataformatet och gå igenom alla tre stegen för mallen innan du ersätter data med dina egna.

## <a name="prerequisites"></a>Krav

- En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
- Ett installerade exemplar av Azure Machine Learning arbetsstationen med en arbetsyta skapas.
- För modellen operationalization: Azure Machine Learning Operationalization med en lokal distribution miljökonfiguration och en [modell hanteringskontot](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:

1. Öppna Azure Machine Learning-arbetsstationen
2. På sidan projekt i + loggar och välj nytt projekt
3. Fyll i informationen för det nya projektet i fönstret Skapa nytt projekt
4. Skriv ”förutsägande Underhåll” i sökrutan Sök projektmallar och välj mallen
5. Klicka på Skapa

## <a name="prepare-the-notebook-server-computation-target"></a>Förbereda anteckningsboken server beräkning mål

Att köra på den lokala datorn från AML arbetsstationen `File` -menyn väljer du antingen den `Open Command Prompt` eller `Open PowerShell` CLI. Kör följande kommandon i windows CLI:

`az ml experiment prepare --target docker --run-configuration docker`

 Vi föreslår körs på en standard som DS4_V2 [datavetenskap virtuell dator för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu). När du har konfigurerat DSVM måste du köra följande två kommandon:

`az ml computetarget attach remotedocker --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Med docker-avbildningar _förberedd_, öppna jupyter notebook server antingen inom den *AML arbetsstationen* bärbara datorer, och på fliken om du vill starta en webbläsare-baserad server som kör:`az ml notebook start`

- Bärbara datorer lagras i den `Code` directory hittades i Jupyter-miljö. Vi kör dessa anteckningsböcker sekventiellt som numrerade, startar på (`Code\1_data_ingestionand_and_preparation.ipynb`).

- Välj kernel att matcha dina [projekt_namn] _Template [Desired_Connection_Name] och klicka på Ange Kernel

## <a name="data-description"></a>Beskrivning av data

Mallen tar tre datauppsättningar som indata: ”PM_train.txt”, ”PM_test.txt” och ”PM_truth.txt”
1. Träna data: det är flygplan motorn kör till fel data. Train-data (”PM_train.txt”) består av flera multivariate tidsserier med ”cykel” som tidsenhet, tillsammans med 21 sensoravläsningar för varje cykel. Varje tidsserier kan antas som genereras från en annan modul av samma typ. Varje motor antas för att starta med olika typer av inledande förslitning och produktion variation och den här informationen är okänd för användaren. I den här simulerade data antas motorn fungerar normalt i början av varje tidsserier. Den börjar försämras vid något tillfälle under drift cykler serie. Nedbrytning fortskrider och växer i storlek. När en fördefinierad tröskelvärdet har uppnåtts är motorn som osäkra för ytterligare åtgärd. Den sista cykeln i varje tidsserier kan med andra ord anses felpunkt motsvarande motorns.

2. Testa data: det är flygplan motorn data utan felhändelser som registreras. Testdata (”PM_test.txt”) har samma dataschemat som utbildning-data. Den enda skillnaden är att data inte anger när felet inträffar (med andra ord den senaste tidsperioden inte representerar felpunkt). Det är inte känt hur många fler cykler den här motorn kan gälla innan det misslyckas.

3. Sanningen data: den innehåller information om SANT återstående cykler för varje motor i tester data. Grunden sanningen data innehåller antalet återstående fungerande cykler för motorer i tester data.

## <a name="scenario-structure"></a>Scenario-struktur

Innehållet i scenariot är tillgängligt i [GitHub-lagret] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

I databasen är en [viktigt] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md)-fil som beskriver processer från att förbereda data förrän bygga och operationalizing den modell. Tre Jupyter-anteckningsböcker är tillgängliga i mappen [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) i databasen. 

Därefter beskrivs arbetsflödet stegvisa scenariot:

### <a name="task-1-data-ingestion--preparation"></a>Uppgift 1: Datapåfyllning & förberedelse

Data införandet Jupyter Notebook i den `Code/1_data_ingestion_and_preparation.ipnyb` belastningar tre indata datauppsättningar i `Pandas` dataframe format, förbereder data för modellering del och har några inledande datavisualisering. Data sedan omvandlas till `PySpark` formatera och lagras i ett Azure Blob storage-behållare på prenumerationen för användning i nästa modellering uppgift.

### <a name="task-2-model-building--evaluation"></a>Uppgift 2: Modellskapandet & utvärdering

Modellen byggnad Jupyter Notebook i `Code/2_model_building_and_evaluation.ipnyb` som läser `PySpark` träna och testa datauppsättningar från blob storage. Sedan bygger ett LSTM nätverk med datauppsättningar utbildning. Modell-prestanda mäts på test-uppsättningen. Den resulterande modellen serialiseras och lagras i den lokala beräkning kontexten för användning i aktiviteten operationalization.

### <a name="task-3-operationalization"></a>Uppgift 3: Operationalization

Operationalization Jupyter Notebook i `Code/3_operationalization.ipnyb` webbtjänsten tar lagrade modellen och versioner krävs för funktioner och schema för att anropa modellen på ett Azure-värd. Anteckningsboken testar funktionerna och sänds operationalization tillgångar i en zip-fil som lagras i din Azure Blob storage-behållare.
Den komprimerade filen innehåller:

- `service_schema.json`Definitionsfilen schemat för distributionen. 
- `lstmscore.py`Funktionerna init() och run() krävs av Azure webbtjänsten
- `lstmfull.model`Katalogen model definition.

Anteckningsboken testar funktioner med modelldefinitionen av innan du paketerar operationalization tillgångar för distribution. Instruktioner för distribution ingår i slutet av den bärbara datorn.


## <a name="conclusion"></a>Slutsats

Den här kursen fungerar som en guide för nybörjare vill tillämpa djup learning i förutsägande underhållsdomän i Jupyter-anteckningsbok miljön i *Azure Machine Learning arbetsstationen*. Den här kursen används ett enkelt scenario där endast en datakälla (sensor värden) används för att göra förutsägelser. För mer avancerade scenarier förutsägande Underhåll sådana som i [förutsägande Underhåll modeling guiden](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-modeling-Guide-R-Notebook-1), det finns många andra datakällor (d.v.s. historiska Underhåll poster, felloggar, datorn och operatorn funktioner etc.) som kan kräva olika typer av behandling som ska användas i djup learning nätverk. Eftersom förutsägande Underhåll inte är en typisk domän för djup learning är dess program ett tomt område för forskning.

## <a name="references"></a>Referenser

- [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-modeling-Guide-1)
- [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-modeling-Guide-Python-Notebook-1)
- [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="future-directions-and-improvements"></a>Framtida trender och förbättringar

Den här kursen beskriver grunderna i djup learning i förutsägande underhåll. många problem med förutsägande Underhåll omfattar vanligtvis en mängd olika datakällor som måste beaktas vid tillämpningen av djup learning i den här domänen. Dessutom är det viktigt att finjustera modeller för rätt parametrar, till exempel fönsterstorlek. Läsaren kan redigera relevanta delar av det här scenariot och Använd ett annat problem-scenario som som beskrivs i den [förutsägande Underhåll Modeling guiden](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-modeling-Guide-1) där flera andra datakällor ingår.
