---
title: Konfigurera Azure Machine Learning experiment tjänsten | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Machine Learning-experiment tjänsten med instruktioner om hur du konfigurerar den.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 25f51618e906c2993ebf02795b9a50d4d7a0b464
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Konfigurera Azure Machine Learning experiment Service

## <a name="overview"></a>Översikt
Azure Machine Learning experiment-tjänsten kan data forskare att köra deras experiment med Azure Machine Learning-körningen och köra hanteringsfunktioner. Det ger ett ramverk för flexibel experiment med snabb iterationer. Azure Machine Learning arbetsstationen kan du börja med lokala körs på din dator och en enkel sökväg för att skala uppåt och utåt till andra miljöer, till exempel remote datavetenskap VMs med GPU eller HDInsight-kluster som kör Spark.

Experiment tjänsten är utformad för att tillhandahålla isolerade reproduceras och konsekvent körs från dina experiment. Det hjälper dig att hantera din beräknings-mål, körning miljöer och köra konfigurationer. Med hjälp av Azure Machine Learning arbetsstationen körning och kör hanteringsfunktioner, kan du enkelt flytta mellan olika miljöer. 

Du kan köra en Python eller PySpark-skript i ett projekt för arbetsstationen lokalt eller i skala i molnet. 

Du kan köra skript på: 

* Miljö för Python (3.5.2) på den lokala datorn som installerats av Workbench
* Conda Python-miljö inuti en dockerbehållare på lokal dator
* På en Python-miljö som du äger och hanterar på en fjärrdator Linux
* Conda Python miljö inuti en dockerbehållare på en fjärrdator Linux. Till exempel en () [Ubuntu-baserade DSVM på Azure]https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) på Azure

>[!IMPORTANT]
>Azure Machine Learning experiment tjänsten stöder för närvarande Python 3.5.2 och Spark 2.1.11 som Python och Spark runtime-versioner, respektive. 


### <a name="key-concepts-in-experimentation-service"></a>Viktiga begrepp i experiment Service
Det är viktigt att förstå följande begrepp i Azure Machine Learning-experiment körning. I följande avsnitt diskuterar vi hur du använder dessa koncept i detalj. 

#### <a name="compute-target"></a>Compute-mål
En _compute mål_ anger var du vill köra programmet, till exempel ditt skrivbord, att fjärr-Docker på en virtuell dator eller ett kluster. Ett mål för beräkning måste vara adresserbara och kan nås av dig. Arbetsstationen ger dig möjlighet att skapa beräkning mål och hantera dem med hjälp av programmet arbetsstationen och CLI. 

_Koppla AZ ml computetarget_ kommando i CLI kan du skapa ett beräknings-mål som du kan använda i din körs.

