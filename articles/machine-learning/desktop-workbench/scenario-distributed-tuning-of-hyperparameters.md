---
title: Distribuerad justering av Hyperparametrar med hjälp av Azure Machine Learning Workbench | Microsoft Docs
description: Det här scenariot visas hur du gör distribuerad justering av hyperparametrar med hjälp av Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 920b019640df9d2da174101e2b1b90dfd4da6f56
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578743"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Distribuerad justering av hyperparametrar med hjälp av Azure Machine Learning Workbench

Det här scenariot visar hur du använder Azure Machine Learning Workbench för att skala ut justering av hyperparametrar av machine learning-algoritmer som implementerar scikit-Läs API. Visar vi hur du konfigurerar och använder en fjärransluten Docker-behållare och Spark-kluster som en körning serverdel för justering av hyperparametrar.

## <a name="link-of-the-gallery-github-repository"></a>Länken till galleriet GitHub-lagringsplatsen
Följande är en länk till den offentliga GitHub-lagringsplatsen: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Översikt över användningsfall

Många maskininlärningsalgoritmer har en eller flera rattar, kallas hyperparametrar. Dessa rattar Tillåt justering av algoritmer för att optimera sina prestanda över framtida data, mäts enligt användardefinierade mått (till exempel Precision AUC, RMSE). Dataexpert måste ange värden av hyperparametrar när du skapar en modell med träningsdata och innan du ser framtida testdata. Hur baserat på kända utbildning data kan vi ställa in värdena för hyperparametrar så att modellen har en bra prestanda över okänd testdata? 
    
En populära teknik för justering av hyperparametrar är en *grid search* kombineras med *korsvalidering*. Korsvalidering är en teknik som bedömer hur väl en modell, som tränats på en träningsmängden förutsäger över testet. Med den här tekniken kan vi dela upp datauppsättningen i K vikningar först och sedan träna algoritmen K gånger under en resursallokering. Vi gör det på alla men en av vikningar som kallas ”hålls ut vikning”. Vi beräkna medelvärdet för mått för K modeller K hålls ut vikningar. Den här medelvärdet kallas *cross-verifierade prestanda uppskattning*, beror på värden av hyperparametrar som används när du skapar K modeller. När du anpassar hyperparametrar söka vi igenom området kandidat finjustering värden för att hitta de som optimerar prestanda för korsvalidering beräkna. Rutnätet search är en vanlig metod för sökning. I rutnätet search är utrymme för värden som kandidat i flera hyperparametrar kryssprodukten av uppsättningar kandidat värden av enskilda hyperparametrar. 

