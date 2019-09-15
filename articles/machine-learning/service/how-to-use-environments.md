---
title: Skapa, använda och hantera miljöer för modell utbildning och distribution
titleSuffix: Azure Machine Learning
description: Skapa och hantera miljöer för modell utbildning och distribution. Hantera python-paket och andra inställningar för miljön.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: seodec18
ms.openlocfilehash: 242f3cec0ac766e537bd0483725ba51685bb7ced
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996623"
---
# <a name="create-and-manage-environments-for-training-and-deployment"></a>Skapa och hantera miljöer för utbildning och distribution

I den här artikeln lär du dig att skapa och hantera Azure Machine Learning [miljöer](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) så att du kan spåra och återskapa dina projekts program varu beroenden när de utvecklas.

Program beroende hantering är en vanlig uppgift för utvecklare. Du vill kunna se till att build-versioner återproduceras utan mycket manuell program varu konfiguration. Med lösningar för lokal utveckling, till exempel PIP och Conda i åtanke, tillhandahåller klassen Azure Machine Learning miljöer en lösning för både lokal och distribuerad moln utveckling.

I exemplen i den här artikeln visas hur du:

* Skapa en miljö och ange paket beroenden
* Hämta och uppdatera miljöer
* Använd miljö för utbildning
* Använd miljö för webb tjänst distribution

## <a name="what-are-environments"></a>Vad är miljöer?

Miljöer anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och Poäng skript och kör tider (python, Spark eller Docker). De är hanterade och versions bara entiteter inom din Azure Machine Learning arbets yta som möjliggör återskapande, gransknings bara och bärbara Machine Learning-arbetsflöden över olika beräknings mål.

Du kan använda ett miljö objekt i din lokala beräkning för att utveckla ditt utbildnings skript, återanvända samma miljö på Azure Machine Learning beräkning för modell utbildning i stor skala och till och med distribuera din modell med samma miljö.

Följande illustrerar att samma miljö objekt kan användas i både din körnings konfiguration för utbildning och i din inferencing-och distributions konfiguration för webb tjänst distributioner.

