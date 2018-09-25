---
title: Konfigurera Azure Machine Learning Experimentation Service | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Machine Learning Experimentation Service med instruktioner om hur du konfigurerar den.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 3c5084e548bbb72fa38aae8b60aa46fb4d462dca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990356"
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Konfigurera Azure Machine Learning-experimentering

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

## <a name="overview"></a>Översikt
Azure Machine Learning Experimentation Service gör det möjligt för dataexperter att köra sina experiment med hjälp av Azure Machine Learning-körningen och köra hanteringsfunktioner. Det ger ett ramverk för flexibel experiment med snabba iterationer. Azure Machine Learning Workbench kan du börja med lokala körs på din dator och ett enkelt sätt för att skala uppåt och utåt till andra miljöer, till exempel remote virtuella datorer för datavetenskap med GPU eller HDInsight-kluster som kör Spark.

Experimenteringstjänsten har utformats för att tillhandahålla isolerade reproducerbar och konsekvent körningar av experiment. Det hjälper dig att hantera dina beräkningsmål körningsmiljöer, och köra konfigurationer. Med hjälp av Azure Machine Learning Workbench körning och kör hanteringsfunktioner, kan du enkelt flytta mellan olika miljöer. 

Du kan köra ett Python eller PySpark-skript i ett projekt i Workbench lokalt eller i stor skala i molnet. 

Du kan köra dina skript på: 

* Python (3.5.2)-miljö på din lokala dator som installerats av Workbench
* Conda Python-miljö i en Docker-behållare på lokal dator
* På en Python-miljö som du äger och hanterar på en fjärrdator med Linux
* Conda Python-miljö i en Docker-behållare på en fjärrdator med Linux. Till exempel en () [Ubuntu-baserad DSVM på Azure]https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight för Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) på Azure

>[!IMPORTANT]
>Azure Machine Learning Experimentation Service stöder för närvarande Python 3.5.2 och Spark 2.1.11 som Python och Spark körningsversioner respektive. 


### <a name="key-concepts-in-experimentation-service"></a>Viktiga begrepp i experimentering
Det är viktigt att förstå följande begrepp i körningsmiljön för Azure Machine Learning-experiment. I följande avsnitt diskuterar vi hur du använder de här koncepten i detalj. 

#### <a name="compute-target"></a>Beräkningsmål
En _beräkningsmålet_ anger var du vill köra ditt program, till exempel ditt skrivbord, remote Docker på en virtuell dator eller ett kluster. Beräkningsmål måste vara adresserbar och kan nås av dig. Workbench ger dig möjlighet att skapa beräkningsmål och hantera dem med hjälp av i Workbench och CLI. 

_AZ ml computetarget bifoga_ kommando i CLI kan du skapa ett beräkningsmål som du kan använda i dina körningar.

