---
title: Använda Azure Machine Learning i Azure Notebooks
description: En översikt över exempel antecknings böckerna för Azure Machine Learning som du kan använda med Azure Notebooks.
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
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496642"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Använda Azure Machine Learning i en bärbar dator

Azure Notebooks är förkonfigurerade med den miljö som krävs för att arbeta med [Azure Machine Learning](/azure/machine-learning/service/). Du kan enkelt klona ett exempel projekt till ditt antecknings boks konto för att utforska en rad olika Machine Learning scenarier.

## <a name="clone-the-sample-into-your-account"></a>Klona exemplet i ditt konto

1. Logga in på [Azure Notebooks](https://notebooks.azure.com/).
1. Välj **Mina projekt** att gå till instrument panelen för projekt.
1. Välj knappen **Ladda upp GitHub lagrings platsen** (uppåtpilen) för att öppna popup-fönstret för att **Ladda upp GitHub-lagringsplatsen** .
1. I popup-fönstret anger du `Azure/MachineLearningNotebooks` **i GitHub-lagringsplatsen**, anger ett namn för projektet i **projekt namn** som "Azure Machine Learning", anger en identifierare i **projekt-ID**, rensar **offentligt** om du vill och väljer sedan **Importera**.

    ![Importera Azure Machine Learning Notebook-exempel till ditt antecknings konto](media/azureml-import-project.png)

1. Efter en minut eller två tar Azure Notebooks automatiskt till det nya projektets instrument panel.

## <a name="run-a-sample-notebook"></a>Köra en exempel-anteckningsbok

1. Välj **00-Configuration. ipynb** för att starta konfigurations avsnittet för antecknings boken och följ anvisningarna för att skapa en Azure Machine Learning-arbetsyta.

    - Eftersom Azure Notebooks redan innehåller de nödvändiga python-paketen kan du bara köra kodfragmentet i steg 2 i kraven för att kontrol lera Azure ML SDK-versionen.

1. När konfigurationen är klar väljer du **01. komma-igång** för att öppna mappen som innehåller 13 olika exempel antecknings böcker, var och en som är själv för klar Ande.

## <a name="next-steps"></a>Nästa steg

Azure Machine Learning-dokumentationen innehåller en mängd andra resurser som hjälper dig att arbeta med Machine Learning i antecknings böcker:

- [Snabb start: Använd python för att komma igång med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Självstudie #1: träna en bild klassificerings modell med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Självstudie #2: Distribuera en bild klassificerings modell i Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Självstudie: träna en klassificerings modell med automatiserad maskin inlärning i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Se även dokumentationen för [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
