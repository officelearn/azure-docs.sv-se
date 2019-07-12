---
title: Snabbstart som kör en bärbar dator på din egen notebook-server
titleSuffix: Azure Machine Learning service
description: Kom igång med Azure Machine Learning-tjänsten. Använd din egen lokala notebook-server för att testa din arbetsyta.  Din arbetsyta är det grundläggande blocket i molnet som används för att experimentera, träna och distribuera machine learning-modeller.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 07/08/2019
ms.custom: seodec18
ms.openlocfilehash: 406797203a99df7e805e08ee7589148599eeffce
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670725"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använd din egen notebook-server för att komma igång med Azure Machine Learning

Använd egna Python-miljön och Jupyter Notebook-Server för att komma igång med Azure Machine Learning-tjänsten.  En Snabbstart med någon SDK-installation finns i [snabbstarten: Använd en molnbaserad notebook-server för att komma igång med Azure Machine Learning](quickstart-run-cloud-notebook.md).

Den här snabbstarten visar hur du kan använda den [Azure Machine Learning-tjänstens arbetsyta](concept-azure-machine-learning-architecture.md) att hålla reda på exempelexperiment för maskininlärning. Du ska köra Python-kod som loggar in värden på arbetsytan.

Visa en videoversion av den här snabbstarten:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2G9N6]

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisites"></a>Förutsättningar

* Python 3.6 notebook-server med Azure Machine Learning SDK installerad
* En arbetsyta för Azure Machine Learning-tjänsten
* En konfigurationsfil för arbetsytan ( **.azureml/config.json**).

Hämta alla dessa krav från [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md#sdk).



## <a name="use-the-workspace"></a>Använda arbetsytan

Skapa ett skript eller starta en anteckningsbok i samma katalog som arbetsytan konfigurationsfilen ( **.azureml/config.json**).

### <a name="attach-to-workspace"></a>Ansluta till arbetsytan

Den här koden läser information från konfigurationsfilen för att ansluta till din arbetsyta.

```
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="log-values"></a>Log-värden

Kör den här koden som använder basic-API: er av SDK för att spåra experimentkörningar.

1. Skapa ett experiment på arbetsytan.
1. Logga ett värde i experimentet.
1. Logga en lista över värden i experimentet.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Visa loggade resultat

När körningen är klar kan du visa experimentkörningen på Azure-portalen. Om du vill skriva ut en URL som navigerar till resultatet för den senaste körningen använder du följande kod:

```python
print(run.get_portal_url())
```

Den här koden returnerar en länk som du kan använda för att visa de loggade värdena i Azure portal i webbläsaren.

![Loggade värden i Azure Portal](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Rensa resurser 

>[!IMPORTANT]
>Du kan använda resurserna som du har skapat här som förhandskrav för andra självstudier och instruktionsartiklar om Machine Learning.

Om du inte planerar att använda de resurser som du skapade i den här artikeln kan du ta bort dem så att du undviker eventuella kostnader.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att skapa de resurser som du behöver för att experimentera med och distribuera modeller. Du har kört kod i en notebook och du har utforskat körningshistoriken eller koden på arbetsytan i molnet.

> [!div class="nextstepaction"]
> [Självstudier: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)

Du kan även utforska [mer avancerade exempel på GitHub](https://aka.ms/aml-notebooks) eller visa den [SDK användarhandboken](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