Beräkningsmål som stöds är:
* Lokal Python (3.5.2)-miljö på datorn installerats av Workbench.
* Lokala Docker på datorn
* Användarhanterade, Python-miljön på fjärranslutna virtuella Linux-Ubuntu-datorer. Till exempel en [Ubuntu-baserad DSVM på Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Fjärransluten Docker på virtuella Linux-Ubuntu-datorer. Till exempel en [Ubuntu-baserad DSVM på Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight för Spark-kluster](https://azure.microsoft.com/services/hdinsight/apache-spark/) på Azure

Experimentering för närvarande stöder Python 3.5.2 Spark 2.1.11 som Python och Spark-körningsversioner, respektive. 

>[!IMPORTANT]
> Windows virtuella datorer körs Docker är **inte** stöds som fjärransluten beräkningsmål.

#### <a name="execution-environment"></a>Körningsmiljö
Den _körningsmiljö_ definierar konfigurationen för körning och de beroenden som krävs för att köra programmet i Workbench.

Du kan hantera lokal körningsmiljö med dina favoritverktyg och pakethanterare om du kör på en Workbench standard. 

Conda används för att hantera lokala Docker och den fjärranslutna Docker körningar, samt HDInsight-baserade körningar. För dessa beräkningsmål miljökonfiguration körning hanteras via **Conda_dependencies.yml** och **Spark_dependencies.yml filer**. De här filerna finns i den **aml_config** mapp inuti ditt projekt.

**Körningar som stöds för körningsmiljöer är:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Köra konfiguration
Förutom beräkningsmiljö för mål och körning Azure Machine Learning tillhandahåller ett ramverk för att definiera och ändra *kör konfigurationer*. Olika körningar av experimentet kan kräva olika konfiguration som en del av iterativa experiment. Du kan oinskränkt olika parametern intervall, med hjälp av olika datakällor och justera spark-parametrar. Experimenteringstjänsten ger ett ramverk för att hantera kör konfigurationer.

Kör _az ml computetarget bifoga_ kommandot ger två filer i din **aml_config** mappen i projektet: ”.compute” och en ”.runconfig” följer denna konvention: _< your_ computetarget_name > .compute_ och _< your_computetarget_name > .runconfig_. Filen .runconfig skapas automatiskt för din bekvämlighet när du skapar ett beräkningsmål. Du kan skapa och hantera andra kör konfigurationer med hjälp av _az ml runconfigurations_ i CLI. Du kan också skapa och redigera dem i ditt filsystem.

Kör konfiguration i Workbench kan du ange miljövariabler. Du kan ange miljövariabler och använda dem i din kod genom att lägga till följande avsnitt i filen .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Dessa miljövariabler kan nås i din kod. Till exempel det här kodfragmentet phyton skriver ut den miljövariabel som heter ”EXAMPLE_ENV_VAR1”
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Följande bild visar det övergripande flödet för första körningen av experimentet.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scenarier för körning av experiment
I det här avsnittet ska vi fördjupa dig i körningen scenarier och lär dig mer om hur Azure Machine Learning körs experiment, särskilt kör ett experiment lokalt, på en fjärransluten virtuell dator och på ett HDInsight-kluster. Det här avsnittet finns en genomgång som startar från att skapa ett beräkningsmål för att köra dina experiment.

>[!NOTE]
>För resten av den här artikeln använder använder vi CLI (kommandoradsgränssnittet)-kommandon för att visa begreppen och funktionerna. Funktioner som beskrivs här kan också användas från Workbench.

## <a name="launching-the-cli"></a>Starta CLI
Ett enkelt sätt att starta CLI är att öppna ett projekt i Workbench och navigera till **Arkiv--> Öppna Kommandotolken**.

![](media/experimentation-service-configuration/opening-cli.png)

Det här kommandot startar ett terminalfönster där du kan ange kommandon för att köra skript i den aktuella projektmappen. Det här terminalfönstret har konfigurerats med Python 3.5.2 miljön, som installeras med Workbench.

>[!NOTE]
> När du kör någon _az ml_ kommando i kommandofönstret du måste autentiseras mot Azure. CLI använder en oberoende autentiseringscache och sedan på skrivbordsappen och så logga in på Workbench innebär inte att du är autentiserad i din CLI-miljö. Använd följande för att autentisera, stegen. Autentiseringstoken cachelagrats lokalt för en viss tidsperiod, så du behöver bara Upprepa dessa steg när token upphör att gälla. När token upphör att gälla eller om det uppstår autentiseringsfel, kör du följande kommandon:

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
>När du kör _az ml_ kommandot i en projektmapp, till exempel, se till att projektet tillhör en Azure Machine Learning-experimenteringskonto på den _aktuella_ Azure-prenumeration. Annars kan det uppstå körningsfel.


## <a name="running-scripts-and-experiments"></a>Köra skript och experiment
Med Workbench, kan du köra Python och PySpark-skript på olika beräkningsmål med hjälp av den _az ml-experiment skicka_ kommando. Det här kommandot kräver en körningskonfiguration definition. 

Workbench skapar en motsvarande runconfig-fil när du skapar ett beräkningsmål, men du kan skapa ytterligare kör konfigurationer med hjälp av _az ml runconfiguration skapa_ kommando. Du kan också manuellt redigera kör konfigurationsfilerna.

Kör konfigurationer visas som en del av experimentet körning i Workbench. 

>[!NOTE]
>Du kan läsa mer om den kör konfigurationsfilen i den [adresskonfigurationen som refereras till experimentet körning](experimentation-service-configuration-reference.md) avsnittet.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Kör ett skript lokalt på Workbench-installerade runtime
Workbench kan du köra dina skript direkt mot Workbench installerat Python 3.5.2-körning. Den här standard-körningen är installerat i Workbench konfiguration tid och innehåller Azure Machine Learning-bibliotek och beroenden. Körningsresultat och artefakter för lokala körningar sparas fortfarande kör historik-tjänst i molnet.

Till skillnad från Docker-baserade körningar den här konfigurationen är _inte_ hanteras av Conda. Du måste manuellt etablera paketberoenden för den lokala arbetsstationen Python-miljön.

Du kan köra följande kommando för att köra skriptet lokalt i Workbench-installerade Python-miljön. 

```
$az ml experiment submit -c local myscript.py
```

Du kan hitta sökvägen till Python standardmiljön genom att skriva följande kommando i Workbench CLI-fönstret.
```
$ conda env list
```

>[!NOTE]
>Köra PySpark lokalt direkt mot lokala Spark miljöer är för närvarande **inte** stöds. Workbench stöder PySpark-skript som körs på lokala Docker. Azure Machine Learning basavbildningen för Docker levereras med Spark 2.1.11 som redan är installerat. 

_**Översikt över lokal körning för ett Python-skript:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Köra ett skript på lokala Docker
Du kan också köra dina projekt på en Docker-behållare på din lokala dator via experimentering. Workbench innehåller en grundläggande Docker-avbildning som kommer med Azure Machine Learning-bibliotek och även som Spark 2.1.11 runtime för att se lokala Spark körningar enkelt. Docker måste redan körs på den lokala datorn.

Du kan köra följande kommandon i CLI för att köra ditt Python eller PySpark-skript på lokala Docker.

```
$az ml experiment submit -c docker myscript.py
```
eller
```
az ml experiment submit --run-configuration docker myscript.py
```

Körningsmiljö på lokala Docker förbereds med hjälp av Azure Machine Learning-grundläggande Docker-avbildningen. Workbench laddar ned den här avbildningen när du kör för första gången och överlägg med paket som anges i filen conda_dependencies.yml. Den här åtgärden gör första kör långsammare men efterföljande körningar är betydligt snabbare tack vare Workbench återanvända cachelagrade lager. 

>[!IMPORTANT]
>Du måste köra _az ml-experiment förbereda - c docker_ kommandot först för att förbereda Docker-avbildningen för din första körningen. Du kan också ange den **PrepareEnvironment** parametern på Sant i din docker.runconfig-fil. Den här åtgärden förbereder automatiskt din miljö som en del av din kör körning.  

>[!NOTE]
>Om du kör ett PySpark-skript på Spark, används också spark_dependencies.yml förutom conda_dependencies.yml.

Köra ditt skript på en Docker-avbildningen ger dig följande fördelar:

1. Det innebär att ditt skript kan köras på ett tillförlitligt sätt i andra körningsmiljöer. Som körs på en Docker-behållare kan du identifiera och undvika eventuella lokala referenser som kan påverka portabilitet. 

2. Det kan du snabbt testa koden på körningar och ramverk som är komplext för att installera och konfigurera, till exempel Apache Spark, utan att behöva installera dem själv.


_**Översikt över lokala Docker-körning för ett Python-skript:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Köra ett skript på en fjärransluten Docker
I vissa fall kanske inte tillgängliga resurser på den lokala datorn är tillräckligt för att träna önskade modellen. I det här fallet kan experimentering ett enkelt sätt att köra dina Python eller PySpark-skript på mer kraftfulla virtuella datorer med hjälp av Docker fjärrkörning. 

Fjärransluten virtuell dator bör uppfylla följande krav:
* Virtuella fjärrdatorn måste köra Linux Ubuntu och bör vara tillgänglig via SSH. 
* Fjärransluten virtuell dator måste ha Docker som körs.

>[!IMPORTANT]
> Windows virtuella datorer körs Docker är **inte** stöds som fjärransluten beräkningsmål


Du kan använda följande kommando för att skapa båda beräkning target definitionen och köra konfiguration för fjärranslutna Docker-baserade körningar.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

När du har konfigurerat beräkningsmål kan du använda följande kommando för att köra skriptet.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Tänk på att körningen miljö konfigureras med hjälp av specifikationerna i conda_dependencies.yml. spark_dependencies.yml används även om PySpark framework har angetts i .runconfig-filen. 

Processen för Docker-konstruktion för fjärranslutna virtuella datorer är exakt densamma som processen för lokala Docker körs så att du kan förvänta dig en liknande upplevelse för körning.

>[!TIP]
>Om du vill undvika svarstiden genom att skapa Docker-avbildningen för din första körning kan använda du följande kommando för att förbereda beräkningsmål innan du kör skriptet. AZ ml-experiment förbereda - c remotedocker

_**Översikt över virtuella fjärrdatorn körningen för ett Python-skript:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Köra ett skript på en fjärransluten virtuell dator som riktar in sig på användarhanterade miljöer
Experimenteringstjänsten stöder också köra ett skript på användarens egna Python-miljö i en fjärransluten virtuell Ubuntu-dator. På så sätt kan du hantera din egen miljö för körning och fortfarande använda Azure Machine Learning-funktioner. 

Följ stegen nedan om du vill köra skriptet på din egen miljö.
* Förbered din Python-miljö på en fjärransluten Ubuntu VM eller en DSVM installera dina beroenden.
* Installera Azure Machine Learning-krav med följande kommando.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>I vissa fall kan behöva du körs det här kommandot med sudo beroende på dina behörigheter. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Använd följande kommando för att skapa både beräkning target definitions- och kör konfigurationen för användarhanterade körs på den fjärranslutna VM körningar.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>Detta anger ”userManagedEnvironment”-parametern i konfigurationsfilen .compute till true.

* Ange platsen för Python-körning körbara i din .compute-fil. Du bör använda den fullständiga sökvägen till din körbara python. 
```
pythonLocation: python3
```

När du har konfigurerat beräkningsmål kan du använda följande kommando för att köra skriptet.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> När du kör på en DSVM, bör du använda följande kommandon

Om du vill köra direkt i DSVMS globala python-miljö kan du köra kommandot.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Köra ett skript på ett HDInsight-kluster
HDInsight är en populär plattform för analys av stordata stöd för Apache Spark. Workbench gör det möjligt för experimentering på stordata med HDInsight Spark-kluster. 

>[!NOTE]
>HDInsight-klustret måste använda Azure Blob som primär lagring. Användning av Azure Data Lake-lagring stöds inte ännu.

Du kan skapa ett beräkningsmål och kör konfigurationen för ett HDInsight Spark-kluster med följande kommando:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Om du använder fullständigt domännamn i stället för en IP-adress och ditt HDI Spark-kluster har namnet _foo_, SSH-slutpunkten är på drivrutinsnod med namnet _foo-ssh.azurehdinsight.net_. Glöm inte den **-ssh** postfixet in servernamnet när du använder det fullständiga Domännamnet för _--adress_ parametern.


När du har beräkningskontexten kan köra du följande kommando för att köra PySpark-skript.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench förbereder och hanterar körningsmiljö på HDInsight-kluster med Conda. Konfiguration hanteras av _conda_dependencies.yml_ och _spark_dependencies.yml_ konfigurationsfiler. 

Du behöver SSH-åtkomst till HDInsight-klustret för att köra experiment i det här läget. 

>[!NOTE]
>Konfigurationer som stöds är HDInsight Spark-kluster som kör Linux (Ubuntu med Python/PySpark 3.5.2 och Spark 2.1.11).

_**Översikt över HDInsight-baserad körning för ett PySpark-skript**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Köra ett skript på GPU
Du kan följa riktlinjerna i den här artikeln för att köra dina skript på GPU:[hur du använder GPU i Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Med hjälp av SSH-nyckel för autentisering för att skapa och använda beräkningsmål
Azure Machine Learning Workbench kan du skapa och använda beräkningsmål med hjälp av SSH-nyckel-baserad autentisering förutom användarnamn/lösenord-baserade-schema. Du kan använda den här funktionen när du använder remotedocker eller kluster som din beräkningsmål. När du använder det här schemat, skapar ett offentligt/privat nyckelpar Workbench och rapporterar tillbaka den offentliga nyckeln. Du lägga till den offentliga nyckeln till ~/.ssh/authorized_keys-filer för ditt användarnamn. Azure Machine Learning Workbench använder sedan ssh-nyckel-baserad autentisering för åtkomst och körs på den här beräkningsmål. Eftersom den privata nyckeln för beräkningsmål som sparas i nyckelarkivet för arbetsytan, kan andra användare av arbetsytan använda beräkningsmål på samma sätt genom att ange användarnamnet som angetts för att skapa beräkningsmål.  

Du kan följa stegen nedan för att använda den här funktionen. 

- Skapa ett beräkningsmål med någon av följande kommandon.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
eller
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Lägg till den offentliga nyckeln som genererats av Workbench till ~/.ssh/authorized_keys-filen på den anslutna beräkningsmål. 

>[!IMPORTANT]
>Du måste logga in beräkningsmål med samma användarnamn som du använde för att skapa beräkningsmål. 

- Nu kan du förbereda och använder beräkningsmål med hjälp av SSH-nyckel för autentisering.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Nästa steg
* [Skapa och installera Azure Machine Learning](quickstart-installation.md)
* [Modellhantering](model-management-overview.md)
