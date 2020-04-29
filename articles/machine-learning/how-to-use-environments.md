---
title: Skapa återanvändbara ML-miljöer
titleSuffix: Azure Machine Learning
description: Skapa och hantera miljöer för modell utbildning och distribution. Hantera python-paket och andra inställningar för miljön.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536360"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Återanvänd miljöer för utbildning och distribution med hjälp av Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar och hanterar Azure Machine Learning [miljöer](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Använd miljöer för att spåra och återskapa dina projekts program varu beroenden när de utvecklas.

Program beroende hantering är en vanlig uppgift för utvecklare. Du vill se till att build-versioner är reproducerbara utan en omfattande manuell program konfiguration. Azure Machine Learning `Environment` klass konton för lokala utvecklings lösningar som pip och Conda, och tillhandahåller en lösning för både lokal och distribuerad moln utveckling.

I exemplen i den här artikeln visas hur du:

* Skapa en miljö och ange paket beroenden.
* Hämta och uppdatera miljöer.
* Använd en miljö för utbildning.
* Använd en miljö för webb tjänst distribution.

En översikt över hur miljöer fungerar i Azure Machine Learning finns i [Vad är ml-miljöer?](concept-environments.md).

## <a name="prerequisites"></a>Krav

* [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Skapa en miljö

I följande avsnitt hittar du flera olika sätt att skapa en miljö för experimenten.

### <a name="use-a-curated-environment"></a>Använda en granskad miljö

Du kan välja en av de granskade miljöer som ska börja med: 

* _Azureml-minimal_ miljö innehåller en minimal uppsättning paket för att aktivera kör spårning och till gångs överföring. Du kan använda den som utgångs punkt för din egen miljö.

* _Azureml – själv studie_ miljön innehåller gemensamma data vetenskaps paket. Paketen innehåller Scikit-lär, Pandas, matplotlib och en större uppsättning azureml-SDK-paket.

De granskade miljöerna backas upp av cachelagrade Docker-avbildningar. Den här säkerhets kopieringen minskar kostnaden för att köra förberedelser.

Använd `Environment.get` metoden för att välja en av de granskade miljöerna:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Du kan visa en lista över de granskade miljöerna och deras paket med hjälp av följande kod:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Starta inte ditt eget miljö namn med _azureml_ -prefixet. Det här prefixet är reserverat för granskade miljöer.

### <a name="instantiate-an-environment-object"></a>Instansiera ett miljö objekt

Om du vill skapa en miljö manuellt importerar `Environment` du klassen från SDK: n. Använd sedan följande kod för att instansiera ett miljö objekt.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Använda filer för Conda och pip-specifikation

Du kan också skapa en miljö från en Conda-specifikation eller en fil med krav på pip. Använd [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) metoden eller [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) metoden. I metod argumentet inkluderar du ditt miljö namn och sökvägen till den fil som du vill använda.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Använd befintliga Conda-miljöer

Om du har en befintlig Conda-miljö på den lokala datorn kan du använda tjänsten för att skapa ett miljö objekt. Genom att använda den här strategin kan du återanvända din lokala interaktiva miljö på fjärrkörningar.

Följande kod skapar ett miljö objekt från den befintliga Conda-miljön `mycondaenv`. [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) Metoden används.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Skapa miljöer automatiskt

Skapa en miljö automatiskt genom att skicka en utbildnings körning. Skicka in körningen med hjälp `submit()` av-metoden. När du skickar in en utbildnings körning kan det ta flera minuter att skapa den nya miljön. Bygg tiden beror på storleken på de nödvändiga beroendena. 

Om du inte anger en miljö i körnings konfigurationen innan du skickar körningen, skapas en standard miljö åt dig.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

På samma sätt kan du, om [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) du använder ett objekt för utbildning, direkt skicka in uppskattnings instansen som en körning utan att ange någon miljö. `Estimator` Objektet kapslar redan in miljön och Compute-målet.

## <a name="add-packages-to-an-environment"></a>Lägga till paket i en miljö

Lägg till paket i en miljö med Conda-, pip-eller privata Wheel-filer. Ange varje paket beroende med hjälp av [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) klassen. Lägg till den i miljöns `PythonSection`.

### <a name="conda-and-pip-packages"></a>Conda-och pip-paket

Om ett paket är tillgängligt i en Conda-lagringsplats, rekommenderar vi att du använder Conda-installationen i stället för pip-installationen. Conda-paket levereras normalt med färdiga binärfiler som gör installationen mer tillförlitlig.

Följande exempel lägger till i miljön. Den lägger till version 1.17.0 `numpy`av. `pillow` Paketet läggs också till `myenv`. Exemplet använder [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) metoden respektive [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) metod.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Om du använder samma miljö definition för en annan körning återanvänds den cachelagrade avbildningen av miljön i Azure Machine Learnings tjänsten. Om du skapar en miljö med ett ej fixerat paket beroende kan du till ```numpy```exempel använda paket versionen som är installerad _när miljön skapades_. Dessutom fortsätter all framtida miljö med matchnings definition att använda den gamla versionen. Mer information finns i [miljö utveckling, cachelagring och åter användning](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Privata Wheel-filer

Du kan använda privata pip-drivrutinsfiler genom att först ladda upp dem till lagrings utrymmet. Du laddar upp dem med hjälp av [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) en statisk metod. Sedan fångar du lagrings-URL: en och skickar URL `add_pip_package()` : en till-metoden.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Hantera miljöer

Hantera miljöer så att du kan uppdatera, spåra och återanvända dem i beräknings mål och med andra användare av arbets ytan.

### <a name="register-environments"></a>Registrera miljöer

Miljön registreras automatiskt med din arbets yta när du skickar en kör eller distribuerar en webb tjänst. Du kan också registrera miljön manuellt med hjälp av- [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) metoden. Den här åtgärden gör miljön till en entitet som spåras och versions hantering i molnet. Entiteten kan delas mellan arbets ytans användare.

Följande kod registrerar `myenv` miljön på `ws` arbets ytan.

```python
myenv.register(workspace=ws)
```

När du använder miljön för första gången i utbildning eller distribution är den registrerad i arbets ytan. Sedan skapas och distribueras det på beräknings målet. Tjänsten cachelagrar miljöerna. Att återanvända en cachelagrad miljö tar mycket mindre tid än att använda en ny tjänst eller en som har uppdaterats.

### <a name="get-existing-environments"></a>Hämta befintliga miljöer

`Environment` Klassen erbjuder metoder som gör att du kan hämta befintliga miljöer i din arbets yta. Du kan hämta miljöer efter namn, som en lista eller av en speciell utbildning. Den här informationen är användbar för fel sökning, granskning och reproducerbarhet.

#### <a name="view-a-list-of-environments"></a>Visa en lista över miljöer

Visa miljöerna i arbets ytan med hjälp av [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) -klassen. Välj sedan en miljö som ska återanvändas.

#### <a name="get-an-environment-by-name"></a>Hämta en miljö efter namn

Du kan också få en speciell miljö efter namn och version. I följande [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) kod används metoden för att hämta versionen `1` av `myenv` miljön på `ws` arbets ytan.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Träna en miljö som körs

Om du vill hämta miljön som användes för en speciell körning när utbildningen har slutförts använder du [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) -metoden i- `Run` klassen.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Uppdatera en befintlig miljö

Anta att du ändrar en befintlig miljö, till exempel genom att lägga till ett python-paket. En ny version av miljön skapas sedan när du skickar en körning, distribuerar en modell eller manuellt registrerar miljön. Med-versionen kan du Visa miljöns ändringar över tid.

Om du vill uppdatera en python-paket version i en befintlig miljö anger du versions numret för paketet. Om du inte använder det exakta versions numret kommer Azure Machine Learning att återanvända den befintliga miljön med dess ursprungliga paket versioner.

### <a name="debug-the-image-build"></a>Felsöka avbildnings versionen

I följande exempel används [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) metoden för att skapa en miljö manuellt som en Docker-avbildning. Den övervakar utgående loggar från avbildnings versionen med hjälp [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)av. Den skapade avbildningen visas sedan i arbets ytans Azure Container Registry instans. Den här informationen är användbar för fel sökning.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Aktivera Docker

 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) I klassen Azure Machine Learning `Environment` kan du finjustera och kontrol lera gäst operativ systemet som du kör din utbildning på.

När du aktiverar Docker skapar tjänsten en Docker-avbildning. Det skapar också en python-miljö som använder dina specifikationer i den Docker-behållaren. Den här funktionen ger ytterligare isolering och reproducerbarhet för din utbildning.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Som standard visas den nyligen inbyggda Docker-avbildningen i behållar registret som är kopplat till arbets ytan.  Databas namnet har formatet *azureml/azureml_\<\>UUID*. Den unika identifieraren (*UUID*) i namnet motsvarar en hash som beräknas från miljö konfigurationen. Den här korrespondensen gör att tjänsten kan avgöra om det redan finns en avbildning för den aktuella miljön för åter användning.

Dessutom använder tjänsten automatiskt en av de Ubuntu Linux-baserade [bas avbildningarna](https://github.com/Azure/AzureML-Containers). De angivna python-paketen installeras. Bas avbildningen har processor versioner och GPU-versioner. Azure Machine Learning identifierar automatiskt vilken version som ska användas.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Du kan också ange en anpassad Dockerfile. Det är enklast att starta från en av Azure Machine Learning Base-avbildningar ```FROM``` med hjälp av Docker-kommandot och sedan lägga till egna anpassade steg. Använd den här metoden om du behöver installera icke-python-paket som beroenden.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Använda användar hanterade beroenden

I vissa situationer kan din anpassade bas avbildning redan innehålla en python-miljö med paket som du vill använda.

Som standard skapar Azure Machine Learning-tjänsten en Conda-miljö med beroenden som du har angett, och kör i den miljön i stället för att använda python-bibliotek som du har installerat på bas avbildningen. 

Ange parametern `Environment.python.user_managed_dependencies = True`om du vill använda dina egna installerade paket. Se till att bas avbildningen innehåller en python-tolk och har de paket som krävs för utbildnings skriptet.

Om du till exempel vill köra i en grundläggande Miniconda-miljö där NumPy-paketet är installerat ska du först ange en Dockerfile med ett steg för att installera paketet. Ange sedan de användar hanterade beroendena `True`till. 

Du kan också ange en sökväg till en speciell python-tolk i bilden, genom att `Environment.python.interpreter_path` ange variabeln.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Använda miljöer för utbildning

Om du vill skicka en utbildnings körning måste du kombinera din miljö, [Beräkna mål](concept-compute-target.md)och ditt Python-skript i en körnings konfiguration. Den här konfigurationen är ett wrapper-objekt som används för att skicka körningar.

När du skickar in en utbildnings körning kan det ta flera minuter att skapa en ny miljö. Varaktigheten beror på storleken på de nödvändiga beroendena. Miljöerna cachelagras av tjänsten. Så länge miljö definitionen är oförändrad, tillkommer du bara den fullständiga konfigurations tiden en gång.

Följande lokala skript körnings exempel visar var du skulle använda [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) som wrapper-objekt.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Om du vill inaktivera körnings historik eller köra ögonblicks bilder använder du `ScriptRunConfig.run_config.history`inställningen under.

Om du inte anger miljön i din körnings konfiguration skapar tjänsten en standard miljö när du skickar in din körning.

### <a name="use-an-estimator-for-training"></a>Använd en uppskattning för utbildning

Om du använder en [uppskattning](how-to-train-ml-models.md) för utbildning kan du skicka uppskattnings instansen direkt. Den kapslar redan in miljön och Compute-målet.

I följande kod används en uppskattning för att köra en utbildning med en nod. Den körs på en fjärrberäkning för en `scikit-learn` modell. Det förutsätter att du redan har skapat ett Compute Target- `compute_target`objekt, och ett data lager `ds`objekt.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Använda miljöer för webb tjänst distribution

Du kan använda miljöer när du distribuerar din modell som en webb tjänst. Den här funktionen möjliggör ett återreproducerbart, anslutet arbets flöde. I det här arbets flödet kan du träna, testa och distribuera din modell genom att använda samma bibliotek i både din utbildning och din data bearbetning.

Om du vill distribuera en webb tjänst kombinerar du miljön, ett bedömnings beräknings skript och en registrerad modell i ditt distributions objekt [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Mer information finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

I det här exemplet förutsätter vi att du har slutfört en tränings körning. Nu vill du distribuera modellen till Azure Container Instances. När du skapar webb tjänsten monteras modell-och poängsättnings filerna på avbildningen och den Azure Machine Learning-härlednings stacken läggs till i avbildningen.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Exempel på bärbara datorer

Den här [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expanderas efter koncept och metoder som visas i den här artikeln.

[Distribuera en modell med hjälp av en anpassad Docker-basadress](how-to-deploy-custom-docker-image.md) visar hur du distribuerar en modell med hjälp av en anpassad Docker-bas avbildning.

Den här [exempel datorn](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) visar hur du distribuerar en spark-modell som en webb tjänst.

## <a name="create-and-manage-environments-with-the-cli"></a>Skapa och hantera miljöer med CLI

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) speglar de flesta funktionerna i python SDK. Du kan använda den för att skapa och hantera miljöer. De kommandon som vi diskuterar i det här avsnittet demonstrerar grundläggande funktioner.

Följande kommando autogenererar filerna för en standard miljö definition i den angivna katalogen. De här filerna är JSON-filer. De fungerar som motsvarande klass i SDK. Du kan använda filerna för att skapa nya miljöer med anpassade inställningar. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Kör följande kommando för att registrera en miljö från en angiven katalog.

```azurecli-interactive
az ml environment register -d myenvdir
```

Kör följande kommando för att visa en lista över alla registrerade miljöer.

```azurecli-interactive
az ml environment list
```

Hämta en registrerad miljö med hjälp av följande kommando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Nästa steg

* Om du vill använda ett hanterat beräknings mål för att träna en modell, se [Självstudier: träna en modell](tutorial-train-models-with-aml.md).
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa [ `Environment` klass SDK-referensen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Mer information om de begrepp och metoder som beskrivs i den här artikeln finns i [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