Rutnätet sökning med hjälp av korsvalidering kan ta lång tid. Om en algoritm har fem hyperparametrar varje med fem kandidat värden kan använda vi K = 5 gånger. Vi Slutför en grid sökning efter utbildning 5<sup>6</sup>= 15625 modeller. Som tur är kan grid-sökning med hjälp av korsvalidering är en embarrassingly parallel process och alla dessa modeller kan tränas parallellt.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
* En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) följande den [installera och skapa Quickstart](../service/quickstart-installation.md) installera Workbench och skapa konton.
* Det här scenariot förutsätter att du använder Azure ML Workbench på Windows 10- eller MacOS med Docker-motorn installerad lokalt. 
* Om du vill köra scenariot med en fjärransluten dockerbehållare, etablera Ubuntu Data Science Virtual Machine (DSVM) genom att följa den [instruktioner](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Vi rekommenderar att du använder en virtuell dator med minst 8 kärnor och 28 Gb minne. D4 instanser av virtuella datorer har denna kapacitet. 
* Om du vill köra det här scenariot med en Spark-kluster, etablera Spark HDInsight-kluster genom att följa dessa [instruktioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Vi rekommenderar att du har ett kluster med följande konfiguration i både huvud-och worker:
    - fyra arbetsnoder
    - åtta kärnor
    - 28 Gb minne  
      
  D4 instanser av virtuella datorer har denna kapacitet. 

     **Felsöka**: din Azure-prenumeration kan ha en kvot på antalet kärnor som kan användas. Azure-portalen tillåter inte skapandet av kluster med det totala antalet kärnor som överskrider kvoten. Du kvot finns i avsnittet prenumerationer Azure portal, klickar du på den prenumeration som används för att distribuera ett kluster och klicka sedan på **användning + kvoter**. Vanligtvis kvoter definieras per Azure-region och du kan välja att distribuera Spark-kluster i en region där du har tillräckligt många lediga kärnor. 

* Skapa ett Azure storage-konto som används för att lagra datauppsättningen. Följ den [instruktioner](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att skapa ett lagringskonto.

## <a name="data-description"></a>Databeskrivning

Vi använder [TalkingData datauppsättning](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Den här datauppsättningen innehåller händelser från appar i mobiltelefoner. Målet är att förutsäga kön och ålder användarkategori mobiltelefon beroende på vilken telefon och de händelser som genererade nyligen.  

## <a name="scenario-structure"></a>Scenario-struktur
Det här scenariot har flera mappar i GitHub-lagringsplatsen. Filer för kod och konfiguration finns i **kod** mappen all dokumentation finns i **Docs** mappen och alla avbildningar är **avbildningar** mapp. Rotmappen har README-filen som innehåller en kort sammanfattning av det här scenariot.

### <a name="getting-started"></a>Komma igång
Klicka på ikonen Azure Machine Learning Workbench för att köra Azure Machine Learning Workbench och skapar ett projekt med mallen ”distribuerad justering av Hyperparametrar”. Du kan hitta detaljerade anvisningar om hur du skapar ett nytt projekt i [installera snabbstarten och skapa](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Konfigurationen av körningsmiljöer
Vi visar hur du använder vår kod i en fjärransluten dockerbehållare och i ett Spark-kluster. Vi börjar med en beskrivning av de inställningar som är gemensamma för både miljöer. 

Vi använder [scikit-Läs](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), och [azure-storage](https://pypi.python.org/pypi/azure-storage) paket som inte ingår i standard Docker-behållaren i Azure Machine Learning Workbench. Azure storage-paketet kräver installation av [kryptografi](https://pypi.python.org/pypi/cryptography) och [azure](https://pypi.python.org/pypi/azure) paket. Om du vill installera dessa paket i Docker-behållaren och noderna i Spark-kluster, ändrar vi conda_dependencies.yml fil:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

Ändrade conda\_dependencies.yml filen lagras i katalogen aml_config i självstudien. 

I nästa steg ska ansluta vi körningsmiljö till Azure-konto. Klicka på Arkiv-menyn från det övre vänstra hörnet AML-arbetsstationen. Välj ”Öppna Kommandotolken”. Kör sedan i CLI

    az login

Du får ett meddelande

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Gå till den här sidan, ange koden och logga in på ditt Azure-konto. Efter det här steget körs i CLI

    az account list -o table

och hitta det ID för Azure-prenumeration som innehåller ditt AML Workbench Workspace-konto. Kör slutligen i CLI

    az account set -s <subscription ID>

att slutföra anslutningen till din Azure-prenumeration.

I följande två avsnitt visar vi hur du slutför konfigurationen av fjärranslutna docker- och Spark-kluster.

#### <a name="configuration-of-remote-docker-container"></a>Konfigurationen av fjärransluten Docker-behållare

 Om du vill konfigurera en fjärransluten dockerbehållare som körs i CLI

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

med IP-adress, användarnamn och lösenord i DSVM. IP-adressen för DSVM finns i översiktsavsnittet för din DSVM-sida i Azure-portalen:

![IP-adress för virtuell dator](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Konfiguration av Spark-kluster

Om du vill konfigurera Spark-miljö som körs i CLI

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

med namnet på klustret, klustrets SSH-användarnamn och lösenord. Standardvärdet för SSH-användarnamn är `sshuser`, om du inte ändrat det under etableringen av klustret. Namnet på klustret finns i avsnittet för egenskaper av kluster-sidan i Azure-portalen:

![Klusternamn](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Vi kan använda spark-sklearn paketet för att ha Spark som en körningsmiljö för distribuerad justering av hyperparametrar. Vi ändrade spark_dependencies.yml filen för att installera det här paketet när Spark körningsmiljö används:

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

Ändrade spark\_dependencies.yml filen lagras i katalogen aml_config i självstudien. 

### <a name="data-ingestion"></a>Datainhämtning
Koden i det här scenariot förutsätter att data lagras i Azure blob storage. Vi visar först hur du hämtar data från Kaggle plats på din dator och överföra den till blob-lagringen. Sedan visar vi hur du läser data från blob storage. 

För att hämta data från Kaggle, gå till [datauppsättning sidan](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) och klicka på knappen ladda ned. Du blir ombedd att logga in på Kaggle. När du har loggat in omdirigeras du tillbaka till sidan för datauppsättningen. Hämta först sju filerna i den vänstra kolumnen genom att markera dem och klicka på knappen ladda ned. Den totala storleken på de hämtade filerna är 289 Mb. Skapa blob storage-behållare 'dataset' om du vill överföra filerna till blob storage i ditt storage-konto. Du kan göra det genom att gå till Azure sida i ditt storage-konto, Blobbar och sedan klicka på + behållare. Ange 'dataset' som namn och klicka på OK. Följande skärmbilder visar de här stegen:

![Öppna blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![öppna behållare](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Efter det Välj datauppsättningen behållare i listan och klicka på knappen ladda upp. Azure-portalen kan du ladda upp flera filer samtidigt. Klicka på mappen under ”ladda upp blob”, markera alla filer från datauppsättningen, klicka på Öppna och klickar på överföring. Följande skärmbild visar de här stegen:

![Ladda upp blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Ladda upp filer tar flera minuter beroende på din Internet-anslutning. 

I vår kod använder vi [Azure Storage SDK](https://docs.microsoft.com/python/azure/) att hämta datauppsättningen från blob storage till den aktuella körningsmiljön. Hämtas i belastningen\_anropades funktionen från load_data.py-fil. Om du vill använda den här koden, du måste ersätta < kontonamn > och < ACCOUNT_KEY > med namnet och den primära nyckeln för ditt lagringskonto som är värd för datauppsättningen. Du kan se kontonamn i det övre vänstra hörnet av Azure sidan för ditt lagringskonto. För att hämta konto key, väljer åtkomstnycklar i Azure storage-sida (se första skärmbilden under datainmatning) och kopiera lång sträng i den första raden i nyckelkolumn:
 
![Åtkomstnyckel](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Följande kod från load_data() funktion hämtar en enskild fil:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Observera att du inte behöver köra load_data.py filen manuellt. Den anropas från andra filer vid ett senare tillfälle.

### <a name="feature-engineering"></a>Funktionstekniker
Koden för databehandling alla funktioner är i funktionen\_engineering.py fil. Du behöver inte köra feature_engineering.py filen manuellt. Senare på anropas den från andra filer.

Vi skapar flera funktionsuppsättningar:
* En frekvent kodning av märke och modell för mobiltelefonen (en\_frekvent\_brand_model funktionen)
* Bråkdel av händelser som genererats av användare i varje veckodag (veckodag\_hour_features funktionen)
* Bråkdel av händelser som genererats av användare i varje timme (veckodag\_hour_features funktionen)
* Bråkdel av händelser som genererats av användare i varje kombination av veckodag och timme (veckodag\_hour_features funktionen)
* Bråkdel av händelser som genererats av användare i varje app (en\_frekvent\_app_labels funktionen)
* Bråkdel av händelser som genererats av användare i varje app etikett (en\_frekvent\_app_labels funktionen)
* Bråkdel av händelser som genererats av användare i varje appkategori (text\_category_features funktionen)
* Indikatorn funktioner för appkategorier som används för att genereras händelser (en\_hot_category funktionen)

De här funktionerna blev INSPIRERAD av Kaggle kernel [en linjär modell på appar och etiketter](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Beräkning av dessa funktioner kräver mycket minne. Vi försökte inledningsvis compute funktioner i den lokala miljön med 16 GB RAM-minne. Vi kunde compute första fyra uppsättningar med funktioner, men tog emot ”slut på minne” fel vid beräkning femte funktionsuppsättningen. Beräkning av de fyra första funktionsuppsättningarna finns i singleVMsmall.py fil och det kan utföras i den lokala miljön genom att köra 

     az ml experiment submit -c local .\singleVMsmall.py   

CLI-fönstret.

Eftersom lokala miljön är för liten för databehandling alla egenskapsuppsättningar växlar vi till remote DSVM som har större minne. Körning i en DSVM görs i Docker-behållare som hanteras av AML-arbetsstationen. Med den här DSVM går det att beräkna alla funktioner och skapa modeller och finjustera hyperparametrar (se nästa avsnitt). singleVM.py filen har slutförts funktionen beräkning och modellering kod. I nästa avsnitt visar vi hur du kör singleVM.py i remote DSVM. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Justering av hyperparametrar med hjälp av remote DSVM
Vi använder [xgboost](https://anaconda.org/conda-forge/xgboost) implementering [1] av toning trädet öka. Vi använder också [scikit-Läs](http://scikit-learn.org/) paketet för att finjustera hyperparametrar av xgboost. Även om xgboost inte ingår i scikit-Läs paketet, den implementerar scikit-Läs API och kan därför användas tillsammans med finjustering justering funktioner i scikit-Läs. 

Xgboost har åtta hyperparametrar, beskrivs [här](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* delprov
* Mål  
 
Första gången vi använder fjärråtkomst DSVM och finjustera hyperparametrar från ett litet rutnät med kandidat värden:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Det här rutnätet har fyra kombinationer av värden av hyperparametrar. Vi använder 5-fold mellan validering, vilket resulterar i 4 x 5 = 20 körs av xgboost. För att mäta prestanda hos modeller, använder vi negativt log förlust mått. Följande kod hittar värden av hyperparametrar från rutnätet som optimerar validerade mellan negativa log förlust. Koden använder dessa värden för att träna den slutliga modellen över fullständig träningsmängden:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

När du har skapat modellen, kan vi spara resultaten av den finjustering av hyperparametrar. Vi använda loggning API AML Workbench för att spara de bästa värdena av hyperparametrar och motsvarande cross-verifierade uppskattning av negativa log förlust:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Vi kan också skapa sweeping_results.txt filen med flera verifierade, negativt log förluster av alla kombinationer av finjustering värden i rutnätet.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Den här filen lagras i en särskild. / matar ut directory. Senare visar vi hur du hämtar den.  

 Innan du kör singleVM.py i remote DSVM för första gången måste skapa vi en Docker-behållare det genom att köra 

    az ml experiment prepare -c dsvm

i windows för CLI. Skapa en Docker-behållaren tar flera minuter. Därefter kör vi singleVM.py i DSVM:

    az ml experiment submit -c dsvm .\singleVM.py

Det här kommandot har slutförts i 1 timme-38 minuter när DSVM har 8 kärnor och 28 Gb minne. Loggade värden visas i fönstret för Körningshistorik AML-arbetsstationen:

![Körningshistorik](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Som standard visas Körningshistorik fönster värden och diagram över första 1 – 2 loggade värden. Om du vill se en fullständig lista över de valda värdena av hyperparametrar, klickar du på ikonen för inställningar markeras med röd cirkel i föregående skärmbild. Välj hyperparametrar som ska visas i tabellen. Dessutom för att välja diagrammen som visas i den övre delen av fönstret kör tidigare, klicka på ikonen inställningen markeras med blå cirkeln och välj diagrammen i listan. 

De valda värdena av hyperparametrar kan också undersökas i fönstret Egenskaper för kör: 

![Egenskaper för körning](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

I det övre högra hörnet i fönstret Egenskaper för kör finns ett avsnitt utdatafilerna med en lista över alla filer som skapades i ”. \output' mappen. omfattande\_resultat.txt kan laddas ned därifrån genom att markera den och klicka på knappen ladda ned. sweeping_results.txt bör ha följande utdata:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Justering av hyperparametrar med hjälp av Spark-kluster
Vi kan använda Spark-kluster för att skala ut justering av hyperparametrar och använda större rutnätet. Vår nya grid är

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Det här rutnätet har 16 kombinationer av värden av hyperparametrar. Eftersom vi använder 5-fold mellan verifiering, vi kör xgboost 16 x 5 = 80 gånger.

scikit-Läs paketet inte har ett inbyggt stöd för justering av hyperparametrar med hjälp av Spark-kluster. Som tur är kan [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) paket från Databricks fyller luckan. Det här paketet innehåller GridSearchCV-funktion som har nästan samma API som GridSearchCV funktion i scikit-Läs. Att använda spark-sklearn och justera hyperparametrar med Spark måste vi skapa ett Spark-sammanhang

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Sedan ersätter vi 

    from sklearn.model_selection import GridSearchCV

med  

    from spark_sklearn import GridSearchCV

Även vi Ersätt anropet till GridSearchCV från scikit-Lär dig hur du det från spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Den sista kod för justering av hyperparametrar med Spark i distribueras\_sweep.py fil. Skillnaden mellan singleVM.py och distributed_sweep.py är i definitionen av rutnätet och ytterligare fyra rader med kod. Observera också att på grund av AML Workbench tjänster, loggning koden inte ändras när du ändrar körningsmiljö från fjärranslutna DSVM till Spark-kluster.

Innan du kör distributed_sweep.py i Spark-kluster för första gången, som vi behöver installera det Python-paket. Detta kan uppnås genom att köra 

    az ml experiment prepare -c spark

i windows för CLI. Den här installationen tar flera minuter. Därefter kör vi distributed_sweep.py i Spark-kluster:

    az ml experiment submit -c spark .\distributed_sweep.py

Det här kommandot har slutförts i 1 timme-6 minuter när Spark-kluster har 6 arbetsnoder med 28 Gb minne. Resultatet av finjustering justering kan nås i Azure Machine Learning Workbench på samma sätt som DSVM fjärrkörning. (dvs loggar, bästa värdena för hyperparametrar och sweeping_results.txt-fil)

### <a name="architecture-diagram"></a>Arkitekturdiagram

Följande diagram visar det totala arbetsflödet: ![arkitektur](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Sammanfattning 

I det här scenariot visade vi hur du använder Azure Machine Learning Workbench för att utföra justering av hyperparametrar i fjärranslutna virtuella datorer och Spark-kluster. Vi såg att Azure Machine Learning Workbench innehåller verktyg för enkel konfiguration av körningsmiljöer. Det gör också enkelt växla mellan dem. 

## <a name="references"></a>Referenser

[1] T. Hasselgren och C. Guestrin. [XGBoost: Ett skalbart träd Boosting System](https://arxiv.org/abs/1603.02754). KDD 2016.




