---
title: Använd Azure Machine Learning i Azure Notebooks för hands version
description: En översikt över exempel antecknings böckerna för Azure Machine Learning som du kan använda med Azure Notebooks för hands version.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c0e0e9ccea079d7e8f4e35e9af2a0e1c1ec5051
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646960"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Använd Azure Machine Learning i Azure Notebooks för hands version

Azure Notebooks är förkonfigurerade med den miljö som krävs för att arbeta med [Azure Machine Learning](/azure/machine-learning/service/). Du kan enkelt klona en exempelprojektet i ditt konto för bärbara datorer att utforska en mängd olika scenarier för Machine Learning.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>Klona exemplet till ditt konto

1. Logga in på [Azure anteckningsböcker](https://notebooks.azure.com/).
1. Välj **Mina projekt** att gå till instrument panelen för projekt.
1. Välj knappen **Ladda upp GitHub lagrings platsen** (uppåtpilen) för att öppna popup-fönstret för att **Ladda upp GitHub-lagringsplatsen** .
1. I popup-fönstret anger du `Azure/MachineLearningNotebooks` **i GitHub-lagringsplatsen**, anger ett namn för projektet i **projekt namn** som "Azure Machine Learning", anger en identifierare i **projekt-ID**, rensar **offentligt** om du vill och väljer sedan **Importera**.

    ![Importera Azure Machine Learning Notebook exemplet till ditt konto för bärbara datorer](media/azureml-import-project.png)

1. Efter en minut eller två går anteckningsböcker i Azure automatiskt du till det nya projektet instrumentpanelen.

## <a name="run-a-sample-notebook"></a>Kör en exempel-anteckningsbok

1. Välj **00 - configuration.ipynb** att starta i konfigurationsavsnittet för denna notebook och anvisningar om att skapa en Azure Machine Learning-arbetsyta.

    - Eftersom Azure anteckningsböcker redan innehåller de Python-paket som behövs, kan du bara köra kodfragmentet i steg 2 av kraven för att verifiera Azure ML SDK-version.

1. När konfigurationen är klar väljer du **01. komma-igång** för att öppna mappen som innehåller 13 olika exempel antecknings böcker, var och en som är själv för klar Ande.

## <a name="next-steps"></a>Nästa steg

Azure Machine Learning-dokumentationen innehåller en mängd andra resurser som hjälper dig att arbeta med Machine Learning i antecknings böcker:

- [Snabbstart: Använda Python för att komma igång med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Självstudie #1: träna en bild klassificerings modell med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Självstudie #2: Distribuera en avbildning klassificeringsmodellen i Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Självstudie: träna en klassificerings modell med automatiserad maskin inlärning i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Även finns i dokumentationen för den [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
