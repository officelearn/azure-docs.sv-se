---
title: Skapa återanvändbara ML-miljöer
titleSuffix: Azure Machine Learning
description: Skapa och hantera miljöer för modellutbildning och distribution. Hantera Python-paket och andra inställningar för miljön.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536360"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Återanvända miljöer för utbildning och distribution med hjälp av Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln kan du lära dig hur du skapar och hanterar Azure Machine [Learning-miljöer](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Använd miljöerna för att spåra och återskapa projektens programvaruberoenden när de utvecklas.

Hantering av programvaruberoende är en vanlig uppgift för utvecklare. Du vill se till att byggen är reproducerbara utan omfattande manuell programvarukonfiguration. Klassen Azure `Environment` Machine Learning står för lokala utvecklingslösningar som pip och Conda, och det ger en lösning för både lokal och distribuerad molnutveckling.

Exemplen i den här artikeln visar hur du:

* Skapa en miljö och ange paketberoenden.
* Hämta och uppdatera miljöer.
* Använd en miljö för träning.
* Använd en miljö för webbtjänstdistribution.

En översikt på hög nivå över hur miljöer fungerar i Azure Machine Learning finns i [Vad är ML-miljöer?](concept-environments.md)

## <a name="prerequisites"></a>Krav

* [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Skapa en miljö

I följande avsnitt beskrivs flera sätt som du kan skapa en miljö för dina experiment.

### <a name="use-a-curated-environment"></a>Använda en kurerad miljö

Du kan välja en av de kurerade miljöerna som du vill börja med: 

* _AzureML-Minimal-miljön_ innehåller en minimal uppsättning paket för att möjliggöra körningsspårning och uppladdning av tillgångar. Du kan använda den som utgångspunkt för din egen miljö.

* _AzureML-Tutorial-miljön_ innehåller vanliga data science-paket. Dessa paket inkluderar Scikit-Learn, Pandas, Matplotlib och en större uppsättning azureml-sdk-paket.

Kurerade miljöer backas upp av cachelagrade Docker-avbildningar. Detta stöd minskar kostnaden för körningsförberedelser.

Använd `Environment.get` metoden för att välja en av de kurerade miljöerna:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Du kan lista de kurerade miljöerna och deras paket med hjälp av följande kod:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Starta inte ditt eget miljönamn med _AzureML-prefixet._ Det här prefixet är reserverat för kurerade miljöer.

### <a name="instantiate-an-environment-object"></a>Instansiera ett miljöobjekt

Om du vill skapa en `Environment` miljö manuellt importerar du klassen från SDK. Använd sedan följande kod för att instansiera ett miljöobjekt.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Använd Conda- och pip-specifikationsfiler

Du kan också skapa en miljö från en Conda-specifikation eller en pipkravsfil. Använd [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) metoden eller [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) metoden. I metodargumentet ska du inkludera miljönamnet och filsökvägen för den fil som du vill använda.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Använda befintliga Conda-miljöer

Om du har en befintlig Conda-miljö på den lokala datorn kan du använda tjänsten för att skapa ett miljöobjekt. Med den här strategin kan du återanvända din lokala interaktiva miljö på fjärrkörningar.

Följande kod skapar ett miljöobjekt från den `mycondaenv`befintliga Conda-miljön . Den använder [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metoden.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Skapa miljöer automatiskt

Skapa automatiskt en miljö genom att skicka in en träningskörning. Skicka körningen med `submit()` hjälp av metoden. När du skickar in en träningskörning kan det ta flera minuter att bygga den nya miljön. Byggvaraktigheten beror på storleken på de nödvändiga beroendena. 

Om du inte anger en miljö i körningskonfigurationen innan du skickar körningen skapas en standardmiljö åt dig.

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

Om du använder ett [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objekt för utbildning kan du skicka direkt uppskattningsinstansen som en körning utan att ange en miljö. Objektet `Estimator` kapslar redan in miljön och beräkningsmålet.

## <a name="add-packages-to-an-environment"></a>Lägga till paket i en miljö

Lägg till paket i en miljö med conda-, pip- eller privata hjulfiler. Ange varje paketberoende med [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) hjälp av klassen. Lägg till den i `PythonSection`miljöns .

### <a name="conda-and-pip-packages"></a>Conda- och pippaket

Om ett paket är tillgängligt i en Conda-paketlagringsplats rekommenderar vi att du använder Conda-installationen i stället för pip-installationen. Conda-paket levereras vanligtvis med färdiga binärfiler som gör installationen mer tillförlitlig.

Följande exempel lägger till miljön. Den lägger till version 1.17.0 av `numpy`. Det lägger `pillow` också `myenv`till paketet, . I exemplet [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) används metoden [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) respektive metoden.

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
> Om du använder samma miljödefinition för en annan körning återanvänder Azure Machine Learning-tjänsten den cachelagrade avbildningen i din miljö. Om du skapar en miljö med ett oinstallerat paketberoende, till exempel, ```numpy```kommer den miljön att fortsätta att använda paketversionen som installerades när miljön _skapas_. Dessutom kommer alla framtida miljöer med matchande definition att fortsätta använda den gamla versionen. Mer information finns i [Miljöbygga, cachelagring och återanvändning](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Privata hjulfiler

Du kan använda privata pip hjulfiler genom att först ladda upp dem till din arbetsyta lagring. Du laddar upp dem [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) med hjälp av en statisk metod. Sedan kan du fånga lagrings-URL:en och skicka webbadressen `add_pip_package()` till metoden.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Hantera miljöer

Hantera miljöer så att du kan uppdatera, spåra och återanvända dem över beräkningsmål och med andra användare av arbetsytan.

### <a name="register-environments"></a>Registrera miljöer

Miljön registreras automatiskt med arbetsytan när du skickar en körning eller distribuerar en webbtjänst. Du kan också registrera miljön manuellt [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) med hjälp av metoden. Den här åtgärden gör miljön till en entitet som spåras och versions i molnet. Entiteten kan delas mellan arbetsyteanvändare.

Följande kod registrerar `myenv` miljön till `ws` arbetsytan.

```python
myenv.register(workspace=ws)
```

När du använder miljön för första gången i utbildning eller distribution registreras den med arbetsytan. Sedan är den byggd och distribuerad på beräkningsmålet. Tjänsten cachelagrar miljöerna. Det tar mycket kortare tid att återanvända en cachelagrad miljö än att använda en ny tjänst eller en som har uppdaterats.

### <a name="get-existing-environments"></a>Hämta befintliga miljöer

Klassen `Environment` erbjuder metoder som gör att du kan hämta befintliga miljöer på arbetsytan. Du kan hämta miljöer efter namn, som en lista eller efter en viss träningskörning. Den här informationen är användbar för felsökning, granskning och reproducerbarhet.

#### <a name="view-a-list-of-environments"></a>Visa en lista över miljöer

Visa miljöerna på arbetsytan med [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) hjälp av klassen. Välj sedan en miljö att återanvända.

#### <a name="get-an-environment-by-name"></a>Få en miljö efter namn

Du kan också få en specifik miljö efter namn och version. Följande kod använder [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metoden för `1` att `myenv` hämta `ws` version av miljön på arbetsytan.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Träna en körspecifik miljö

Om du vill hämta miljön som användes för en viss [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) körning `Run` efter att träningen har avslutats använder du metoden i klassen.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Uppdatera en befintlig miljö

Anta att du ändrar en befintlig miljö, till exempel genom att lägga till ett Python-paket. En ny version av miljön skapas sedan när du skickar en körning, distribuerar en modell eller registrerar miljön manuellt. Med versionshanteringen kan du visa miljöns ändringar över tid.

Om du vill uppdatera en Python-paketversion i en befintlig miljö anger du versionsnumret för paketet. Om du inte använder det exakta versionsnumret återanvänds den befintliga miljön med de ursprungliga paketversionerna.

### <a name="debug-the-image-build"></a>Felsöka bildversionen

I följande exempel [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) används metoden för att manuellt skapa en miljö som en Docker-avbildning. Den övervakar utdataloggarna från avbildningsversionen med hjälp [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)av . Den inbyggda avbildningen visas sedan i arbetsytans Azure Container Registry-instans. Den här informationen är användbar för felsökning.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Aktivera Docker

 Med [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) klassen Azure `Environment` Machine Learning kan du finjustera och styra gästoperativsystemet där du kör din utbildning.

När du aktiverar Docker skapar tjänsten en Docker-avbildning. Det skapar också en Python-miljö som använder dina specifikationer i Den Docker-behållaren. Den här funktionen ger ytterligare isolering och reproducerbarhet för dina utbildningskörningar.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Som standard visas den nybyggda Docker-avbildningen i behållarregistret som är associerat med arbetsytan.  Databasnamnet har formuläret *azureml/azureml_\<uuid\>*. Den unika identifieraren (*uuid)* delen av namnet motsvarar en hash som beräknas från miljökonfigurationen. Med den här korrespondensen kan tjänsten avgöra om det redan finns en bild för den angivna miljön för återanvändning.

Dessutom använder tjänsten automatiskt en av de Ubuntu Linux-baserade [basavbildningarna](https://github.com/Azure/AzureML-Containers). Den installerar de angivna Python-paketen. Basavbildningen har CPU-versioner och GPU-versioner. Azure Machine Learning identifierar automatiskt vilken version som ska användas.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Du kan också ange en anpassad Dockerfile. Det är enklast att utgå från en av Azure ```FROM``` Machine Learning basavbildningar med Docker-kommandot och sedan lägga till egna anpassade steg. Använd den här metoden om du behöver installera icke-Python-paket som beroenden.

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

### <a name="use-user-managed-dependencies"></a>Använda användarhanterade beroenden

I vissa situationer kan din anpassade basavbildning redan innehålla en Python-miljö med paket som du vill använda.

Som standard skapar Azure Machine Learning-tjänsten en Conda-miljö med beroenden som du har angett och kör körningen i den miljön i stället för att använda pythonbibliotek som du har installerat på basavbildningen. 

Om du vill använda egna installerade `Environment.python.user_managed_dependencies = True`paket anger du parametern . Se till att basavbildningen innehåller en Python-tolk och har de paket som ditt träningsskript behöver.

Om du till exempel vill köra i en grundläggande Miniconda-miljö som har NumPy-paketet installerat anger du först en Dockerfile med ett steg för att installera paketet. Ange sedan de användarhanterade beroendena till `True`. 

Du kan också ange en sökväg till en specifik `Environment.python.interpreter_path` Python-tolk i bilden genom att ställa in variabeln.

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

## <a name="use-environments-for-training"></a>Använd miljöer för utbildning

Om du vill skicka in en utbildningskörning måste du kombinera din miljö, [beräkningsmål](concept-compute-target.md)och ditt python-skript för utbildning i en körningskonfiguration. Den här konfigurationen är ett omslagsobjekt som används för att skicka körningar.

När du skickar in en träningskörning kan det ta flera minuter att bygga en ny miljö. Varaktigheten beror på storleken på de nödvändiga beroendena. Miljöerna cachelagras av tjänsten. Så länge miljödefinitionen är oförändrad ådrar du dig bara hela installationstiden en gång.

Följande lokala skriptkörningsexempel visar [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) var du skulle använda som omslagsobjekt.

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
> Om du vill inaktivera körningshistoriken eller `ScriptRunConfig.run_config.history`köra ögonblicksbilder använder du inställningen under .

Om du inte anger miljön i körningskonfigurationen skapar tjänsten en standardmiljö när du skickar in körningen.

### <a name="use-an-estimator-for-training"></a>Använd en skattningsgivare för utbildning

Om du använder en [uppskattning för](how-to-train-ml-models.md) utbildning kan du skicka uppskattningsinstansen direkt. Den kapslar redan in miljön och beräkningsmålet.

Följande kod använder en uppskattning för en körning med en nod. Den körs på en `scikit-learn` fjärrberäkning för en modell. Det förutsätter att du tidigare har `compute_target`skapat ett beräkningsmålobjekt och ett `ds`datalagerobjekt.

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

## <a name="use-environments-for-web-service-deployment"></a>Använda miljöer för distribution av webbtjänster

Du kan använda miljöer när du distribuerar din modell som en webbtjänst. Den här funktionen möjliggör ett reproducerbart, anslutet arbetsflöde. I det här arbetsflödet kan du träna, testa och distribuera din modell med samma bibliotek i både din träningsberäkning och din inferensberäkning.

Om du vill distribuera en webbtjänst kombinerar du miljön, inferensberäkningen, bedömningsskriptet och den registrerade modellen i distributionsobjektet [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Mer information finns i [Hur och var du kan distribuera modeller](how-to-deploy-and-where.md).

I det här exemplet antar du att du har slutfört en träningskörning. Nu vill du distribuera den modellen till Azure Container Instances. När du skapar webbtjänsten monteras modellen och bedömningsfilerna på avbildningen och Azure Machine Learning-slutningsstacken läggs till i avbildningen.

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

## <a name="example-notebooks"></a>Exempel på anteckningsböcker

Det här [exemplet innehåller en anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) för exempel på begrepp och metoder som visas i den här artikeln.

[Distribuera en modell med hjälp av en anpassad Docker-basavbildning](how-to-deploy-custom-docker-image.md) visar hur du distribuerar en modell med hjälp av en anpassad Docker-basavbildning.

Den här [exempeldatorn](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) visar hur du distribuerar en Spark-modell som en webbtjänst.

## <a name="create-and-manage-environments-with-the-cli"></a>Skapa och hantera miljöer med CLI

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) speglar de flesta av funktionerna i Python SDK. Du kan använda den för att skapa och hantera miljöer. De kommandon som vi diskuterar i det här avsnittet visar grundläggande funktioner.

Följande kommando byggnadsställningar filerna för en standardmiljödefinition i den angivna katalogen. Dessa filer är JSON filer. De fungerar som motsvarande klass i SDK. Du kan använda filerna för att skapa nya miljöer med anpassade inställningar. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Kör följande kommando för att registrera en miljö från en angiven katalog.

```azurecli-interactive
az ml environment register -d myenvdir
```

Kör följande kommando för att lista alla registrerade miljöer.

```azurecli-interactive
az ml environment list
```

Hämta en registrerad miljö med hjälp av följande kommando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Nästa steg

* Om du vill använda ett hanterat beräkningsmål för att träna en modell läser du [Självstudiekurs: Träna en modell](tutorial-train-models-with-aml.md).
* När du har en tränad modell kan du läsa [om hur och var du kan distribuera modeller](how-to-deploy-and-where.md).
* Visa [ `Environment` klassen SDK-referens](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Mer information om begreppen och metoderna som beskrivs i den här artikeln finns i [exempelanteckningsboken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
