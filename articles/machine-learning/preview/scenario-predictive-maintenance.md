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
ms.openlocfilehash: dc73c052ad9e0fe12af5042289f304a0e48ae413
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>Verkligt scenario med förutsägande underhåll.

Effekten av utrustning oplanerade driftstopp kan vara skadliga för alla företag. Det är viktigt därför håll fältet utrustning kör för att maximera användningen och prestanda och minimerar kostsamma, oplanerade driftstopp. Tidig identifiering av problem kan hjälpa allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedjan processer. 

I det här scenariot använder vi en relativt [storskaliga data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) funktion för att hjälpa användaren genom de viktigaste stegen från datapåfyllning tekniker, modellskapandet, och slutligen modellen operationalization och distribution. Koden för hela processen är skriven i PySpark och implementeras med hjälp av Jupyter-anteckningsböcker Azure ML-arbetsstationen. Bästa modellen är slutligen operationalized med hjälp av Azure Machine Learning modellen Management för användning i en produktionsmiljö för att göra realtid fel förutsägelser.   

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Följande är en länk till den offentliga GitHub-lagringsplatsen: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Använd case-översikt

Ett stort problem för företag i tillgångsinformation frekventa branscher är betydande kostnader som är associerade med fördröjningar mekaniskt problem. De flesta företag är intresserad av att förutsäga när problemen uppstår för att proaktivt hindra dem innan de inträffar. Målet är att minska kostnaderna genom att minska driftstopp och eventuellt ökar säkerheten. Referera till den [playbook för förebyggande underhåll](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance) för en detaljerad förklaring av gemensamma använder fall samt den modellering metod som används för förebyggande underhåll.

Det här scenariot använder idéer från playbook i syfte att tillhandahålla stegen att implementera en förutsägelsemodell för ett scenario som baseras på en sammanfattning av flera verkliga affärsproblem. Det här exemplet innehåller gemensamma uppgifter som observerats bland många förutsägande Underhåll användningsfall.

Problem i verksamheten för den här simulerade data är att förutsäga problem på grund av fel. Företag frågan är därför ”*vad är sannolikheten att en dator stängs av på grund av fel på en komponent*”? Det här problemet formateras som ett multiklass-baserad klassificeringsproblem (flera komponenter per dator) och en maskininlärningsalgoritmen används för att skapa förutsägelsemodellen. Modellen har tränats på historiska data som samlas in från datorer. I det här scenariot går användaren igenom de olika stegen för att implementera en sådan modell i Azure Machine Learning arbetsstationen-miljön.

## <a name="prerequisites"></a>Krav

