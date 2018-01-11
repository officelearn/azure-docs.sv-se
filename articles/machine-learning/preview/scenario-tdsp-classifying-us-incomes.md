---
title: "Intäkter klassificering - teamet datavetenskap Process - Azure Machine Learning | Microsoft Docs"
description: "Hur du använder mallen Team datavetenskap processen för att skapa ett projekt i Azure Machine Learning som klassificerar USA inkomster."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: dc6279a1bac230146f4f0cebf2fbdbb6333cb7a1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Intäkter klassificering med Team Data vetenskap processen (TDSP)-projekt

## <a name="introduction"></a>Introduktion

Standardisering av strukturen och dokumentation av datavetenskap projekt som är förankrad vid en upprättad [datavetenskap livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), för att underlätta effektivt samarbete i datavetenskap team. Skapa Azure Machine Learning-projekt med den [Team Data vetenskap processen (TDSP)](https://github.com/Azure/Microsoft-TDSP) mallen innehåller ett ramverk för sådana standardisering.

Vi hade tidigare ut en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Men det var inte möjligt fram till nu för att skapa en instans TDSP struktur och mallar i ett datavetenskap verktyg. Vi har nu aktiverats skapandet av Azure Machine Learning-projekt som instansieras med [TDSP struktur och dokumentation mallar för Azure Machine Learning](https://github.com/amlsamples/tdsp). Anvisningar om hur du använder TDSP struktur och mallar i Azure Machine Learning tillhandahålls [här](https://aka.ms/how-to-use-tdsp-in-aml). Här kan vi ge ett exempel på hur ett faktiska machine learning-projekt kan skapas TDSP struktur med, fylls i med projektspecifika kod, artefakter och dokument, och körs i Azure Machine Learning.

## <a name="link-to-github-repository"></a>Länka till GitHub-lagringsplatsen
Vi tillhandahåller sammanfattning dokumentationen [här](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) om exemplet. Mer omfattande dokumentation finns på webbplatsen GitHub.

### <a name="purpose"></a>Syfte
Det huvudsakliga syftet med det här exemplet är att visa hur du initiera och kör machine learning-projekt med den [Team Data vetenskap processen (TDSP)](https://github.com/Azure/Microsoft-TDSP) struktur och mallar i Azure Machine Learning. För detta ändamål vi använda den välkända [1994 oss inventering data från databasen UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/adult). Aktiviteten modellering är att förutsäga USA årliga intäkter klasser från oss inventering information (till exempel ålder, RAS, utbildningsnivå, land ursprung, etc.)

### <a name="scope"></a>Omfång
 * Datagranskning, utbildning och distribution av en maskininlärningsmodell som göra förutsägelser problemet som beskrivs i fallet översikt för användning. 
 * Körningen av projektet i Azure Machine Learning med mallen Team Data vetenskap processen (TDSP) från Azure Machine Learning för det här projektet. För körning av projektet och rapportering, är det dags att använda TDSP livscykel.
 * Operationalization lösning direkt från Azure Machine Learning i Azure Container Services.

 Projektet visar flera funktioner i Azure Machine Learning sådana TDSP struktur instansiering och Använd, körning av koden i Jupyter-anteckningsböcker samt Python filer och enkelt operationalization i Azure Container Services med Docker och Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Livscykel för team Data vetenskap processen (TDSP)
Se [Team datavetenskap processen (TDSP) livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Förutsättningar
### <a name="required-subscription-hardware-software"></a>Obligatoriskt: prenumeration, maskinvara, programvara
1. En Azure [prenumeration](https://azure.microsoft.com). Du kan få en [kostnadsfri prenumeration](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) att köra det här exemplet också.
2. En [Azure Data vetenskap virtuell dator (DSVM) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (VM-storlek: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), med 4 virtuella processorer och 14 Gb RAM-minne). Även om testats på en Azure-DSVM kommer att fungera på en Windows 10-dator.
3. Läs dokumentationen om Azure Machine Learning och dess relaterade tjänster (se nedan för länkar).
4. Kontrollera att du har korrekt installerat Azure Machine Learning av den [installation snabbstartsguiden](quickstart-installation.md).

Datamängden för det här exemplet är från UCI ML databasen [[länk]](https://archive.ics.uci.edu/ml/datasets/adult). Den hämtas från databasen 1994 oss inventering och innehåller information om inventering och intäkter för ungefär 50 000 personer. Detta är strukturerade dataset med numeriska och kategoriska funktioner och ett kategoriska mål som består av två intäkter kategorier ('> 50 K' eller ' < = 50 K ”). 

### <a name="optional-version-control-repository"></a>Valfritt: Version källkontrollen
Om du vill spara och version projektet och dess innehåll, du måste ha en version för källkontrollen där detta kan göras. När du skapar det nya projektet använder TDSP mallen i Azure Machine Learning kan du ange platsen för Git-lagringsplatsen. Se [använda Git i Azure Machine Learning](using-git-ml-project.md) för mer information.

### <a name="informational-about-azure-machine-learning"></a>Information: Om Azure Machine Learning
* [Vanliga frågor och svar - komma igång](frequently-asked-questions.md)
* [Översikt](overview-what-is-azure-ml.md)
* [Installation](quickstart-installation.md)
* [Körning](experimentation-service-configuration.md)
* [Med hjälp av TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Läsa och skriva filer](how-to-read-write-files.md)
* [Använda Git med Azure Machine Learning](using-git-ml-project.md)
* [Distribuera en ML-modell som en webbtjänst](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den  **+**  och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”klassificera USA inkomster - TDSP projekt” och välj mallen
5.  Klicka på **Skapa**

Om du anger ett tomt Git-databasplats under Skapa projekt (i motsvarande ruta) sedan fylls den databasen med projektstruktur och innehåll efter att projektet har skapats.

## <a name="use-case-overview"></a>Använd case-översikt
Problemet är att förstå hur sociala ekonomiska data som hämtats i oss inventering kan hjälpa till att förutse årliga från personer i USA. Baserat på dessa funktioner för inventering, machine learning uppgiften är att förutsäga om en enskild intäkter är högre än 50 000 eller inte (binär klassificering aktivitet).

## <a name="data-description"></a>Beskrivning av data
Detaljerad information om data som finns på [beskrivning](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) UCI på lagringsplatsen. 

Dessa data har extraherats från inventering Bureau databasen finns på: https://www.census.gov/en.html. 


* Det finns totalt 48,842 instanser (innan någon filtrering), blanda kontinuerliga och diskreta (träna = 32, 561, testa = 16, 281)
* Sannolikheten för etiketten ' > 50 K': 23.93% / 24.78% (utan obekanta faktorer)
* Sannolikheten för etiketten ' < = 50 K': 76.07% / 75.22% (utan obekanta faktorer)  

* **MÅLET**: intäkter klassen-> 50 K', ' < = 50 K'. Dessa ersätts med 1 och 0 i data förberedelsefasen.
* **FUNKTIONER**: ålder, arbete klass, utbildningsnivå, utbildningsnivå, RAS, kön, arbetstimmar per vecka, osv.


## <a name="project-structure-execution-and-reporting"></a>Projektstruktur, köra och rapportering

### <a name="structure"></a>struktur
För det här projektet vi använder TDSP mappen struktur och dokumentation mallar (nedan) som följer den [TDSP livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projektet har skapats utifrån anvisningarna [här](https://aka.ms/how-to-use-tdsp-in-aml). När den är fylld med projektets koden och artefakter strukturen ser ut som följer (se projektstruktur omsluta i rött i figuren nedan).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Körning
I det här exemplet vi köra kod i **lokala beräkning miljö**. Hänvisa till Azure Machine Learning-dokument för mer information om [körning alternativ](experimentation-service-configuration.md).

Det är lätt att köra en Python-skriptet i en lokal Python-körning:

    az ml experiment submit -c local my_script.py

IPython filerna kan användaren dubbelklickar på projektet struktur till vänster i Azure Machine Learning-Användargränssnittet och köras i Jypyter anteckningsboken Server.


Stegvisa datavetenskap arbetsflödet var enligt följande:

* [**Datainsamling och förstå**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Data som hämtades i CSV-format från URL: er i UCI ML lagret [[länk]](https://archive.ics.uci.edu/ml/datasets/adult). Funktioner, mål och deras omvandlingar beskrivs i detalj i filen ProjectReport.md.

Koden för datainsamling och förstå finns på: 01_data_acquisition_and_understanding-kod.

Datagranskning utförs med Python-3 [IDEAR (Interaktiv datautforskning och rapportering) verktyget](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) publiceras som en del av [TDSP suite vetenskap verktyg för](https://github.com/Azure/Azure-TDSP-Utilities). Det här verktyget hjälper till att generera standardiserade utforskning rapporter för data som innehåller numeriska och kategoriska funktioner och målet. Information om hur verktyget Python 3 IDEAR användes finns nedan. 

Den slutliga data utforskning rapporten finns [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). En vy i rapporten IDEAR visas nedan:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modeling**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Vi har skapat två modeller med 3-fold korsvalidering: elastiska Net och slumpmässiga skog. Vi använde [59 punkt provtagning](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) för slumpmässiga rutnätet sökning som en strategi för korsvalidering och modellen parametern optimering. Riktighet modeller har mäts med AUC (området under kurvan) på datamängden som test. 

För modellering finns på: 02_modeling-kod.

AUC elastisk Net och slumpmässiga skog modeller har > 0.85. Vi sparar båda modellerna i pickled.pkl filer och utgående ROC ritas för båda modellerna. AUC av slumpmässiga Skogsmodell var 0.92 och av elastisk Net modellen 0,90. Dessutom för modellen tolkning funktionen vikten för slumpmässiga Skogsmodell är utdata i en CSV-fil och visas i en PDF-fil (de 20 största förutsägande funktioner).

ROC-kurvan för **slumpmässiga Skogsmodell** på testdatorn data visas nedan. Det var den modell som har distribuerats:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Funktionen vikten (överst 20) av slumpmässiga Skogsmodell visas nedan. Den visar funktioner kapital vinst belopp, eduction, civilstånd, har högst prioritet för funktionen.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Distribution**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Distribuerade vi slumpmässiga Skogsmodell som en webbtjänst på ett kluster i den [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Operationalization miljö bestämmelserna Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om operationalization [här](model-management-service-deploy.md). 

Kod för distribution finns i: / kod/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Sista projektrapport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Information om de olika avsnitten ovan finns i rapporten kompileras slutliga projekt [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Rapporten projektet innehåller även ytterligare information om användningsfall, modell prestandamått, distribution och infrastruktur som projektet utvecklas och distribueras.

Projektet rapporten tillsammans med innehållet i hela projektmappen och version för källkontrollen levereras till klienten.


## <a name="conclusion"></a>Sammanfattning

I det här exemplet visade nu för att använda TDSP struktur och mallar i Azure Machine Learning. Via de dokument och artefakt mallarna kan du:
1. Definiera korrekt syfte och omfånget för ett projekt
2. Skapa en projektgrupp distribuerade roller och ansvarsområden
3. Struktur och köra projekt enligt livscykelstadier för TDSP
4. Utveckla standardiserade rapporter med TDSP datavetenskap verktyg (till exempel IDEAR data från kartläggning av naturresurser och visualisering rapporten).
5. Förbereda en slutlig datavetenskap projektrapport som kan skickas till en klient

Vi hoppas att du använder den här funktionen i Azure Machine Learning för att underlätta med standardisering och samarbete inom dina datavetenskap team.

## <a name="next-steps"></a>Nästa steg

Se referenser nedan för att komma igång:

[Hur du använder Team Data vetenskap processen (TDSP) i Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Team datavetenskap Process (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[TDSP projektmall för Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[USA intäkter datauppsättning från UCI ML-databas](https://archive.ics.uci.edu/ml/datasets/adult)