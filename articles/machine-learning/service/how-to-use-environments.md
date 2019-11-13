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
ms.date: 09/27/2019
ms.openlocfilehash: 62f298e0efb5c54efdcd15cf470ed4640f720058
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73957848"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>Återanvänd miljöer för utbildning & distribution med Azure Machine Learning.
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

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

I följande avsnitt visas hur du kan använda samma miljö objekt i både din körnings konfiguration för utbildning och i konfigurationen för din konfiguration för distribution av webb tjänster.

![Diagram över miljö i Machine Learning-arbetsflöde](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas in i tre kategorier: **granskade**, **användar hanterade** och **systemhanterade**.

Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De innehåller samlingar med python-paket och inställningar som hjälper dig att komma igång med olika ramverk för maskin inlärning. 

För en användar hanterad miljö ansvarar du för att konfigurera din miljö och installera varje paket som utbildnings skriptet behöver på beräknings målet. Conda kommer inte att kontrol lera din miljö eller installera något åt dig. 

System-hanterade miljöer används när du vill att [Conda](https://conda.io/docs/) ska hantera python-miljön och skript beroenden åt dig. Tjänsten antar den här typen av miljö som standard, på grund av dess användbarhet på fjärranslutna beräknings mål som inte kan konfigureras manuellt.

## <a name="prerequisites"></a>Krav

* Azure Machine Learning SDK för python har [installerats](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).


## <a name="create-an-environment"></a>Skapa en miljö

Det finns flera sätt att skapa en miljö för experimenten.

### <a name="use-curated-environment"></a>Använd granskad miljö

Du kan välja en av de granskade miljöer som ska börja med. 

* __Azureml-minimal__ miljö innehåller en minimal uppsättning paket för att aktivera kör spårning och till gångs överföring. Du kan använda den som utgångs punkt för din egen miljö.

* __Azureml – själv studie__ miljön innehåller gemensamma data vetenskaps paket, till exempel Scikit – lära, Pandas och matplotlib, och större uppsättning azureml-SDK-paket.

De granskade miljöerna backas upp av cachelagrade Docker-avbildningar, vilket minskar kostnaden för att köra förberedelser.

Använd __miljö. get__ -metoden för att välja en av de granskade miljöerna:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Du kan visa en lista över de granskade miljöerna och deras paket med följande kod:
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Starta inte ditt eget miljö namn med _azureml_ -prefix. Den är reserverad för granskade miljöer.

### <a name="instantiate-an-environment-object"></a>Instansiera ett miljö objekt

Om du vill skapa en miljö manuellt importerar du klassen miljö från SDK och instansierar ett miljö objekt med följande kod.

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

Följande kod skapar ett miljö objekt av den befintliga Conda-miljön `mycondaenv` med metoden [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

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

På samma sätt kan du, om du använder ett [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objekt för utbildning, skicka uppskattnings instansen direkt som en körning utan att behöva ange en miljö. `Estimator`-objektet kapslar redan in miljön och Compute-målet.


## <a name="add-packages-to-an-environment"></a>Lägga till paket i en miljö

Lägg till paket i en miljö med Conda-, pip-eller privata Wheel-filer. Ange varje paket beroende med [klassen CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)och Lägg till det i miljöns PythonSection.

### <a name="conda-and-pip-packages"></a>Conda-och pip-paket

Om ett paket är tillgängligt i en Conda-lagringsplats bör du använda Conda över pip-installationen. Orsaken är att Conda-paket vanligt vis levereras med färdiga binärfiler som gör installationen mer tillförlitlig.

I följande exempel läggs `scikit-learn`, som är 0.21.3 och `pillow` paketet till miljön, `myenv` med metoderna [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) och [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) .

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

Du kan använda privata pip-drivrutinsfiler genom att först ladda upp den till din lagrings plats med hjälp av statisk [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) Metod, sedan skapa lagrings-URL: en och skicka URL: en till `add_pip_package()`-metoden

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

Följande kod registrerar miljön, `myenv`till arbets ytan `ws`.

```python
myenv.register(workspace=ws)
```

När den används för första gången, i utbildning eller distribution, registreras miljön på arbets ytan, skapas och distribueras på beräknings målet. Miljöerna cachelagras av tjänsten. Att återanvända en cachelagrad miljö tar mycket mindre tid än att använda en ny tjänst eller en som har uppdaterats.

### <a name="get-existing-environments"></a>Hämta befintliga miljöer

Miljö klassen erbjuder metoder som gör att du kan hämta befintliga miljöer i din arbets yta efter namn, som en lista eller av en speciell utbildning som körs för fel söknings-eller gransknings syfte, samt reproducerbarhet.

#### <a name="view-list-of-environments"></a>Visa lista över miljöer

Visa miljöerna i arbets ytan med [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)och välj sedan en att återanvända.

#### <a name="get-environment-by-name"></a>Hämta miljö efter namn

Du kan också få en speciell miljö efter namn och version.
I följande kod används metoden [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) för att hämta version `1` av miljön `myenv` på arbets ytan `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Utbildning som kör en speciell miljö

Om du vill hämta miljön som används för en speciell körning när utbildningen är klar använder du metoden [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) i klassen Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Uppdatera en befintlig miljö

Om du gör ändringar i en befintlig miljö, t. ex. lägger till ett python-paket, skapas en ny version av en miljö när du antingen skickar kör, distribuerar modell eller manuellt registrerar miljön. Med-versionen kan du Visa ändringar i miljön över tid.

Om du vill uppdatera en python-paket version av en befintlig miljö anger du det exakta versions numret för paketet. Annars kommer Azure Machine Learning att återanvända den befintliga miljön med paket versioner från när miljön skapades.

### <a name="debug-the-image-build"></a>Felsöka avbildnings versionen

I det här exemplet används metoden [build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) för att skapa en miljö manuellt som en Docker-avbildning och övervakar utgående loggar från avbildnings versionen med hjälp av [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Den skapade avbildningen visas sedan under arbets ytan Azure Container Registry, vilket är användbart för fel sökning.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker och miljöer

 [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) i Azure Machine Learning `Environments`-klassen gör att du kan anpassa och kontrol lera i detalj det gäst operativ system som din utbildning körs i.

När du `enable` Docker skapar tjänsten en Docker-avbildning och skapar en python-miljö med dina specifikationer i den Docker-behållaren. Detta ger ytterligare isolering och reproducerbarhet för dina utbildnings körningar.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

När den har skapats visas Docker-avbildningen i Azure Container Registry som är kopplad till arbets ytan som standard.  Databas namnet har formatet *azureml/azureml_\<uuid\>* . Den unika identifieraren (*UUID*) motsvarar en hash beräknad från miljö konfigurationen. Detta gör att tjänsten kan avgöra om en avbildning som motsvarar den aktuella miljön redan finns för åter användning.

Dessutom använder tjänsten automatiskt en av de Ubuntu Linux-baserade [bas avbildningarna](https://github.com/Azure/AzureML-Containers)och installerar de angivna python-paketen. Bas avbildningen har processor-och GPU-versioner. Azure Machine Learning identifierar automatiskt vilken version som ska användas.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> Om du anger `environment.python.user_managed_dependencies=False` när du använder en anpassad Docker-avbildning kommer tjänsten att skapa en Conda-miljö i avbildningen och köra körningen i den miljön, i stället för att använda python-bibliotek som du kan ha installerat på bas avbildningen. Ange parametern till `True` för att använda dina egna installerade paket.

## <a name="using-environments-for-training"></a>Använda miljöer för utbildning

Om du vill skicka en tränings miljö måste du kombinera din miljö, [Beräkna mål](concept-compute-target.md) och träna Python-skript till en körnings konfiguration. ett wrapper-objekt som används för att skicka körningar.

När du skickar in en utbildnings körning kan det ta flera minuter att skapa en ny miljö, beroende på storleken på de nödvändiga beroendena. Miljöerna cachelagras av tjänsten, så så länge miljö definitionen förblir oförändrad, påförs alltid den fullständiga konfigurationen en gång.

Följande lokala skript körnings exempel visar var du skulle använda [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) som wrapper-objekt.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

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
> Om du vill inaktivera körnings historik eller köra ögonblicks bilder använder du inställningen under `ScriptRunConfig.run_config.history`.

Om du inte anger miljön i din körnings konfiguration kommer tjänsten att skapa en standard miljö när du skickar in din körning.

### <a name="train-with-an-estimator"></a>Träna med en kostnadsuppskattning

Om du använder en [uppskattning](how-to-train-ml-models.md) för utbildning kan du bara skicka uppskattnings instansen direkt, eftersom den redan kapslar in miljön och Compute-målet.

I följande kod används en uppskattning för att köra en enda nod-utbildning i en fjärrberäkning för en scikit-modell och förutsätter ett tidigare skapat Compute Target-objekt, `compute_target` och data lager objekt `ds`.

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

Du kan använda miljöer när du distribuerar din modell som en webb tjänst. Detta gör det möjligt att skapa ett reproducerat arbets flöde där du kan träna, testa och distribuera din modell med exakt samma bibliotek i både din utbildning och dina härlednings beräkningar.

Om du vill distribuera en webb tjänst kombinerar du miljö, bedömnings beräkning, bedömnings skript och registrerad modell i ditt distributions objekt, [distribuerar ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Lär dig mer om att [distribuera webb tjänster](how-to-deploy-and-where.md).

I det här exemplet förutsätter vi att du har slutfört en tränings körning och vill distribuera den modellen till en Azure Container Instance (ACI). När du skapar webb tjänsten monteras modell-och poängsättnings filerna på avbildningen och den Azure Machine Learning-härlednings stacken läggs till i avbildningen.

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

* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa [miljö klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) SDK-referensen.
