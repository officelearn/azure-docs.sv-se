---
title: Distribuerad justering av Justeringsmodeller med hjälp av Azure Machine Learning arbetsstationen | Microsoft Docs
description: Det här scenariot visas hur du gör distribuerade finjustering av justeringsmodeller med hjälp av Azure Machine Learning arbetsstationen
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 70baa1160e9f521ac5533eaa15d4a9ab57433a49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Distribuerad justering av justeringsmodeller med hjälp av Azure Machine Learning arbetsstationen

Det här scenariot visas hur du använder Azure Machine Learning-arbetsstationen för att skala ut finjustering av justeringsmodeller av maskininlärningsalgoritmer som implementerar scikit-Läs API. Visar vi hur du konfigurerar och använder en fjärransluten dockerbehållare och Spark-kluster som en körning serverdel för att finjustera justeringsmodeller.

## <a name="link-of-the-gallery-github-repository"></a>Länk för galleriet GitHub-lagringsplats
Följande är en länk till den offentliga GitHub-lagringsplatsen: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Använd case-översikt

Många maskininlärningsalgoritmer har en eller flera rattar kallas justeringsmodeller. Dessa rattar Tillåt finjustering av algoritmer för att optimera prestanda över framtida data som mäts enligt användardefinierade mått (till exempel Precision, AUC, RMSE). Data forskare måste ange värden för justeringsmodeller när du skapar en modell över träningsdata och innan du ser framtida testdata. Hur baserat på kända utbildning data kan vi ställa in värdena för justeringsmodeller så att modellen har goda prestanda över okänd testdata? 
    
En populär teknik för att finjustera justeringsmodeller är en *rutnätet Sök* kombineras med *korsvalidering*. Korsvalidering är en teknik som utvärderar hur väl en modell med en träningsmängden beräknar över test. Med den här tekniken kan vi dela först datauppsättningen i K vikningar och träna algoritmen K gånger på ett sätt för resursallokering. Vi kan göra detta på alla men en av vikningar som kallas ”vikning hålls out”. Vi beräkna medelvärdet av mätvärden för K modeller K lagras utanför vikningar. Den här medelvärdet kallas *cross-validerade prestanda uppskattning*, är beroende av värdena för justeringsmodeller som används när du skapar K modeller. När inställningen för justeringsmodeller, söka vi igenom utrymme för kandidat hyperparameter värden för att hitta de som optimerar prestanda för korsvalidering beräkna. Rutnätet search är en vanlig metod för sökning. I rutnätet sökningen är utrymme för värden som kandidat i flera justeringsmodeller kryssprodukten av mängder av kandidat värden för enskilda justeringsmodeller. 

