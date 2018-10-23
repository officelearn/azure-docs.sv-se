---
title: 'Snabbstart: Använda Python SDK för att skapa en arbetsyta för Machine Learning-tjänsten – Azure Machine Learning'
description: Kom igång med Azure Machine Learning. Installera Python SDK och använd den för att skapa en arbetsyta. Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: cc348ca50b942b6b8b1474ed4dac4067d107a4af
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378007"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använda Python för att komma igång med Azure Machine Learning

I den här snabbstarten använder du Azure Machine Learning SDK för Python för att skapa och sedan använda en [arbetsyta](concept-azure-machine-learning-architecture.md) för Machine Learning-tjänsten. Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning.

I den här självstudien installerar du Python SDK och gör följande:

* Skapa en arbetsyta i din Azure-prenumeration.
* Skapa en konfigurationsfil för den arbetsytan för senare användning i andra notebooks och skript.
* Skriva kod som loggar värden i arbetsytan.
* Visa de loggade värdena på din arbetsyta.

I den här snabbstarten skapar du en arbetsyta och en konfigurationsfil. Du kan använda dem som förhandskrav för andra Machine Learning-självstudier och instruktionsartiklar. Precis som med andra Azure-tjänster finns det begränsningar och kvoter som är associerade med Machine Learning. [Läs mer om kvoter och hur du begär mer.](how-to-manage-quotas.md)

Följande Azure-resurser läggs automatiskt till din arbetsyta när de är regionalt tillgängliga:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="install-the-sdk"></a>Installera SDK:n

*Hoppa över det här avsnittet om du använder en virtuell dator för datavetenskap skapats efter den 27 September 2018.* Dessa virtuella datorer för datavetenskap levereras med Python SDK förinstallerad.

Innan du installerar SDK:n rekommenderar vi att du skapar en isolerad Python-miljö. Den här snabbstarten använder [Miniconda](https://conda.io/docs/user-guide/install/index.html), men du kan även använda fullständiga [Anaconda](https://www.anaconda.com/) installerat eller [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installera Miniconda


[Ladda ned](https://conda.io/miniconda.html) och installera Miniconda. Välj Python 3.7 eller en senare version. Välj inte versionen Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Skapa en isolerad Python-miljö 

Öppna ett kommandotolksfönster. Skapa sedan en ny conda-miljö med namnet `myenv` med Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Aktivera miljön.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Installera SDK:n

I den aktiverade conda-miljön installerar du SDK:n. Den här koden installerar kärnkomponenter för Machine Learning-SDK. Den installerar även en Jupyter Notebook-server i conda-miljön `myenv`. Installationen tar **cirka fyra minuter** att slutföra.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Du startar Jupyter Notebook genom att ange det här kommandot.
```sh
jupyter notebook
```

I webbläsarfönstret skapar du en ny notebook med hjälp av `Python 3`-standardkernel. 

För att visa SDK-versionen anger du följande Python-kod i en notebook-cell och köra den.

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

För att visa information om arbetsytan, till exempel associerad lagring, containerregister och nyckelvalv, anger du följande kod.

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Skriva en konfigurationsfil

Spara informationen om arbetsytan i en konfigurationsfil i den aktuella katalogen. Den här filen kallas ”aml_config\config.json”.  

Den här konfigurationsfilen för arbetsyta gör det enkelt att läsa in samma arbetsyta senare. Du kan läsa in den med andra notebooks och skript i samma katalog eller en underkatalog. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

API-anropet `write_config()` skapar konfigurationsfilen i den aktuella katalogen. Filen `config.json` innehåller följande skript.

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

# create a new experiment
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

![Loggade värden i portalen](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Rensa resurser 
>[!IMPORTANT]
>Resurser som har skapats kan användas som förhandskrav för andra självstudier och instruktionsartiklar om Machine Learning.

Om du inte planerar att använda de resurser som du skapade här tar du bort dem så att du inte debiteras.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Nästa steg

Du skapade de nödvändiga resurser som du behöver för att experimentera med och distribuera modeller. Du körde även kod på en notebook. Dessutom utforskade du körningshistoriken från koden på arbetsytan i molnet.

Du behöver några fler paket i din miljö för att använda den med självstudierna om Machine Learning.

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
