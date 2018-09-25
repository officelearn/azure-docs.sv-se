---
title: Prognostisering av serverns arbetsbelastning i terabyte med data – Azure | Microsoft Docs
description: Hur du tränar en modell för maskininlärning på stordata med hjälp av Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ROBOTS: NOINDEX
ms.openlocfilehash: 8f3bd4e62aa85c69a0bfafeacf13bc3e472136d5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964709"
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prognostisering av serverns arbetsbelastning i terabyte med data

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Den här artikeln beskriver hur datatekniker kan använda Azure Machine Learning Workbench för att utveckla lösningar som kräver användning av stordata. Du kan starta från ett exempel på en stor datauppsättning, gå igenom databearbetning, funktionsframställning och maskininlärning och utöka sedan processen för att hela stor datauppsättning. 

Du får lära dig om följande viktiga funktioner i Machine Learning Workbench:
* Enkelt växla mellan beräkningsmål. Du kan konfigurera olika beräkningsmål och använda dem i experimentering. I det här exemplet använder du en Ubuntu-DSVM och ett Azure HDInsight-kluster som beräkningsmål. Du kan också konfigurera beräkningsmål, beroende på tillgängligheten för resurser. I synnerhet när skala ut Spark-kluster med flera arbetsnoder, kan du använda resurser via Machine Learning Workbench påskynda experimentkörningar.
* Kör spårning av logg. Du kan använda Machine Learning Workbench för att spåra prestanda för machine learning-modeller och andra mått av intresse.
* Driftsättning av machine learning-modell. Du kan använda inbyggda verktyg i Machine Learning Workbench för att distribuera tränade machine learning-modell som en webbtjänst på Azure Container Service. Du kan också använda webbtjänsten för att hämta Mini batch förutsägelser via REST API-anrop. 
* Stöd för terabyte data.

