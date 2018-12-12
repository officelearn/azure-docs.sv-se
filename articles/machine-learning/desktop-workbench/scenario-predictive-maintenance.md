---
title: Förebyggande underhåll för verkliga scenarier | Microsoft Docs
description: Förebyggande underhåll för verkliga scenarier med hjälp av PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9c638ed9132612db7b82168d3a57057aba9b2d60
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870342"
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Förebyggande underhåll för verkliga scenarier

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Följderna av oplanerade avbrott för utrustning kan vara skadliga för alla företag. Det är viktigt att hålla fältet utrustning som kör maximerar användning och prestanda och minimera dyra, oplanerade driftstopp. Tidig identifiering av problem kan allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedja processer. 

Det här scenariot utforskar en relativt [storskaliga simulerad datamängd](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) om du vill gå igenom en förebyggande underhåll datavetenskapsprojekt från datainmatning, funktioner, modellskapandet och driftsättning av modellen, och distribution. Koden för hela processen är skriven i Jupyter-anteckningsbok med PySpark i Azure Machine Learning Workbench. Den slutliga modellen distribueras med hjälp av Azure Machine Learning-modellhantering för att göra i realtid utrustning fel förutsägelser.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence-galleriet GitHub-lagringsplats

Cortana Intelligence-galleriet för PM-självstudiekursen är en offentlig GitHub-lagringsplats ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) där du kan rapportera problem och göra bidrag.


## <a name="use-case-overview"></a>Översikt över användningsfall

Ett stort problem som företag inom tillgången omfattande är betydande kostnader som är associerade med fördröjningar på grund av mekanisk problem. De flesta företag är intresserade att förutsäga när dessa problem kan uppstå att proaktivt förhindra dem innan de inträffar. Målet är att minska kostnaderna genom att minska driftstopp och eventuellt ökar säkerhet. 

Det här scenariot tar idéer från den [förebyggande underhåll spelbok](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) att demonstrera hur du skapar en förutsägelsemodell för en simulerad datamängd. Exempeldata härleds från vanliga element som observerats i många användningsområden för förebyggande underhåll.

Affärsproblem för den här simulerade data är att förutsäga problem som orsakas av komponentfel. Företag frågan är ”*vad är sannolikheten att en virtuell dator stängs av på grund av fel på en komponent?*” Det här problemet formateras som ett klassificeringsproblem med flera (flera komponenter per dator). En maskininlärningsalgoritm används för att skapa förutsägande modell. Modellen tränas historiska data som samlas in från datorer. I det här scenariot går användaren igenom olika steg för att implementera modellen i Machine Learning Workbench-miljö.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
* En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Följ den [Snabbstart installationsguide](quickstart-installation.md) att installera programmet och skapa en arbetsyta.
* Azure Machine Learning Operationalization kräver en lokal distribution-miljö och en [Azure Machine Learning-modellhantering konto](model-management-overview.md).

