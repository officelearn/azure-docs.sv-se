---
title: Bild klassificering med Azure Machine Learning (AML) paket för datorn Vision (AMLPCV) och Team datavetenskap Process (TDSP) | Microsoft Docs
description: Beskriver användningen av TDSP (Team vetenskap av data) och AMLPCV för bilden klassificering
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837226"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>SKALFORMAT Bröstcancerdiagnoser avbildningen klassificering med Azure Machine Learning (AML)-paketet för datorn Vision (AMLPCV) och Team Data vetenskap processen (TDSP)

## <a name="introduction"></a>Introduktion

Den här artikeln visar hur du använder den [Azure Machine Learning-paketet för datorn Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) för att träna, testa och distribuera en **bild klassificering** modell. Används av TDSP struktur och mallar i [Azure Machine Learning arbetsstationen](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Hela exemplet tillhandahålls i den här genomgången. Den använder [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) som djup learning framework och utbildning utförs på en [datavetenskap VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU-datorn. Azure ML Operationalization CLI används i distributionen.

Många program i datorn vision domänen kan bestämmas som bild klassificering problem. Dessa omfattar att skapa modeller att besvara frågor som ”är ett objekt som finns i avbildningen”? (objektet kunde *hund*, *bilen*, eller *levereras*) och mer komplexa frågor som ”vilken klass av öga sjukdom allvarlighetsgrad evinced av den här tålamod retinal genomsökning”? AMLPCV förenklar bilden klassificering databehandling och modellering pipeline. 

## <a name="link-to-github-repository"></a>Länka till GitHub-lagringsplatsen
Vi ger här sammanfattning dokumentationen om exemplet. Mer omfattande dokumentation finns på den [GitHub plats](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Team datavetenskap processen (TDSP) genomgången med AMLPCV

Den här genomgången använder den [Team Data vetenskap processen (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) livscykel.

Den här genomgången innehåller följande steg i livscykeln:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Data acquision](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
ISIC dataset används för aktiviteten avbildningen klassificering. ISIC (det internationella skal avbildning samarbetet) är en partership mellan akademisk och industrin för att underlätta tillämpningen av digitala skal imaging om du vill undersöka och minska melanoma livslängd. Den [ISIC Arkiv](https://isic-archive.com/#images) innehåller över 13 000 skal skada bilder med etiketter ofarlig eller maligna. Vi hämta ett exempel på bilderna från ISIC Arkiv.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modeling](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
I modellering steg utförs följande stegen. 

<b>2.1 skapa en Dataset</b><br>

Ange en rotkatalog av avbildningar för att generera ett Dataset-objekt i AMLPCV på den lokala disken. 

<b>2.2 bild anteckningar och visualisering</b><br>

Visualisera bilder i dataset-objekt och korrigera vissa av etiketterna om det behövs.

<b>2.3 avbildningen förstärkning</b><br>

Utöka en dataset-objektet med transformationer som beskrivs i den [imgaug](https://github.com/aleju/imgaug) bibliotek.

<b>2.4 DNN Model Definition</b><br>

Definiera modell-arkitektur som används i steget utbildning. Sex olika per tränats djupa neurala nätverket modeller stöds i AMLPCV: AlexNet, Resnet 18, Resnet 34 och Resnet-50, Resnet 101 och Resnet 152.

<b>2.5 klassificerare utbildning</b><br>

Träna de neurala nätverk med standard- eller anpassade parametrar.

<b>2.6 utvärdering och visualisering</b><br>

Substep innehåller funktioner som utvärderar prestanda för den tränade modellen på ett oberoende testdata. Utvärderingen mått innehåller noggrannhet, precision och återkallande och ROC-kurvan.

Dessa stegen beskrivs i detalj i motsvarande Jupyter-anteckningsboken. Vi tillhandahöll också riktlinjer för att aktivera parametrar, till exempel hastighet, mini batchstorlek och annullerad hastighet att ytterligare förbättra prestanda för modellen.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Distribution](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Det här steget operationalizes modellen genereras från modellering steg. Det inför operationalization förutsättningarna och installationsprogrammet. Slutligen förklaras också förbrukningen av webbtjänsten. Du kan lära dig att skapa djup learning-modeller med AMLPCV och operationalisera modellen i Azure via den här kursen.

## <a name="next-steps"></a>Nästa steg
Läs dokumentationen på [Azure Machine Learning-paketet för datorn Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) och [Team Data vetenskap processen (TDSP)](https://aka.ms/tdsp) att komma igång.


## <a name="references"></a>Referenser

* [Azure Machine Learning paketet för datorn Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Datavetenskap VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

