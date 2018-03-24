---
title: Förutsägande Underhåll för verkliga scenarier | Microsoft Docs
description: Förutsägande Underhåll för verkliga scenarier med hjälp av PySpark
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
ms.openlocfilehash: bb6e4f9f147db2fb70d991922cf0bb8d16b29671
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Förutsägande Underhåll för verkliga scenarier

Effekten av utrustning oplanerade driftstopp kan vara skadliga för alla företag. Det är viktigt att behålla fältet utrustning kör för maximal användning och prestanda och för att minimera kostsamma, oplanerade driftavbrott. Tidig identifiering av problem kan hjälpa allokera begränsad Underhåll resurser på ett kostnadseffektivt sätt och förbättra kvaliteten och ange kedjan processer. 

Det här scenariot utforskar en relativt [storskaliga simulerade datauppsättning](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) funktion om du vill gå igenom ett förutsägande Underhåll datavetenskap projekt från datapåfyllning, ingenjörer och modellskapandet modellen operationalization och distribution. Koden för hela processen skrivs i Jupyter-anteckningsbok med PySpark i Azure Machine Learning-arbetsstationen. Den slutliga modellen distribueras med hjälp av Azure Machine Learning modellen Management för att kommentera realtid utrustning fel.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub-lagringsplatsen

Cortana Intelligence Gallery PM genomgång är en offentlig GitHub-lagringsplats ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) där du kan rapportera problem och bidrag.


## <a name="use-case-overview"></a>Använd case-översikt

Ett större problem som företag i tillgångsinformation frekventa branscher inför är betydande kostnader som är associerade med fördröjningar på grund av mekanisk problem. De flesta företag är intresserad av att förutsäga när dessa problem kan uppstå till att hindra dem innan de inträffar. Målet är att minska kostnaderna genom att minska driftstopp och eventuellt ökar säkerheten. 

Det här scenariot tar idéer från den [förutsägande Underhåll playbook](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) att demonstrera hur du skapar en förutsägelsemodell för en simulerad datauppsättning. Exempeldata härleds från vanliga element som observerats vid många förutsägande Underhåll användningsområden.

Problem i verksamheten för den här simulerade data är att förutsäga problem som orsakas av fel. Företag frågan är ”*vad är sannolikheten att en dator stängs av på grund av fel på en komponent?*” Det här problemet formateras som ett klassificeringsproblem med flera klassen (flera komponenter per dator). En maskininlärningsalgoritmen används för att skapa förutsägelsemodellen. Modellen har tränats på historiska data som samlas in från datorer. I det här scenariot genomgår användaren olika steg för att implementera modellen i Machine Learning arbetsstationsmiljö.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
* En installerad kopia av [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md). Följ den [Quickstart installationsguiden](./quickstart-installation.md) att installera programmet och skapa en arbetsyta.
* Azure Machine Learning Operationalization kräver en lokal distributionsmiljö och en [konto i Azure Machine Learning modellen Management](model-management-overview.md).

