---
title: Installera publicerad program – H2O Sparkling Water - Azure HDInsight
description: Installera och använda Hadoop-program H2O Sparkling Water från tredje part.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e1e43bc06b30959bc5e7e692d88bd0d053a03ad4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593561"
---
# <a name="install-published-application---h2o-sparkling-water"></a>Installera publicerad program – H2O Sparkling Water

Den här artikeln beskrivs hur du installerar och kör den [H20 Sparkling Water](http://www.h2o.ai/) publicerat Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattform och en lista över tillgängliga oberoende programvaruleverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>Om H2O Sparkling Water

H2O Sparkling Water är en öppen källkod, fullständigt distribuerad minnesintern maskininlärningsplattform med Linjär skalbarhet. H2O Sparkling Water kan du kombinera de snabba, skalbara maskininlärningsalgoritmerna i H2O med funktionerna i Spark. Med Sparkling Water kan användarna förbättra beräkningen från Scala, R och Python med H2O Flow-Användargränssnittet.

H2O Sparkling Water innehåller:

* **Enkel att använda webbgränssnittet och välbekanta gränssnitten** – ange dig och kom igång snabbt med hjälp av antingen H2O intuitiva webbaserade Flow GUI eller programmeringsmiljöer som R, Python, Java, Scala, JSON och H2O-API: er.
* **Data-oberoende stöd för alla vanliga typer av databasen och filen** – enkelt utforska och modellera Big Data inifrån Microsoft Excel, R Studio, Tableau och mer. Anslut till data från HDFS, S3, SQL och NoSQL-datakällor.
* **Mycket skalbar munging för Stordata och analys** – H2O stora kopplingar kan utföra 7 x snabbare än R data.table åtgärder och skalas linjärt till 10 miljarder x 10 miljarder rad kopplingar.
* **Data i realtid bedömning** – snabbt distribuera modeller till produktion med hjälp av oformaterad gamla Java-objekt (POJO), modell-optimerade Java-objekt (MOJO) eller H2O REST API.

### <a name="resource-links"></a>Resurslänkar

* [H2O.AI teknisk översikt](http://jira.h2o.ai/)
* [H2O.AI hem](http://www.h2o.ai/)
* [H2O.AI dokumentation](http://docs.h2o.ai/)
* [H2O.AI Support](https://support.h2o.ai/)
* [H2O.AI öppen källkod kodbas](https://github.com/h2oai/)

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på ett nytt HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Kluster tier(s): Standard eller Premium
* Typ av kluster: Spark
* Kluster-versioner: 3.5 eller 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Installera H2O Sparkling Water publicerad program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program finns [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Starta H2O Sparkling Water

1. Efter installationen kan du kan börja använda H2O Sparkling Water (h2o sparklingwater) från ditt kluster i Azure-portalen genom att öppna Jupyter-anteckningsböcker (`https://<ClusterName>.azurehdinsight.net/jupyter`). Du kan också öppna Jupyter genom att välja **klusterinstrumentpanel** från fönstret kluster i portalen, väljer **Jupyter Notebook**. Du uppmanas att ange dina autentiseringsuppgifter. Ange klustrets Hadoop-autentiseringsuppgifter som anges på klustret skapas.

2. I Jupyter, visas tre mappar: H2O-PySparkling-exempel, exempel PySpark och Scala-exempel. Välj den **H2O-PySparkling-exempel** mapp.

    ![Startsida för Jupyter-anteckningsböcker](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Det första steget när du skapar en ny anteckningsbok är att konfigurera Spark-miljö. Den här informationen ingår i den **Sentiment_analysis_with_Sparkling_Water** exempel. När du konfigurerar Spark-miljö, måste du använda rätt jar och ange IP-adressen som tillhandahålls av utdata från den första cellen.

    ![Startsida för Jupyter-anteckningsböcker](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Starta H2O-kluster.

    ![Starta kluster](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. När H2O klustret är igång, öppna H2O Flow genom att gå till **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Om du inte kan öppna H2O Flow, försök rensa webbläsarens cacheminne. Om du fortfarande det går inte att nå den, du förmodligen inte har tillräckligt med resurser i ditt kluster. Prova att öka antalet arbetarnoder under den **skala kluster** alternativet i kluster-fönstret.

    ![H2O Flow instrumentpanel](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Välj den **Million_Songs.flow** exempel på menyn till höger. När du uppmanas att göra med en varning klickar du på **belastningen Notebook**. Den här demon är avsedd att köras på några minuter med hjälp av verkliga data. Målet är att förutsäga från data om Låt gavs ut före eller efter 2004 med hjälp av binär klassificering.

    ![Välj Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Hitta den sökväg som innehåller **milsongs-cls-train.csv.gz**, och Ersätt hela sökvägen med **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Hitta den sökväg som innehåller **milsongs-cls-test.csv.gz** och Ersätt den med **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. För att köra alla instruktioner i anteckningsboken cellerna, Välj den **kör alla** i verktygsfältet.

    ![Köra alla](./media/hdinsight-apps-install-h2o/run-all.png)

10. Du bör se utdata som liknar följande efter ett par minuter.

    ![Resultat](./media/hdinsight-apps-install-h2o/output.png)

Klart! Du har harnessed artificiell intelligens i Spark inom några minuter. Nu kan du utforska fler exempel som visar hur olika typer av machine learning-algoritmer i H2O Flow.

## <a name="next-steps"></a>Nästa steg

* [H2O dokumentation](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använda tomma kantnoder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom edge-nod för att komma åt HDInsight-kluster och för testning och som är värd för HDInsight-program.
