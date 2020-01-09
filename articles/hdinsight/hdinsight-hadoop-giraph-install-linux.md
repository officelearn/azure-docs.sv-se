---
title: Installera och använda Giraph på Azure HDInsight
description: Lär dig hur du installerar Giraph i HDInsight-kluster med hjälp av skript åtgärder. Du kan använda Giraph för att göra diagram bearbetning i Apache Hadoop i Azure-molnet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552261"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installera Apache Giraph på HDInsight Hadoop-kluster och Använd Giraph för att bearbeta storskaliga diagram

Lär dig hur du installerar Apache Giraph i ett HDInsight-kluster. Med funktionen skript åtgärd i HDInsight kan du anpassa klustret genom att köra ett bash-skript. Skript kan användas för att anpassa kluster under och efter att klustret har skapats.

## <a name="what-is-giraph"></a>Vad är Giraph

Med [Apache Giraph](https://giraph.apache.org/) kan du utföra diagram bearbetning genom att använda Hadoop och kan användas med Azure HDInsight. Diagram modell relationer mellan objekt. Till exempel anslutningarna mellan routrar i ett stort nätverk, till exempel Internet, eller relationer mellan personer i sociala nätverk. Med diagram bearbetning kan du på grund av relationerna mellan objekt i ett diagram, till exempel:

* Identifiera potentiella vänner utifrån dina aktuella relationer.

* Identifiera den kortaste vägen mellan två datorer i ett nätverk.

* Beräknar sid rangordningen för webb sidor.

> [!WARNING]  
> Komponenter som ingår i HDInsight-klustret stöds fullt ut – Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter, till exempel Giraph, tar emot affärsmässigt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Microsoft Support kanske kan lösa problemet. Om inte, måste du kontakta webb grupper med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, t. ex. [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projekt har även projekt webbplatser på [https://apache.org](https://apache.org), till exempel: [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Vad skriptet gör

Det här skriptet utför följande åtgärder:

* Installerar Giraph till `/usr/hdp/current/giraph`.

* Kopierar `giraph-examples.jar`-filen till standard lagring (WASB) för klustret: `/example/jars/giraph-examples.jar`.

## <a name="install-giraph-using-script-actions"></a>Installera Giraph med skript åtgärder

Det finns ett exempel skript för att installera Giraph i ett HDInsight-kluster på `https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`

Det här avsnittet innehåller anvisningar om hur du använder exempel skriptet när du skapar klustret med hjälp av Azure Portal.

> [!NOTE]  
> En skript åtgärd kan tillämpas med någon av följande metoder:
> * Azure PowerShell
> * Azure CLI
> * HDInsight .NET SDK
> * Azure Resource Manager-mallar
> 
> Du kan också använda skript åtgärder för att redan köra kluster. Mer information finns i [Anpassa HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

1. Börja skapa ett kluster genom att följa stegen i [skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md), men Slutför inte skapandet. Du måste använda den **klassiska skapa-upplevelsen** och **Anpassad (storlek, inställningar, appar)** .

1. I avsnittet **kluster storlek** ser du till att **antalet arbetsnoder** är minst 2, i det här exemplet.

1. Ange följande information i avsnittet **skript åtgärder** :

    |Egenskap |Värde |
    |---|---|
    |Skript typ|– Anpassad|
    |Namn|Installera Giraph|
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |Node-typ (er)|Huvud|
    |Parametrar|Lämna tomt|

    Mer information finns i [använda en skript åtgärd när klustret skapas](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation).

1. Fortsätt att skapa klustret enligt beskrivningen i [skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="how-do-i-use-giraph-in-hdinsight"></a>Hur gör jag för att använda Giraph i HDInsight?

När klustret har skapats kan du använda följande steg för att köra SimpleShortestPathsComputation-exemplet som ingår i Giraph. I det här exemplet används den grundläggande [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) -implementeringen för att hitta den kortaste vägen mellan objekt i ett diagram.

1. Använd [SSH-kommandot](./hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Använd följande kommando för att skapa en fil med namnet **tiny_graph. txt**:

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

    Dessa data beskriver en relation mellan objekt i ett riktat diagram med hjälp av formatet `[source_id, source_value,[[dest_id], [edge_value],...]]`. Varje rad representerar ett förhållande mellan ett `source_id`-objekt och ett eller flera `dest_id` objekt. `edge_value` kan ses som anslutningens styrka eller avstånd mellan `source_id` och `dest\_id`.

    Med hjälp av värdet (eller vikten) som avstånd mellan objekt kan data se ut som i följande diagram:

    ![tiny_graph. txt ritas som cirklar med linjer av varierande avstånd mellan](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Om du vill spara filen använder du **CTRL + X**, sedan **Y**och slutligen **RETUR** för att godkänna fil namnet.

4. Använd följande för att lagra data i Primär lagring för ditt HDInsight-kluster:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Kör SimpleShortestPathsComputation-exemplet med följande kommando:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > Värdet som skickas till `-w` måste vara mindre än eller lika med det faktiska antalet arbetsnoder.

    De parametrar som används med det här kommandot beskrivs i följande tabell:

   | Parameter | Vad verktyget gör |
   | --- | --- |
   | jar |Jar-filen som innehåller exemplen. |
   | org. apache. Giraph. GiraphRunner |Den klass som används för att starta exemplen. |
   | org. apache. Giraph. exempel. SimpleShortestPathsComputation |Exemplet som används. I det här exemplet beräknar den kortast sökvägen mellan ID 1 och alla andra ID: n i grafen. |
   | – ca mapred. job. Spårare |Huvudnoden för klustret. |
   | -vif |Det indataformat som ska användas för indata. |
   | -VIP |Indatafilen för indata. |
   | -vof |Utdataformatet. I det här exemplet är ID och värde som oformaterad text. |
   | – OP |Platsen för utdata. |
   | – w 2 |Antalet arbetare som ska användas. I det här exemplet 2. |

    Mer information om dessa och andra parametrar som används med Giraph-exempel finns i [snabb start för Giraph](https://giraph.apache.org/quick_start.html).

6. När jobbet är klart lagras resultaten i **/example/output/shortestpaths** -katalogen. Namnen på utdatafilerna börjar med **del-m-och-** slut med en siffra som visar den första, andra och så vidare, fil. Använd följande kommando för att visa utdata:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Utdata ser ut ungefär som i följande text:

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    SimpleShortestPathComputation-exemplet är hårdkodat för att starta med objekt-ID 1 och hitta den kortaste sökvägen till andra objekt. Utdata är i formatet `destination_id` och `distance`. `distance` är värdet (eller vikten) för de kanter som överförs mellan objekt-ID 1 och mål-ID.

    Visualisera dessa data, du kan kontrol lera resultaten genom att flytta de kortaste Sök vägarna mellan ID 1 och alla andra objekt. Den kortaste sökvägen mellan ID 1 och ID 4 är 5. Det här värdet är det totala avståndet mellan ID 1 och 3, och sedan ID 3 och 4.

    ![Ritning av objekt som cirklar med kortaste sökvägar som dras mellan](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Nästa steg

[Installera och Använd nyans på HDInsight-kluster](hdinsight-hadoop-hue-linux.md).
