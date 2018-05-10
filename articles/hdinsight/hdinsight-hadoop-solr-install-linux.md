---
title: Använd skriptåtgärder om du vill installera Solr på Linux-baserat HDInsight - Azure | Microsoft Docs
description: Lär dig hur du installerar Solr på Linux-baserade HDInsight Hadoop-kluster med hjälp av skriptåtgärder.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 707f25a71a22f136a3cfc537ec3dd939e06dea08
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installera och använda Solr på HDInsight Hadoop-kluster

Lär dig hur du installerar Solr på Azure HDInsight med hjälp av skriptåtgärder. Solr är en kraftfull sökplattform och företagsnivå sökfunktioner om data som hanteras av Hadoop.

> [!IMPORTANT]
    > Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> Exempelskript som används i det här dokumentet installerar Solr 4.9 med en viss konfiguration. Om du vill konfigurera Solr klustret med olika samlingar, delar, scheman, repliker, etc., måste du ändra skript och Solr binärfiler.

## <a name="whatis"></a>Vad är Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) är en enterprise search-plattform som gör att kraftfulla fulltextsökning i data. Hadoop gör det möjligt för lagring och hantering av stora mängder data, ger Apache Solr sökfunktioner snabbt hämta data.

> [!WARNING]
> Komponenter som ingår i HDInsight-kluster stöds fullt ut av Microsoft.
>
> Anpassade komponenter, till exempel Solr, få kommersiellt rimliga stöd för att hjälpa dig att felsöka problemet ytterligare. Microsoft-supporten kanske inte kan lösa problem med anpassade komponenter. Du kan behöva engagera communities för öppen källkod för att få hjälp. Det finns till exempel många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Apache-projekt har också project-webbplatser [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Skriptet innehåller

Det här skriptet gör följande ändringar till HDInsight-klustret:

* Installerar Solr 4.9 i `/usr/hdp/current/solr`
* Skapar en användare **solrusr**, som används för att köra tjänsten Solr
* Anger **solruser** som ägare till `/usr/hdp/current/solr`
* Lägger till en [Upstart](http://upstart.ubuntu.com/) konfiguration som startar Solr automatiskt.

## <a name="install"></a>Installera Solr med hjälp av skriptåtgärder

Ett exempelskript för att installera Solr på ett HDInsight-kluster finns på följande plats:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Om du vill skapa ett kluster med installerat Solr, Följ stegen i den [skapa HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentet. Under skapandeprocessen, Använd följande steg för att installera Solr:

1. Från den __klustret sammanfattning__ avsnitt, select__Advanced settings__, sedan __skript åtgärder__. Använd följande information för att fylla i formuläret:

   * **NAMNET**: Ange ett eget namn för skriptåtgärden.
   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: Markera det här alternativet
   * **WORKER**: Markera det här alternativet
   * **ZOOKEEPER**: Markera det här alternativet att installera på noden Zookeeper
   * **PARAMETRARNA**: lämna fältet tomt

2. Längst ned i den **skript åtgärder** Använd den **Välj** för att spara konfigurationen. Använd slutligen den **nästa** vill gå tillbaka till den __klustret sammanfattning__

3. Från den __klustret sammanfattning__ väljer __skapa__ att skapa klustret.

## <a name="usesolr"></a>Hur använder Solr i HDInsight

> [!IMPORTANT]
> Stegen i det här avsnittet visar grundläggande Solr funktioner. Mer information om hur du använder Solr finns i [Apache Solr plats](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Indexinformationen

Använd följande steg för att lägga till exempeldata i Solr och fråga den:

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

     > [!IMPORTANT]
     > Stegen senare i det här dokumentet använder en SSH-tunnel för att ansluta till Solr webbgränssnittet. Om du vill använda de här stegen, måste du upprätta en SSH-tunnel och sedan konfigurera webbläsaren för att använda den.
     >
     > Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentet.

2. Har Solr index exempeldata med hjälp av följande kommandon:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Följande utdata returneras till konsolen:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Den `post.jar` verktyget lägger till den **solr.xml** och **monitor.xml** dokument i indexet.
  
3. Använd följande kommando för att fråga Solr REST-API:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Det här kommandot söker **collection1** för alla dokument som matchar **\*:\*** (kodad som \*% 3A\* i frågesträngen). Följande JSON-dokumentet är ett exempel på svaret:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Med hjälp av instrumentpanelen Solr

Solr instrumentpanelen är ett webbgränssnitt som hjälper dig att arbeta med Solr via webbläsaren. Solr instrumentpanelen visas inte direkt via Internet från ditt HDInsight-kluster. Du kan använda en SSH-tunnel för att komma åt den. Mer information om hur du använder en SSH-tunnel finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentet.

När du har skapat en SSH-tunnel, Solr instrumentpanel med hjälp av följande steg:

1. Fastställa värdnamnet för den primära headnode:

   1. Använda SSH för att ansluta till klustrets huvudnod. Till exempel `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Mer information om hur du använder SSH finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Använd följande kommando för att hämta det fullständigt kvalificerade värdnamnet:

        ```bash
        hostname -f
        ```

        Det här kommandot returnerar ett värde som liknar följande värdnamn:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Spara det värde som returneras, eftersom den används senare.

2. Anslut till i din webbläsare **http://HOSTNAME:8983/solr/#/**, där **VÄRDNAMN** är namnet du bestämde i föregående steg.

    Begäran dirigeras via SSH-tunnel till Solr webbgränssnittet på ditt kluster. Sidan ser ut som följande bild:

    ![Bild av Solr instrumentpanelen](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. Använd från den vänstra rutan i **Core Selector** listrutan för att välja **collection1**. Flera poster ska dem visas under **collection1**.

4. Från posterna nedan **collection1**väljer **frågan**. Använd följande värden för att fylla i sidan Sök efter:

   * I den **q** text Ange  **\*:**\*. Den här frågan returnerar alla dokument som indexeras i Solr. Om du vill söka efter en specifik sträng inom dokument som kan du ange den här strängen.
   * I den **wt** text väljer utdataformatet. Standardvärdet är **json**.

     Välj slutligen den **köra frågan** knappen längst ned i Sök-pate.

     ![Använd skriptåtgärder för att anpassa ett kluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     Utdata returnerar två dokument som du tidigare lagt till indexet. Utdata liknar följande JSON-dokumentet:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Starta och stoppa Solr

Stoppa och starta Solr manuellt med hjälp av följande kommandon:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Indexerade säkerhetskopieringsdata

Använd följande steg för att säkerhetskopiera Solr data till standardlagring för klustret:

1. Anslut till klustret med SSH och använda följande kommando för att hämta värdnamnet för huvudnoden:

    ```bash
    hostname -f
    ```

2. Använd följande kommando för att skapa en ögonblicksbild av indexerade data. Ersätt **VÄRDNAMN** med namn som returnerades från föregående kommando:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    Svaret liknar följande XML-filen:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Ändra kataloger till `/usr/hdp/current/solr/example/solr`. Det finns en underkatalog för varje samling. Varje samling katalogen innehåller en `data` katalog som innehåller en ögonblicksbild för samlingen.

4. Om du vill skapa en komprimerad fil i mappen med ögonblicksbilder, använder du följande kommando:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Ersätt den `snapshot.20150806185338855` värden med namnet på ögonblicksbilden för samlingen.

    Det här kommandot skapas ett arkiv med namnet **snapshot.20150806185338855.tgz**, som innehåller innehållet i den **snapshot.20150806185338855** directory.

5. Du kan sedan lagra arkivet till klustrets primära lagring med hjälp av följande kommando:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Mer information om hur du arbetar med Solr säkerhetskopiering och återställning finns [ https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups ](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Nästa steg

* [Installera Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd anpassning av klustret för att installera Giraph på HDInsight Hadoop-kluster. Giraph kan du utföra bearbetning med hjälp av Hadoop och kan användas med Azure HDInsight.

* [Installera Hue i HDInsight-kluster](hdinsight-hadoop-hue-linux.md). Använd anpassning av klustret för att installera Hue på HDInsight Hadoop-kluster. Hue är en uppsättning webbprogram som används för att interagera med ett Hadoop-kluster.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
