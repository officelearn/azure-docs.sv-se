---
title: Skapa och distribuera en modell för likheter av avbildning med hjälp av Azure Machine Learning-paket för visuellt innehåll.
description: Lär dig mer om att skapa, träna, testa och distribuera en avbildning likheter modellen för visuellt innehåll med hjälp av Azure Machine Learning-paketet för visuellt innehåll.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093657"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Skapa och distribuera avbildningen likheter modeller med Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


I den här artikeln lär du dig hur du använder Azure Machine Learning-paket för datorn Vision (AMLPCV) för att träna, testa och distribuera en avbildning likheter modell. En översikt över det här paketet och dess detaljerad referensdokumentation finns i https://aka.ms/aml-packages/vision.

Ett stort antal problem i datordomän för visuellt innehåll kan lösas med hjälp av avbildningen likhet. Dessa problem är att skapa modeller som svar på frågor som:
+ _Är ett objekt i bilden? Till exempel ”hund”, ”bil”, ”skicka” och så vidare_
+ _Vilken klass av öga sjukdomar allvarlighetsgrad evinced av den här patientens retinal genomsökning med?_

När du skapar och distribuerar den här modellen med AMLPCV, gå igenom följande steg:
1. Skapa en datauppsättning
2. Bild visualisering och anteckning
3. Bild tokenomvandling
4. Djupa Neurala nätverk (DNN) Service-definitionen
5. Klassificerare utbildning
6. Utvärdering och visualisering
7. Distribution av webbtjänster
8. Belastningstest för tjänsten

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) används som deep learning-ramverk utbildning utförs lokalt på en GPU-baserade dator som den ([Deep learning virtuell dator för datavetenskap](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), och distributionen använder Azure ML driftsättning CLI.

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. Följande konton och programmet måste ställas in och installerad:
   - Ett konto för Machine Learning-experimentering 
   - Ett konto i Azure Machine Learning-modellhantering
   - Azure Machine Learning Workbench installerat

   Om dessa tre är ännu inte skapats eller installerats följer den [installationen av Azure Machine Learning-Quickstart och Workbench](../desktop-workbench/quickstart-installation.md) artikeln. 

1. Azure Machine Learning-paket för visuellt innehåll måste installeras. Lär dig hur du installerar det här paketet enligt beskrivningen i https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Exempeldata och notebook

### <a name="get-the-jupyter-notebook"></a>Hämta Jupyter-anteckningsbok

Ladda ned anteckningsboken för att köra exemplet som beskrivs här själv.

> [!div class="nextstepaction"]
> [Hämta Jupyter-anteckningsbok](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Läsa in exempeldata

I följande exempel används en datauppsättning som består av 63 Bords avbildningar. Varje bild är märkta som tillhör en av fyra olika klasser (bowl, cup, bestick, lj). Antalet bilder i det här exemplet är liten så att det här exemplet kan utföras snabbt. I praktiken måste minst 100 bilder per klass anges. Alla avbildningar finns på *”... /sample_data/imgs_recycling / ”*, i underkataloger som kallas” bowl ”,” cup ”,” bestick ”och” skylt ”.

![Azure Machine Learning-datauppsättning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Machine Learning-paket för visuellt innehåll i de här artiklarna:

+ Lär dig hur du [förbättra den här modellen](how-to-improve-accuracy-for-computer-vision-models.md).

+ Läs den [paketera översikt](https://aka.ms/aml-packages/vision).

+ Utforska den [referensdokumentation](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) för det här paketet.

+ Lär dig mer om [andra Python-paket för Azure Machine Learning](reference-python-package-overview.md).