Rutnätet sökning med korsvalidering kan ta lång tid. Om en algoritm har fem justeringsmodeller varje med fem kandidat värden kan använda vi K = 5 gånger. Vi Slutför en rutnätet sökning efter utbildning 5<sup>6</sup>= 15625 modeller. Lyckligtvis rutnätet Sök med hjälp av korsvalidering är en embarrassingly parallella process och alla modeller kan exempelvis tränas parallellt.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
* En installerad kopia av [Azure Machine Learning arbetsstationen](../service/overview-what-is-azure-ml.md) följande den [installera och skapa Quickstart](../service/quickstart-installation.md) att installera arbetsstationen och skapa konton.
* Det här scenariot förutsätter att du använder Azure ML-arbetsstationen på Windows 10 eller MacOS med Docker-motorn har installerats lokalt. 
* Om du vill köra ett scenario med en fjärransluten dockerbehållare etablera Ubuntu Data vetenskap virtuell dator (DSVM) genom att följa den [instruktioner](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Vi rekommenderar att du använder en virtuell dator med minst 8 kärnor och 28 Gb minne. D4 instanser av virtuella datorer har denna kapacitet. 
* Om du vill köra det här scenariot med ett Spark-kluster, etablera HDInsight Spark-kluster genom att följa dessa [instruktioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Vi rekommenderar att du har ett kluster med följande konfiguration i huvud- och arbetsroller noder:
    - fyra arbetsnoder
    - åtta kärnor
    - 28 Gb minne  
      
  D4 instanser av virtuella datorer har denna kapacitet. 

     **Felsökning av**: din Azure-prenumeration kan ha en kvot på antal kärnor som kan användas. Azure-portalen tillåter inte att skapa klustret med det totala antalet kärnor överskrider kvoten. Du kvoten finns i avsnittet prenumerationer Azure portal, klickar du på den prenumeration som används för att distribuera ett kluster och klicka sedan på **användning + kvoter**. Vanligtvis kvoter definieras per Azure-region och du kan välja att distribuera Spark-kluster i en region där du har tillräckligt med ledigt kärnor. 

* Skapa ett Azure storage-konto som används för att lagra dataset. Följ den [instruktioner](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att skapa ett lagringskonto.

## <a name="data-description"></a>Beskrivning av data

Vi använder [TalkingData dataset](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Den här datauppsättningen har händelser från appar i mobiltelefoner. Målet är att förutsäga kön och ålder kategori av mobiltelefon som användaren angett typ av telefon och händelserna som användaren nyligen skapade.  

## <a name="scenario-structure"></a>Scenario-struktur
Det här scenariot har flera mappar i GitHub-lagringsplatsen. Koden och konfigurationsfiler som finns i **kod** mappen all dokumentation finns i **dokument** mapp och alla bilder är **bilder** mapp. Rotmappen har README-filen som innehåller en kort sammanfattning av det här scenariot.

### <a name="getting-started"></a>Komma igång
Klicka på ikonen Azure Machine Learning-arbetsstationen för att köra Azure Machine Learning arbetsstationen och skapa ett projekt med mallen ”distribuerade finjustering av Justeringsmodeller”. Du kan hitta detaljerade anvisningar om hur du skapar ett nytt projekt i [installera och skapa Quickstart](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Konfigurationen av körningen miljöer
Visar vi hur du kör vår kod i en fjärransluten dockerbehållare och ett Spark-kluster. Vi börjar med en beskrivning av de inställningar som är gemensamma för båda miljöerna. 

Vi använder [scikit-Läs](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), och [azure storage](https://pypi.python.org/pypi/azure-storage) paket som inte ingår i Docker standardbehållaren Azure Machine Learning-arbetsstationen. Azure storage-paketet måste du installera [kryptografi](https://pypi.python.org/pypi/cryptography) och [azure](https://pypi.python.org/pypi/azure) paket. Om du vill installera dessa paket i Docker-behållaren och noderna i Spark-kluster, ändrar vi conda_dependencies.yml fil:

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

Ändrade conda\_dependencies.yml filen lagras i katalogen aml_config i självstudiekursen. 

I nästa steg ska ansluta vi körningsmiljön på Azure-konto. Klicka på Arkiv-menyn från det övre vänstra hörnet AML arbetsstationen. Välj ”öppnar du Kommandotolken”. Kör i CLI

    az login

Du får ett meddelande

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Gå till den här webbsidan ange koden och logga in på ditt Azure-konto. När det här steget Kör i CLI

    az account list -o table

och hitta Azure prenumerations-ID som har kontot AML arbetsstationen arbetsytan. Slutligen körs i CLI

    az account set -s <subscription ID>

att slutföra anslutningen till din Azure-prenumeration.

I följande två avsnitt visar vi hur du slutför konfigurationen av fjärråtkomst docker och Spark-kluster.

#### <a name="configuration-of-remote-docker-container"></a>Konfiguration av fjärråtkomst dockerbehållare

 Om du vill konfigurera en fjärransluten dockerbehållare köras i CLI

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

med IP-adress, användarnamn och lösenord i DSVM. IP-adressen för DSVM finns i avsnittet översikt på sidan DSVM i Azure-portalen:

![VIRTUELLA IP](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Konfigurationen av Spark-kluster

Om du vill konfigurera Spark miljö körs i CLI

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

med namnet på klustret, klustrets SSH-användarnamn och lösenord. Standardvärdet för SSH-användarnamn är `sshuser`, såvida inte du har ändrat under etablering av klustret. Namnet på klustret finns i avsnittet Egenskaper på sidan kluster i Azure-portalen:

![Klusternamn](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Vi kan använda spark sklearn paketet för att ha Spark som en körningsmiljö för distribuerade justering av justeringsmodeller. Vi har ändrat spark_dependencies.yml fil om du vill installera det här paketet när Spark körningsmiljö används:

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

Ändrade spark\_dependencies.yml filen lagras i katalogen aml_config i självstudiekursen. 

### <a name="data-ingestion"></a>Datainhämtning
Koden i det här scenariot förutsätter att data lagras i Azure blob storage. Först beskrivs hur du hämtar data från Kaggle platsen till din dator och överföra den till blob-lagring. Sedan visar vi hur du läsa data från blob storage. 

Om du vill hämta data från Kaggle, gå till [dataset sidan](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) och klicka på Hämta. Du blir ombedd att logga in på Kaggle. När du loggar in, omdirigeras du tillbaka till sidan för datauppsättningen. Hämta sju första filerna i den vänstra kolumnen genom att markera dem och klicka på hämtningsknappen. Den totala storleken på de hämta filerna är 289 Mb. Skapa blob storage-behållare 'dataset' överför filerna till blob-lagring i ditt lagringskonto. Du kan göra det genom att gå till Azure sida i ditt lagringskonto, Blobbar och sedan klicka på + behållare. Ange 'dataset' som namn på och klicka på OK. Följande skärmdumpar visar de här stegen:

![Öppna blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![öppna behållare](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Efter det markerar dataset-behållaren i listan och klicka på knappen Skicka. Azure-portalen kan du överföra flera filer samtidigt. I avsnittet ”överför blob” klickar du på mappknappen markerar du alla filer i DataSet, klicka på Öppna, och klicka sedan på Överför. Följande skärmbild visar de här stegen:

![Överför blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Överför filer tar flera minuter beroende på din Internet-anslutning. 

I vår kod vi använder [Azure Storage SDK: N](https://azure-storage.readthedocs.io/en/latest/) att ladda ned datauppsättningen från blob storage till den aktuella körningsmiljön. Hämtas i belastning\_anropades funktion från load_data.py-filen. Om du vill använda den här koden, måste du ersätta < kontonamn > och < ACCOUNT_KEY > med namnet och primärnyckel för ditt lagringskonto som är värd för datauppsättningen. Du kan se namnet på kontot i det övre vänstra hörnet av Azure storage-konto-sidan. För att hämta kontot nyckel, Välj snabbtangenter i Azure sidan av lagring (se första skärmbilden under Datapåfyllning) och kopiera lång sträng i den första raden i nyckelkolumn:
 
![Snabbtangent](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Följande kod från load_data() funktionen laddar ned en fil:

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
Koden för alla funktioner finns i funktionen\_engineering.py fil. Du behöver inte köra feature_engineering.py filen manuellt. Vid ett senare tillfälle anropas från andra filer.

Vi skapar flera funktionsuppsättningar:
* En hot kodning av märke och modell för mobiltelefonen (en\_varm\_brand_model funktionen)
* Andel av händelser som genererats av användare i varje veckodag (veckodag\_hour_features funktionen)
* Andel av händelser som genererats av användaren på varje timme (veckodag\_hour_features funktionen)
* Andel av händelser som genererats av användare i varje kombination av veckodag och timme (veckodag\_hour_features funktionen)
* Andel av händelser som genererats av användare i varje app (en\_varm\_app_labels funktionen)
* Andel av händelser som genererats av användare i varje app etikett (en\_varm\_app_labels funktionen)
* Andel av händelser som genererats av användare i varje kategori app (text\_category_features funktionen)
* Indikator funktioner för kategorier av appar som brukar genereras händelser (en\_hot_category funktionen)

De här funktionerna har inspirerat av Kaggle kernel [linjär modell på appar och etiketter](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Beräkningen av dessa funktioner kräver mycket minne. Först ett försök gjordes att beräkna funktioner i den lokala miljön med 16 GB RAM-minne. Vi har kunnat compute första fyra uppsättningar med funktioner, men 'slut på minne-fel uppstod när funktionsuppsättningen femte. Beräkning av de fyra första funktionen som finns i singleVMsmall.py fil och det kan utföras i den lokala miljön genom att köra 

     az ml experiment submit -c local .\singleVMsmall.py   

i fönstret CLI.

Eftersom lokala miljön är för liten för att beräkna alla egenskapsuppsättningar växlar vi till remote DSVM som har större minne. Körning i DSVM görs i dockerbehållare som hanteras av AML arbetsstationen. Med den här DSVM kommer du att beräkna alla funktioner och träna modeller och finjustera justeringsmodeller (se nästa avsnitt). singleVM.py filen har slutförts funktionen beräknings- och modellering kod. I nästa avsnitt visar vi hur du kör singleVM.py i fjärranslutna DSVM. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Justera justeringsmodeller med fjärråtkomst DSVM
Vi använder [xgboost](https://anaconda.org/conda-forge/xgboost) av toning trädet förstärkning implementering [1]. Vi använder också [scikit-Läs](http://scikit-learn.org/) paket att justera justeringsmodeller av xgboost. Även om xgboost inte är en del av scikit-Läs paketet, den implementerar scikit-Läs API och kan därför användas tillsammans med hyperparameter justera funktioner i scikit-Läs. 

Xgboost har åtta justeringsmodeller, beskrivs [här](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* delprov
* Mål  
 
Från början vi använda remote DSVM och finjustera justeringsmodeller från ett litet rutnät av möjliga värden:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Det här rutnätet har fyra kombinationer av värden för justeringsmodeller. Vi använder 5-fold mellan verifiering, vilket resulterar i 4 x 5 = 20 körs av xgboost. Vi använder negativt loggen förlust mått för att mäta prestanda hos modeller. Följande kod hittar värdena för justeringsmodeller från rutnätet som maximera cross-validerade negativt logg går förlorade. Dessa värden används också för att träna modellen slutliga över fullständig träningsmängden:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Efter att modellen kan spara vi resultaten av hyperparameter justering. Vi använder loggning API AML arbetsstationen för att spara bästa värdena för justeringsmodeller och motsvarande cross-validerade uppskattning av negativt loggen förlust:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Vi kan också skapa sweeping_results.txt filen med cross-validerade, negativt loggen förlust av alla kombinationer av hyperparameter värden i rutnätet.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Den här filen lagras i en särskild. / matar ut directory. Vid ett senare tillfälle visar vi hur du hämtar den.  

 Innan du kör singleVM.py i fjärranslutna DSVM för första gången ska skapa vi en dockerbehållare genom att köra 

    az ml experiment prepare -c dsvm

i windows CLI. Skapande av dockerbehållare tar flera minuter. Därefter kör vi singleVM.py i DSVM:

    az ml experiment submit -c dsvm .\singleVM.py

Det här kommandot har slutförts i 1 timme-38 minuter när DSVM har 8 kärnor och 28 Gb minne. Loggade värden visas i fönstret kör tidigare AML arbetsstationen:

![Kör historik](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Som standard visar Körningshistorik fönster värdena och diagram över första 1 – 2 loggade värden. Om du vill se en fullständig lista över de valda värdena för justeringsmodeller, klicka på ikonen för inställningar markeras med röd cirkel i föregående skärmbild. Markera justeringsmodeller som ska visas i tabellen. Även om du vill välja diagrammen som visas i den övre delen av fönstret kör tidigare klickar du på ikonen inställning som markerats med blå cirkel och välj diagrammen i listan. 

De valda värdena för justeringsmodeller undersökte även i fönstret Egenskaper för kör: 

![Kör egenskaper](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

I det övre högra hörnet i fönstret Egenskaper för kör finns ett avsnitt utdatafilerna med listan över alla filer som har skapats i '. \output-mappen. omfattande\_resultat.txt kan hämtas därifrån genom att markera den och klicka på hämtningsknappen. sweeping_results.txt bör ha följande utdata:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Justera justeringsmodeller med Spark-kluster
Vi använder Spark-kluster för att skala ut justera justeringsmodeller och använda större rutnätet. Vår nya rutnätet

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Det här rutnätet har 16 kombinationer av värden för justeringsmodeller. Eftersom vi använder 5-fold mellan verifiering, vi kör xgboost 16 × 5 = 80 gånger.

scikit-Läs paketet inte har inbyggt stöd för att justera justeringsmodeller med Spark-kluster. Lyckligtvis [spark sklearn](https://spark-packages.org/package/databricks/spark-sklearn) paket från Databricks fyller luckan. Det här paketet innehåller GridSearchCV-funktionen med nästan samma API som GridSearchCV funktion i scikit-Läs. Om du vill använda spark sklearn och finjustera justeringsmodeller med Spark måste vi skapa ett Spark-kontexten

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Vi Ersätt 

    from sklearn.model_selection import GridSearchCV

med 

    from spark_sklearn import GridSearchCV

Även vi ersätta anropet till GridSearchCV från scikit – Lär dig hur du från spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Den slutliga kod för att finjustera justeringsmodeller med Spark i distribueras\_sweep.py fil. Skillnaden mellan singleVM.py och distributed_sweep.py är i definitionen av rutnät och ytterligare fyra rader med kod. Observera också att på grund av AML arbetsstationen services loggning koden inte ändras när du ändrar körningsmiljö från fjärranslutna DSVM till Spark-kluster.

Innan du kör distributed_sweep.py i Spark-kluster för första gången, som vi behöver installera det Python-paket. Detta kan uppnås genom att köra 

    az ml experiment prepare -c spark

i windows CLI. Den här installationen tar flera minuter. Därefter kör vi distributed_sweep.py i Spark-kluster:

    az ml experiment submit -c spark .\distributed_sweep.py

Det här kommandot har slutförts i 1 timme-6 minuter när 6 arbetarnoder med 28 Gb minne har i Spark-kluster. Resultaten av hyperparameter justering kan nås i Azure Machine Learning-arbetsstationen på samma sätt som DSVM fjärrkörning. (dvs loggar, bästa värdena för justeringsmodeller och sweeping_results.txt-fil)

### <a name="architecture-diagram"></a>Arkitekturdiagram

Följande diagram visar det totala arbetsflödet: ![arkitektur](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Sammanfattning 

I det här scenariot visade vi hur du använder Azure Machine Learning-arbetsstationen för att utföra finjustering av justeringsmodeller i fjärranslutna virtuella datorer och Spark-kluster. Vi såg att Azure Machine Learning arbetsstationen innehåller verktyg för enkel konfiguration av körningen miljöer. Det gör också enkelt växla mellan dem. 

## <a name="references"></a>Referenser

[1] T. Hasselgren och C. Guestrin. [XGBoost: Ett skalbart träd förstärkning System](https://arxiv.org/abs/1603.02754). KDD 2016.




