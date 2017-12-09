---
title: "Servern arbetsbelastning prognoser på terabyte data - Azure | Microsoft Docs"
description: "Så här tränar en maskininlärningsmodell om stordata med hjälp av Azure Machine Learning-arbetsstationen."
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: c7ed8e695097d0cf2f5c99f8ccf3378c4e553c3b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prognostisering av serverns arbetsbelastning i terabyte med data

Den här artikeln beskriver hur datavetare kan använda Azure Machine Learning-arbetsstationen för att utveckla lösningar som kräver användning av stordata. Du kan starta från ett urval av stora datamängder, gå igenom förberedelse av data, funktionen ingenjörer och maskininlärning och utöka sedan processen för att hela stora datamängder. 

Du lär dig om följande viktiga funktioner i Machine Learning arbetsstationen:
* Enkelt växla mellan beräkna mål. Du kan konfigurera olika beräkning mål och Använd dem i experiment. I det här exemplet använder du en Ubuntu DSVM och ett Azure HDInsight-kluster som mål för beräkning. Du kan också konfigurera beräkning mål, beroende på tillgängligheten för resurser. I synnerhet efter skala ut Spark-kluster med flera arbetarnoder, kan du använda resurser via Machine Learning arbetsstationen påskynda experiment körs.
* Kör spårning av logg. Du kan använda Machine Learning-arbetsstationen för att spåra prestanda för machine learning-modeller och andra mått av intresse.
* Operationalization av machine learning-modellen. Du kan använda inbyggda verktyg Machine Learning-arbetsstationen för att distribuera modellen för utbildade maskininlärning som en webbtjänst på Azure Container Service. Du kan också använda webbtjänsten för att hämta Mini batch förutsägelser via REST API-anrop. 
* Stöd för terabyte data.