* En [Azure-konto](https://azure.microsoft.com/en-us/free/) (gratisutvärderingar finns).
* En installerad kopia av [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](./quickstart-installation.md) att installera programmet och skapa en arbetsyta.
* Azure Machine Learning Operationalization kräver en lokal distributionsmiljö och en [modell hanteringskontot](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

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

Att köra på den lokala datorn från AML arbetsstationen `File` -menyn väljer du antingen den `Open Command Prompt` eller `Open PowerShell CLI`. Kör följande kommandon i windows CLI:

`az ml experiment prepare --target docker --run-configuration docker`

Vi rekommenderar att du kör på en datavetenskap virtuell dator för Linux (Ubuntu). När du har konfigurerat DSVM kör du följande två kommandon:

`az ml computetarget attach --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword] --type remotedocker`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Med docker-bilder är förberedd öppna Jupyter-anteckningsbok server inom fliken AML arbetsstationen bärbara datorer eller för att starta en webbläsare-baserad server, kör: `az ml notebook start`.

Bärbara datorer lagras i den `Code` katalog i Jupyter-miljön. Kör vi de bärbara datorerna, från första (`Code\1_data_ingestion.ipynb`) bärbar dator. När du öppnar varje bärbar dator, uppmanas du att välja kernel för beräkning. Välj [projekt_namn] _Template [Desired_Connection_Name] och klicka på Ange Kernel.

## <a name="data-description"></a>Beskrivning av data

Den [simulerade data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) består av fem filer med kommaavgränsade värden (CSV). Följ länkarna för att få mer en mer detaljerad beskrivning av datauppsättningar.

* [Datorer](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): funktioner skilja varje dator. Ålder och modell.
* [Fel](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): felloggen innehåller hårt fel uppstod medan datorn fortfarande fungerar. Dessa fel anses inte vara fel, trots att de kan vara förutsägande i en framtida felhändelse. Fel datum och tid avrundas till närmaste timme eftersom telemetridata samlas in i en frekvens som per timme.
* [Underhåll](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): Underhåll loggen innehåller både planerade och oplanerade Underhåll poster. Schemalagt underhåll motsvarar regelbunden kontroll av komponenter, oplanerat Underhåll kan uppstå i mekaniskt fel eller andra prestandaförsämring. Underhåll tidsvärdet avrundas till närmaste timme eftersom telemetridata samlas in i en frekvens som per timme.
* [Telemetri](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): tidsserier telemetridata består av spänning, rotation, tryck, vibration sensor mått och som samlats in från varje dator i realtid. Data är ett genomsnitt över en timme och lagras i loggarna telemetri
* [Fel](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): fel som motsvarar komponenten ersättningar i loggen för underhåll. Varje post innehåller maskin-ID, komponenttyp och Ersättningsdatum och tid. Dessa poster används för att skapa etiketter som modellen försöker förutsäga för maskininlärning.

Finns det [Datapåfyllning](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter-anteckningsbok scenariot under kod för att hämta rådata datauppsättningar från GitHub-lagringsplatsen och skapa PySpark datauppsättningar för den här analysen.

## <a name="scenario-structure"></a>Scenario-struktur
Innehållet i scenariot är tillgängligt på den [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Det finns i databasen, en [viktigt](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fil som beskriver processer från att förbereda data förrän några modellutveckling och operationalizing en av de bästa modellerna slutligen. Det finns fyra Jupyter notebooks i den [kod](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) mapp i databasen.   

Vi beskriver nästa steg för steg-scenariot arbetsflödet. Slutpunkt till slutpunkt-scenario är skriven i PySpark och delas in i fyra datorer:

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Den här anteckningsboken hämtar de fem inkommande CSV-filerna, kräver vissa förberedande Datarensning och visualisering. Anteckningsboken konverterar data till PySpark-format och lagrar resultatet i ett Azure blob-behållaren för användning i funktionen engineering uppgiften.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Med den rensade datauppsättningen från föregående steg, fördröjning och aggregerade funktioner har skapats för de telemetri sensorer och fel antal komponenten ersättningarna, information om datorn är ansluten till telemetridata. Fel-relaterade komponenten ersättningar används för att skapa etiketter som beskriver vilka komponenten kunde inte. Märkt funktionsdata sparas i en Azure blob för modellen skapa uppgiften.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Använda märkt funktionen-datamängden modellering anteckningsboken delar upp data i tåg och dev datauppsättningar längs datum-och tidsstämpel. Anteckningsboken kan installationsprogrammet set experiment med `pyspark.ml.classification` modeller. Av att träningsinformationen är vectorized och användaren kan experimentera med antingen en `DecisionTreeClassifier` eller en `RandomForestClassifier`, ändra justeringsmodeller för att hitta det bästa utför modellen. Prestanda bestäms genom att utvärdera mått statistik för dev-datauppsättningen. Statistiken loggas tillbaka i tiden AML arbetsstationen kör skärmen för spårning. Vid varje körning sparar anteckningsboken resulterande modellen till den lokala disken med Jupyter-anteckningsbok kernel. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Med den senaste modellen sparas till disk för lokala (Jupyter-anteckningsbok kernel), anteckningsboken bygger komponenter för operationalizing modellen till en Azure-webbtjänst. Fullständig operativa tillgångar komprimerad till den `o16n.zip` filen lagras i en annan Azure blob-behållare. Den komprimerade filen innehåller:

* `service_schema.json`Definitionsfilen schemat för distributionen. 
* `pdmscore.py`Funktionerna init() och run() krävs av Azure webbtjänsten
* `pdmrfull.model`Katalogen model definition.
    
 Anteckningsboken testar funktioner med modelldefinitionen av innan du paketerar operationalization tillgångar för distribution. Instruktioner för distribution ingår i slutet av den bärbara datorn.

## <a name="conclusion"></a>Slutsats

Det här scenariot ger läsaren en översikt över hur du skapar en slutpunkt till slutpunkt förutsägande Underhåll lösning med hjälp av PySpark i Jupyter-anteckningsbok miljön i Azure ML-arbetsstationen. Scenariot hjälper också läsaren på hur den bästa modellen kan enkelt operationalized och distribueras via Azure Machine Learning modellen Management miljö för användning i en produktionsmiljö för att göra realtid fel förutsägelser. Läsaren kan sedan redigera relevanta delar av scenariot för att konforma sina affärsbehov.  

## <a name="references"></a>Referenser

Den här användningsfall har utvecklats tidigare på flera plattformar:

* [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll Modeling Guide med SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>Nästa steg

Det finns många andra exempelscenarier Azure Machine Learning-arbetsstationen som visar ytterligare funktioner i produkten. 