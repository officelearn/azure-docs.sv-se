---
title: Azure Machine Learning Experimentation Service configuration-filer
description: Det här dokumentet beskriver konfigurationsinställningarna för Azure ML-experimentering.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 43bee297b917143c9014b28049c6dfa28727b757
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650373"
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Azure Machine Learning Experimentation Service configuration-filer

När du kör ett skript i Azure Machine Learning (Azure ML) Workbench beteendet för körningen styrs av filer i den **aml_config** mapp. Den här mappen är under mappen projektroten. Det är viktigt att förstå innehållet i filerna för att uppnå önskat utfall för din körning optimalt.

Följande är de relevanta filerna under den här mappen:
- conda_dependencies.yml
- spark_dependencies.yml
- Compute-filer
    - \<Compute målnamn > .compute
- Kör konfigurationsfiler
    - \<Kör namn > .runconfig

>[!NOTE]
>Du kan vanligtvis har en målfil för beräkning och kör konfigurationsfil för varje beräkningsmål som du skapar. Du kan dock skapar de här filerna oberoende av varandra och har flera kör konfigurationsfiler som pekar på samma beräkningsmål.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Den här filen är en [conda miljöfil](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) som anger Python runtime-versionen och paket som är beroende av din kod. När Azure ML Workbench kör ett skript i en Docker-behållare eller ett HDInsight-kluster, skapas en [conda miljö](https://conda.io/docs/using/envs.html) för skriptet ska köras på. 

I den här filen anger du Python-paket som krävs för ditt skript för körning. Azure ML-experimentering skapar conda-miljö enligt din lista över beroenden. De paket som anges här måste kunna nås av motorn för körning via kanaler som:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* en offentligt tillgänglig slutpunkt (URL)
* eller en lokal filsökväg
* andra kan nås av motorn för körning

>[!NOTE]
>När du kör på HDInsight-kluster kan skapar Azure ML Workbench en conda-miljö för din specifika körning. På så sätt kan olika användare ska köras på olika python-miljöer i samma kluster.  

Här är ett exempel på en typisk **conda_dependencies.yml** fil.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local Docker target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench använder samma conda-miljö utan att återskapa den så länge som den **conda_dependencies.yml** förblir densamma. Den återskapas miljön ändrar dina beroenden.

>[!NOTE]
>Om du anpassar körning mot _lokala_ beräkningskontext, **conda_dependencies.yml** filen är **inte** används. Paketberoenden för den lokala Azure ML Workbench Python-miljön måste installeras manuellt.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Den här filen anger namnet på Spark-programmet när du skickar in ett PySpark-skript och Spark-paket som måste installeras. Du kan också ange en offentlig Maven-lagringsplats som Spark-paket som finns i dessa Maven-databaser.

Här är ett exempel:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Klustret justering parametrar, t.ex arbetsstorlek och kärnor bör ingå i ”configuration”-avsnittet i spark_dependecies.yml 

>[!NOTE]
>Om du kör skriptet i Python-miljön *spark_dependencies.yml* filen ignoreras. Den används endast om du kör mot Spark (antingen på Docker eller HDInsight-kluster).

## <a name="run-configuration"></a>Köra konfiguration
Om du vill ange en specifik kör konfiguration, behöver du en .compute-fil och en .runconfig-fil. Dessa skapas vanligtvis med en CLI-kommando. Du kan också klona avslutar sådana, byta namn på dem och redigera dem.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Det här kommandot skapar ett par baserat på den angivna måltypen för beräkning. Anta att du har gett din beräkningsmål _foo_. Det här kommandot genererar _foo.compute_ och _foo.runconfig_ i din **aml_config** mapp.

>[!NOTE]
> _lokala_ eller _docker_ namn för kör konfigurationsfilerna är valfri. Azure ML Workbench lägger till dessa två kör konfigurationer när du skapar ett tomt projekt för din bekvämlighet. Du kan byta namn på ”<run configuration name>.runconfig” som medföljer projektmallen eller skapa nya med vilket namn som helst.

### <a name="compute-target-namecompute"></a>\<Compute målnamn > .compute
_\<Compute målnamn > .compute_ filen anger information om anslutning och konfiguration för beräkningsmål. Det är en lista över namn / värde-par. Följande är inställningarna som stöds:

**typ**: typ av compute-miljö. Värden som stöds är:
  - lokal
  - fjärråtkomst
  - Docker
  - remotedocker
  - kluster

**baseDockerImage**: The Docker-avbildning som används för att köra Python/PySpark-skript. Standardvärdet är _microsoft/mmlspark:plus-0.7.91_. Vi har också stöd för en annan bild: _microsoft/mmlspark:plus-gpu-0.7.91_, vilket ger du GPU åtkomst till värddatorn (om GPU finns).

**adress**: IP-adress eller FQDN (fullständigt kvalificerade domännamn) för den virtuella datorn eller HDInsight-kluster huvudnoden.

**användarnamn**: SSH användarnamn för att komma åt den virtuella datorn eller HDInsight-huvudnoden.

**lösenord**: det krypterade lösenordet för SSH-anslutning.

**sharedVolumes**: flagga för att skicka en signal som motorn för körning ska använda Docker delad volym-funktionen för att leverera projektfilerna fram och tillbaka. Med den här flaggan aktiveras kan snabba upp körningen eftersom Docker kan komma åt projekt direkt utan att behöva kopiera dem. Det är bäst att ange _FALSKT_ om Docker-motorn körs på Windows eftersom volymen delning för Docker på Windows kan vara flaky. Ange den till _SANT_ om den körs på macOS eller Linux.

**nvidiaDocker**: den här flaggan när _SANT_, talar om för tjänsten Azure ML-experimentering att använda _nvidia docker_ kommandot, till skillnad från vanliga _docker_kommandot för att starta Docker-avbildningen. Den _nvidia docker_ motorn kan Docker-behållare till åtkomst GPU maskinvara. Inställningen krävs om du vill köra GPU-körning i Docker-behållare. Har stöd för Linux-värd _nvidia docker_. Till exempel Linux-baserade dsvm: er i Azure levereras med _nvidia docker_. _NVIDIA docker_ från och med nu stöds inte i Windows.

**nativeSharedDirectory**: den här egenskapen anger den grundläggande katalogen (till exempel: _~/.azureml/share/_) där filer kan sparas för att delas mellan körs på samma beräkningsmål. Om den här inställningen används när du kör i en Docker-behållare _sharedVolumes_ måste anges till true. I annat fall går inte att köra.

**userManagedEnvironment**: den här egenskapen anger om den här beräkningsmål är hanteras av användaren direkt eller via experimentering.  

**pythonLocation**: den här egenskapen anger platsen för python-körning som ska användas på beräkningsmål för att köra användarens program. 

### <a name="run-configuration-namerunconfig"></a>\<Kör namn > .runconfig
_\<Kör namn > .runconfig_ anger den Azure ML experimentera körningsbeteende. Du kan konfigurera körningsbeteende, till exempel spåra historiken för körning eller vad beräkningsmål som ska användas tillsammans med många andra. Namnen på de kör konfigurationsfilerna används för att fylla i listrutan på kontexten körning i Azure ML Workbench skrivbordsprogram.

**ArgumentVector**: det här avsnittet anger skript som ska köras som en del av den här körningen och parametrar för skriptet. Exempel: Om du har följande kodavsnitt i din ”<run configuration name>.runconfig” fil 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_”az ml-experiment skicka foo.runconfig”_ automatiskt kör kommandot med _myscript.py_ filen skicka 234 som en parameter och uppsättningar--utförlig flaggan.

**Target**: den här parametern är namnet på den _.compute_ filen som den _runconfig_ filen referenser. Den allmänt pekar på _foo.compute_ filen men du kan redigera den så att den pekar till en annan beräkningsmål.

**Miljövariabler**: det här avsnittet kan du ställa in miljövariabler som en del av deras körs. Användaren kan ange miljövariablerna med hjälp av namn / värde-par i följande format:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Dessa miljövariabler kan nås i användarens kod. Exempelvis kan den här Python-koden skriver ut miljövariabeln med namnet ”EXAMPLE_ENV_VAR”
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: den här egenskapen anger om Azure ML Workbench ska starta en Spark-session för att köra skriptet. Standardvärdet är _PySpark_. Ange den till _Python_ om du inte kör PySpark-kod som kan hjälpa att starta jobbet snabbare med lägre kostnader.

**CondaDependenciesFile**: den här egenskapen pekar på den fil som anger conda-beroenden för miljön i den *aml_config* mapp. Om inställd _null_, den standardikonen **conda_dependencies.yml** fil.

**SparkDependenciesFile**: den här egenskapen pekar på den fil som anger Spark-beroenden i den **aml_config** mapp. Det är inställt på _null_ som standard och det indikerar standardikonen **spark_dependencies.yml** fil.

**PrepareEnvironment**: den här egenskapen har värdet _SANT_, talar du om tjänsten experimentering förbereda conda-miljön utifrån conda-beroenden som anges som del av din första körning. Den här egenskapen gäller bara när du kör mot Docker-miljö. Den här inställningen har ingen effekt om du kör mot ett _lokala_ miljö. 

**TrackedRun**: den här flaggan signalerar tjänsten experimentering om att spåra körningen i Azure ML Workbench kör historik infrastruktur eller inte. Standardvärdet är _SANT_. 

**UseSampling**: _UseSampling_ anger om aktiva exemplen på datauppsättningar för datakällor som används för körningen. Om inställd _FALSKT_, datakällor mata in och använda den fullständiga data läses från datalagret. Om inställd _SANT_, aktiva exempel används. Användare kan använda den **DataSourceSettings** att ange vilka specifika exempel på datauppsättningar ska användas om de vill åsidosätta active exemplet. 

**DataSourceSettings**: den här konfigurationen anger inställningar för datakälla. I det här avsnittet anger användaren vilka befintliga data-exemplet för en viss datakälla används som en del av körningen. 

Följande Konfigurationsinställningen anger det exemplet med namnet ”MySample” används för datakällan med namnet ”MyDataSource”
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: Data källan ersättningar kan användas när du vill växla från en datakälla till en annan utan att ändra sin kod. Användare kan till exempel växla från en samplas ned, lokal fil till den ursprungliga, större datamängd som lagras i Azure Blob genom att ändra datakällreferensen. När du använder en ersättning, körs Azure ML Workbench dina datakällor och paket för förberedelse av data genom att referera till datakällan ersättning.

I följande exempel ersätter ”mylocal.datasource” referenserna i Azure ML-datakällor och paket för förberedelse av data med ”myremote.dsource”. 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Baserat på ersättningen ovan kan läser följande kodexempel nu från ”myremote.dsource” i stället för ”mylocal.dsource” utan att användarna ändrar sin kod.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Nästa steg
Läs mer om [Experimentation Service configuration](experimentation-service-configuration.md).
