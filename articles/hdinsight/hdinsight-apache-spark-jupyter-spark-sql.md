<properties
    pageTitle="Skapa ett Spark-kluster i HDInsight Linux och använda Spark SQL från Jupyter för interaktiv analys | Microsoft Azure"
    description="Stegvisa instruktioner om hur du snabbt skapar ett Apache Spark-kluster i HDInsight och sedan använder Spark SQL från Jupyter-anteckningsböcker för att köra interaktiva frågor."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/25/2016"
    ms.author="nitinme"/>


# Komma igång: Skapa ett Apache Spark-kluster i HDInsight Linux och köra interaktiva frågor med Spark SQL

Lär dig hur du skapar ett Apache Spark-kluster i HDInsight och sedan använder  [Jupyter](https://jupyter.org)-anteckningsboken för att köra interaktiva Spark SQL-frågor i Spark-klustret.

   ![Komma igång med Apache Spark i HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Get started using Apache Spark in HDInsight tutorial. Steps illustrated: create a storage account; create a cluster; run Spark SQL statements")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Krav:**

- **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **En Secure Shell-klient (SSH)**: Linux-, Unix- och OS X-system etablerar en SSH-klient via `ssh`-kommandot. För Windows-system rekommenderar vi [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Secure Shell-nycklar (SSH) (valfritt)**: Du kan skydda det SSH-konto som används för att ansluta till klustret med ett lösenord eller en offentlig nyckel. Med hjälp av ett lösenord kommer du snabbt igång och du bör använda det här alternativet om du snabbt vill skapa ett kluster och köra några testjobb. Att använda en nyckel är säkrare men kräver ytterligare inställningar. Du kanske vill använda den här metoden när du skapar ett produktionskluster. I den här artikeln använder vi lösenordsmetoden. Instruktioner om hur du skapar och använder SSH-nycklar med HDInsight finns i följande artiklar:

    -  Från en Linux-dator – [Använd SSH med Linux-baserat HDInsight (Hadoop) från Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  Från en Windows-dator – [Använd SSH med Linux-baserat HDInsight (Hadoop) från Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE]  Den här artikeln använder en ARM-mall för att skapa ett Spark-kluster som använder [Azure Storage-blobar som klusterlagring](hdinsight-hadoop-use-blob-storage.md). Du kan också skapa ett Spark-kluster som använder [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) som ett ytterligare lagringsutrymme, utöver Azure Storage-blobar som standardlagring. Instruktioner finns i [Skapa ett HDInsight-kluster med Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


## Skapa Spark-kluster

I det här avsnittet skapar du ett HDInsight-kluster av version 3.4 (Spark-version 1.6.1) med hjälp av en Azure ARM-mall. Information om HDInsight-versioner och deras serviceavtal finns i [Versionshantering för HDInsight-komponenter](hdinsight-component-versioning.md). Information om andra metoder för att skapa kluster finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att öppna en ARM-mall i Azure Portal.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    ARM-mallen finns i den offentlig blob-behållaren *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. Skriv följande på bladet Parametrar:

    - **Klusternamn**: Ange ett namn för det Hadoop-kluster du vill skapa.
    - **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard ”admin”.
    - **SSH-användarnamn och lösenord**.
    
    Skriv ned dessa värden.  Du behöver dem senare under kursen.

    > [AZURE.NOTE] SSH används för att få fjärråtkomst till HDInsight-klustret med hjälp av en kommandorad. Det användarnamn och lösenord du använder här används när du ansluter till klustret via SSH. Användarnamnet för SSH måste dessutom vara unikt, eftersom det skapar ett användarkonto på alla HDInsight-klusternoder. Nedan följer några av de kontonamn som är reserverade för användning av tjänsterna i klustret och inte kan användas som SSH-användarnamn:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    > Mer information om hur du använder SSH med HDInsight finns i följande artiklar:

    > * [Använda SSH med Linux-baserat Hadoop i HDInsight från Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Använda SSH med Linux-baserat Hadoop i HDInsight från Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Spara parametrarna genom att klicka på **OK**.

4. I bladet **Anpassad distribution** klickar du på listrutan **Resursgrupp** och sedan på **Ny** för att skapa en ny resursgrupp. Resursgruppen är en behållare som grupperar klustret, det beroende lagringskontot och andra länkade resurser.

5. Klicka på **Juridiska villkor** och sedan på **Skapa**.

6. Klicka på **Skapa**. En ny panel visas med rubriken Skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret och SQL Database.



## Köra Spark SQL-frågor med en Jupyter-anteckningsbok

I det här avsnittet använder du Jupyter-anteckningsboken för att köra Spark SQL-frågor mot Spark-klustret. HDInsight Spark-kluster tillhandahåller två kernlar som du kan använda med Jupyter-anteckningsboken. Dessa är:

* **PySpark** (för appar som skrivits i Python)
* **Spark** (för appar som skrivits i Scala)

I den här artikeln använder vi PySpark-kerneln. I artikeln [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) kan du få mer information om fördelarna med att använda PySpark-kerneln. Några av de främsta fördelarna med att använda PySpark-kerneln är:

* Du behöver inte ange kontexter för Spark och Hive. Dessa anges automatiskt åt dig.
* Du kan använda användbara cellfunktioner som `%%sql` för att köra dina SQL- eller Hive-frågor direkt utan några föregående kodfragment.
* Utdata för SQL- eller Hive-frågor visualiseras automatiskt.

### Skapa en Jupyter-anteckningsbok med PySpark-kernel 

1. På startsidan i [Azure-portalen](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   

2. Klicka på **Snabblänkar** på Spark-klusterbladet och sedan på **Jupyter Notebook** på **Klusterinstrumentpanel**-bladet. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    > [AZURE.NOTE] Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt __CLUSTERNAME__ med namnet på klustret:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Skapa en ny anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

    ![Skapa en ny Jupyter-anteckningsbok](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Create a new Jupyter notebook")

3. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.

    ![Ange ett namn för anteckningsboken](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")

4. Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. Du kan börja med att importera de typer som krävs för det här scenariot. Det gör du genom att klistra in följande kodfragment i en cell och trycka på **SKIFT + RETUR**.

        from pyspark.sql.types import *
        
    Varje gång du kör ett jobb i Jupyter kommer fönsterrubriken i din webbläsare att visa statusen **(Upptagen)**  tillsammans med anteckningsbokens titel. Du kan även se en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras denna till en tom cirkel.

     ![Status för ett Jupyter-anteckningsboksjobb](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Status of a Jupyter notebook job")

4. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight, kopieras exempeldatafilen **hvac.csv** till det associerade lagringskontot under **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Klistra in följande kodexempel i en tom cell och tryck på **SKIFT + RETUR**. Den här kodexemplet registrerar data i en tillfällig tabell som kallas **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Eftersom du använder en PySpark-kernel kan du nu direkt köra en SQL-fråga för den tillfälliga tabellen **hvac** som du just skapade med den användbara `%%sql`-funktionen. Mer information om den användbara `%%sql`, samt andra mycket användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. När jobbet har slutförts visas följande tabellutdata som standard.

    ![Tabellutdata från frågeresultat](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Table output of query result")

    Du kan också visa resultaten i andra visualiseringar. Ett områdesdiagram för samma utdata skulle som exempel se ut enligt nedan.

    ![Områdesdiagram över frågeresultat](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Area graph of query result")


6. När du har kört appen bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Då avslutas anteckningsboken och stängs ned.

##Ta bort klustret

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Se även


* [Översikt: Apache Spark i Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenarier

* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](hdinsight-apache-spark-use-bi-tools.md)

* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](hdinsight-apache-spark-eventhub-streaming.md)

* [Webbplatslogganalys med Spark i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Application Insight-telemetridataanalys i HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Verktyg och tillägg

* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Använda externa paket med Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=sep16_HO1-->


