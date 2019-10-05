---
title: Använda Azure Machine Learning-tjänster i Azure-anteckningsböcker
description: En översikt över exempelanteckningsböcker för Azure Machine Learning-tjänster som du kan använda med Azure-datorer.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973019"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Använda Azure Machine Learning tjänst i en bärbar dator

Azure Notebooks är förkonfigurerade med nödvändig miljö för att fungera med [Azure Machine learnings tjänsten](/azure/machine-learning/service/). Du kan enkelt klona en exempelprojektet i ditt konto för bärbara datorer att utforska en mängd olika scenarier för Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Klona exemplet till ditt konto

1. Logga in på [Azure anteckningsböcker](https://notebooks.azure.com/).
1. Välj **Mina projekt** att gå till instrument panelen för projekt.
1. Välj knappen **Ladda upp GitHub lagrings platsen** (uppåtpilen) för att öppna popup-fönstret för att **Ladda upp GitHub-lagringsplatsen** .
1. I popup-fönstret anger du `Azure/MachineLearningNotebooks` i **GitHub-lagringsplatsen**, anger ett namn för projektet i **projekt namn** som "Azure Machine Learning tjänst", anger en identifierare i **projekt-ID**, rensar **offentligt** om du vill och väljer sedan **Importera** .

    ![Importera Azure Machine Learning Notebook exemplet till ditt konto för bärbara datorer](media/azureml-import-project.png)

1. Efter en minut eller två går anteckningsböcker i Azure automatiskt du till det nya projektet instrumentpanelen.

## <a name="run-a-sample-notebook"></a>Kör en exempel-anteckningsbok

1. Välj **00 - configuration.ipynb** att starta i konfigurationsavsnittet för denna notebook och anvisningar om att skapa en Azure Machine Learning-arbetsyta.

    - Eftersom Azure anteckningsböcker redan innehåller de Python-paket som behövs, kan du bara köra kodfragmentet i steg 2 av kraven för att verifiera Azure ML SDK-version.

1. När konfigurationen är klar väljer du **01. komma-igång** för att öppna mappen som innehåller 13 olika exempel antecknings böcker, var och en som är själv för klar Ande.

## <a name="next-steps"></a>Nästa steg

Dokumentation för Azure Machine Learning Services innehåller en mängd andra resurser som hjälper dig att arbeta med Machine Learning-tjänsten i anteckningsböcker:

- [Snabbstart: Använd python för att komma igång med Azure Machine Learning @ no__t-0
- [Självstudie nr 1: Träna en bild klassificerings modell med Azure Machine Learning-tjänsten @ no__t-0
- [Självstudie 2: Distribuera en bild klassificerings modell i Azure Container Instance (ACI) ](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Självstudier: Träna en klassificerings modell med automatisk maskin inlärning i Azure Machine Learning-tjänsten @ no__t-0

Även finns i dokumentationen för den [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
