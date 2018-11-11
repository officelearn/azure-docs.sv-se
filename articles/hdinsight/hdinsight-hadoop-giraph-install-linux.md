---
title: Installera och använda Giraph på HDInsight (Hadoop) – Azure
description: Lär dig mer om att installera Giraph på Linux-baserade HDInsight-kluster med skriptåtgärder. Skriptåtgärder kan du anpassa klustret när du skapar genom att ändra konfigurationen för klustret eller installera tjänster och verktyg.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 202a68d219bec3a70e50afa68228cbc6bf453518
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011671"
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installera Giraph på HDInsight Hadoop-kluster och använda Giraph för att bearbeta storskaliga diagram

Lär dig mer om att installera Apache Giraph på ett HDInsight-kluster. Funktionen för åtgärden skriptet i HDInsight kan du anpassa ditt kluster genom att köra ett bash-skript. Skript kan användas för att anpassa kluster under och när klustret har skapats.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Vad är Giraph

[Apache Giraph](http://giraph.apache.org/) kan du utföra diagrambearbetning med hjälp av Hadoop och kan användas med Azure HDInsight. Diagram modell relationer mellan objekt. Till exempel anslutningar mellan routrar på ett stort nätverk som Internet eller relationer mellan personer på sociala nätverk. Bearbeta diagram gör att du att resonera kring relationerna mellan objekt i ett diagram som:

* Identifiera potentiella vänner utifrån dina aktuella relationer.

* Identifiera den kortaste vägen mellan två datorer i ett nätverk.

* Beräkna den sida rangordningen för webbsidor.

> [!WARNING]
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut – Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter, till exempel Giraph, får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Microsoft Support kanske kan lösa problemet. Om inte, du måste läsa öppen källkod-communities där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Vad skriptet gör

Det här skriptet utför följande åtgärder:

* Installerar Giraph till `/usr/hdp/current/giraph`

* Kopior på `giraph-examples.jar` filen till standard storage (WASB) för klustret: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installera Giraph med skriptåtgärder

Ett exempelskript för att installera Giraph på ett HDInsight-kluster finns på följande plats:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Det här avsnittet innehåller instruktioner om hur du använder exempelskriptet när du skapade klustret med hjälp av Azure portal.

> [!NOTE]
> En skriptåtgärd kan tillämpas med hjälp av någon av följande metoder:
> * Azure PowerShell
> * Den klassiska Azure CLI
> * HDInsight .NET SDK
> * Azure Resource Manager-mallar
> 
> Du kan också använda skriptåtgärder för kluster som körs redan. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

1. Börja skapa ett kluster med hjälp av stegen i [skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md), men inte att skapa.

2. I den **valfri konfiguration** väljer **skriptåtgärder**, och ange följande information:

   * **NAMN på**: Ange ett eget namn för skriptåtgärden.

   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Kontrollera den här posten

   * **WORKER**: lämna det avmarkerat

   * **ZOOKEEPER**: lämna det avmarkerat

   * **PARAMETRAR**: lämna fältet tomt

3. Längst ned på den **skriptåtgärder**, använda den **Välj** för att spara konfigurationen. Använd slutligen den **Välj** knappen längst ned på den **valfri konfiguration** avsnitt för att spara informationen som valfri konfiguration.

4. Fortsätta att skapa klustret enligt beskrivningen i [skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Hur kan jag använda Giraph på HDInsight?

När klustret har skapats, kan du använda följande steg för att köra exemplet SimpleShortestPathsComputation ingår Giraph. Det här exemplet används grundläggande [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) implementering för att hitta den kortaste vägen mellan objekt i ett diagram.

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att skapa en fil med namnet **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Använd följande text som filens innehåll:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Den här informationen beskriver en relation mellan objekt i en riktad graf i formatet `[source_id, source_value,[[dest_id], [edge_value],...]]`. Varje rad motsvarar en relation mellan en `source_id` objekt och en eller flera `dest_id` objekt. Den `edge_value` kan betraktas som styrkan eller avståndet från anslutningen mellan `source_id` och `dest\_id`.

    Utdragen, och använder värdet (eller vikt) som avståndet mellan objekten kan data kan se ut som i följande diagram:

    ![tiny_graph.txt ritas som cirklar med varierande avståndet mellan rader](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Om du vill spara filen, Använd **Ctrl + X**, sedan **Y**, och slutligen **RETUR** att acceptera filnamnet.

4. Använd följande för att lagra data i primär lagring för ditt HDInsight-kluster:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Kör exemplet SimpleShortestPathsComputation med följande kommando:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    I följande tabell beskrivs de parametrar som används med det här kommandot:

   | Parameter | Vad läget gör |
   | --- | --- |
   | `jar` |Jar-filen som innehåller exemplen. |
   | `org.apache.giraph.GiraphRunner` |Den klass som används för att starta exemplen. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Exempel som används. I det här exemplet beräknar den kortaste vägen mellan ID 1 och alla andra ID: N i diagrammet. |
   | `-ca mapred.job.tracker` |Huvudnoden för klustret. |
   | `-vif` |Indataformatet för indata. |
   | `-vip` |Indata-filen. |
   | `-vof` |Utdataformat. I det här exemplet ID och värdet som oformaterad text. |
   | `-op` |Platsen. |
   | `-w 2` |Antal arbetare att använda. I det här exemplet 2. |

    Mer information om dessa och andra parametrar som används med Giraph exempel finns i den [Giraph snabbstarten](http://giraph.apache.org/quick_start.html).

6. När jobbet har slutförts resultaten lagras i den **/example/out/shotestpaths** directory. Utdata-filnamn som börjar med **del-m -** och måste sluta med ett tal som anger först, andra, etc.-fil. Använd följande kommando för att visa utdata:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Utdata ser ut som följande text:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exempel är svårt att börja med kodade objekt-ID 1 och hitta den kortaste vägen till andra objekt. Utdata är i formatet `destination_id` och `distance`. Den `distance` värde (eller vikt) av kanter rest mellan objekt-ID 1 och mål-ID.

    Du kan visualisera datan för att kontrollera resultaten av resa kortaste sökvägar mellan ID 1 och alla andra objekt. Den kortaste vägen mellan ID 1 och 4-ID är 5. Det här värdet är det totala avståndet mellan <span style="color:orange">ID 1 och 3</span>, och sedan <span style="color:red">ID 3 och 4</span>.

    ![Ritning av objekt som cirklar med kortaste sökvägar mellan](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Nästa steg

* [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md).

* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md).
