---
title: Använda Azure Machine Learning i Azure Notebooks
description: En översikt över exempel antecknings böckerna för Azure Machine Learning som du kan använda med Azure Notebooks.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277459"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Använda Azure Machine Learning i en bärbar dator

Azure Notebooks är förkonfigurerade med den miljö som krävs för att arbeta med [Azure Machine Learning](/azure/machine-learning/service/). Du kan enkelt klona en exempelprojektet i ditt konto för bärbara datorer att utforska en mängd olika scenarier för Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Klona exemplet till ditt konto

1. Logga in på [Azure Notebooks](https://notebooks.azure.com/).
1. Välj **Mina projekt** att gå till instrument panelen för projekt.
1. Välj knappen **Ladda upp GitHub lagrings platsen** (uppåtpilen) för att öppna popup-fönstret för att **Ladda upp GitHub-lagringsplatsen** .
1. I popup-fönstret anger du `Azure/MachineLearningNotebooks` **i GitHub-lagringsplatsen**, anger ett namn för projektet i **projekt namn** som "Azure Machine Learning", anger en identifierare i **projekt-ID**, rensar **offentligt** om du vill och väljer sedan **Importera**.

    ![Importera Azure Machine Learning Notebook exemplet till ditt konto för bärbara datorer](media/azureml-import-project.png)

1. Efter en minut eller två går anteckningsböcker i Azure automatiskt du till det nya projektet instrumentpanelen.

## <a name="run-a-sample-notebook"></a>Kör en exempel-anteckningsbok

1. Välj **00-Configuration. ipynb** för att starta konfigurations avsnittet för antecknings boken och följ anvisningarna för att skapa en Azure Machine Learning-arbetsyta.

    - Eftersom Azure anteckningsböcker redan innehåller de Python-paket som behövs, kan du bara köra kodfragmentet i steg 2 av kraven för att verifiera Azure ML SDK-version.

1. När konfigurationen är klar väljer du **01. komma-igång** för att öppna mappen som innehåller 13 olika exempel antecknings böcker, var och en som är själv för klar Ande.

## <a name="next-steps"></a>Nästa steg

Azure Machine Learning-dokumentationen innehåller en mängd andra resurser som hjälper dig att arbeta med Machine Learning i antecknings böcker:

- [Snabb start: Använd python för att komma igång med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Självstudie #1: träna en bild klassificerings modell med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Självstudie #2: Distribuera en bild klassificerings modell i Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Självstudie: träna en klassificerings modell med automatiserad maskin inlärning i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Se även dokumentationen för [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