> [!NOTE]
> Kodexempel och annat material som rör det här exemplet finns [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Använd case-översikt

Prognoser arbetsbelastningen på servrar är en vanliga affärsbehov för teknikföretag som hanterar sina egna infrastruktur. För att minska kostnaden för infrastruktur, ska tjänster som körs på servrar under används grupperas tillsammans för att köras på ett mindre antal datorer. Tjänster som körs på överansträngda servrar bör tilldelas flera datorer ska köras. 

I det här scenariot fokuserar på arbetsbelastningen förutsägelsen för varje dator (eller server). I synnerhet använda sessionsdata på varje server för att förutsäga arbetsbelastningsklass för servern i framtiden. Du klassificera belastningen på varje server i låg, medium och hög klasser med hjälp av slumpmässiga skog Klassificeraren i [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Maskininlärning tekniker och arbetsflöden i det här exemplet kan enkelt utökas till andra liknande problem. 


## <a name="prerequisites"></a>Krav

Förutsättningar för att kunna köra det här exemplet är följande:

* En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
* En installerad kopia av [Machine Learning arbetsstationen](./overview-what-is-azure-ml.md). Om du vill installera programmet och skapa en arbetsyta, finns det [quickstart installationsguiden](./quickstart-installation.md).
* Windows 10 (instruktionerna i det här exemplet är vanligtvis samma för macOS system).
* En datavetenskap virtuell dator (DSVM) för Linux (Ubuntu). Du kan etablera ett Ubuntu DSVM genom att följa [instruktionerna](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Du kan också se [denna Snabbstart](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Vi rekommenderar att du använder en virtuell dator med minst 8 kärnor och 32 GB minne. Du behöver DSVM IP-adress, användarnamn och lösenord för att testa det här exemplet. Spara i följande tabell med DSVM information senare stegen:

 Fältnamn| Värde |  
 |------------|------|
DSVM IP-adress | xxx|
 Användarnamn  | xxx|
 Lösenord   | xxx|

 Du kan välja att använda en virtuell dator med [Docker-motorn](https://docs.docker.com/engine/) installerad.

* Ett HDInsight Spark-kluster med Hortonworks Data Platform 3,6 och Spark-versionen 2.1.x. Besök [skapar ett Apache Spark-kluster i Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) mer information om hur du skapar HDInsight-kluster. Vi rekommenderar tre worker-kluster med varje arbetsprocessen har 16 kärnor och 112 GB minne. Eller kan du bara välja VM typ `D12 V2` för huvudnod, och `D14 V2` för arbetsnoden. Distributionen av klustret tar ungefär 20 minuter. Du behöver klusternamnet, SSH-användarnamn och lösenord för att testa det här exemplet. Spara i följande tabell med information för Azure HDInsight-kluster för senare steg:

 Fältnamn| Värde |  
 |------------|------|
 Klusternamn| xxx|
 Användarnamn  | xxx (sshuser som standard)|
 Lösenord   | xxx|


* Ett Azure Storage-konto. Du kan följa [instruktionerna](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att skapa en. Dessutom skapa två privata blob-behållare med namnet `fullmodel` och `onemonthmodel` i det här lagringskontot. Storage-konto används för att spara mellanliggande beräkning resultat och maskininlärning modeller. Du måste lagringskontots namn och åtkomstnyckel att testa det här exemplet. Spara i följande tabell med information för Azure storage-konto senare stegen:

 Fältnamn| Värde |  
 |------------|------|
 Lagringskontonamn| xxx|
 Snabbtangent  | xxx|


Ubuntu DSVM och Azure HDInsight-klustret skapas i listan över nödvändiga är mål för beräkning. Beräkna mål är beräkningsresursen i samband med Machine Learning-arbetsstationen, vilket kan skilja sig från den dator där arbetsstationen körs.   

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt genom att använda det här exemplet som en mall:
1.  Öppna Machine Learning arbetsstationen.
2.  På den **projekt** väljer den  **+**  logga in och välja **nytt projekt**.
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet.
4.  I den **Sök projektmallar** sökrutan, Skriv **arbetsbelastning prognoser på terabyte Data**, och välj mallen.
5.  Välj **Skapa**.

Du kan skapa ett projekt för arbetsstationen med en förskapad git-lagringsplats genom att följa [den här instruktionen](./tutorial-classifying-iris-part-1.md).  
Kör `git status` att kontrollera status för filerna för versionen spårning.

## <a name="data-description"></a>Beskrivning av data

De data som används i det här exemplet är syntetiskt arbetsbelastning serverdata. Det finns i ett Azure Blob storage-konto som är offentligt tillgänglig. Kontoinformation för specifika lagring finns i den `dataFile` i [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). Du kan använda data direkt från Blob storage. Om lagringsutrymmet som används av många användare samtidigt, kan du använda [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) att hämta data till din egen lagring. 

Den totala Datastorleken är cirka 1 TB. Varje fil är cirka 1 – 3 GB och är i CSV-format utan rubrik. Varje rad med data representerar inläsningen av en transaktion på en viss server. Detaljerad information för dataschemat är följande:

Kolumnnumret | Fältnamn| Typ | Beskrivning |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    Starttid för session
2  |`SessionEnd`    | DateTime | Sluttid för session
3 |`ConcurrentConnectionCounts` | Integer | Antalet samtidiga anslutningar
4 | `MbytesTransferred` | dubbla | Normaliserade data som överförs i megabyte
5 | `ServiceGrade` | Integer |  Service-klass för session
6 | `HTTP1` | Integer|  Sessionen använder HTTP1 eller HTTP2
7 |`ServerType` | Integer   |Servertyp
8 |`SubService_1_Load` | dubbla |   Läs in subservice 1
9 | `SubService_1_Load` | dubbla |  Läs in subservice 2
10 | `SubService_1_Load` | dubbla |     Läs in subservice 3
11 |`SubService_1_Load` | dubbla |  Läs in subservice 4
12 | `SubService_1_Load`| dubbla |      Läs in subservice 5
13 |`SecureBytes_Load`  | dubbla | Läsa in säker byte
14 |`TotalLoad` | dubbla | Totalt antal belastningen på servern
15 |`ClientIP` | Sträng|    Klientens IP-adress
16 |`ServerIP` | Sträng|    Serverns IP-adress



Observera att de förväntade datatyperna visas i tabellen ovan. Det finns ingen garanti för att datatyperna är faktiskt som förväntat på grund av värden som saknas och problem med felaktig data. Databearbetning bör ta hänsyn till detta. 


## <a name="scenario-structure"></a>Scenario-struktur

Filerna i det här exemplet är uppdelade enligt följande.

| Filnamn | Typ | Beskrivning |
|-----------|------|-------------|
| `Code` | Mapp | Mappen innehåller all kod i exemplet. |
| `Config` | Mapp | Mappen innehåller konfigurationsfilerna. |
| `Image` | Mapp | Mappen som används för att spara avbildningar för README-filen. |
| `Model` | Mapp | Mappen som används för att spara modellfiler som hämtas från Blob storage. |
| `Code/etl.py` | Python-fil | Python-fil som används för förberedelse av data och funktionen tekniker. |
| `Code/train.py` | Python-fil | Python-fil som används för att träna en tre-klass multi-kreditriskanalys modell.  |
| `Code/webservice.py` | Python-fil | Python-fil som används för operationalization.  |
| `Code/scoring_webservice.py` | Python-fil |  Python-fil används för Dataomvandling av och anropa webbtjänsten. |
| `Code/O16Npreprocessing.py` | Python-fil | Python-fil som används för att Förbearbeta data för scoring_webservice.py.  |
| `Code/util.py` | Python-fil | Python-fil som innehåller koden för läsning och skrivning Azure BLOB.  
| `Config/storageconfig.json` | JSON-fil | Konfigurationsfilen för Azure blob-behållare som lagrar mellanresultat och modell för bearbetning och utbildning på data i en månad. |
| `Config/fulldata_storageconfig.json` | JSON-fil | Konfigurationsfilen för Azure blob-behållare som lagrar mellanresultat och modell för bearbetning och utbildning på en fullständig uppsättning data.|
| `Config/webservice.json` | JSON-fil | Konfigurationsfilen för scoring_webservice.py.|
| `Config/conda_dependencies.yml` | YAML-fil | Conda beroende fil. |
| `Config/conda_dependencies_webservice.yml` | YAML-fil | Conda beroende fil för webbtjänsten.|
| `Config/dsvm_spark_dependencies.yml` | YAML-fil | Spark beroende fil för Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | YAML-fil | Spark beroende fil för HDInsight Spark-klustret. |
| `README.md` | Markdown-fil | Filen Viktigt markdown. |
| `Code/download_model.py` | Python-fil | Python-fil som används för att hämta modellfiler från Azure blob till en lokal disk. |
| `Docs/DownloadModelsFromBlob.md` | Markdown-fil | Markdown-fil som innehåller instruktioner om hur du kör `Code/download_model.py`. |



### <a name="data-flow"></a>Dataflöde

Koden i [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) läser in data från en offentligt tillgänglig behållare (`dataFile` i [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Den omfattar förberedelse av data och funktionen tekniker och sparar mellanliggande beräkning resultat och modeller i din egen privata behållare. Koden i [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) läser in mellanliggande beräknings-resultat från behållaren privat, tränar flera klassen klassificering modellen och skriver utbildade maskininlärning modellen till behållaren privat. 

Du bör använda en behållare för experimentering på datamängden som en månad, och en annan för experiment i den fullständiga data. Eftersom data och modeller sparas som parkettgolv fil, är faktiskt en mapp i behållaren som innehåller flera blobbar i varje fil. Den resulterande behållaren ser ut som följer:

| BLOB-prefixet | Typ | Beskrivning |
|-----------|------|-------------|
| featureScaleModel | Parkettgolv | Standard scaler modell för numeriska funktioner. |
| stringIndexModel | Parkettgolv | Strängen indexeraren modellen för icke-numeriska funktioner.|
| oneHotEncoderModel|Parkettgolv | En hot kodare modell för kategoriska funktioner. |
| mlModel | Parkettgolv | Utbildade maskininlärningsmodell. |
| Info| Python pickle-fil | Information om omvandlade data, inklusive utbildning start, utbildning slut, varaktighet, tidstämpel för train-test delning och kolumner för indexering och en hot kodning.

Alla filer och blobbar i föregående tabell används för operationalization.


### <a name="model-development"></a>Modell-utveckling

#### <a name="architecture-diagram"></a>Arkitekturdiagram


Följande diagram visar slutpunkt till slutpunkt-arbetsflöde med Machine Learning-arbetsstationen för att utveckla modellen: ![arkitektur](media/scenario-big-data/architecture.PNG)

I följande avsnitt visar vi utvecklingen modellen genom att använda remote beräkning mål i Machine Learning-arbetsstationen. Vi först läsa in en liten mängd exempeldata och kör skriptet [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) på en Ubuntu DSVM för snabb iteration. Vi kan ytterligare begränsa arbetet i [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) genom att skicka ett extra argument för snabbare iteration. I slutet kan använda vi ett HDInsight-kluster och träna med fullständiga data.     

Den [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) filen läses in och förbereder data och utför funktionen tekniker. Den accepterar två argument:
* En konfigurationsfil för blobblagringsbehållare för lagring av beräkning av mellanliggande resultat och modeller.
* Ett config-argument för felsökning för snabbare iteration.

Det första argumentet, `configFilename`, är en lokal konfigurationsfil där du lagrar Blob storage-informationen och ange var du vill läsa in data. Som standard är det [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), och den ska användas i en månad data kör. Vi också innehålla [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), som du behöver använda i den fullständiga data kör. Innehållet i konfigurationen är följande: 

| Fält | Typ | Beskrivning |
|-----------|------|-------------|
| StorageAccount | Sträng | Azure Storage-kontonamn |
| storageContainer | Sträng | Behållaren i Azure Storage-konto för att lagra mellanresultat |
| storageKey | Sträng |Azure åtkomstnyckeln för Lagringskontot |
| DataFile|Sträng | Datakällfilerna  |
| Varaktighet| Sträng | Varaktighet för data i datakällfilerna|

Ändra både `Config/storageconfig.json` och `Config/fulldata_storageconfig.json` att konfigurera lagringskontot och nyckeln för säkerhetslagring blob-behållaren mellanliggande resultaten ska sparas. Blob-behållare för en månad data kör är som standard `onemonthmodel`, och blob-behållaren för fullständig datauppsättning kör `fullmodel`. Kontrollera att du skapar dessa två behållare på ditt lagringskonto. Den `dataFile` i [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) konfigurerar vilka data läses in i [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). Den `duration` fältet anger intervall som innehåller information. Om varaktigheten är ONE_MONTH ska data läses in vara en CSV-fil mellan sju filer av data för juni 2016. Om varaktighet är FULL, fullständiga datauppsättningen (1 TB) har lästs in. Du behöver inte ändra `dataFile` och `duration` i dessa två konfigurationsfiler.

Det andra argumentet är felsökning. Ange värdet till FILTER_IP kan en snabbare iteration. Användning av den här parametern är användbart när du vill felsöka skriptet.

> [!NOTE]
> Ersätt alla argumentet variabler med det faktiska värdet i alla följande kommandon.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Modellen utveckling på Docker Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Ställ in beräknings-mål

Starta kommandoraden från Machine Learning arbetsstationen genom att välja **filen** > **öppnar du kommandotolken**. Kör sedan: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Följande två filer skapas i mappen aml_config i projektet:

-  dockerdsvm.Compute: den här filen innehåller information om anslutning och konfiguration för ett mål för fjärrkörning.
-  dockerdsvm.runconfig: den här filen är en uppsättning kör alternativ som används i programmet för arbetsstationen.

Bläddra till dockerdsvm.runconfig och ändra konfigurationen av dessa fält till följande:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Förbereda projektmiljön genom att köra:

```az ml experiment prepare -c dockerdsvm```


Med `PrepareEnvironment` inställd på Sant Machine Learning arbetsstationen skapar körningsmiljön när du skickar ett jobb. `Config/conda_dependencies.yml`och `Config/dsvm_spark_dependencies.yml` innehåller anpassning av körningsmiljön. Du kan alltid ändra Conda beroenden, Spark-konfigurationen och Spark beroenden genom att redigera dessa två YMAL-filer. I det här exemplet vi har lagt till `azure-storage` och `azure-ml-api-sdk` som extra Python-paket i `Config/conda_dependencies.yml`. Vi har även lagt till `spark.default.parallelism`, `spark.executor.instances`, och `spark.executor.cores` i `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Förberedelse av data och funktionen tekniker på DSVM Docker

Kör skriptet `etl.py` på DSVM Docker. Använd en debug-parameter som filtrerar inlästa data med specifika IP-adresser:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Bläddra till panelen sida och välj **kör** att visa de kör `etl.py`. Observera att körtiden är ungefär två minuter. Om du planerar att ändra koden för att inkludera nya funktioner, ger tillhandahåller FILTER_IP som det andra argumentet en snabbare iteration. Du kan behöva köra det här steget flera gånger när du hanterar din egen maskininlärning problem, om du vill utforska datauppsättningen eller skapa nya funktioner. 

Med anpassade begränsning på vilka data att ladda och ytterligare filtrering av vilka data som ska bearbeta snabbare du iteration processen i din modell-utveckling. När du experimenterar bör du regelbundet spara ändringarna i koden Git-lagringsplatsen. Observera att vi används följande kod i `etl.py` att aktivera åtkomst till privata behållare:

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

Bläddra till panelen sida och välj **kör** att visa de kör `etl.py`. Observera att körtiden är ungefär fyra minuter. Bearbetade resultatet av det här steget sparas i behållaren och laddas för train.py. Dessutom sparas sträng indexerare, kodare pipelines och standard scalers till behållaren privat. Dessa används i operationalization. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Modellen utbildning på DSVM Docker

Kör skriptet `train.py` på DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Det här steget läser in mellanliggande beräknings-resultat från att köras i `etl.py`, och tränar en maskininlärningsmodell. Det här steget tar ungefär två minuter.

När experiment på små data har slutförts, kan du fortsätta att köra experiment i den fullständiga data. Du kan starta med hjälp av samma kod, och experimentera med argumentet och beräkna mål ändringar.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Modellen utveckling på HDInsight-kluster

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Skapa beräknings-mål i Machine Learning-arbetsstationen för HDInsight-kluster

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Följande två filer skapas i mappen aml_config:
    
-  myhdo.Compute: den här filen innehåller information om anslutning och konfiguration för ett mål för fjärrkörning.
-  myhdi.runconfig: den här filen är en uppsättning kör alternativ som används i programmet för arbetsstationen.


Bläddra till myhdi.runconfig och ändra konfigurationen av dessa fält till följande:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Förbereda projektmiljön genom att köra:

```az ml experiment prepare -c myhdi```

Det här steget kan ta upp till sju minuter.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Förberedelse av data och funktionen tekniker på HDInsight-kluster

Kör skriptet `etl.py`, med fullständiga data i HDInsight-klustret:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Eftersom det här jobbet varar relativt lång tid (ungefär två timmar), kan du använda `-a` till utdata strömningen inaktiveras. När jobbet är klart i **Körningshistorik**, du kan visa drivrutinen och domänkontrollanten loggar. Om du har en större kluster du alltid kan konfigurera om konfigurationerna i `Config/hdi_spark_dependencies.yml` att använda flera instanser eller kärnor. Om du har ett kluster med fyra worker noder kan du till exempel öka värdet på `spark.executor.instances` från 5 till 7. Du kan se utdata från det här steget i den **fullmodel** behållare på ditt lagringskonto. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Modellen utbildning på HDInsight-kluster

Kör skriptet `train.py` på HDInsight-kluster:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Eftersom det här jobbet varar relativt lång tid (cirka 30 minuter), kan du använda `-a` till utdata strömningen inaktiveras.

#### <a name="run-history-exploration"></a>Kör historik undersökning

Körningshistorik är en funktion som gör det möjligt för spårning av dina experiment i Machine Learning-arbetsstationen. Den spårar varaktigheten för experiment som standard. I vårt exempel, när vi flyttar till fullständig datauppsättning för `Code/etl.py` i experiment, Observera att varaktigheten ökar avsevärt. Du kan också logga specifika mått i spårningssyfte. Lägg till följande rader med kod till chefen för Python-fil om du vill aktivera spårning av mått:
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

På rätt sidopanelen i arbetsstationen, bläddrar du till **körs** att visa körningshistorik för varje Python-fil. Du kan även gå till GitHub-lagringsplatsen. En ny gren skapas med namn som börjar med ”AMLHistory” för att spåra ändringar i skriptet i varje körning. 


### <a name="operationalize-the-model"></a>Operationalisera modellen

I det här avsnittet operationalisera modell som du skapade i föregående steg som en webbtjänst. Du också lära dig hur du använder webbtjänsten för att förutsäga arbetsbelastning. Använd datorn språk operationalization kommandoradsverktyget gränssnitt (CLIs) att paketera koden och beroenden som Docker-bilder och för att publicera modellen som en av webbtjänst. Mer information finns i [översikten](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md).

Du kan använda i Kommandotolken i Machine Learning-arbetsstationen för att köra CLIs.  Du kan också köra CLIs på Ubuntu Linux genom att följa den [installationsguiden](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Ersätt alla argumentet variabler med det faktiska värdet i alla följande kommandon. Det tar ungefär 40 minuter för att slutföra det här avsnittet.
> 

Välj en unik sträng som miljön för operationalization. Här kan använda vi strängen ”[unik]” som representerar den textsträng som du väljer.

1. Skapa miljö för operationalization och skapa resursgruppen.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Observera att du kan använda Container Service som miljön med hjälp av `--cluster` i den `az ml env setup` kommando. Du kan operationalisera maskininlärning modellen på [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Den använder [Kubernetes](https://kubernetes.io/) för att automatisera distribution, skalning och hantering av program. Det här kommandot tar ungefär 20 minuter för att köra. Använd följande för att kontrollera om distributionen har slutförts: 

        az ml env show -g [unique]rg -n [unique]

   Ange distributionsmiljö som du just har skapat genom att köra följande:

        az ml env set -g [unique]rg -n [unique]

2. Skapa och använda ett konto för hantering av modellen. Skapa ett konto för hantering av modellen genom att köra följande:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Använd hanteringskonsolen modellen för operationalization genom att köra följande:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   En modell management kontot används för att hantera modeller och webbtjänster. Du kan se en ny modell management kontot har skapats från Azure-portalen. Du kan se modeller, manifesten, Docker-bilder och tjänster som skapas med hjälp av det här kontot för hantering av modellen.

3. Hämta och registrera modeller.

   Hämta modeller i den **fullmodel** behållare till din lokala dator i en katalog i koden. Hämta inte parkettgolv datafilen med namnet ”vmlSource.parquet”. Det är inte en modellfil; Det är ett resultat av mellanliggande beräkning. Du kan också återanvända modellfilerna som ingår i Git-lagringsplats. Mer information finns i [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Gå till den `Model` mapp i CLI och registrera modeller som följer:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Utdata från varje kommando ger ett modell-ID som krävs i nästa steg. Spara dem i en textfil för framtida användning.

4. Skapa ett manifest för webbtjänsten.

   Ett manifest innehåller koden för webbtjänst, machine learning-modeller och runtime environment beroenden. Gå till den `Code` mapp i CLI och kör sedan följande kommando:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   Utdata ger ett manifest ID för nästa steg. 

   Stanna kvar i den `Code` directory, och du kan testa webservice.py genom att köra följande: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Skapa en Docker-avbildning. 

        az ml image create -n [unique]image --manifest-id $manifestID

   Utdata ger avbildnings-ID för nästa steg, docker avbildningen används i Container Service. 

6. Distribuera webbtjänsten till Container Service-kluster.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   Utdata ger ett service-ID. Du måste använda den för att hämta nyckel för auktorisering och URL-tjänsten.

7. Anropa webbtjänsten i Python-kod för att samla in i mini-grupper.

   Använd följande kommando för att hämta nyckel för auktorisering:

         az ml service keys realtime -i $ServiceID 

   Använder du följande kommando för att hämta tjänsten bedömningen URL:

        az ml service usage realtime -i $ServiceID

   Ändra innehållet i `./Config/webservice.json` med en tjänst bedömningen URL och auktorisering. Behåll ”innehavaren” i den ursprungliga filen och ersätta ”xxx”-delen. 
   
   Gå till rotkatalogen för ditt projekt och testa webbtjänsten för Mini batchbedömningsjobbet med hjälp av följande:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Skala webbtjänsten. 

   Mer information finns i [så här skalar operationalization i Azure Container Service-kluster](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md).
 

## <a name="next-steps"></a>Nästa steg

Det här exemplet illustrerar hur du använder Machine Learning-arbetsstationen för att träna machine learning-modell på stordata och använda den tränade modellen. I synnerhet du lärt dig hur du konfigurerar och använder olika beräkning mål och kör historiken för spårning av mätvärden och använda olika körs.

Du kan utöka kod för att utforska korsvalidering och hyper-parametern justering. Mer information om finjustering av korsvalidering och hyper-parametern finns [GitHub resursen](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Läs mer om tidsserier Prognosticering i [GitHub resursen](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
