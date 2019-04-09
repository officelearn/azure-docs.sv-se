---
title: Använda Azure Machine Learning-tjänster i Azure-anteckningsböcker
description: En översikt över exempelanteckningsböcker för Azure Machine Learning-tjänster som du kan använda med Azure-datorer.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2ef327721fd42e5274381834721fd987ec7e9d75
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263289"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Använda Azure Machine Learning-tjänsten på en bärbar dator

Azure-datorer är förkonfigurerad med nödvändiga miljö att arbeta med [Azure Machine Learning-tjänsten](/azure/machine-learning/service/). Du kan enkelt klona en exempelprojektet i ditt konto för bärbara datorer att utforska en mängd olika scenarier för Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Klona exemplet till ditt konto

1. Logga in på [Azure anteckningsböcker](https://notebooks.azure.com/).
1. Välj **Mina projekt** att gå till instrumentpanelen för projekt.
1. Välj den **överför GitHub-lagringsplatsen** (på uppåt-pilen) knappen Öppna den **överför GitHub-lagringsplatsen** popup-fönstret.
1. I popup-fönstret anger du `Azure/MachineLearningNotebooks` i **GitHub-lagringsplatsen**, ange ett namn för projektet i **projektnamn** som ”Azure Machine Learning-tjänsten” ange en identifierare i **projekt-ID** avmarkerar **offentliga** om du vill välja **Import**.

    ![Importera Azure Machine Learning Notebook exemplet till ditt konto för bärbara datorer](media/azureml-import-project.png)

1. Efter en minut eller två går anteckningsböcker i Azure automatiskt du till det nya projektet instrumentpanelen.

## <a name="run-a-sample-notebook"></a>Kör en exempel-anteckningsbok

1. Välj **00 - configuration.ipynb** att starta i konfigurationsavsnittet för denna notebook och anvisningar om att skapa en Azure Machine Learning-arbetsyta.

    - Eftersom Azure anteckningsböcker redan innehåller de Python-paket som behövs, kan du bara köra kodfragmentet i steg 2 av kraven för att verifiera Azure ML SDK-version.

1. När konfigurationen är klar, Välj **01.getting igång** för att navigera till mappen som innehåller tretton olika exempelanteckningsböcker som är självförklarande.

## <a name="next-steps"></a>Nästa steg

Dokumentation för Azure Machine Learning Services innehåller en mängd andra resurser som hjälper dig att arbeta med Machine Learning-tjänsten i anteckningsböcker:

- [Snabbstart: Använda Python för att komma igång med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Självstudie #1: Träna en modell för bildklassificering med Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Självstudie #2: Distribuera en avbildning klassificeringsmodellen i Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Självstudier: Träna en modell för klassificering med automatiserade machine learning i Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Även finns i dokumentationen för den [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
