---
title: "Verkligt Scenario med förutsägande Underhåll | Microsoft Docs"
description: "Förutsägande Underhåll verkliga världen scenariot med PySpark"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 0299e73aecca3b3e5714b37c8b0b776ec8561e29
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="predictive-maintenance-real-world-scenario"></a>Verkligt scenario med förutsägande underhåll.

Effekten av utrustning oplanerade driftstopp kan vara skadliga för alla företag. Det är viktigt därför håll fältet utrustning kör för att maximera användningen och prestanda och minimerar kostsamma, oplanerade driftstopp. Tidig identifiering av problem kan hjälpa allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedjan processer. 

Det här scenariot utforskar en relativt [storskaliga simulerade datauppsättning](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) funktion om du vill gå igenom ett förutsägande Underhåll datavetenskap projekt från datapåfyllning, ingenjörer och modellskapandet modellen operationalization och distribution. Koden för hela processen är skriven i Jupyter-anteckningsböcker med hjälp av PySpark Azure ML-arbetsstationen. Den slutliga modellen distribueras med hjälp av en Azure Machine Learning modellen Management realtid utrustning fel förutsägelser.   

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Följande är en länk till den offentliga GitHub-lagringsplatsen: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Använd case-översikt

Ett stort problem för företag i tillgångsinformation frekventa branscher är betydande kostnader som är associerade med fördröjningar mekaniskt problem. De flesta företag är intresserad av att förutsäga när problemen uppstår för att proaktivt hindra dem innan de inträffar. Målet är att minska kostnaderna genom att minska driftstopp och eventuellt ökar säkerheten. 

Det här scenariot tar idéer från den [förutsägande Underhåll playbook](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) att demonstrera bygga en förutsägelsemodell för en simulerad datauppsättning. Exempeldata härleds från vanliga element som observerats vid många förutsägande Underhåll användningsfall.