> [!NOTE]
> Kodexempel och annat material som rör det här exemplet finns i [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Översikt över användningsfall

Prognoser arbetsbelastningen på servrar är en vanliga affärsbehov för teknikföretag som hanterar sina egna infrastruktur. Om du vill minska infrastrukturkostnader ska tjänster som körs på underanvänd servrar grupperas tillsammans för att köras på ett mindre antal datorer. Tjänster som körs på överansträngda servrar ska få fler datorer ska köras. 

I det här scenariot kan fokusera du på arbetsbelastningen förutsägelser för varje dator (eller server). I synnerhet Använd sessionsdata på varje server för att förutsäga arbetsbelastningsklass för servern i framtiden. Du klassificera belastningen på varje server i låg, medium och hög klasser med hjälp av slumpmässiga skog Klassificeraren i [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Machine learning-tekniker och arbetsflöde i det här exemplet kan enkelt utökas till andra liknande problem. 


## <a name="prerequisites"></a>Förutsättningar

Förutsättningar för att kunna köra det här exemplet är följande:

* En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
* En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Om du vill installera programmet och skapa en arbetsyta, se den [Snabbstart installationsguide](quickstart-installation.md). Om du har flera prenumerationer kan du [ange den önskade prenumerationen ska vara den aktuella aktiva prenumerationen](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (anvisningarna i det här exemplet är vanligtvis samma för macOS-system).
* En Data Science Virtual Machine (DSVM) för Linux (Ubuntu), helst i regionen USA, östra där data hittar. Du kan etablera en Ubuntu DSVM genom att följa [instruktionerna](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Du kan också se [snabbstarten](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Vi rekommenderar att du använder en virtuell dator med minst 8 kärnor och 32 GB minne. 

Följ den [instruktionen](../desktop-workbench/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) att aktivera lösenord utan sudoer åtkomst på den virtuella datorn för AML-arbetsstationen.  Du kan välja att använda [SSH-nyckel-baserad autentisering för att skapa och använda den virtuella datorn i AML Workbench](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). I det här exemplet använder vi lösenord för att få åtkomst till den virtuella datorn.  Spara tabellen nedan med DSVM information i efterföljande steg:

 Fältnamn| Värde |  
 |------------|------|
DSVM IP-adress | xxx|
 Användarnamn  | xxx|
 Lösenord   | xxx|


 Du kan välja att använda en virtuell dator med [Docker-motorn](https://docs.docker.com/engine/) installerad.

* Ett HDInsight Spark-kluster med Hortonworks Data Platform version 3.6 eller Spark-version 2.1.x, helst i regionen USA, östra där data hittar. Besök [skapar ett Apache Spark-kluster i Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) mer information om hur du skapar HDInsight-kluster. Vi rekommenderar att du använder en tre-worker-kluster med varje arbetsroll med 16 kärnor och 112 GB minne. Eller bara välja typ av virtuell dator `D12 V2` för huvudnoden, och `D14 V2` för arbetsnoden. Distributionen av klustret tar cirka 20 minuter. Du behöver klusternamnet, SSH-användarnamn och lösenord för att prova att använda det här exemplet. Spara tabellen nedan med information för Azure HDInsight-kluster i efterföljande steg:

 Fältnamn| Värde |  
 |------------|------|
 Klusternamn| xxx|
 Användarnamn  | xxx (sshuser som standard)|
 Lösenord   | xxx|


* Ett Azure Storage-konto. Du kan följa [instruktionerna](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att skapa en. Dessutom skapar två privata blob-behållare med namn `fullmodel` och `onemonthmodel` i det här lagringskontot. Lagringskontot används för att spara resultaten för mellanliggande beräkningar och machine learning-modeller. Du behöver namn och lagringskontonyckeln du prova att använda det här exemplet. Spara tabellen nedan med Azure storage-kontoinformation för senare steg:

 Fältnamn| Värde |  
 |------------|------|
 Lagringskontonamn| xxx|
 Åtkomstnyckel  | xxx|


Ubuntu-DSVM och Azure HDInsight-kluster som skapats i listan över nödvändiga är beräkningsmål. Compute riktar sig beräkningsresurs i samband med Machine Learning Workbench, som kan skilja sig från den dator där Workbench körs.   

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Machine Learning Workbench.
2.  På den **projekt** väljer den **+** loggar och väljer **nytt projekt**.
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet.
4.  I den **Sök efter projektmallar** Sök-rutan, skriver du in **arbetsbelastning prognostisering på terabyte Data**, och välj mallen.
5.  Välj **Skapa**.

Du kan skapa en Workbench-projekt med en förskapad git-lagringsplats genom att följa [den här instruktionen](./tutorial-classifying-iris-part-1.md).  
Kör `git status` granska status för filerna för spårning av versionen.

## <a name="data-description"></a>Databeskrivning

De data som används i det här exemplet är syntetiserade serverdata för arbetsbelastning. Den finns i ett Azure Blob storage-konto som är offentligt tillgänglig i regionen USA, östra. Kontoinformation för specifika lagringsutrymmen finns i den `dataFile` i [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) i formatet ”wasb: / /<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>”. Du kan använda data direkt från Blob-lagringen. Om lagringen används av många användare samtidigt, kan du använda [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) att ladda ned data till lagringen för en bättre upplevelse för experimentering. 

Den totala Datastorleken är cirka 1 TB. Varje fil är cirka 1 – 3 GB och är i CSV-filformat, utan rubrik. Varje datarad representerar belastningen på en transaktion på en viss server. Detaljerad information om dataschemat är följande:

Kolumnnummer | Fältnamn| Typ | Beskrivning |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    Starttid för session
2  |`SessionEnd`    | DateTime | Sluttid för session
3 |`ConcurrentConnectionCounts` | Integer | Antalet samtidiga anslutningar
4 | `MbytesTransferred` | Double-värde | Normaliserade data som överförs i megabyte
5 | `ServiceGrade` | Integer |  Tjänsten i företagsklass för sessionen
6 | `HTTP1` | Integer|  Sessionen använder HTTP1 eller HTTP2
7 |`ServerType` | Integer   |Servertyp
8 |`SubService_1_Load` | Double-värde |   Läs in deltjänst 1
9 | `SubService_2_Load` | Double-värde |  Läs in deltjänst 2
10 | `SubService_3_Load` | Double-värde |     Läs in deltjänst 3
11 |`SubService_4_Load` | Double-värde |  Läs in deltjänst 4
12 | `SubService_5_Load`| Double-värde |      Läs in deltjänst 5
13 |`SecureBytes_Load`  | Double-värde | Läsa in säker byte
14 |`TotalLoad` | Double-värde | Totalt antal belastningen på servern
15 |`ClientIP` | Sträng|    Klientens IP-adress
16 |`ServerIP` | Sträng|    Serverns IP-adress



Observera att de förväntade datatyperna visas i tabellen ovan. På grund av värden som saknas och felaktig data problem finns det ingen garanti för att datatyperna är faktiskt som förväntat. Databearbetning bör ha i åtanke. 


## <a name="scenario-structure"></a>Scenario-struktur

Så här organiseras filerna i det här exemplet.

| Filnamn | Typ | Beskrivning |
|-----------|------|-------------|
| `Code` | Mapp | Mappen innehåller all kod i det här exemplet. |
| `Config` | Mapp | Mappen innehåller konfigurationsfilerna. |
| `Image` | Mapp | Mappen som används för att spara bilder för README-filen. |
| `Model` | Mapp | Mappen som används för att spara modellfiler som hämtas från Blob storage. |
| `Code/etl.py` | Python-fil | Python-filen som används för förberedelse av data och funktioner. |
| `Code/train.py` | Python-fil | Python-filen som används för att träna en modell för tre klass multi-kreditriskanalys.  |
| `Code/webservice.py` | Python-fil | Python-filen som används för driftsättning.  |
| `Code/scoring_webservice.py` | Python-fil |  Python-filen används för transformering av data och anropa webbtjänsten. |
| `Code/O16Npreprocessing.py` | Python-fil | Python-filen som används för att Förbearbeta data för scoring_webservice.py.  |
| `Code/util.py` | Python-fil | En Python-fil som innehåller kod för läsning och skrivning av Azure-blobar.  
| `Config/storageconfig.json` | JSON-fil | Konfigurationsfilen för Azure blob-behållare som lagrar mellanliggande resultat och modell för bearbetning och utbildning på data i en månad. |
| `Config/fulldata_storageconfig.json` | JSON-fil | Konfigurationsfilen för Azure blob-behållare som lagrar mellanliggande resultat och modell för bearbetning och utbildning på en fullständig uppsättning data.|
| `Config/webservice.json` | JSON-fil | Konfigurationsfilen för scoring_webservice.py.|
| `Config/conda_dependencies.yml` | YAML-fil | Filen Conda-beroenden. |
| `Config/conda_dependencies_webservice.yml` | YAML-fil | Filen Conda beroende för webbtjänsten.|
| `Config/dsvm_spark_dependencies.yml` | YAML-fil | Spark beroende fil för Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | YAML-fil | Spark beroende fil för HDInsight Spark-klustret. |
| `README.md` | Markdown-fil | Filen Viktigt markdown. |
| `Code/download_model.py` | Python-fil | Python-filen som används för att hämta modellfiler från Azure blob till en lokal disk. |
| `Docs/DownloadModelsFromBlob.md` | Markdown-fil | Markdown-fil som innehåller instruktioner om hur du kör `Code/download_model.py`. |



### <a name="data-flow"></a>Dataflöde

Koden i [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) läser in data från offentligt tillgänglig behållaren (`dataFile` i [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Innehåller förberedelse av data och de funktioner och sparar resultatet för mellanliggande beräkningar och modeller till ditt eget privata behållare. Koden i [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) läser in mellanliggande beräkning resultaten från behållaren privat, träna klassificeringsmodellen flera och skriver tränade machine learning-modellen till behållaren privat. 

Du bör använda en behållare för experimentering på en månad datauppsättningen, och en annan för experimentering i den fullständiga data. Eftersom data och modeller sparas som Parquet-filer, är faktiskt en mapp i den behållare som innehåller flera blobar i varje fil. Resulterande behållaren ser ut så här:

| Prefix för blobbnamn | Typ | Beskrivning |
|-----------|------|-------------|
| featureScaleModel | Parquet | Standard scaler modell för numeriska funktioner. |
| stringIndexModel | Parquet | Sträng indexeraren modell för icke-numeriska funktioner.|
| oneHotEncoderModel|Parquet | En frekvent encoder modell för kategoriska funktioner. |
| mlModel | Parquet | Tränad modell för maskininlärning. |
| info| Python pickle-filen | Information om transformerade data, inklusive utbildning start, utbildning slutet, varaktighet, tidsstämpel för train-test delning och kolumner för indexering och en frekvent kodning.

Alla filer och blobar i tabellen ovan används för driftsättning.


### <a name="model-development"></a>Modeller

#### <a name="architecture-diagram"></a>Arkitekturdiagram


Följande diagram visar arbetsflödet slutpunkt till slutpunkt med att använda Machine Learning Workbench för att utveckla modellen: ![arkitektur](media/scenario-big-data/architecture.PNG)

I följande avsnitt visar vi modellen utvecklingen genom att använda remote beräkning mål i Machine Learning Workbench. Vi först läsa in lite exempeldata och kör skriptet [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) på en Ubuntu DSVM för snabb iteration. Vi kan ytterligare begränsa det arbete som vi göra [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) genom att skicka ett extra argument för snabbare iteration. I slutet använder vi ett HDInsight-kluster för att träna med fullständig data.     

Den [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) filen läses in och förbereds data och utför funktioner. Den accepterar två argument:
* En konfigurationsfil för Blob storage-behållare, för att lagra mellanliggande databearbetningen resultat och modeller.
* Ett config-argument för felsökning för snabbare iteration.

Det första argumentet `configFilename`, är en lokal konfigurationsfil där du lagrar Blob storage-information och ange var du vill läsa in data. Som standard är det [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), och det kommer att användas i en månad data kör. Vi också innehålla [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), som du behöver använda i den fullständiga data kör. Innehållet i konfigurationen är följande: 

| Fält | Typ | Beskrivning |
|-----------|------|-------------|
| storageAccount | Sträng | Azure Storage-kontonamn |
| storageContainer | Sträng | Behållare i Azure Storage-konto för att lagra resultat. |
| storageKey | Sträng |Åtkomstnyckel för Azure Storage-konto |
| Datafil|Sträng | Datakällfilerna  |
| varaktighet| Sträng | Varaktigheten för data i datakällfilerna|

Ändra både `Config/storageconfig.json` och `Config/fulldata_storageconfig.json` att konfigurera storage-konto, lagringsnyckeln och blob-behållaren mellanliggande resultaten ska sparas. Som standard är blob-behållare för en månads data kör `onemonthmodel`, och blob-behållare för fullständig datauppsättning kör är `fullmodel`. Se till att skapa dessa två behållare i ditt lagringskonto. Den `dataFile` i [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) konfigurerar vilka data har lästs in i [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). Den `duration` fältet anger det intervall som innehåller information. Om varaktigheten har angetts till ONE_MONTH vara data in en CSV-fil bland de sju filerna av data för juni 2016. Om körningstiden är FULL, laddas den fullständiga datauppsättningen (1 TB). Du behöver inte ändra `dataFile` och `duration` i dessa två konfigurationsfiler.

Det andra argumentet är felsökning. Ange värdet till FILTER_IP kan en snabbare iteration. Användning av den här parametern är användbar när du vill felsöka skriptet.

> [!NOTE]
> I alla av följande kommandon, ersätter du alla argument variabler med det verkliga värdet.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Modellen utveckling på Docker på Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Konfigurera beräkningsmål

Starta Kommandotolken från Machine Learning Workbench genom att välja **filen** > **öppna Kommandotolken**. Kör sedan: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Följande två filer skapas i mappen aml_config i ditt projekt:

-  dockerdsvm.Compute: den här filen innehåller information om anslutningen och konfigurationen för ett mål för fjärrkörning.
-  dockerdsvm.runconfig: den här filen är en uppsättning kör alternativ som används i Workbench-programmet.

Bläddra till dockerdsvm.runconfig och ändra konfigurationen för de här fälten så här:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Förbereda projektmiljön genom att köra:

```az ml experiment prepare -c dockerdsvm```


Med `PrepareEnvironment` inställd på Sant Machine Learning Workbench skapar runtime-miljö när du skickar ett jobb. `Config/conda_dependencies.yml` och `Config/dsvm_spark_dependencies.yml` innehåller anpassning av för-körningsmiljön. Du kan alltid ändra Conda-beroenden, konfiguration av Spark och Spark beroenden genom att redigera dessa två YMAL-filer. I det här exemplet vi har lagt till `azure-storage` och `azure-ml-api-sdk` som extra Python-paket i `Config/conda_dependencies.yml`. Vi även lagt till `spark.default.parallelism`, `spark.executor.instances`, och `spark.executor.cores` i `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Förberedelse av data och de funktioner på DSVM Docker

Kör skriptet `etl.py` på DSVM Docker. Använd en debug-parameter som filtrerar inlästa data med specifika IP-adresser:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Bläddra till den sida-panelen och välj **kör** att se körningshistoriken för `etl.py`. Observera att körtiden är ungefär två minuter. Om du planerar att ändra koden för att inkludera nya funktioner, ger vilket ger FILTER_IP som det andra argumentet en snabbare iteration. Du kan behöva köra det här steget flera gånger när du hanterar din egen machine learning problem för att utforska datauppsättningen eller skapa nya funktioner. 

Med den anpassade begränsningen på vilka data som ska belastning och ytterligare filtrering av vilka data som ska bearbeta kan du påskynda iteration i utvecklingen av din modell. Eftersom, bör du regelbundet spara ändringarna i din kod till Git-lagringsplats. Observera att vi används följande kod i `etl.py` att aktivera åtkomst till den privata behållaren:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Kör skriptet `etl.py` på DSVM Docker utan parametern debug FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Bläddra till den sida-panelen och välj **kör** att se körningshistoriken för `etl.py`. Observera att körtiden är ungefär fyra minuter. Bearbetade resultatet av det här steget sparas i behållaren och har lästs in för träning i train.py. Dessutom sparas sträng indexerare, encoder pipelines och standard scalers till behållaren privat. De används i driftsättningen. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Modellträning på DSVM Docker

Kör skriptet `train.py` på DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Det här steget läser in mellanliggande beräknings-resultat från körningen `etl.py`, och träna en maskininlärningsmodell. Det här steget tar ungefär två minuter.

När experimentering på små data har slutförts, kan du fortsätta att köra experimentering i den fullständiga data. Du kan starta med hjälp av samma kod och experimentera med argumentet och compute target ändringar.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Modellen utveckling på HDInsight-kluster

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Skapa beräkningsmål i Machine Learning Workbench för HDInsight-kluster

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Följande två filer skapas i mappen aml_config:
    
-  myhdi.Compute: den här filen innehåller information om anslutningen och konfigurationen för ett mål för fjärrkörning.
-  myhdi.runconfig: den här filen är en uppsättning kör alternativ som används i Workbench-programmet.


Bläddra till myhdi.runconfig och ändra konfigurationen för de här fälten så här:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Förbereda projektmiljön genom att köra:

```az ml experiment prepare -c myhdi```

Det här steget kan ta upp till sju minuter.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Förberedelse av data och de funktioner i HDInsight-kluster

Kör skriptet `etl.py`, med fullständig data i HDInsight-kluster:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Eftersom det här jobbet varar i relativt lång tid (ungefär två timmar), kan du använda `-a` till utdata strömningen inaktiveras. När jobbet är klart, i **Körningshistorik**, du kan visa drivrutinen och domänkontrollanten loggar. Om du har ett större kluster kan du alltid kan konfigurera om konfigurationerna i `Config/hdi_spark_dependencies.yml` att använda fler instanser eller kärnor. Om du har ett kluster med fyra worker noder kan du till exempel öka värdet för `spark.executor.instances` från 5 till 7. Du kan se utdata från det här steget i den **fullmodel** behållare i ditt storage-konto. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Modellträning på HDInsight-kluster

Kör skriptet `train.py` på HDInsight-kluster:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Eftersom det här jobbet varar i relativt lång tid (cirka 30 minuter), kan du använda `-a` till utdata strömningen inaktiveras.

#### <a name="run-history-exploration"></a>Kör historik utforskning

Körningshistorik är en funktion som gör att spåra dina experiment i Machine Learning Workbench. Som standard spårar den varaktigheten för experimentering. I vårt specifika exempel när vi flyttar till fullständiga datauppsättningen för `Code/etl.py` i experimentering, vi upptäcker att varaktighet ökar avsevärt. Du kan också logga specifika mått i spårningssyfte. Lägg till följande rader med kod till chefen för Python-fil om du vill aktivera mått spårning:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Här är ett exempel för att spåra ett specifikt mått:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

På rätt sidopanelen i arbetsstationen, bläddrar du till **körs** att se körningshistoriken för varje Python-fil. Du kan även gå till GitHub-databasen. En ny gren skapas med det namn som börjar med ”AMLHistory”, för att spåra ändringen du gjorde i skriptet i varje körning. 


### <a name="operationalize-the-model"></a>Operationalisera modellen

I det här avsnittet ska operationalisera du modellen som du skapade i föregående steg som en webbtjänst. Du också lära dig hur du använder webbtjänsten för att förutsäga arbetsbelastning. Använd maskinkod driftsättning command-line gränssnitt (CLI) att paketera av koden och beroenden som Docker-avbildningar och publicera modellen som en behållare webbtjänst.

Du kan använda Kommandotolken i Machine Learning Workbench för att köra de CLI: erna.  Du kan också köra CLI på Ubuntu Linux genom att följa den [installationsguide](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> Ersätt alla argument variabler med det verkliga värdet i alla följande kommandon. Det tar cirka 40 minuter för att slutföra det här avsnittet.
> 

Välj en unik sträng som miljön för driftsättning. Här kan använda vi strängen ”[unikt]” som representerar den textsträng som du väljer.

1. Skapa miljö för driftsättning och skapa resursgruppen.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Observera att du använder Container Service som miljön med hjälp av `--cluster` i den `az ml env setup` kommando. Du kan utföra åtgärder för machine learning-modellen på [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Den använder [Kubernetes](https://kubernetes.io/) för att automatisera distribution, skalning och hantering av behållarbaserade program. Det här kommandot tar cirka 20 minuter för att köra. Använd följande för att kontrollera om distributionen har slutförts: 

        az ml env show -g [unique]rg -n [unique]

   Ange distributionsmiljö som du nyss skapade genom att köra följande:

        az ml env set -g [unique]rg -n [unique]

2. Skapa och använda ett konto för modellhantering. Om du vill skapa ett modellhanteringskonto, kör du följande:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Använda modellhantering för driftsättning genom att köra följande:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Ett modellhanteringskonto används för att hantera modeller och webbtjänster. Du kan se ett nytt konto för modellhantering har skapats från Azure-portalen. Du kan se modeller, manifest, Docker-avbildningar och tjänster som skapas med hjälp av den här modellhanteringskontot.

3. Ladda ned och registrera modellerna.

   Ladda ned modeller i den **fullmodel** behållare till den lokala datorn i katalogen med kod. Hämta inte parquet-datafilen med namnet ”vmlSource.parquet”. Det är inte en modellfil. Det är ett resultat för mellanliggande beräkningar. Du kan även återanvända modellfilerna som ingår i Git-lagringsplats. Mer information finns i [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Gå till den `Model` mapp i CLI och registrera modeller som följer:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Utdata för varje kommando ger en modell-ID som krävs i nästa steg. Spara dem i en textfil för framtida användning.

4. Skapa ett manifest för webbtjänsten.

   Ett manifest innehåller kod för webbtjänsten, machine learning-modeller och runtime-miljö beroenden. Gå till den `Code` mappen i CLI och kör sedan följande kommando:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   Utdata ger ett manifest-ID för nästa steg. 

   Stanna kvar i den `Code` och du kan testa webservice.py genom att köra följande: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Skapa en Docker-avbildning. 

        az ml image create -n [unique]image --manifest-id $manifestID

   Utdata ger en avbildnings-ID för nästa steg, används den här docker-avbildning i Container Service. 

6. Distribuera webbtjänsten till Container Service-kluster.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   Utdata ger ett-ID. Du måste använda den för att hämta auktoriseringsnyckel för och tjänstens URL.

7. Anropa webbtjänsten i Python-kod kan bedöma i mini-batchar.

   Använd följande kommando för att få auktoriseringsnyckeln:

         az ml service keys realtime -i $ServiceID 

   Använd följande kommando för att få tjänsten bedömnings-URL:

        az ml service usage realtime -i $ServiceID

   Ändra innehållet i `./Config/webservice.json` med lämplig tjänst bedömning URL och auktoriseringsnyckel. Behåll ”ägar” i den ursprungliga filen och Ersätt den ”xxx”-delen. 
   
   Gå till rotkatalogen för ditt projekt och testa webbtjänsten för Mini batchbedömnings med hjälp av följande:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Skala webbtjänsten. 

   Mer information finns i [så här skalar du driftsättning i ditt Azure Container Service-kluster](how-to-scale-clusters.md).
 

## <a name="next-steps"></a>Nästa steg

Det här exemplet visar hur du använder Machine Learning Workbench för att träna en maskininlärningsmodell på stordata och operationalisera den tränade modellen. I synnerhet du lärt dig hur du konfigurerar och använder olika beräkningsmål och kör historiken för uppföljning av mått och använda olika körs.

Du kan utöka koden för att utforska korsvalidering och hyper-parametern justering. Läs mer om korsvalidering och hyper-parametern justering i [GitHub resursen](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Läs mer om tidsserieprognostisering i [GitHub resursen](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).