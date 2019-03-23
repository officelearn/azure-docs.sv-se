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
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8bb601ac5c1ed8e9ca6d2027fe1b774a938e1f0a
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362413"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använd din egen notebook-server för att komma igång med Azure Machine Learning

I den här artikeln får du använda din egen notebook-server för att köra kod som loggas i den [Azure Machine Learning-tjänstens arbetsyta](concept-azure-machine-learning-architecture.md). Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning.

Den här snabbstarten använder egna Python-miljön och Jupyter Notebook-Server. En Snabbstart där inte har installerats finns [Snabbstart: Använd en molnbaserad notebook-server för att komma igång med Azure Machine Learning](quickstart-run-cloud-notebook.md) 

Visa en videoversion av den här snabbstarten:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

I den här snabbstarten kommer du att göra följande:

* Skriva kod som loggar värden i arbetsytan.
* Visa de loggade värdena på din arbetsyta.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisites"></a>Förutsättningar

* Python 3.6 notebook-server med Azure Machine Learning SDK installerad
* En arbetsyta för Azure Machine Learning-tjänsten
* En konfigurationsfil för arbetsytan (**aml_config/config.json** ).

Hämta alla dessa krav från [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Använda arbetsytan

Skapa ett skript eller starta en anteckningsbok i samma katalog som arbetsytan konfigurationsfilen. Kör den här koden som använder basic-API: er av SDK för att spåra experimentkörningar.

1. Skapa ett experiment på arbetsytan.
1. Logga ett värde i experimentet.
1. Logga en lista över värden i experimentet.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Visa loggade resultat

När körningen är klar kan du visa experimentkörningen på Azure-portalen. Om du vill skriva ut en URL som navigerar till resultatet för den senaste körningen använder du följande kod:

```python
print(run.get_portal_url())
```

Använd länken för att visa de loggade värdena på Azure-portalen i webbläsaren.

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
