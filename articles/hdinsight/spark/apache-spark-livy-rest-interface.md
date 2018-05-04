---
title: Använda Livius Spark för att skicka jobb till Spark-kluster i Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder Apache Spark REST API för att skicka Spark jobb via fjärranslutning till ett Azure HDInsight-kluster.
keywords: Apache spark rest-api, Livius spark
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 29cf245a03b38be4f5396a3c83c966a27cf038f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Använd Apache Spark REST API för att skicka remote jobb till ett HDInsight Spark-kluster

Lär dig hur du använder Livius Apache Spark REST API, som används för att skicka remote jobb till ett Azure HDInsight Spark-kluster. Mer detaljerad dokumentation finns [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Du kan använda Livius för att köra interaktiva Spark tankar eller skicka batchjobb körs på Spark. Den här artikeln handlar om med Livy för att skicka batchjobb. Kodavsnitt i den här artikeln använder cURL till REST API-anrop till Livius Spark-slutpunkten.

**Krav:**

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett HDInsight Spark-kluster.

## <a name="submit-a-livy-spark-batch-job"></a>Skicka ett Livius Spark batchjobb
Innan du skickar ett batchjobb måste du överföra programmet jar i klusterlagringen som associeras med klustret. Du kan använda [ **AzCopy**](../../storage/common/storage-use-azcopy.md), ett kommandoradsverktyg för att göra detta. Det finns olika klienter kan använda för att överföra data. Du kan hitta mer om dem i [överföra data för Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exempel**:

* Om jar-fil på klustret storage (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Om du vill skicka jar-filnamnet och klassnamnet som en del av en indatafil (i det här exemplet input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Hämta information om Livius Spark batchar som körs i klustret
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exempel**:

* Om du vill hämta alla Livius Spark-batchar som körs i klustret:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Om du vill hämta en viss grupp med en viss batch-ID
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Ta bort batchjobb Livius Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exempel**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livius Spark och hög tillgänglighet
Livius ger hög tillgänglighet för Spark jobb som körs i klustret. Här är några exempel.

* Om tjänsten Livius kraschar när du har skickat ett jobb via fjärranslutning till ett Spark-kluster fortsätter jobbet i bakgrunden. När Livius säkerhetskopiera återställer status för jobb och rapporter som det igen.
* Jupyter-anteckningsböcker för HDInsight är drivs av Livius i serverdelen. Om en bärbar dator med ett Spark-jobb och Livius tjänsten hämtar startas om, fortsätter den bärbara datorn att köra kod celler. 

## <a name="show-me-an-example"></a>Visa ett exempel
I det här avsnittet titta vi på exempel för att använda Livius Spark kan skicka batchjobb, övervaka förloppet för jobbet och ta sedan bort den. Programmet som vi använder i det här exemplet är det som har utvecklats i artikeln [skapa en fristående Scala program och körs på HDInsight Spark-kluster](apache-spark-create-standalone-application.md). De här stegen förutsätter som:

* Du har redan kopieras programmet jar till storage-konto som är associerade med klustret.
* Du har CuRL installerat på den dator där du försöker de här stegen.

Utför följande steg:

1. Låt oss först kontrollera att Livius Spark körs på klustret. Vi kan göra det genom att hämta en lista över aktiva batchar. Om du kör ett jobb med Livy för första gången ska utdata returnera noll.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Du bör få utdata liknar följande utdrag:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Observera hur den sista raden i resultatet säger **totalt: 0**, som föreslås inga körs batchar.

2. Låt oss nu skicka batchjobb. Följande kodavsnitt använder en indatafil (input.txt) för att skicka namnet på jar och klassnamnet som parametrar. Om du använder de här stegen från en Windows-dator är med hjälp av en indatafil den rekommenderade metoden.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Parametrarna i filen **input.txt** definieras enligt följande:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Du bör se utdata som liknar följande fragment:
   
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
   
    Observera hur den sista raden i resultatet säger **tillstånd: startar**. Det också står **-id: 0**. Här, **0** är batch-ID.

3. Du kan nu hämta status för den här specifika batch med batch-ID.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Du bör se utdata som liknar följande fragment:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Nu visas i utdata **tillstånd: lyckade**, vilket tyder på att jobbet har slutförts.

4. Om du vill kan du nu ta bort gruppen.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Du bör se utdata som liknar följande fragment:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Den sista raden i utdata visar att gruppen har tagits bort. Om du tar bort ett jobb när den körs stoppar också jobbet. Om du tar bort ett jobb som har slutförts utan problem, eller i annat fall tas bort Jobbinformationen helt.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Använda Livius Spark i HDInsight 3.5-kluster

3.5 HDInsight-kluster inaktiverar som standard användningen av lokala sökvägar till access exempeldatafiler eller burkar. Vi rekommenderar att du kan använda den `wasb://` sökväg i stället att komma åt burkar eller exempeldata filer från klustret. Om du vill använda lokal sökväg måste du uppdatera Ambari-konfigurationen i enlighet med detta. Gör så här:

1. Gå till portalen Ambari för klustret. Ambari-Webbgränssnittet är tillgängligt på ditt HDInsight-kluster på https://**KLUSTERNAMN**. azurehdidnsight.net, där KLUSTERNAMN är namnet på klustret.

2. I det vänstra navigeringsfönstret klickar du på **Livius**, och klicka sedan på **konfigurationerna**.

3. Under **Livius standard** Lägg till egenskapsnamnet på `livy.file.local-dir-whitelist` och ange dess värde till **”/”** om du vill ge fullständig åtkomst till filsystemet. Ange sökvägen till katalogen som värde om du vill tillåta åtkomst till en specifik katalog.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Skickar Livius jobben för ett kluster i Azure-nätverk

Om du ansluter till ett HDInsight Spark-kluster från ett Azure Virtual Network, kan du anslutas direkt till Livius i klustret. I sådana fall URL: en för Livius slutpunkten är `http://<IP address of the headnode>:8998/batches`. Här, **8998** är den port som Livius körs på klustret headnode. Mer information om att komma åt tjänster på icke-offentliga portar finns [portar som används av Hadoop-tjänster på HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Felsökning

Här följer några problem som kan uppstå när du använder Livius för fjärranslutna jobbet överföring till Spark-kluster.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Med hjälp av en extern jar från det extra lagringsutrymmet stöds inte

**Problem:** om Livius Spark-jobbet refererar till en extern jar från kontot ytterligare lagringsutrymme som är associerade med klustret, jobbet har misslyckats.

**Lösning:** se till att jar som du vill använda är tillgänglig i standardlagring som associerats med HDInsight-kluster.





## <a name="next-step"></a>Nästa steg

* [Livius REST API-dokumentation](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

