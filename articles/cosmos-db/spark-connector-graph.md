---
title: "Azure Cosmos DB: Utföra analyser av diagram med hjälp av Spark och Apache TinkerPop Gremlin | Microsoft Docs"
description: "Den här artikeln innehåller anvisningar för att installera och köra diagrammet analytics och parallella beräkning i Azure Cosmos-databasen med Spark och TinkerPop SparkGraphComputer."
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: d2ea692c5c353586cc2b653e57eca7ccb8c9c7ce
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Utföra analyser av diagram med hjälp av Spark och Apache TinkerPop Gremlin

[Azure Cosmos-DB](introduction.md) är global och flera olika modeller databastjänsten från Microsoft. Du kan skapa och fråga dokument och nyckel/värde-diagrammet databaser, som dra nytta av funktioner för global distributionsplatsen och skala horisontellt kärnan i Azure Cosmos DB. Har stöd för online transaktionsbearbetning (OLTP) diagrammet arbetsbelastningar som använder Azure Cosmos-DB [Apache TinkerPop Gremlin](graph-introduction.md).

[Spark](http://spark.apache.org/) är ett Apache Software Foundation-projekt som fokuserar på allmänna online analytical processing (OLAP) databearbetning. Spark tillhandahåller en hybrid i-minne/diskbaserad distribuerad datamodell som liknar Hadoop MapReduce-modellen. Du kan distribuera Apache Spark i molnet med hjälp av [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Du kan utföra både OLTP och OLAP-arbetsbelastningar när du använder Gremlin genom att kombinera Azure Cosmos DB och Spark. Den här Snabbkurs artikeln visar hur du kör Gremlin frågor mot Azure Cosmos DB på ett Azure HDInsight Spark-kluster.

## <a name="prerequisites"></a>Krav

Innan du kan köra det här exemplet måste du uppfylla följande krav:
* Azure HDInsight Spark-kluster 2.0
* JDK 1.8 + (om du inte har JDK, köra `apt-get install default-jdk`.)
* Maven (om du inte har Maven kör `apt-get install maven`.)
* En Azure-prenumeration ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Information om hur du ställer in ett Azure HDInsight Spark-kluster finns [etablera HDInsight-kluster](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Skapa ett databaskonto Azure Cosmos DB

Skapa först ett databaskonto med Graph API genom att göra följande:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Lägga till en samling

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Hämta Apache TinkerPop

Hämta Apache TinkerPop genom att göra följande:

1. Fjärråtkomst till huvudnoden för HDInsight-klustret `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Klona TinkerPop3 källkoden, skapar det lokalt och installerar Maven cacheminne.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Installera Spark-Gremlin plugin-program 

    a. Installationen av plugin-programmet hanteras av delvis. Fyll i informationen om databaser för delvis så att den kan hämta plugin-programmet och dess beroenden. 

      Skapa delvis konfigurationsfil om det inte finns på `~/.groovy/grapeConfig.xml`. Använd följande inställningar:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Starta konsolen Gremlin `bin/gremlin.sh`.
        
    c. Installera Spark-Gremlin plugin-program med version 3.3.0-SNAPSHOT som du skapat i föregående steg:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Kontrollera om `Hadoop-Gremlin` aktiveras med `:plugin list`. Inaktivera det här plugin-programmet, eftersom det kan störa Spark-Gremlin plugin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Förbereda TinkerPop3 beroenden

När du har skapat TinkerPop3 i föregående steg hämtas i processen alla jar-beroenden även för Spark och Hadoop i målkatalogen. Använd burkar som är förinstallerade med HDI och ta emot ytterligare beroenden bara vid behov.

1. Gå till Gremlin konsolen målkatalogen på `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Flytta alla burkar under `ext/` till `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Ta bort alla jar bibliotek under `lib/` som inte är i följande lista:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Hämta Azure Cosmos DB Spark-koppling

1. Hämta Azure Cosmos DB Spark kopplingen `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` och Cosmos DB Java SDK `azure-documentdb-1.12.0.jar` från [Cosmos Azure DB Spark Connector på GitHub](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Alternativt kan du skapa den lokalt. Eftersom den senaste versionen av Spark-Gremlin har skapats med Spark 1.6.1 och är inte kompatibel med Spark punkt 2.0.2, som används för närvarande i Azure Cosmos DB Spark-koppling, kan du skapa senaste TinkerPop3 koden och installera burkar manuellt. Gör följande:

    a. Klona Azure Cosmos DB Spark-kopplingen.

    b. Skapa TinkerPop3 (redan har gjort i föregående steg). Installera alla TinkerPop 3.3.0-SNAPSHOT burkar lokalt.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Uppdatera `tinkerpop.version` `azure-documentdb-spark/pom.xml` till `3.3.0-SNAPSHOT`.
    
    d. Skapa med Maven. Nödvändiga burkar placeras i `target` och `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Kopiera de tidigare nämnda burkar till en lokal katalog på ~ / azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuera beroenden till Spark arbetsnoder 

1. Eftersom transformering av diagramdata beror på TinkerPop3, måste du distribuera relaterade beroenden för alla arbetarnoder i Spark.

2. Kopiera de tidigare nämnda Gremlin beroenden, CosmosDB Spark connector jar och CosmosDB Java SDK till arbetsnoderna genom att göra följande:

    a. Kopiera alla burkar i `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Hämta en lista över alla Spark arbetarnoder, som du hittar på Ambari instrumentpanelen i den `Spark2 Clients` lista i den `Spark2` avsnitt.

    c. Kopiera den katalogen till varje nod.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Ställ in miljövariablerna

1. Hitta HDP version av Spark-klustret. Det är katalognamnet under `/usr/hdp/` (till exempel 2.5.4.2-7).

2. Ange hdp.version för alla noder. Ambari instrumentpanel, gå till **YARN avsnittet** > **konfigurationerna** > **Avancerat**, och gör sedan följande: 
 
    a. I `Custom yarn-site`, lägga till en ny egenskap `hdp.version` med värdet för HDP-versionen på huvudnoden. 
     
    b. Spara konfigurationen. Det finns varningar som du kan ignorera. 
     
    c. Starta om tjänsterna YARN och Oozie som notification ikonerna anger.

3. Ange följande miljövariabler för huvudnoden (ersätter värdena med lämpliga):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Förbereda graph-konfiguration

1. Skapa en konfigurationsfil med Azure Cosmos DB anslutningsparametrar och Spark-inställningar och placera den på `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Uppdatering av `spark.driver.extraClassPath` och `spark.executor.extraClassPath` till inkluderingskatalogen burkar som du distribuerade i föregående steg i det här fallet `/home/sshuser/azure-documentdb-spark/*`.

3. Ange följande information för Azure Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Läsa in diagrammet TinkerPop och spara den till Azure Cosmos DB
För att demonstrera hur du bevara ett diagram i Azure Cosmos databas med det här exemplet fördefinierade använder TinkerPop TinkerPop moderna diagram. Diagrammet lagras i Kryo format och anges i TinkerPop-databasen.

1. Eftersom du kör Gremlin i YARN-läge måste du diagrammets data i Hadoop-filsystem. Använd följande kommandon för att göra en katalog och kopiera filen lokala diagram till den. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Uppdatera tillfälligt den `gremlin-spark.properties` fil som ska användas `GryoInputFormat` att läsa i diagrammet. Ange också `inputLocation` som katalogen du skapar, enligt följande:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Starta Gremlin konsolen och skapa följande för att spara data till samlingen konfigurerade Azure Cosmos DB beräkning:  

    a. Skapa diagrammet `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Använd SparkGraphComputer för skrivning `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. Du kan verifiera att data har sparats i Azure Cosmos DB från Data Explorer.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Läsa in diagrammet från Azure Cosmos DB och köra Gremlin frågor

1. För att läsa in diagrammet redigera `gremlin-spark.properties` ange `graphReader` till `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Läsa in diagrammet passerar data och köra Gremlin frågor till den genom att göra följande:

    a. Starta konsolen Gremlin `bin/gremlin.sh`.

    b. Skapa diagrammet med konfigurationen `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Skapa ett diagram traversal med SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Kör följande Gremlin diagram frågor:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Om du vill se mer detaljerad loggning anger loggen i `conf/log4j-console.properties` till en mer detaljerad nivå.
>

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du arbetar med diagram genom att kombinera Azure Cosmos DB och Spark i den här Snabbkurs i artikeln.

> [!div class="nextstepaction"]