Problem i verksamheten för den här simulerade data är att förutsäga problem på grund av fel. Företag frågan är därför ”*vad är sannolikheten att en dator stängs av på grund av fel på en komponent*”? Det här problemet formateras som ett multiklass-baserad klassificeringsproblem (flera komponenter per dator) och en maskininlärningsalgoritmen används för att skapa förutsägelsemodellen. Modellen har tränats på historiska data som samlas in från datorer. I det här scenariot går användaren igenom de olika stegen för att implementera en sådan modell i Azure Machine Learning arbetsstationen-miljön.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/en-us/free/) (gratisutvärderingar finns).
* En installerad kopia av [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md) följande den [quickstart installationsguiden](./quickstart-installation.md) att installera programmet och skapa en arbetsyta.
* Azure Machine Learning Operationalization kräver en lokal distributionsmiljö och en [modell hanteringskontot](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

Det här exemplet kan köras på alla AML arbetsstationen beräknings-kontext. Men det rekommenderas att köra den med minst 16 GB minne. Det här scenariot har skapats och testat på en Windows 10-dator som kör en fjärransluten DS4_V2 standard [datavetenskap virtuell dator för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modellen operationalization gjordes med version 0.1.0a22 av Azure ML CLI.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den  **+**  och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”förutsägande underhåll” och välj mallen
5.  Klicka på **Skapa**

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

Den [simulerade data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) består av fem filer med kommaavgränsade värden (CSV). Följ länkarna för att få mer en mer detaljerad beskrivning av datauppsättningar.

* [Datorer](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): funktioner skilja varje dator. Ålder och modell.
* [Fel](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): felloggen innehåller hårt fel uppstod medan datorn fortfarande fungerar. Dessa fel anses inte vara fel, trots att de kan vara förutsägande i en framtida felhändelse. Fel datum och tid avrundas till närmaste timme eftersom telemetridata samlas in i en frekvens som per timme.
* [Underhåll](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): Underhåll loggen innehåller både planerade och oplanerade Underhåll poster. Schemalagt underhåll motsvarar regelbunden kontroll av komponenter, oplanerat Underhåll kan uppstå i mekaniskt fel eller andra prestandaförsämring. Underhåll tidsvärdet avrundas till närmaste timme eftersom telemetridata samlas in i en frekvens som per timme.
* [Telemetri](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): telemetri data består av tidsserier måtten från flera sensorer inom varje dator. Data loggas av sensor medelvärden under varje period för en timme.
* [Fel](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): fel som motsvarar komponenten ersättningar i loggen för underhåll. Varje post innehåller maskin-ID, komponenttyp och Ersättningsdatum och tid. Dessa poster används för att skapa etiketter som modellen försöker förutsäga för maskininlärning.

Finns det [Datapåfyllning](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter-anteckningsbok scenariot under kod för att hämta rådata datauppsättningar från GitHub-lagringsplatsen och skapa PySpark datauppsättningar för den här analysen.

## <a name="scenario-structure"></a>Scenario-struktur
Innehållet i scenariot är tillgängligt på den [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Den [viktigt](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fil ger en översikt över arbetsflödet från förbereder data, skapa en modell och distribuera en lösning för produktion. Varje steg i arbetsflödet kapslas in i en Jupyter-anteckningsbok i den [kod](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) mapp i databasen.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Den här anteckningsboken hämtar de fem inkommande CSV-filerna, kräver vissa förberedande Datarensning och visualisering. Anteckningsboken konverterar varje datamängd till PySpark-format och lagrar den till en Azure blob-behållare för användning i funktionen engineering anteckningsboken.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Med hjälp av rådata datauppsättningen från Azure blob, modell funktioner är konstruerade med normaltid serien metoden för telemetri, fel och underhåll data. Fel-relaterade komponenten ersättningar används för att skapa modelletiketter som beskriver vilka komponenten kunde inte. Märkt funktionsdata sparas i en Azure blob för modellen bygga bärbar dator.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Använda märkt funktionen-datamängden modellering anteckningsboken delar upp data i tåg och dev datauppsättningar längs datum-och tidsstämpel. Anteckningsboken kan installationsprogrammet set experiment med `pyspark.ml.classification` modeller. Av att träningsinformationen är vectorized och användaren kan experimentera med antingen en `DecisionTreeClassifier` eller en `RandomForestClassifier`, ändra justeringsmodeller för att hitta det bästa utför modellen. Prestanda bestäms genom att utvärdera mått statistik för dev-datauppsättningen. Statistiken loggas tillbaka i tiden AML arbetsstationen kör skärmen för spårning. Vid varje körning sparar anteckningsboken resulterande modellen till den lokala disken med Jupyter-anteckningsbok kernel. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Med den senaste modellen sparas till disk för lokala (Jupyter-anteckningsbok kernel), anteckningsboken bygger komponenter för att distribuera modellen till en Azure-webbtjänst. Fullständig operativa tillgångar komprimerad till den `o16n.zip` filen lagras i en annan Azure blob-behållare. Den komprimerade filen innehåller:

* `service_schema.json`Definitionsfilen schemat för distributionen. 
* `pdmscore.py`Funktionerna init() och run() krävs av Azure webbtjänsten
* `pdmrfull.model`Katalogen model definition.
    
 Anteckningsboken testar funktioner med modelldefinitionen av innan du paketerar operationalization tillgångar för distribution. Instruktioner för distribution ingår i slutet av den bärbara datorn.

## <a name="conclusion"></a>Sammanfattning

Det här scenariot ger läsaren en översikt över hur du skapar en slutpunkt till slutpunkt förutsägande Underhåll lösning med hjälp av PySpark i Jupyter-anteckningsbok miljön i Azure ML-arbetsstationen. Det här exemplet beskrivs också modellen distribution via Azure Machine Learning modellen Management-miljö för att göra realtid utrustning fel förutsägelser.

## <a name="references"></a>Referenser

Den här användningsfall har utvecklats tidigare på flera plattformar:

* [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll Modeling Guide med SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="next-steps"></a>Nästa steg

Det finns många andra exempelscenarier Azure Machine Learning-arbetsstationen som visar ytterligare funktioner i produkten. 