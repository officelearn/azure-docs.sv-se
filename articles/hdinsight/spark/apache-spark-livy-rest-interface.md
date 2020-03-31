---
title: Använd Livy Spark för att skicka jobb till Spark-kluster på Azure HDInsight
description: Lär dig hur du använder Apache Spark REST API för att skicka Spark-jobb på distans till ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206290"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster

Lär dig hur du använder [Apache Livy](https://livy.incubator.apache.org/), Apache Spark REST API, som används för att skicka fjärrjobb till ett Azure HDInsight Spark-kluster. Detaljerad dokumentation finns i [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Du kan använda Livy för att köra interaktiva Spark-skal eller skicka batch-jobb som ska köras på Spark. Den här artikeln talar om att använda Livy att skicka batch jobb. Kodavsnitten i den här artikeln använder cURL för att ringa REST API-anrop till Livy Spark-slutpunkten.

## <a name="prerequisites"></a>Krav

Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Skicka in ett apache livy Spark-batchjobb

Innan du skickar ett batchjobb måste du överföra programburken på klusterlagringen som är associerad med klustret. Du kan använda kommandoradsverktyget [AzCopy](../../storage/common/storage-use-azcopy.md) till att göra detta. Det finns flera andra klienter som du kan använda för att ladda upp data. Det finns mer information om dem i [Ladda upp data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Exempel

* Om jar-filen finns i klusterlagringen (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Om du vill skicka jar-filnamnet och klassnamnet som en del av en indatafil (i det här exemplet input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Få information om Livy Spark-batchar som körs i klustret

Syntax:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Exempel

* Om du vill hämta alla Livy Spark-batchar som körs i klustret:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Om du vill hämta en viss batch med ett visst batch-ID

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Ta bort ett batchjobb i Livy Spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Exempel

Ta bort ett batchjobb `5`med batch-ID .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark och hög tillgänglighet

Livy ger hög tillgänglighet för Spark-jobb som körs i klustret. Här är ett par exempel.

* Om Livy-tjänsten går ned efter att du har skickat ett jobb på distans till ett Spark-kluster fortsätter jobbet att köras i bakgrunden. När Livy är tillbaka upp, återställs status för jobbet och rapporterar tillbaka.
* Jupyter bärbara datorer för HDInsight drivs av Livy i backend. Om en anteckningsbok kör ett Spark-jobb och Livy-tjänsten startas om fortsätter anteckningsboken att köra kodcellerna.

## <a name="show-me-an-example"></a>Visa mig ett exempel

I det här avsnittet tittar vi på exempel för att använda Livy Spark för att skicka batch-jobb, övervaka förloppet för jobbet och sedan ta bort det. Programmet vi använder i det här exemplet är det som utvecklats i artikeln [Skapa ett fristående Scala-program och att köras på HDInsight Spark-kluster](apache-spark-create-standalone-application.md). Stegen här förutsätter:

* Du har redan kopierat över programburken till lagringskontot som är associerat med klustret.
* Du har CuRL installerat på datorn där du försöker med de här stegen.

Utför följande steg:

1. För enkel användning, ange miljövariabler. Det här exemplet baseras på en Windows-miljö och ändrar variabler efter behov för din miljö. Ersätt `CLUSTERNAME`och `PASSWORD` med lämpliga värden.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Kontrollera att Livy Spark körs i klustret. Vi kan göra det genom att få en lista över löpande batchar. Om du kör ett jobb med Livy för första gången ska utdata returnera noll.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Du bör hämta en utdata som liknar följande kodavsnitt:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Lägg märke till hur den sista raden i utdata säger **totalt:0**, vilket tyder på att inga löpande batchar körs.

1. Låt oss nu skicka in ett batchjobb. Följande kodavsnitt använder en indatafil (input.txt) för att skicka jarnamnet och klassnamnet som parametrar. Om du kör dessa steg från en Windows-dator är det rekommenderat att du använder en indatafil.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametrarna i filen **input.txt** definieras på följande sätt:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Lägg märke till hur den sista raden i utdata säger **state:starting**. Det står också, **id:0**. Här är **0** batch-ID.

1. Du kan nu hämta status för den här specifika batchen med hjälp av batch-ID.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Utdata visar nu **state:success**, vilket tyder på att jobbet har slutförts.

1. Om du vill kan du nu ta bort batchen.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Den sista raden i utdata visar att batchen har tagits bort. Att ta bort ett jobb, medan det körs, dödar också jobbet. Om du tar bort ett jobb som har slutförts, framgångsrikt eller på annat sätt, tas jobbinformationen bort helt.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Uppdateringar av Livy-konfigurationen som börjar med HDInsight 3.5-versionen

HDInsight 3.5 kluster och högre, som standard, inaktivera användningen av lokala sökvägar för att komma åt exempeldatafiler eller burkar. Vi rekommenderar att `wasbs://` du använder sökvägen i stället för att komma åt burkar eller exempeldatafiler från klustret.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Skicka Livy-jobb för ett kluster i ett virtuellt Azure-nätverk

Om du ansluter till ett HDInsight Spark-kluster från ett Virtuellt Azure-nätverk kan du ansluta direkt till Livy i klustret. I så fall är `http://<IP address of the headnode>:8998/batches`url:en för Livy-slutpunkten . Här är **8998** den hamn där Livy körs på kluster headnode. Mer information om hur du får tillgång till tjänster i icke-offentliga portar finns [i Portar som används av Apache Hadoop-tjänster på HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Nästa steg

* [Apache Livy REST API-dokumentation](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
