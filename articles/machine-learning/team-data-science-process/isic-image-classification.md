---
title: Bildklassificering med Azure Machine Learning-paket för visuellt innehåll och Team Data Science Process (TDSP) | Microsoft Docs
description: Beskriver användningen av Team Data Science Process (TDSP) och Azure Machine Learning-paket för visuellt innehåll för klassificering av avbildning.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: ee2e797f3838b8b6b36174d14c73e97fe9790315
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392820"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Skalfönstrets cancer bildklassificering med Azure Machine Learning-paket för visuellt innehåll och Team Data Science Process

Den här artikeln visar hur du använder den [Azure Machine Learning-paket för visuellt innehåll](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) för att träna, testa och distribuera en *bildklassificering* modell. I exemplet används Team Data Science Process (TDSP) struktur och mallar i [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation). Den här genomgången innehåller hela exemplet. Den använder den [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) deep learning-ramverk och utbildning utförs på en [Data Science VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU-datorn. Azure Machine Learning operationalization CLI används i distributionen.

Många program i datordomän för visuellt innehåll kan vara framed som bild klassificering problem. Dessa omfattar att skapa modeller att besvara enkla frågor som ”är ett objekt som finns i bild”? objektet kanske där en hund, bil eller leverera. Den har även svar på mer komplexa frågor som ”vilka klass av öga sjukdomar allvarlighetsgrad evinced av den här patientens retinal genomsökning”? Azure Machine Learning-paket för visuellt innehåll strömlinjeformar bildbehandling klassificering data och modellering pipelinen. 

## <a name="link-to-the-github-repository"></a>Länka till GitHub-lagringsplatsen
Den här artikeln är en sammanfattning av dokument om exemplet. Du hittar mer omfattande dokumentation på den [GitHub-webbplatsen](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Genomgång för team Data Science Process

Den här genomgången använder den [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) livscykel. Den här genomgången omfattar följande steg i livscykeln.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Datainsamling](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Datauppsättningen International Kantlinjeskal Imaging samarbete (ISIC) används för aktiviteten bild klassificering. ISIC är ett partnerskap mellan den akademiska och företag att underlätta tillämpningen av digitala kantlinjeskal imaging om du vill undersöka och minska melanoma livslängd. Den [ISIC Arkiv](https://isic-archive.com/#images) innehåller mer än 13 000 kantlinjeskal skada avbildningar som är märkta som antingen ofarliga eller maligna. Hämta ett exempel på avbildningarna från ISIC arkivet.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modellering](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
I steget modellering utförs följande stegen.

#### <a name="dataset-creation"></a>Skapa en datauppsättning

Generera en dataset-objekt i Azure Machine Learning-paket för visuellt innehåll genom att tillhandahålla en rotkatalog av avbildningar på den lokala disken. 

#### <a name="image-visualization-and-annotation"></a>Bild visualisering och anteckning

Visualisera avbildningarna i datamängdsobjektet och korrigera etiketterna efter behov.

#### <a name="image-augmentation"></a>Bild tokenomvandling

Förbättra en dataset-objekt med hjälp av omvandlingarna som beskrivs i den [imgaug](https://github.com/aleju/imgaug) biblioteket.

#### <a name="dnn-model-definition"></a>DNN model definition

Definiera modellen arkitektur som används i utbildnings-steget. Sex förtränade djupa neurala nätverk modeller som stöds i Azure Machine Learning-paket för visuellt innehåll: AlexNet, Resnet-18, Resnet-34, Resnet-50, Resnet-101 och Resnet-152.

#### <a name="classifier-training"></a>Klassificerare utbildning

Träna de neurala nätverk med standard- eller anpassade parametrar.

#### <a name="evaluation-and-visualization"></a>Utvärdering och visualisering

Tillhandahåller funktioner för att utvärdera prestanda hos den tränade modellen på ett oberoende testdata. Utvärderingsmått omfattar noggrannhet, precision, återkallande och ROC-kurvan.

I stegen beskrivs i detalj i motsvarande Jupyter-anteckningsboken. Anteckningsboken innehåller också riktlinjer för att aktivera parametrar, till exempel den inlärningsfrekvensen, mini batchstorlek och annullerad snabbt att ytterligare förbättra modellprestanda.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Distribution](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Det här steget operationalizes modellen som skapas från steg modellering. Den beskriver kraven och de nödvändiga inställningarna. Förbrukningen av webbtjänsten förklaras också. I den här självstudien får du lära dig att skapa modeller för djup maskininlärning med Azure Machine Learning-paket för visuellt innehåll och operationalisera modellen i Azure.

## <a name="next-steps"></a>Nästa steg
- Läs mer dokumentation om [Azure Machine Learning-paket för visuellt innehåll](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Läs den [Team Data Science Process](https://aka.ms/tdsp) dokumentationen för att komma igång.


## <a name="references"></a>Referenser

* [Azure Machine Learning-paket för visuellt innehåll](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation)
* [Virtuell dator för datavetenskap](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

