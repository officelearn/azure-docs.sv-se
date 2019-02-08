---
title: Använda Caffe på Azure HDInsight Spark för distribuerade djupinlärning
description: Använda Caffe på Azure HDInsight Spark för distribuerade djupinlärning
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: c79f840becce43c47287ef38bd39ed3ac9168b73
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891087"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Använda Caffe på Azure HDInsight Spark för distribuerade djupinlärning


## <a name="introduction"></a>Introduktion

Djupinlärning påverkar allt från healthcare att transport till tillverkning och mycket mer. Företag byter till deep learning för att lösa tuffa problem som [bildklassificering](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [taligenkänning](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html)objekt erkännande och datorn översättning. 

Det finns [många populära ramverk](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), inklusive [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, osv. [Caffe](https://caffe.berkeleyvision.org/) är en av de mest berömda ramverken med icke-symboliska (viktigt) neuralt nätverk, och som ofta används i många områden, till exempel visuellt innehåll. Dessutom [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) kombinerar Caffe med Apache Spark, i vilket fall deep learning enkelt kan användas i ett befintligt Hadoop-kluster. Du kan använda djupinlärning tillsammans med Spark ETL-pipelines, minskar komplexiteten för system och svarstid för komplett lösning learning.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) är ett moln Apache Hadoop erbjudande som tillhandahåller optimerade analyskluster med öppen källkod för Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka och ML-tjänster. HDInsight backas upp av ett serviceavtal på 99,9%. Var och en av dessa tekniker för stordata och ISV-program är enkelt distribueras som hanterade kluster med säkerhet och övervakning för företag.

Den här artikeln visar hur du installerar [Caffe på Spark](https://github.com/yahoo/CaffeOnSpark) för ett HDInsight-kluster. Den här artikeln använder också inbyggda MNIST-demo för att visa hur du använder distribuerade Deep Learning med HDInsight Spark på processorer.

Det finns fyra steg att utföra uppgiften:

1. Installera de nödvändiga beroendena på alla noder
2. Skapa Caffe på Spark för HDInsight på huvudnoden
3. Distribuera bibliotek som krävs för att alla arbetsnoder
4. Skapa en modell för Caffe och kör den på ett sätt som är distribuerad.

Eftersom HDInsight är en PaaS-lösning, erbjuder bra plattformsfunktioner - så att det är enkelt att utföra vissa uppgifter. En av de funktioner som används i det här blogginlägget kallas [skriptåtgärd](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), som du kan köra shell-kommandon för att anpassa klusternoder (huvudnoden, arbetsnod eller kantnod).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Steg 1:  Installera de nödvändiga beroendena på alla noder

Du måste installera beroendena för att komma igång. Webbplatsen Caffe och [CaffeOnSpark plats](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) erbjuder vissa användbara wiki för att installera beroenden för Spark på YARN-läge. HDInsight använder också Spark YARN-läge. Men måste du lägga till några fler beroenden för HDInsight-plattformen. Gör du genom att använda en skriptåtgärd och köra den på alla huvudnoderna och arbetsnoder. Den här skriptåtgärden tar cirka 20 minuter, som dessa beroenden beror också på andra paket. Du bör placera den på en plats som är tillgänglig för din HDInsight-klustret, till exempel en GitHub-plats eller BLOB storage-kontot.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


Det finns två steg i skriptåtgärden. Det första steget är att installera bibliotek som krävs. Dessa bibliotek innehåller nödvändiga bibliotek för både kompilera Caffe (till exempel gflags glog) och köra Caffe (till exempel numpy). du använder libatlas för optimering av CPU, men du kan alltid följa CaffeOnSpark wiki om hur du installerar andra optimering bibliotek, till exempel MKL eller CUDA (för GPU).

Det andra steget är att hämta, kompilera och installera protobuf 2.5.0 för Caffe under körning. Protobuf 2.5.0 [krävs](https://github.com/yahoo/CaffeOnSpark/issues/87), men den här versionen inte är tillgänglig som ett paket på Ubuntu 16, så du behöver att kompilera från källkoden. Det finns också några resurser på Internet om hur du kompilera den. Mer information finns i [här](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

För att komma igång, kan du bara köra den här skriptåtgärden mot ditt kluster till alla arbetsnoder och huvudnoder (för HDInsight 3.5). Du kan köra åtgärderna som skript i ett befintligt kluster, eller använda skriptåtgärder när klustret skapas. Mer information om åtgärderna som skript finns i dokumentationen [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Skriptåtgärder för att installera beroenden](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Steg 2: Skapa Caffe på Apache Spark för HDInsight på huvudnoden

Det andra steget är att skapa Caffe på huvudnoden och sedan distribuera de kompilerade bibliotek till alla arbetsnoder. I det här steget måste du [ssh till din huvudnoden](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Sedan kan du följa den [CaffeOnSpark genereringsprocessen](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Nedan visas de skript som du kan använda för att skapa CaffeOnSpark med några ytterligare steg. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Du kan behöva göra mer än vad som står i dokumentationen för CaffeOnSpark. Ändringarna är:
- Ändra till CPU endast och Använd libatlas för den här särskilt ändamål.
- Våra datauppsättningarna BLOB-lagring, vilket är en delad plats som är tillgänglig för alla arbetsnoder för senare användning.
- Placera de kompilerade Caffe-bibliotek till BLOB storage och senare ska du kopiera dessa bibliotek för alla noder som använder skriptåtgärder för att undvika ytterligare kompileringstid.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Felsökning: En Ant BuildException har inträffat: exec returneras: 2

När du först försöker skapa CaffeOnSpark, ibland status är Stopped

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Rensa kodlagringsplatsen av ”gör ren” och sedan kör ”gör skapa” Lös problemet, så länge som du har rätt beroenden.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Felsökning: Maven lagringsplats anslutnings-timeout

Ibland ger maven ett timeout anslutningsfel, liknar följande fragment:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Du måste försöka igen efter några minuter.


### <a name="troubleshooting-test-failure-for-caffe"></a>Felsökning: Test misslyckades för Caffe

Du ser förmodligen ett testfel när du gör den sista kontrollen för CaffeOnSpark. Detta beror förmodligen med UTF-8-kodning, men bör inte påverka användningen av Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Steg 3: Distribuera bibliotek som krävs för att alla arbetsnoder

Nästa steg är att distribuera bibliotek (i praktiken biblioteken i CaffeOnSpark/caffe-offentliga/distribuera/lib/och CaffeOnSpark/caffe-distri/distribuera/lib /) till alla noder. Du placerar dessa bibliotek på BLOB-lagring i steg 2, och i det här steget ska du använda skriptåtgärder för att kopiera den till alla huvudnoderna och arbetsnoder.

Om du vill göra detta kör du en skriptåtgärd som visas i följande kodavsnitt:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Kontrollera att du behöver pekar på rätt plats specifika till ditt kluster)

Eftersom i steg 2, du placera den i BLOB-lagring, som är tillgängliga på alla noder, i det här steget kopiera du bara den till alla noder.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Steg 4: Skapa en modell för Caffe och kör den på en distribuerad sätt

Caffe installeras när du har kört föregående steg. Nästa steg är att skriva en Caffe-modell. 

Caffe med hjälp av en ”lättfattliga arkitektur”, där för att skapa en modell, behöver du bara ange en konfigurationsfil, och utan kodning alls (i de flesta fall). Så Låt oss ta en titt dit. 

Den modell som du tränar är en exempelmodell för MNIST-utbildning. MNIST-databasen med handritade siffror har en uppsättning 60 000 exempel utbildning och en uppsättning 10 000 exempel test. Det är en del av en större grupp som är tillgängliga från NIST. Siffrorna har normaliserade storleken och centrerad i en avbildning av en fast storlek. CaffeOnSpark har vissa skript för att hämta datauppsättningen och omvandla dem till rätt format.

CaffeOnSpark innehåller några exempel på nätverket topologier för MNIST-utbildning. Tjänsten har en bra design av dela nätverksarkitekturen (topologin för nätverket) och optimering. Det finns i det här fallet två filer som krävs: 

filen ”Solver” (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) används för att övervaka optimeringen och generera parametern uppdateringar. Till exempel den definierar om CPU- eller GPU används, vad är dynamiska, hur många iterationer är osv. Den definierar även vilka neuron nätverkstopologi bör programmet använda (som är den andra filen som du behöver). Läs mer om Solver [Caffe dokumentation](https://caffe.berkeleyvision.org/tutorial/solver.html).

Eftersom du använder CPU i stället för GPU, i det här exemplet ska du ändra den sista raden till:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe Config](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

Du kan ändra andra rader efter behov.

Den andra filen (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) definierar hur neuron nätverket ser ut och relevanta indata och utdatafilen. Du måste också uppdatera filen för att återspegla Dataplats utbildning. Ändra följande del i lenet_memory_train_test.prototxt (du måste pekar på rätt plats som är specifika för ditt kluster):

- Ändra ”file:/Users/mridul/bigml/demodl/mnist_train_lmdb” till ”wasb: / / / projekt/machine_learning/image_dataset/mnist_train_lmdb”
- change "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" to "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Caffe Config](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Mer information om hur du definierar nätverket finns i [Caffe-dokumentationen på MNIST datauppsättning](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)

I den här artikeln kan du använda det här MNIST-exemplet. Kör följande kommandon från huvudnoden:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Föregående kommando distribuerar de nödvändiga filerna (lenet_memory_solver.prototxt och lenet_memory_train_test.prototxt) till varje YARN-behållare. Kommandot anger även den relevanta SÖKVÄGEN för varje Spark-drivrutinen/executor till LD_LIBRARY_PATH. LD_LIBRARY_PATH har definierats i föregående kodfragment och pekar på platsen som har CaffeOnSpark bibliotek. 

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning

Eftersom du använder YARN klusterläge kan i så fall kommer att schemaläggas med Spark-drivrutinen till en valfri behållare (och en valfri arbetsnod) bör du endast se i konsolen mata ut ungefär som:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Om du vill se vad som hände behöver du vanligtvis få Spark-drivrutinens logg som innehåller mer information. I det här fallet måste du gå till YARN-Användargränssnittet för att hitta relevant YARN-loggarna. Du kan hämta YARN-Användargränssnittet av denna URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![YARN-ANVÄNDARGRÄNSSNITTET](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Du kan ta en titt på hur många resurser ska allokeras till det aktuella programmet. Du kan klicka på länken ”Scheduler” och sedan visas att för det här programmet, det finns nio behållare som körs. du be YARN att tillhandahålla åtta executors och en annan behållare är för drivrutinen processen. 

![YARN Scheduler](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Du kanske vill kontrollera drivrutinsloggar eller behållarloggarna om det uppstår fel. För drivrutinsloggar kan du klicka på program-ID i YARN-Användargränssnittet och sedan klickar du på knappen ”loggar”. Drivrutinen loggarna skrivs till stderr.

![YARN-ANVÄNDARGRÄNSSNITTET 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Exempelvis kan kan du se några av felet nedan från drivrutinsloggar som anger du allokera för många executors.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Ibland kan kan problemet inträffa i executors i stället för drivrutiner. I det här fallet behöver du kontrollera behållarloggarna. Du kan alltid hämta behållarloggarna och får sedan den misslyckade behållaren. Du kan till exempel uppfylla det här felet när du kör Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

I detta fall använder du behöver misslyckade behållar-ID (i det senare fallet är det container_1485916338528_0008_05_000005). Sedan måste du köra 

    yarn logs -containerId container_1485916338528_0008_03_000005

från huvudnoden. När du har kontrollerat behållare fel orsakade den med hjälp av GPU-läge (där du ska använda CPU-läget i stället) i lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Hämtar resultat

Eftersom du fördelar 8 executors och nätverkets topologi är enkel, bör det endast ta upp till 30 minuter för att köra resultatet. Från kommandoraden, du kan se att du placerar modellen till wasb:///mnist.model och placera resultatet i en mapp med namnet wasb: / / / mnist_features_result.

Du kan få resultaten genom att köra

    hadoop fs -cat hdfs:///mnist_features_result/*

och resultatet ser ut:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID representerar ID: T i MNIST-datauppsättning och etiketten är det nummer som identifierar modellen.


## <a name="conclusion"></a>Sammanfattning

Du har försökt installera CaffeOnSpark med ett enkelt exempel i den här dokumentationen. HDInsight är en fullständigt hanterad distribuerad beräkning molnplattform och är den bästa platsen för att köra maskininlärning och avancerad analysarbetsbelastningar på stor datauppsättning och för distribuerade djupinlärning, kan du använda Caffe i HDInsight Spark för djupinlärning uppgifter.


## <a name="seealso"></a>Se även
* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)

