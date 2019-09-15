---
title: Använda Caffe på Azure HDInsight Spark för distribuerad djup inlärning
description: Använd Caffe på Apache Spark för distribuerad djup inlärning i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: bb234e5b34bd8046c4e65d7cc6812cde0db3b5b2
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995638"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Använda Caffe på Azure HDInsight Spark för distribuerad djup inlärning


## <a name="introduction"></a>Introduktion

Djup inlärningen påverkar allt från hälso vård till transport till tillverkning med mera. Företag vänder sig till djup inlärning för att lösa hårda problem, t. ex. [bild klassificering](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [tal igenkänning](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), objekt igenkänning och maskin översättning. 

Det finns [många populära ramverk](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), inklusive [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano osv. [Caffe](https://caffe.berkeleyvision.org/) är en av de mest berömda som inte är symboliska (tvingande) neurala nätverks ramverk och används ofta i många områden, inklusive dator vision. Dessutom kombinerar [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) Caffe med Apache Spark, vilket innebär att djup inlärningen enkelt kan användas i ett befintligt Hadoop-kluster. Du kan använda djup inlärning tillsammans med Spark ETL-pipelines, minska systemets komplexitet och svars tid för fullständig lösnings inlärning.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) är ett moln Apache Hadoops erbjudande som ger optimerade analys kluster med öppen källkod för Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache KAFKA och ml-tjänster. HDInsight backas upp av ett service avtal på 99,9%. Var och en av dessa stor data tekniker och ISV-program kan enkelt distribueras som hanterade kluster med säkerhet och övervakning för företag.

Den här artikeln visar hur du installerar [Caffe på Spark](https://github.com/yahoo/CaffeOnSpark) för ett HDInsight-kluster. Den här artikeln använder också den inbyggda MNIST-demon för att visa hur du använder distribuerad djup inlärning med HDInsight Spark på CPU: er.

Det finns fyra steg för att utföra uppgiften:

1. Installera nödvändiga beroenden på alla noder
2. Bygg Caffe på Spark för HDInsight på Head-noden
3. Distribuera de nödvändiga biblioteken till alla arbetsnoder
4. Skapa en Caffe-modell och kör den på ett distribuerat sätt.

Eftersom HDInsight är en PaaS-lösning erbjuder den fantastiska plattforms funktioner – så det är enkelt att utföra vissa uppgifter. En av funktionerna som används i det här blogg inlägget kallas [skript åtgärd](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), som du kan använda för att anpassa klusternoder (Head-nod, arbetsnoden eller Edge-nod).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Steg 1:  Installera nödvändiga beroenden på alla noder

Du måste installera beroenden för att komma igång. Caffe-webbplatsen och [CaffeOnSpark-platsen](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) erbjuder en användbar wiki för att installera beroenden för Spark i garn läge. HDInsight använder också spark i garn läge. Du måste dock lägga till några fler beroenden för HDInsight-plattformen. Det gör du genom att använda en skript åtgärd och köra den på alla huvudnoder och arbetsnoder. Den här skript åtgärden tar ungefär 20 minuter eftersom dessa beroenden också är beroende av andra paket. Du bör placeras på en plats som är tillgänglig för ditt HDInsight-kluster, till exempel en GitHub plats eller standard kontot för BLOB Storage.

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


Det finns två steg i skript åtgärden. Det första steget är att installera alla bibliotek som krävs. Biblioteken innehåller de bibliotek som krävs för att både kompilera Caffe (till exempel gflags, Glog) och köra Caffe (till exempel numpy). du använder libatlas för CPU-optimering, men du kan alltid följa CaffeOnSpark-wikin för att installera andra optimerings bibliotek, till exempel MKL eller CUDA (för GPU).

Det andra steget är att ladda ned, kompilera och installera protobuf 2.5.0 för Caffe under körning. Protobuf 2.5.0 [krävs](https://github.com/yahoo/CaffeOnSpark/issues/87), men den här versionen är inte tillgänglig som ett paket på Ubuntu 16, så du måste kompilera den från käll koden. Det finns också några resurser på Internet om hur du kompilerar den. Mer information finns [här](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

För att komma igång kan du bara köra den här skript åtgärden mot klustret till alla arbetsnoder och huvudnoder (för HDInsight 3,5). Du kan antingen köra skript åtgärder i ett befintligt kluster eller använda skript åtgärder när klustret skapas. Mer information om skript åtgärder finns i dokumentationen [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Skript åtgärder för att installera beroenden](./media/apache-spark-deep-learning-caffe/submit-script-action.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Steg 2: Bygg Caffe på Apache Spark för HDInsight på Head-noden

Det andra steget är att bygga Caffe på huvudnoden och sedan distribuera de kompilerade biblioteken till alla arbetsnoder. I det här steget måste du [använda SSH i din huvudnoden](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Efter det måste du följa CaffeOnSpark- [build-processen](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Nedan finns ett skript som du kan använda för att bygga CaffeOnSpark med några ytterligare steg. 

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

Du kan behöva göra mer än vad dokumentationen för CaffeOnSpark säger. Ändringarna är:
- Ändra endast till CPU och Använd libatlas för det här särskilda ändamålet.
- Flytta data uppsättningarna till BLOB Storage, som är en delad plats som är tillgänglig för alla arbetsnoder för senare användning.
- Sätt samman de kompilerade Caffe-biblioteken till BLOB Storage, och senare kopierar dessa bibliotek till alla noder med skript åtgärder för att undvika ytterligare kompilering av tiden.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Telefonbaserad Ett ANT-BuildException har inträffat: exec returnerades: 2

Vid första försöket att bygga CaffeOnSpark, säger det ibland

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Rensa kod lagrings platsen genom att "Rensa" och kör sedan "skapa Build" för att lösa det här problemet, så länge du har rätt beroenden.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Telefonbaserad Tids gräns för anslutning av maven-lagringsplats

Ibland ger maven ett timeout-fel för anslutningen, som liknar följande kodfragment:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Du måste göra ett nytt försök efter några minuter.


### <a name="troubleshooting-test-failure-for-caffe"></a>Telefonbaserad Test haveri för Caffe

Du ser förmodligen ett test haveri när du utför den sista kontrollen av CaffeOnSpark. Detta är förmodligen relaterat till UTF-8-kodning, men bör inte påverka användningen av Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Steg 3: Distribuera de nödvändiga biblioteken till alla arbetsnoder

Nästa steg är att distribuera biblioteken (huvudsakligen biblioteken i CaffeOnSpark/Caffe-Public/distribution/lib/och CaffeOnSpark/Caffe-distri/distribuera/lib/) till alla noder. I steg 2 sätter du i dessa bibliotek på BLOB Storage, och i det här steget använder du skript åtgärder för att kopiera det till alla huvudnoder och arbetsnoder.

Det gör du genom att köra en skript åtgärd som visas i följande kodfragment:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Kontrol lera att du behöver peka på rätt plats som är speciell för ditt kluster)

Eftersom i steg 2 sätter du det på BLOB-lagringen, som är tillgänglig för alla noderna, i det här steget kopierar du bara det till alla noder.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Steg 4: Skapa en Caffe-modell och kör den på ett distribuerat sätt

Caffe installeras när du har kört föregående steg. Nästa steg är att skriva en Caffe-modell. 

Caffe använder en "lättfattliga programspecifika-arkitektur", där för att skapa en modell, behöver du bara definiera en konfigurations fil och utan kodning alls (i de flesta fall). Så låt oss ta en titt där. 

Den modell som du tränar är en exempel modell för MNIST-utbildning. MNIST-databasen med handskrivna siffror har en utbildnings uppsättning på 60 000 exempel och en test uppsättning med 10 000-exempel. Det är en del av en större uppsättning som är tillgänglig från NIST. Siffrorna har storleken-normaliserad och centreras i en bild med fast storlek. CaffeOnSpark har vissa skript för att ladda ned data uppsättningen och konvertera den till rätt format.

CaffeOnSpark innehåller några exempel på nätverkstopologier för MNIST-utbildning. Det har en bra utformning av delning av nätverks arkitekturen (nätverkets topologi) och optimering. I det här fallet finns det två filer som krävs: 

filen "problemlösa ren" ($ {CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) används för att Visa optimeringen och skapa parameter uppdateringar. Till exempel definierar den om CPU eller GPU används, vad är det som är det, hur många iterationer som är osv. Den definierar också vilken neuron-nätverkstopologi som ska användas (vilket är den andra filen du behöver). Mer information om problemlösa ren finns i [Caffe-dokumentationen](https://caffe.berkeleyvision.org/tutorial/solver.html).

I det här exemplet, eftersom du använder processor snarare än GPU, bör du ändra den sista raden till:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe Config1](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

Du kan ändra andra rader efter behov.

Den andra filen ($ {CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) definierar hur neuron-nätverket ser ut och den relevanta indata-och utdatafilen. Du måste också uppdatera filen för att avspegla utbildnings data platsen. Ändra följande del i lenet_memory_train_test. prototxt (du måste peka på rätt plats som är speciell för klustret):

- ändra "File:/Users/Mridul/bigml/demodl/mnist_train_lmdb" till "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- ändra "File:/Users/Mridul/bigml/demodl/mnist_test_lmdb/" till "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Caffe Config2](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

Mer information om hur du definierar nätverket finns i [Caffe-dokumentationen för MNIST data uppsättning](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)

I den här artikeln använder du det här MNIST-exemplet. Kör följande kommandon från Head-noden:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Föregående kommando distribuerar de filer som krävs (lenet_memory_solver. prototxt och lenet_memory_train_test. prototxt) till varje garn behållare. Kommandot anger också den relevanta sökvägen för varje spark-drivrutin/utförar till LD_LIBRARY_PATH. LD_LIBRARY_PATH definieras i föregående kodfragment och pekar på den plats som har CaffeOnSpark-bibliotek. 

## <a name="monitoring-and-troubleshooting"></a>Övervakning och fel sökning

Eftersom du använder ett garn kluster läge, kommer Spark-drivrutinen att schemaläggas till en godtycklig behållare (och en godtycklig arbetsnod) som du bara bör se i konsolen för att lägga till något som:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Om du vill veta vad som hände, behöver du vanligt vis hämta Spark-drivrutinens logg, som innehåller mer information. I så fall måste du gå till garn gränssnittet för att hitta relevanta garn loggar. Du kan hämta garn gränssnittet via denna URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![GARN GRÄNSSNITT](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

Du kan ta en titt på hur många resurser som allokeras för det aktuella programmet. Du kan klicka på länken "Scheduler". då kommer du att se att för det här programmet, finns det nio behållare som körs. du ber garn att tillhandahålla åtta körningar och en annan behållare är för driv rutins processen. 

![GARN Scheduler](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

Du kanske vill kontrol lera driv rutins loggarna eller container loggarna om det uppstår problem. För driv rutins loggar kan du klicka på program-ID i garn UI och sedan klicka på knappen loggar. Driv rutins loggarna skrivs in i stderr.

![GARN GRÄNSSNITT 2](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

Du kan till exempel se felet nedan från driv rutins loggarna, vilket indikerar att du allokerar för många körningar.

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

Ibland kan problemet uppstå i körningar i stället för driv rutiner. I så fall måste du kontrol lera behållar loggarna. Du kan alltid hämta behållar loggarna och sedan hämta den felande behållaren. Du kan till exempel möta det här felet när du kör Caffe.

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

I det här fallet måste du hämta ID för den misslyckade behållaren (i ovanstående fall är det container_1485916338528_0008_05_000005). Sedan måste du köra 

    yarn logs -containerId container_1485916338528_0008_03_000005

från huvudnoden. Efter kontroll av container haveri orsakas det av ett GPU-läge (där du bör använda CPU-läget i stället) i lenet_memory_solver. prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Hämtar resultat

Eftersom du allokerar 8 körningar och nätverk sto pol Ogin är enkel bör det bara ta cirka 30 minuter att köra resultatet. På kommando raden kan du se att du har försatt modellen till wasb:///mnist.model och att du anger resultatet i en mapp med namnet wasb:///mnist_features_result.

Du kan få resultaten genom att köra

    hadoop fs -cat hdfs:///mnist_features_result/*

och resultatet ser ut så här:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID representerar ID: t i MNIST-datauppsättningen och etiketten är talet som modellen identifierar.


## <a name="conclusion"></a>Sammanfattning

I den här dokumentationen har du försökt att installera CaffeOnSpark med ett enkelt exempel. HDInsight är en fullständigt hanterad beräknings plattform för molnet och är den bästa platsen för att köra maskin inlärnings-och avancerade analys arbets belastningar på stora data mängder och för distribuerad djup inlärning kan du använda Caffe på HDInsight Spark för att utföra djup inlärning uppgifter.


## <a name="seealso"></a>Se även
* [: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med Machine Learning: Använda spark i HDInsight för analys av bygg temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)

