---
title: Bild klassificering med Azure Machine Learning-paketet för datorn vision och Team Data vetenskap processen (TDSP) | Microsoft Docs
description: Beskriver användningen av Team Data vetenskap processen (TDSP) och Azure Machine Learning-paketet för datorn vision för klassificering av avbildningen.
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
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300816"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Skalfönstrets bröstcancerdiagnoser avbildningen klassificering med Azure Machine Learning-paketet för datorn vision och Team datavetenskap Process

Den här artikeln visar hur du använder den [Azure Machine Learning-paketet för datorn vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) för att träna, testa och distribuera en *bild klassificering* modell. Används av Team Data vetenskap processen (TDSP) struktur och mallar i [Azure Machine Learning arbetsstationen](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Den här genomgången innehåller hela exemplet. Den använder den [Microsoft kognitiva Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) som djup learning framework och utbildning utförs på en [datavetenskap virtuella](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU-datorn. Azure Machine Learning operationalization CLI används i distributionen.

Många program i datorn vision domänen kan bestämmas som bild klassificering problem. Dessa omfattar att skapa modeller att besvara enkla frågor som ”är ett objekt som finns i avbildningen”? objektet kanske där hund, car och leverera. Även svar på mer komplexa frågor som ”vilken klass av öga sjukdom allvarlighetsgrad evinced av den här tålamod retinal genomsökning”? Azure Machine Learning-paketet för datorn vision förenklar bilden klassificering databehandling och modellering pipeline. 

## <a name="link-to-the-github-repository"></a>Länka till GitHub-lagringsplatsen
Den här artikeln är en sammanfattande dokument om exemplet. Du hittar mer omfattande dokumentation på den [GitHub plats](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Team datavetenskap Process genomgång

Den här genomgången använder den [Team datavetenskap Process](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) livscykel. Den här genomgången innehåller följande steg i livscykeln.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Datainsamling](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Dataset internationella skal Imaging samarbete (ISIC) används för aktiviteten avbildningen klassificering. ISIC är en koppling mellan akademisk och industrin för att underlätta tillämpningen av digitala skal imaging om du vill undersöka och minska melanoma livslängd. Den [ISIC Arkiv](https://isic-archive.com/#images) innehåller mer än 13 000 skal skada avbildningar som är märkta som antingen ofarlig eller maligna. Hämta ett exempel på bilderna från ISIC arkivet.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modeling](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
I steget modellering utförs följande stegen.

#### <a name="dataset-creation"></a>Skapa en datauppsättning

Generera ett dataset-objekt i Azure Machine Learning-paketet för datorn vision genom att tillhandahålla en rotkatalog bilder på den lokala disken. 

#### <a name="image-visualization-and-annotation"></a>Anteckningar och bild visualiseringen

Visualisera bilder i dataset-objekt och korrigera etiketterna efter behov.

#### <a name="image-augmentation"></a>Bild förstärkning

Förbättra ett dataset-objekt med hjälp av omvandlingarna som beskrivs i den [imgaug](https://github.com/aleju/imgaug) bibliotek.

#### <a name="dnn-model-definition"></a>DNN model definition

Definiera modellen arkitekturen som används i steget utbildning. Sex före utbildade djupa neurala nätverket modeller stöds i Azure Machine Learning-paketet för datorn vision: AlexNet, Resnet 18, Resnet 34, Resnet-50, Resnet 101 och Resnet 152.

#### <a name="classifier-training"></a>Klassificerare utbildning

Träna de neurala nätverk med standard- eller anpassade parametrar.

#### <a name="evaluation-and-visualization"></a>Utvärdering och visualisering

Innehåller funktioner för att utvärdera den tränade modellen på ett oberoende testdata. Utvärderingen mått innehåller noggrannhet, precision, återkallning och ROC-kurvan.

I stegen beskrivs i detalj i motsvarande Jupyter-anteckningsboken. Den bärbara datorn även innehåller riktlinjer för att aktivera parametrar, till exempel inlärningsfrekvensen, mini batchstorlek och annullerad hastighet att ytterligare förbättra prestanda för modellen.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Distribution](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Det här steget operationalizes modellen som skapas från modellering steg. Det inför kraven och de nödvändiga inställningarna. Användningen av webbtjänsten förklaras också. I kursen får du lära dig att skapa djup learning-modeller med Azure Machine Learning-paketet för datorn vision och operationalisera modellen i Azure.

## <a name="next-steps"></a>Nästa steg
- Läs ytterligare dokumentation om [Azure Machine Learning-paketet för datorn vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Läs den [Team datavetenskap Process](https://aka.ms/tdsp) dokumentation för att komma igång.


## <a name="references"></a>Referenser

* [Azure Machine Learning-paketet för datorn vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Datavetenskap virtuell dator](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

