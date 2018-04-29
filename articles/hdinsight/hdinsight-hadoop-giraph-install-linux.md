---
title: Installera och använda Giraph på HDInsight (Hadoop) - Azure | Microsoft Docs
description: Lär dig hur du installerar Giraph på Linux-baserade HDInsight-kluster med skriptåtgärder. Skriptåtgärder kan du anpassa klustret när skapas genom att ändra klusterkonfigurationen eller installera tjänster och verktyg.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 03e72c29bedf6a3125a5ae0272e93cdf58632bc6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installera Giraph på HDInsight Hadoop-kluster och använda Giraph för att bearbeta stora diagram

Lär dig hur du installerar Apache Giraph på ett HDInsight-kluster. Funktionen skript åtgärd i HDInsight kan du anpassa ditt kluster genom att köra ett bash-skript. Kan använda skript för att anpassa kluster under och efter klustret skapas.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Vad är Giraph

[Apache Giraph](http://giraph.apache.org/) kan du utföra bearbetning med hjälp av Hadoop och kan användas med Azure HDInsight. Diagram modell förhållanden mellan objekt. Anslutningar mellan routrar i ett större nätverk som till exempel Internet eller relationer mellan personer på sociala nätverk. Diagrammet bearbetning kan du därför om förhållanden mellan objekt i ett diagram som:

* Identifiera potentiella vänner baserat på din aktuella relationer.

* Identifiera den kortaste vägen mellan två datorer i ett nätverk.

* Beräkning av sidan rangordningen för webbsidor.

> [!WARNING]
> Komponenter som ingår i HDInsight-kluster stöds fullt ut - Microsoft-supporten hjälper till att identifiera och lösa problem relaterade till komponenterna.
>
> Anpassade komponenter, till exempel Giraph, få kommersiellt rimliga stöd för att hjälpa dig att felsöka problemet ytterligare. Microsoft-supporten kanske kan lösa problemet. Om inte, måste du kontakta öppen källkod communities där djup expertis för att teknik finns. Det finns till exempel många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Apache-projekt har också project-webbplatser [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Skriptet innehåller

Det här skriptet utförs följande åtgärder:

* Installerar Giraph till `/usr/hdp/current/giraph`

* Kopior av `giraph-examples.jar` fil som standard storage (WASB) för klustret: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installera Giraph med hjälp av skriptåtgärder

Ett exempelskript för att installera Giraph på ett HDInsight-kluster finns på följande plats:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Det här avsnittet innehåller instruktioner om hur du använder exempelskriptet när klustret skapas med hjälp av Azure portal.

> [!NOTE]
> En skriptåtgärd kan användas med någon av följande metoder:
> * Azure PowerShell
> * Azure CLI
> * HDInsight .NET SDK
> * Azure Resource Manager-mallar
> 
> Du kan även gälla skriptåtgärder kluster som körs redan. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

1. Skapa ett kluster med hjälp av stegen i [skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md), men inte slutföra skapandet.

2. I den **valfri konfiguration** väljer **skriptåtgärder**, och ange följande information:

   * **NAMNET**: Ange ett eget namn för skriptåtgärden.

   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Kontrollera posten

   * **WORKER**: lämna det här alternativet är avmarkerat

   * **ZOOKEEPER**: lämna det här alternativet är avmarkerat

   * **PARAMETRARNA**: lämna fältet tomt

3. Längst ned i den **skriptåtgärder**, använda den **Välj** för att spara konfigurationen. Använd slutligen den **Välj** knappen längst ned i den **valfri konfiguration** avsnittet för att spara konfigurationsinformationen för valfria.

4. Fortsätta att skapa klustret enligt beskrivningen i [skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Hur använder Giraph i HDInsight?

När klustret har skapats kan du använda följande steg för att köra exemplet SimpleShortestPathsComputation medföljer Giraph. Det här exemplet används grundläggande [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) implementering för att hitta den kortaste sökvägen mellan objekt i ett diagram.

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att skapa en fil med namnet **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Använd följande text som innehållet i den här filen:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Den här informationen beskriver en relation mellan objekt i en riktat diagram i formatet `[source_id, source_value,[[dest_id], [edge_value],...]]`. Varje rad representerar en relation mellan en `source_id` objekt och en eller flera `dest_id` objekt. Den `edge_value` kan betraktas som styrkan eller avståndet mellan anslutningen mellan `source_id` och `dest\_id`.

    Dras ut, och använder värdet (eller vikt) som avståndet mellan objekt, data kan se ut som i följande diagram:

    ![tiny_graph.txt ritas som cirklar med rader med olika avståndet mellan](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Om du vill spara filen, Använd **Ctrl + X**, sedan **Y**, och slutligen **RETUR** att acceptera filnamnet.

4. Använd följande för att lagra data till primära lagringsplatsen för HDInsight-kluster:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Kör SimpleShortestPathsComputation exemplet med följande kommando:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    I följande tabell beskrivs de parametrar som används med det här kommandot:

   | Parameter | Vad läget gör |
   | --- | --- |
   | `jar` |Jar-filen som innehåller exemplen. |
   | `org.apache.giraph.GiraphRunner` |Den klass som används för att starta exemplen. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Exempel som används. I det här exemplet beräknar den kortaste sökvägen mellan ID 1 och alla andra ID i diagrammet. |
   | `-ca mapred.job.tracker` |Headnode för klustret. |
   | `-vif` |Indataformatet för indata. |
   | `-vip` |Indatafilen. |
   | `-vof` |Format för utdata. I det här exemplet ID och värdet som oformaterad text. |
   | `-op` |Platsen. |
   | `-w 2` |Antalet anställda att använda. I det här exemplet 2. |

    Mer information om dessa och andra parametrar som används med Giraph exempel finns i [Giraph quickstart](http://giraph.apache.org/quick_start.html).

6. När jobbet har slutförts resultatet lagras i den **/example/out/shotestpaths** directory. Utdata-filnamn som börjar med **del-m -** och avslutas med ett tal som anger först, andra, etc.-filen. Använd följande kommando för att visa utdata:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Utdata liknar följande:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exempel är hårddisken kodat börja med objekt-ID 1 och hitta den kortaste sökvägen till andra objekt. Utdata är i formatet `destination_id` och `distance`. Den `distance` värde (eller vikt) av kanter förflyttat sig mellan objekt-ID 1 och mål-ID.

    Visualisera data kan du kontrollera resultatet av reser kortaste sökvägar mellan ID 1 och alla andra objekt. Den kortaste sökvägen mellan ID 1 och 4-ID är 5. Det här värdet är det totala avståndet mellan <span style="color:orange">ID 1 och 3</span>, och sedan <span style="color:red">ID 3 och 4</span>.

    ![Ritning av objekt som cirklar med kortast sökvägar mellan](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Nästa steg

* [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md).

* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md).