Det här exemplet körs på en Machine Learning arbetsstationen beräknings-kontext. Men rekommenderar vi att du kör exemplet med minst 16 GB minne. Det här scenariot har skapats och testat på en Windows 10-dator som kör en fjärransluten DS4_V2 standard [Data vetenskap virtuell dator (DSVM) för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modellen operationalization gjordes med hjälp av version 0.1.0a22 av Azure Machine Learning CLI.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt genom att använda det här exemplet som en mall:
1.  Öppna Machine Learning Workbench.
2.  På den **projekt** väljer **+**, och välj sedan **nytt projekt**.
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet.
4.  I den **Sök projektmallar** sökrutan, Skriv ”förutsägande underhåll” och välj den **förutsägande Underhåll** mall.
5.  Välj **Skapa**.

## <a name="prepare-the-notebook-server-computation-target"></a>Förbereda anteckningsboken server beräkning mål

Att köra på den lokala datorn från Machine Learning-arbetsstationen **filen** -menyn väljer du antingen **öppnar du kommandotolken** eller **öppna PowerShell CLI**. Gränssnittet CLI får du åtkomst till Azure-tjänster med hjälp av den `az` kommandon. Först logga in på ditt Azure-konto med kommandot:

```
az login
``` 

Det här kommandot ger en autentiseringsnyckel ska användas med https:\\aka.ms\devicelogin URL. CLI väntar tills enheten inloggningen åtgärden returnerar och ger vissa anslutningsinformationen. Nästa, om du har en lokal [Docker](https://www.docker.com/get-docker) installation, Förbered lokala beräknings-miljö med kommandot:

```
az ml experiment prepare --target docker --run-configuration docker
```

Det är bättre att köra på en [DSVM för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) för minne och diskutrymme. När DSVM har konfigurerats, Förbered fjärr Docker-miljö med följande två kommandon:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

När du är ansluten till den fjärranslutna dockerbehållare Förbered DSVM Docker beräknings-miljö med kommandot: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Med Docker-beräkning miljön förberedd, öppnar du Jupyter-anteckningsbok servern från Azure Machine Learning arbetsstationen **anteckningsböcker** fliken eller starta en webbläsare-baserad server med kommandot: 

```
az ml notebook start
```

Exempel-anteckningsböcker som lagras i katalogen kod. De bärbara datorerna är konfigurerade att köras efter varandra startar på den första (Code\1_data_ingestion.ipynb)-anteckningsboken. När du öppnar varje bärbar dator, uppmanas du att välja kernel för beräkning. Välj [projekt_namn] _Template [Connection_Name] kernel ska köras på den tidigare konfigurerade DSVM.

## <a name="data-description"></a>Beskrivning av data

Den [simulerade data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) består av fem filer med kommaavgränsade värden (CSV). Använd följande länkar för att få detaljerade beskrivningar om datauppsättningar.

* [Datorer](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): funktioner som skilja mellan varje dator, till exempel ålder och modell.
* [Fel](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): felloggen innehåller hårt fel som utlöses när datorn är fortfarande fungerar. Dessa fel inte anses vara fel, även om de kan vara förutsägande i en framtida felhändelse. Datum / tid-värden för felen avrundas till närmaste timme eftersom telemetridata samlas in i en frekvens som per timme.
* [Underhåll](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): Underhåll loggen innehåller både planerade och oplanerade Underhåll poster. Schemalagt underhåll motsvarar en regelbunden kontroll av komponenter. Oplanerat Underhåll kan uppstå när mekaniskt fel eller andra prestandaförsämring. Datum / tid-värden för underhåll avrundas till närmaste timme eftersom telemetridata samlas in i en frekvens som per timme.
* [Telemetri](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): telemetridata består av tid serien mått från flera sensorer inom varje dator. Data loggas av sensor medelvärden under varje period för en timme.
* [Fel](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): fel som motsvarar komponenten ersättningar i loggen för underhåll. Varje post innehåller maskin-ID, komponenttyp och Ersättningsdatum och tid. Dessa poster används för att skapa etiketter som modellen försöker förutsäga för maskininlärning.

För att hämta rådata datauppsättningar från GitHub-lagringsplatsen och skapa PySpark datauppsättningar för den här analysen, finns det [Datapåfyllning](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter-anteckningsbok scenariot i mappen kod.

## <a name="scenario-structure"></a>Scenario-struktur
Innehållet i scenariot är tillgängligt på den [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Den [viktigt](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fil ger en översikt över arbetsflödet från förbereder data, skapa en modell och distribuera en lösning för produktion. Varje steg i arbetsflödet kapslas in i en Jupyter-anteckningsbok i den [kod](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) mapp i databasen.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): anteckningsboken hämtar de fem inkommande CSV-filerna och har vissa preliminär Datarensning och visualisering. Anteckningsboken konverterar varje datamängd till PySpark-format och lagrar den i ett Azure blob-behållaren för användning i funktionen Engineering anteckningsboken.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): modell-funktionerna är uppbyggda från rådata datauppsättningen från Azure Blob storage med hjälp av en serie normaltid-metod för telemetri, fel och underhåll data. Fel-relaterade komponenten ersättningar används för att skapa modelletiketter som beskriver vilka komponenten inte kunde. Märkt funktionsdata sparas i en Azure blob för modellen byggnad anteckningsboken.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): I modellen byggnad anteckningsboken använder märkt data funktionsuppsättningen och delar upp data i tåg och dev datauppsättningar längs datum-och tidsstämpel. Den bärbara datorn har konfigurerats som ett experiment med pyspark.ml.classification modeller. Av att träningsinformationen är vectorized. Användaren kan experimentera med antingen en **DecisionTreeClassifier** eller **RandomForestClassifier** att manipulera justeringsmodeller för att hitta det bästa utför modellen. Prestanda bestäms genom att utvärdera mätningsstatistik dev datauppsättningen. Statistiken loggas i Machine Learning arbetsstationen runtime skärmen för spårning. Vid varje körning sparar anteckningsboken resulterande modellen till den lokala disken med Jupyter-anteckningsbok kernel. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): anteckningsboken använder den senaste modellen som sparas på den lokala disken (Jupyter-anteckningsbok kernel) att skapa komponenter för att distribuera modellen till en Azure-webbtjänst. Fullständig operativa tillgångar komprimerad till filen o16n.zip som lagras i en annan Azure blob-behållaren. Den komprimerade filen innehåller:

* **service_schema.JSON**: definitionsfilen schemat för distributionen. 
* **pdmscore.PY**: den **init()** och **run()** funktioner som krävs av Azure-webbtjänsten.
* **pdmrfull.Model**: katalogen model definition.
    
Anteckningsboken testar funktioner med modelldefinitionen av innan du paketerar operationalization tillgångar för distribution. Instruktioner för distribution ingår i slutet av den bärbara datorn.

## <a name="conclusion"></a>Sammanfattning

Det här scenariot ger en översikt över skapar en lösning för slutpunkt till slutpunkt förutsägande Underhåll genom att använda PySpark i Jupyter-anteckningsbok-miljön i Machine Learning-arbetsstationen. Det här exemplet beskrivs också modellen distribution via Machine Learning modellen Management-miljö för att göra realtid utrustning fel förutsägelser.

## <a name="references"></a>Referenser

Följande referens ger exempel på andra förutsägande Underhåll användningsfall för olika plattformar:

* [Förutsägande Underhåll Lösningsmall](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Förutsägande Underhåll Modeling Guide med SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Förutsägande Underhåll Modeling guiden Python-anteckningsbok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Förutsägande Underhåll med hjälp av PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Djup learning för förebyggande underhåll](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Nästa steg

Andra scenarier är tillgängliga i Machine Learning-arbetsstationen som visar ytterligare funktioner i produkten. 