Stöds beräkning mål är:
* Lokala Python (3.5.2)-miljön på datorn installeras av arbetsstationen.
* Lokala Docker på datorn
* Användarhanterat, Python-miljön på fjärranslutna Ubuntu Linux virtuella datorer. Till exempel en [Ubuntu-baserade DSVM på Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Remote Docker på Ubuntu Linux virtuella datorer. Till exempel en [Ubuntu-baserade DSVM på Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight för Spark-kluster](https://azure.microsoft.com/services/hdinsight/apache-spark/) på Azure

Experiment tjänsten för närvarande stöder Python 3.5.2 Väck 2.1.11 som Python och Väck runtime versioner respektive. 

>[!IMPORTANT]
> Virtuella Windows-datorer körs Docker är **inte** stöds som mål för fjärranslutna beräkning.

#### <a name="execution-environment"></a>Körningsmiljö
Den _körningsmiljö_ definierar konfigurationen för körning och beroenden som krävs för att köra programmet i arbetsstationen.

Du kan hantera den lokala körningsmiljön med dina favoritverktyg och paketet chefer om du kör på arbetsstationen standard runtime. 

Conda används för att hantera Docker lokala och fjärranslutna Docker körningar samt HDInsight-baserade körningar. För dessa compute mål körning miljö konfigurationen hanteras via **Conda_dependencies.yml** och **Spark_dependencies.yml filer**. Dessa filer finns i den **aml_config** mappen i projektet.

**Stöds körningar för körning av miljöer är:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Kör konfigurationen
Förutom beräkning mål och körning av miljön, Azure Machine Learning ger ett ramverk för att definiera och ändra *kör konfigurationer*. Olika körningar av experimentet kan kräva olika konfigurationer som en del av repetitiva försök. Du kan omfattande annan områden med olika datakällor och justera spark-parametrar. Experiment Service ger ett ramverk för att hantera kör konfigurationer.

Kör _az ml computetarget bifoga_ kommandot ger två filer i din **aml_config** mappen i projektet: ”.compute” och en ”.runconfig” följa denna konvention: _< your_ computetarget_name > .compute_ och _< your_computetarget_name > .runconfig_. Filen .runconfig skapas automatiskt för din bekvämlighet när du skapar ett mål för beräkning. Du kan skapa och hantera andra kör konfigurationer med hjälp av _az ml runconfigurations_ i CLI. Du kan också skapa och redigera dem på din dator.

Kör konfigurationen i arbetsstationen kan du ange miljövariabler. Du kan ange miljövariabler och använda dem i din kod genom att lägga till följande avsnitt i filen .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

De här miljövariablerna kan användas i koden. Till exempel skrivs den här phyton kodstycke miljövariabel som heter ”EXAMPLE_ENV_VAR1”
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Följande bild visar det övergripande flödet för första körningen av experimentet.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scenarier för körningen av experimentet
I det här avsnittet vi fördjupa dig i körningen scenarier och lär dig mer om hur Azure Machine Learning körs experiment, särskilt körs ett experiment lokalt på en fjärransluten virtuell dator och på ett HDInsight-kluster. Det här avsnittet är en genomgång från att skapa en beräknings-mål för att köra dina experiment.

>[!NOTE]
>I resten av den här artikeln använder du CLI (Command-line-interface)-kommandon för att visa koncept och funktioner. Funktioner som beskrivs här kan också användas från arbetsstationen.

## <a name="launching-the-cli"></a>Starta CLI
Ett enkelt sätt att starta CLI öppnar ett projekt i arbetsstationen och navigera till **filen--> Öppna Kommandotolken**.

![](media/experimentation-service-configuration/opening-cli.png)

Detta kommando startar ett terminalfönster där du kan ange kommandon för att köra skript i den aktuella projektmappen. Den här terminalfönster har konfigurerats med Python 3.5.2 miljön, som installeras med arbetsstationen.

>[!NOTE]
> När du kör någon _az ml_ kommando du behöver autentiseras mot Azure i kommandofönstret. CLI använder en oberoende authentication cache och klicka sedan på skrivbordsappen så logga in på arbetsstationen innebär inte du är autentiserad i CLI-miljö. För att autentisera, Använd följande stegen. Autentiseringstoken cachelagras lokalt för en viss tidsperiod, så du behöver bara Upprepa dessa steg när token upphör att gälla. När token upphör att gälla eller om du ser autentiseringsfel, kör du följande kommandon:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>När du kör _az ml_ kommandot i en projektmapp, kontrollera att projektet tillhör ett konto i Azure Machine Learning-experiment på den _aktuella_ Azure-prenumeration. Annars kan det uppstå körningsfel.


## <a name="running-scripts-and-experiments"></a>Kör skript och experiment
Med arbetsstationen, kan du köra dina Python och PySpark-skript på olika beräkna mål med hjälp av den _az ml experiment skicka_ kommando. Kommandot kräver en konfigurationsdefinition av kör. 

Arbetsstationen skapar en motsvarande runconfig-fil när du skapar ett beräknings-mål, men du kan skapa ytterligare kör konfigurationer med hjälp av _az ml runconfiguration skapa_ kommando. Du kan också manuellt redigera kör konfigurationsfilerna.

Kör konfigurationer visas som en del av experimentet körning i arbetsstationen. 

>[!NOTE]
>Du kan lära dig mer om kör konfigurationsfilen i den [adresskonfigurationen som refereras till Experiment körning](experimentation-service-configuration-reference.md) avsnitt.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Köra ett skript lokalt på arbetsstationen installerat runtime
Arbetsstationen kan du köra skripten direkt mot arbetsstationen installerat Python 3.5.2-körning. Den här standard runtime är installerad för närvarande arbetsstationen installation och innehåller Azure Machine Learning-bibliotek och beroenden. Kör resultat och artefakter för lokala körningar sparas fortfarande kör historik tjänst i molnet.

Till skillnad från Docker-baserade körningar kommer den här konfigurationen är _inte_ hanteras av Conda. Du måste manuellt etablera paketberoenden för din lokala arbetsstationen Python-miljö.

Du kan köra följande kommando för att köra skriptet lokalt i arbetsstationen installerat Python-miljö. 

```
$az ml experiment submit -c local myscript.py
```

Du kan hitta sökvägen till standard Python-miljö genom att skriva följande kommando i fönstret arbetsstationen CLI.
```
$ conda env list
```

>[!NOTE]
>Kör lokalt PySpark direkt mot lokala Spark miljöer är för närvarande **inte** stöds. Arbetsstationen stöder PySpark-skript som körs på lokala Docker. Azure Machine Learning Docker basavbildning levereras med Spark 2.1.11 förinstallerat. 

_**Översikt över lokala körningen för Python-skriptet:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Köra ett skript på lokala Docker
Du kan också köra projekt i en dockerbehållare på din lokala dator via experiment-tjänsten. Arbetsstationen ger en Docker-basavbildning som levereras med Azure Machine Learning-bibliotek och samt som Spark 2.1.11 runtime att göra lokala Spark körningar enkelt. Docker måste redan körs på den lokala datorn.

För att köra skriptet Python eller PySpark på lokala Docker, kan du köra följande kommandon i CLI.

```
$az ml experiment submit -c docker myscript.py
```
eller
```
az ml experiment submit --run-configuration docker myscript.py
```

Körningsmiljön på lokala Docker förbereds med hjälp av Azure Machine Learning Docker basavbildningen. Arbetsstationen laddar ned den här avbildningen när du kör för första gången och överlägg med paket som angetts i conda_dependencies.yml-filen. Den här åtgärden gör första kör långsammare men efterföljande körs är betydligt snabbare tack vare arbetsstationen återanvända cachelagrade lager. 

>[!IMPORTANT]
>Du måste köra _az ml experiment förbereda - c docker_ kommandot först för att förbereda avbildningen Docker för din första gången du kör. Du kan också ange den **PrepareEnvironment** parameter till true i docker.runconfig-filen. Den här åtgärden förbereder automatiskt din miljö som en del av din kör körning.  

>[!NOTE]
>Om du kör ett skript för PySpark på Spark, används också spark_dependencies.yml förutom conda_dependencies.yml.

Kör skript på en Docker-avbildning ger följande fördelar:

1. Det garanterar att skripten kan köras på ett tillförlitligt sätt i andra miljöer för körning. Körs på en dockerbehållare hjälper dig att identifiera och undvika eventuella lokala referenser som kan påverka portability. 

2. Det gör att du snabbt testa koden på körningar och ramverk som är komplext för att installera och konfigurera, till exempel Apache Spark, utan att behöva installera dem manuellt.


_**Översikt över lokala Docker-körning för Python-skriptet:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Köra ett skript på en fjärransluten Docker
I vissa fall kanske resurser som är tillgängliga på den lokala datorn inte tillräckligt för att träna önskade modellen. I den här situationen kan experiment ett enkelt sätt att köra skripten Python eller PySpark på kraftigare virtuella datorer med Docker fjärrkörning. 

Fjärråtkomst VM ska uppfylla följande krav:
* Remote VM måste använda Ubuntu Linux och ska vara tillgängligt via SSH. 
* Fjärråtkomst VM måste ha Docker körs.

>[!IMPORTANT]
> Virtuella Windows-datorer körs Docker är **inte** stöds som mål för fjärranslutna beräkning


Du kan använda följande kommando för att skapa båda beräkning mål definitionen och kör konfigurationen för fjärråtkomst Docker-baserade körningar.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

När du konfigurerar beräknings-mål, kan du använda följande kommando för att köra skriptet.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Tänk på att körningen miljön är konfigurerad med specifikationerna i conda_dependencies.yml. spark_dependencies.yml används även om PySpark framework har angetts i .runconfig-filen. 

Docker konstruktionen processen för fjärranslutna virtuella datorer är exakt detsamma som processen för lokala Docker körs så du bör få en liknande körning upplevelse.

>[!TIP]
>Om du vill undvika fördröjning som introducerades av Docker-avbildning för din första gången du kör använda du följande kommando för att förbereda mål beräkning innan du kör skriptet. AZ ml experiment förbereda - c remotedocker

_**Översikt över fjärråtkomst vm-körning för Python-skriptet:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Köra ett skript på en fjärransluten VM riktad användarhanterat miljöer
Experiment tjänsten stöder också köra ett skript på användarens egna Python-miljö i en fjärransluten Ubuntu virtuell dator. På så sätt kan du hantera din egen miljö för körning och fortfarande använda Azure Machine Learning-funktioner. 

Följ anvisningarna nedan om du vill köra skriptet i din egen miljö.
* Förbereda din miljö för Python på en fjärransluten Ubuntu-VM eller en DSVM installera dina beroenden.
* Installera Azure Machine Learning krav med följande kommando.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>I vissa fall kan behöva du köra det här kommandot i sudo läge beroende på din behörighet. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Använd följande kommando för att skapa både definition av beräkning mål och kör konfigurationen för användarhanterat körs på den fjärranslutna VM körningar.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>Detta anger ”userManagedEnvironment”-parametern i konfigurationsfilen .compute till true.

* Ange platsen för Python-körning körbara i .compute-filen. Du måste referera till den fullständiga sökvägen för din körbara python. 
```
pythonLocation: python3
```

När du konfigurerar beräknings-mål, kan du använda följande kommando för att köra skriptet.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> När du kör på en DSVM, bör du använda följande kommandon

Om du vill köra direkt på DSVMS globala python-miljö kan du köra kommandot.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Köra ett skript på ett HDInsight-kluster
HDInsight är en populär plattform för stordata stöder Apache Spark. Arbetsstationen kan experiment på stordata med HDInsight Spark-kluster. 

>[!NOTE]
>HDInsight-klustret måste använda Azure Blob som primär lagring. Användning av Azure Data Lake-lagring stöds inte ännu.

Du kan skapa ett beräknings-mål och kör konfigurationen för ett HDInsight Spark-kluster med hjälp av följande kommando:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Om du använder FQDN i stället för en IP-adress och HDI Spark-kluster kallas _foo_, SSH-slutpunkten är på Drivrutinsnoden med namnet _foo-ssh.azurehdinsight.net_. Glöm inte den **-ssh** postfix i namnet på servern när du använder FQDN för _--adress_ parameter.


När du har beräknings-kontext kan köra du följande kommando för att köra skriptet PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Arbetsstationen förbereder och hanterar körningsmiljön på HDInsight-kluster med hjälp av Conda. Konfigurationen hanteras av _conda_dependencies.yml_ och _spark_dependencies.yml_ konfigurationsfiler. 

Du behöver SSH-åtkomst till HDInsight-klustret för att kunna utföra experiment i det här läget. 

>[!NOTE]
>Konfigurationer som stöds är HDInsight Spark-kluster körs Linux (Ubuntu med Python/PySpark 3.5.2 och Spark 2.1.11).

_**Översikt över HDInsight-baserade körning för ett PySpark-skript**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Köra ett skript på GPU
Du kan följa riktlinjerna i den här artikeln om du vill köra skript på GPU:[hur du använder GPU i Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Med hjälp av SSH-nyckel för autentisering för att skapa och använda beräknings-mål
Azure Machine Learning arbetsstationen kan du skapa och använda beräkning mål med hjälp av SSH-nyckeln-baserad autentisering förutom användarnamn/lösenord-baserade system. Du kan använda den här funktionen när du använder remotedocker eller kluster som beräknings-mål. När du använder det här schemat arbetsstationen skapar ett offentligt/privat nyckelpar och rapporterar tillbaka den offentliga nyckeln. Du lägger till den offentliga nyckeln till ~/.ssh/authorized_keys filer för ditt användarnamn. Azure Machine Learning arbetsstationen använder sedan ssh key-baserad autentisering för åtkomst och körs på den här beräknings-målet. Eftersom den privata nyckeln för beräknings-mål har sparats i KeyStore för arbetsytan kan kan andra användare i arbetsytan använda beräknings-målet på samma sätt genom att ange användarnamnet tillhandahålls för att skapa beräknings-målet.  

Du kan följa stegen nedan för att använda den här funktionen. 

- Skapa en beräknings-målet med något av följande kommandon.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
eller
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Lägg till den offentliga nyckeln som genererats av Workbench till ~/.ssh/authorized_keys-filen på målet bifogade beräkning. 

>[!IMPORTANT]
>Du måste logga in på målet beräkning med samma användarnamn som du använde för att skapa beräknings-målet. 

- Nu kan du förbereda och använder beräkning målet med SSH-nyckel för autentisering.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Nästa steg
* [Skapa och installera Azure Machine Learning](../service/quickstart-installation.md)
* [Modellhantering av](model-management-overview.md)