Det här exemplet körs på alla kontexter för beräkning av Machine Learning Workbench. Vi rekommenderar dock att du kör exemplet med minst 16 GB minne. Det här scenariot har skapats och testats på en Windows 10-dator som kör en fjärransluten DS4_V2 standard [Data Science Virtual Machine (DSVM) för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modellen driftsättning gjordes med hjälp av version 0.1.0a22 av Azure Machine Learning CLI.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Machine Learning Workbench.
2.  På den **projekt** väljer **+**, och välj sedan **nytt projekt**.
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet.
4.  I den **Sök efter projektmallar** sökrutan skriver du ”förebyggande underhåll” och väljer den **förebyggande underhåll** mall.
5.  Välj **Skapa**.

## <a name="prepare-the-notebook-server-computation-target"></a>Förbered notebook server beräkning mål

Att köra på den lokala datorn från Machine Learning Workbench **filen** menyn väljer du antingen **öppna Kommandotolken** eller **öppna PowerShell CLI**. CLI-gränssnittet kan du komma åt dina Azure-tjänster med hjälp av den `az` kommandon. Logga först in på Azure-kontot med kommandot:

```
az login
``` 

Det här kommandot ger en autentiseringsnyckel ska användas med https:\\aka.ms\devicelogin URL: en. CLI väntar tills enheten inloggningen åtgärden returnerar och vissa anslutningsinformation. Nästa, om du har en lokal [Docker](https://www.docker.com/get-docker) installation, förbereda lokala compute-miljö med kommandot:

```
az ml experiment prepare --target docker --run-configuration docker
```

Det är bättre att köra en [DSVM för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) för minne och krav. När DSVM har konfigurerats kan du förbereda den fjärranslutna Docker-miljön med följande två kommandon:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

När du är ansluten till den fjärranslutna Docker-behållaren, förbereda beräkningsmiljö DSVM Docker med kommandot: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Med Docker beräkningsmiljö som förberetts, öppna Jupyter notebook-server från Azure Machine Learning Workbench **anteckningsböcker** fliken eller starta en webbläsare-baserad server med kommandot: 

```
az ml notebook start
```

Exempel-anteckningsböcker lagras i katalogen kod. De bärbara datorerna är inställda som körs sekventiellt, från och med den första (Code\1_data_ingestion.ipynb)-anteckningsboken. När du öppnar varje bärbar dator, uppmanas du att välja beräknings-kernel. Välj [projekt_namn] _namn [Connection_Name] kernel ska köras på den tidigare konfigurerade DSVM.

## <a name="data-description"></a>Databeskrivning

Den [simulerade data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide) består av följande [fem fil med kommaavgränsade värden (.csv) filer](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data):

* [Datorer](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/machines.csv): funktioner som skilja varje dator, till exempel ålder och modell.
* [Fel](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/errors.csv): felloggen innehåller bakåtkompatibla fel som kan uppkomma medan datorn fortfarande fungerar. De här felen beaktas inte fel, men de kan vara prediktiva för en framtida felhändelse. Datum / tid-värdena för fel som avrundas till närmaste timme eftersom dessa data som samlas in till ett timpris.
* [Underhåll](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/maint.csv): Underhåll loggen innehåller både planerade och oplanerade Underhåll poster. Schemalagt underhåll motsvarar en regelbunden kontroll av komponenter. Oplanerat Underhåll kan uppstå från mekanisk fel eller andra prestandaförsämring. Datum / tid-värdena för underhåll avrundas till närmaste timme eftersom dessa data som samlas in till ett timpris.
* [Telemetri](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/telemetry.csv): telemetridata som består av time series mått från flera sensorer på varje dator. Data loggas som medelvärdet sensorvärdena över varje intervall med en timme.
* [Fel](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/failures.csv): fel som motsvarar komponenten ersättningar i loggen för underhåll. Varje post innehåller maskin-ID, komponenttyp och av Ersättningsdatum och tid. Dessa poster används för att skapa de machine learning-etiketter som du försöker förutse modellen.

Ladda ner raw datauppsättningar från GitHub-lagringsplatsen och skapa PySpark-datauppsättningar för den här analysen genom att se den [datainmatning](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook-scenario i mappen kod.

## <a name="scenario-structure"></a>Scenario-struktur
Innehållet för scenariot finns på den [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Den [Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) filen beskriver arbetsflödet från förbereda data, skapa en modell och sedan distribuera en lösning för produktion. Varje steg i arbetsflödet är inkapslad i en Jupyter-anteckningsbok i den [kod](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) mapp i databasen.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): den här anteckningsboken hämtar de fem inkommande CSV-filerna och har vissa preliminär Datarensning och visualisering. Anteckningsboken konverterar varje datauppsättning till PySpark-format och lagrar den i en Azure blob-behållare för användning i funktionen Engineering anteckningsboken.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): modellfunktioner skapas från rådata datauppsättningen från Azure Blob storage med hjälp av en serie normaltid-metod för telemetri, fel och underhåll data. Fel-relaterade komponenten ersättningar används för att konstruera modelletiketter som beskriver vilken komponent misslyckades. Taggade funktionsdata sparas i en Azure-blob för anteckningsboken för att bygga modellen.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): The modell att skapa anteckningsboken använder märkta data funktionsuppsättningen och delar upp data i träna upp och dev datauppsättningar med datum-och tidsstämpel. Anteckningsboken har ställts in som ett experiment med pyspark.ml.classification modeller. Träningsdata vectorized. Du kan experimentera med antingen en **DecisionTreeClassifier** eller **RandomForestClassifier** att manipulera hyperparametrar för att hitta den bästa modellen. Prestanda påverkas genom att utvärdera mätningsstatistik på dev-datauppsättning. Statistiken loggas i Machine Learning Workbench runtime skärmen för spårning. Vid varje körning sparar anteckningsboken resulterande modellen till den lokala disken med Jupyter notebook kernel. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): den här anteckningsboken använder den senaste modellen som sparas på den lokala disken (Jupyter notebook kernel) att skapa komponenter för att distribuera modellen till en Azure-webbtjänst. Fullständig operativa tillgångar komprimeras till filen o16n.zip som lagras i en annan Azure-blob-behållare. Den komprimerade filen innehåller:

* **service_schema.JSON**: definitionsfilen för schemat för distributionen. 
* **pdmscore.PY**: den **init()** och **run()** funktioner som krävs av Azure-webbtjänsten.
* **pdmrfull.Model**: katalogen model definition.
    
Anteckningsboken testar funktioner med modelldefinitionen av innan paketering driftsättning tillgångar för distribution. Instruktioner för distributionen ingår i slutet av anteckningsboken.

## <a name="conclusion"></a>Sammanfattning

Det här scenariot ger en översikt över att skapa en lösning för förebyggande underhåll för slutpunkt till slutpunkt med hjälp av PySpark i Jupyter-anteckningsbok miljön i Machine Learning Workbench. Det här scenariot beskriver även modelldistribution genom Machine Learning-modellhantering-miljö för att göra i realtid utrustning fel förutsägelser.

## <a name="references"></a>Referenser

Följande referens ger exempel på andra förebyggande underhåll användningsfall för olika plattformar:

* [Lösningsmallen för förebyggande underhåll](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förebyggande underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll modellering Guide med hjälp av SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande modellering Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förebyggande underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Djupinlärning för förutsägande Underhåll](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Nästa steg

Andra scenarier är tillgängliga i Machine Learning Workbench som visar hur du ytterligare funktioner i produkten. 
