---
title: 'Snabbstart: Använda Python SDK för att skapa en arbetsyta för Machine Learning-tjänsten – Azure Machine Learning'
description: Kom igång med Azure Machine Learning.  Installera Python SDK och använd den för att skapa en arbetsyta. Den här arbetsytan är själva grunden i molnet för att experimentera, träna och distribuera maskininlärningsmodeller med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067776"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använda Python för att komma igång med Azure Machine Learning

I den här snabbstarten använder du Azure Machine Learning SDK för Python för att skapa och sedan använda en [arbetsyta](concept-azure-machine-learning-architecture.md) för Machine Learning-tjänsten. Den här arbetsytan är själva grunden för att experimentera, träna och distribuera maskininlärningsmodeller i molnet med Azure Machine Learning-tjänsten.

I den här självstudien kommer du att installera Python SDK och göra följande:
* Skapa en arbetsyta i din Azure-prenumeration
* Skapa en konfigurationsfil för den arbetsytan för senare användning i andra notebooks och skript
* Skriva kod som loggar värden i arbetsytan
* Visa de loggade värdena på din arbetsyta

Arbetsytan och dess konfigurationsfil som du skapar i den här snabbstarten kan användas som nödvändiga komponenter till andra självstudier och instruktionsartiklar om Azure Machine Learning. Precis som med andra Azure-tjänster finns det begränsningar och kvoter som är associerade med Azure Machine Learning-tjänsten. [Läs mer om kvoter och hur du begär mer.](how-to-manage-quotas.md)

För att underlätta för dig läggs följande Azure-resurser till automatiskt till din arbetsyta om de är tillgängliga i din region: [containerregister](https://azure.microsoft.com/services/container-registry/), [lagring](https://azure.microsoft.com/services/storage/), [programinsikter](https://azure.microsoft.com/services/application-insights/) och [nyckelvalv](https://azure.microsoft.com/services/key-vault/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


##  <a name="install-the-sdk"></a>Installera SDK:n

**Hoppa över det här avsnittet om du använder** en Data Science Virtual Machine (DSVM) som skapats efter den 27 september 2018 eftersom dessa DSVM levereras med Python SDK förinstallerat.

Innan du installerar SDK rekommenderar vi att du först skapar en isolerad Python-miljö. Den här snabbstarten använder [Miniconda](https://conda.io/docs/user-guide/install/index.html), men du kan även använda fullständiga [Anaconda](https://www.anaconda.com/) installerat eller [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installera Miniconda


[Ladda ned](https://conda.io/miniconda.html) och installera Miniconda. Välj Python 3.7 eller en senare version. Välj inte Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Skapa en isolerad Python-miljö 

Starta ett kommandoradsfönster och skapa en ny conda-miljö med namnet `myenv` med Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Aktivera miljön.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Installera SDK:n

I den aktiverade conda-miljön installerar du SDK:n. Den här koden installerar kärnkomponenterna i Azure Machine Learning SDK samt en Jupyter Notebook-server i conda-miljön `myenv`.  Installationen tar **cirka 4 minuter** att slutföra.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Starta Jupyter Notebook genom att skriva det här kommandot.
```sh
jupyter notebook
```

I webbläsarfönstret skapar du en ny notebook med hjälp av `Python 3`-standardkernel. 

Visa SDK-versionen genom att skriva följande Python-kod i en notebook-cell och köra den.

```python
import azureml.core
print(azureml.core.VERSION)
```

Skapa en ny Azure-resursgrupp och en ny arbetsyta.

Hitta ett värde för `<azure-subscription-id>` i [prenumerationslistan i Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Använda valfri prenumeration där din roll är ägare eller deltagare.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region
                     )
```

När föregående kod körs kan den utlösa ett nytt webbläsarfönster där du loggar in på ditt Azure-konto. När du har loggat in cachelagras autentiseringstoken lokalt.

Du kan visa informationen om arbetsytan såsom associerad lagring, containerregister och nyckelvalv genom att skriva:

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Skriva en konfigurationsfil

Spara informationen om arbetsytan i en konfigurationsfil i den aktuella katalogen. Den här filen kallas ”aml_config\config.json”.  

Den här konfigurationsfilen för arbetsytan gör det enkelt att läsa in samma arbetsyta senare med andra notebooks och skript i samma katalog eller en underkatalog. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

API-anropet `write_config()` skapar konfigurationsfilen i den aktuella katalogen. Filen `config.json` innehåller följande:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Använda arbetsytan

Skriva en del kod som använder de grundläggande API:erna i SDK:n för att spåra experimentkörningar.

```python
from azureml.core import Experiment

# create a new experiemnt
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>Visa loggade resultat
När körningen är klar kan du visa experimentkörningen på Azure-portalen. Använd följande kod för att skriva ut en URL till resultatet för den senaste körningen.

```python
print(run.get_portal_url())
```

Använd länken för att visa de loggade värdena på Azure-portalen i webbläsaren.

![loggade värden i portalen](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Rensa resurser 
>[!IMPORTANT]
>Resurser som har skapats kan användas som förutsättningar för att andra självstudier och instruktionsartiklar om Azure Machine Learning.

Om du inte ska använda det du har skapat här kan du ta bort de resurser som du skapade med den här snabbstarten, så att du inte behöver betala något.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Nästa steg

Nu har du skapat de resurser som krävs för att du ska kunna experimentera och distribuera modeller. Du har också kört kod i en notebook och utforskat körningshistoriken från koden på arbetsytan i molnet.

Du behöver några fler paket i din miljö för att använda den med självstudierna om Azure Machine Learning:

1. Stäng din notebook i webbläsaren.
1. I kommandoradsfönstret använder du `Ctrl`+`C` för att stoppa notebook-servern.
1. Installera ytterligare paket.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

När du har installerat de här paketen följer du självstudierna för att träna och distribuera en modell.  

> [!div class="nextstepaction"]
> [Självstudie: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)

Du kan även utforska [mer avancerade exempel på GitHub](https://aka.ms/aml-notebooks).