![Diagram över miljö i Machine Learning-arbetsflöde](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas upp i två kategorier: **användar-hanterade** och **systemhanterade**.

För en användar hanterad miljö ansvarar du för att konfigurera din miljö och installera varje paket som utbildnings skriptet behöver på beräknings målet. Conda kommer inte att kontrol lera din miljö eller installera något åt dig. 

System-hanterade miljöer används när du vill att [Conda](https://conda.io/docs/) ska hantera python-miljön och skript beroenden åt dig. Tjänsten antar den här typen av miljö som standard, på grund av dess användbarhet på fjärranslutna beräknings mål som inte kan konfigureras manuellt.

## <a name="prerequisites"></a>Förutsättningar

* Azure Machine Learning SDK för python har [installerats](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Skapa en miljö

Det finns flera sätt att skapa en miljö för experimenten.

### <a name="instantiate-an-environment-object"></a>Instansiera ett miljö objekt

Om du vill skapa en miljö manuellt importerar du miljö klassen från SDK: n och instansierar ett miljö objekt med följande kod.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Filer för Conda och pip-specifikation

Du kan också skapa en miljö från en Conda-specifikation eller en fil med krav på pip.
Använd metoden [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) eller [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) och inkludera miljö namnet och fil Sök vägen för önskad fil i metod argumentet.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Befintlig Conda-miljö

Om du har en befintlig Conda-miljö på den lokala datorn erbjuder tjänsten en lösning för att skapa ett miljö objekt från den. På så sätt kan du återanvända din lokala interaktiva miljö på fjärrkörningar.

Följande skapar ett miljö objekt av den befintliga Conda-miljön `mycondaenv` med [from_existing_conda_environment ()-](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metoden.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Skapa miljöer automatiskt

Skapa en miljö automatiskt genom att skicka en utbildning som körs med metoden Submit (). När du skickar in en utbildnings körning kan det ta flera minuter att skapa en ny miljö, beroende på storleken på de nödvändiga beroendena. 

Om du inte anger en miljö i kör konfigurationen innan du skickar in körningen, skapas en standard miljö åt dig.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

På samma sätt kan du, om [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) du använder ett objekt för utbildning, skicka uppskattnings instansen direkt som en körning utan att behöva ange en miljö, detta `Estimator` beror på att objektet redan kapslar in miljön och Compute Target.


## <a name="add-packages-to-an-environment"></a>Lägga till paket i en miljö

Lägg till paket i en miljö med Conda-, pip-eller privata Wheel-filer. Ange varje paket beroende med [klassen CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)och Lägg till det i miljöns PythonSection.

### <a name="conda-and-pip-packages"></a>Conda-och pip-paket

Om ett paket är tillgängligt i en Conda-lagringsplats bör du använda Conda över pip-installationen. Orsaken är att Conda-paket vanligt vis levereras med färdiga binärfiler som gör installationen mer tillförlitlig.

I följande exempel lägger `scikit-learn`till, specifikt version 0.21.3 och `pillow` `myenv` paket till miljön med [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) respektive [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) -metoderna.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Privata Wheel-filer

Du kan använda privata pip-drivrutinsfiler genom att först ladda upp den till din lagrings plats med hjälp [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) av en statisk metod, sedan fånga in lagrings-URL: en `add_pip_package()` och skicka URL: en till metoden

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

Miljön registreras automatiskt med din arbets yta när du skickar en kör eller distribuerar en webb tjänst. Du kan också registrera miljön manuellt med hjälp av metoden [Registrera ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Den här åtgärden gör miljön till en entitet som spåras och versions hantering i molnet och kan delas mellan arbets ytans användare.

Följande registrerar miljön, `myenv`till `ws`arbets ytan.

```python
myenv.register(workspace=ws)
```

När den används för första gången, i utbildning eller distribution, registreras miljön på arbets ytan, skapas och distribueras på beräknings målet. Miljöerna cachelagras av tjänsten. Att återanvända en cachelagrad miljö tar mycket mindre tid än att använda en ny tjänst eller en som har uppdaterats.

### <a name="get-existing-environments"></a>Hämta befintliga miljöer

Miljö klassen erbjuder metoder som gör att du kan hämta befintliga miljöer i din arbets yta efter namn, som en lista eller genom att köra en speciell utbildning. För fel sökning eller gransknings syfte, reproducerbarhet

#### <a name="view-list-of-environments"></a>Visa lista över miljöer

Visa miljöer i din arbets yta med [List ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)och välj sedan en att återanvända.

```python
from azureml.core import Environment
list("workspace_name")
```

#### <a name="get-environment-by-name"></a>Hämta miljö efter namn

Du kan också få en speciell miljö efter namn och version.
I följande kod används metoden [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) för att hämta versionen `1` `myenv` av `ws` miljön på arbets ytan.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Utbildning som kör en speciell miljö

Om du vill hämta miljön som används för en speciell körning när utbildningen har slutförts använder du metoden [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)i klassen Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Uppdatera en befintlig miljö

Om du gör ändringar i en befintlig miljö, t. ex. lägger till ett python-paket, skapas en ny version av en miljö när du antingen skickar kör, distribuerar modell eller manuellt registrerar miljön. Med-versionen kan du Visa ändringar i miljön över tid.

Om du vill uppdatera en python-paket version av en befintlig miljö anger du det exakta versions numret för paketet. Annars kommer Azure Machine Learning att återanvända den befintliga miljön med paket versioner från när miljön skapades.

### <a name="debug-the-image-build"></a>Felsöka avbildnings versionen

I det här exemplet används metoden [build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) för att skapa en miljö manuellt som en Docker-avbildning och övervakar utgående loggar från avbildnings versionen med [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Den skapade avbildningen visas sedan under arbets ytan Azure Container Registry, vilket är användbart för fel sökning.

```python
from azureml.core import Image
build = env.build()
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker och miljöer

 [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) i Azure Machine Learning `Environments` -klassen gör att du kan anpassa och styra i detalj det gäst operativ system som din utbildning körs i.

När du `enable` dockar skapar tjänsten en Docker-avbildning och skapar en python-miljö med dina specifikationer inom den Docker-behållaren. Detta ger ytterligare isolering och reproducerbarhet för dina utbildnings körningar.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

När den har skapats visas Docker-avbildningen i Azure Container Registry som är kopplad till arbets ytan som standard.  Databas namnet har formatet *azureml/azureml_\<\>UUID*. Den unika identifieraren (*uuuid*) motsvarar en hash beräknad från miljö konfigurationen. Detta gör att tjänsten kan avgöra om en avbildning som motsvarar den aktuella miljön redan finns för åter användning.

Dessutom använder tjänsten automatiskt en av de Ubuntu Linux-baserade [bas avbildningarna](https://github.com/Azure/AzureML-Containers)och installerar de angivna python-paketen. Bas avbildningen har processor-och GPU-versioner och du kan ange GPU-avbildningen `gpu_support=True`genom att ange.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"

# Specify GPU image
myenv.docker.gpu_support=True
```

> [!NOTE]
> Om du anger `environment.python.user_managed_dependencies=False` när du använder en anpassad Docker-avbildning kommer tjänsten att skapa en Conda-miljö i avbildningen och köra körningen i den miljön, i stället för att använda python-bibliotek som du kan ha installerat på bas avbildningen. Ange att parametern ska `True` använda dina egna installerade paket.

## <a name="using-environments-for-training"></a>Använda miljöer för utbildning

Om du vill skicka en tränings miljö måste du kombinera din miljö, [Beräkna mål](concept-compute-target.md) och träna Python-skript till en körnings konfiguration. ett wrapper-objekt som används för att skicka körningar.

När du skickar in en utbildnings körning kan det ta flera minuter att skapa en ny miljö, beroende på storleken på de nödvändiga beroendena. Miljöerna cachelagras av tjänsten, så så länge miljö definitionen förblir oförändrad, påförs alltid den fullständiga konfigurationen en gång.

Följande är ett exempel på en lokal skript körning där du kan använda [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) som ett wrapper-objekt.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.compute_target = "local"

# Attach environment to run config
runconfig.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Om du vill inaktivera körnings historik eller köra ögonblicks bilder använder `ScriptRunConfig.run_config.history`du inställningen under.

Om du inte anger miljön i din körnings konfiguration kommer tjänsten att skapa en standard miljö när du skickar in din körning.

### <a name="train-with-an-estimator"></a>Träna med en kostnadsuppskattning

Om du använder en [uppskattning](how-to-train-ml-models.md) för utbildning kan du bara skicka uppskattnings instansen direkt, eftersom den redan kapslar in miljön och Compute-målet.

I följande exempel används en uppskattning för att köra en utbildning på en enskild nod för en scikit-modell, och förutsätter ett tidigare skapat Compute Target- `compute_target` objekt och data lager `ds`objekt.

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

## <a name="using-environments-for-web-service-deployment"></a>Använda miljöer för webb tjänst distribution

Du kan använda miljöer när du distribuerar din modell som en webb tjänst. Detta gör det möjligt att skapa ett reproducerat arbets flöde där du kan träna, testa och distribuera din modell med exakt samma bibliotek i både träning och inferencing-beräkningar.

Om du vill distribuera en webb tjänst kombinerar du miljö, bedömnings beräkning, bedömnings skript och registrerad modell i ditt distributions objekt, [distribuerar ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Lär dig mer om att [distribuera webb tjänster](how-to-deploy-and-where.md).

I det här exemplet förutsätter vi att du har slutfört en utbildning som vill distribuera modellen till en Azure Container Instance (ACI). När du skapar webb tjänsten monteras modell-och poängsättnings filerna på avbildningen och Azure Machine Learning inferencing-stacken läggs till i avbildningen.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Exempel-anteckningsböcker

Den här [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expanderas efter koncept och metoder som visas i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa [miljö klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) SDK-referensen.
