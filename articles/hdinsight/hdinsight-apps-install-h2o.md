---
title: "Installera publicerade program - H2O mousserande vattenstämplar - Azure HDInsight | Microsoft Docs"
description: "Installera och använda Hadoop-programmet H2O mousserande vattenstämplar från tredje part."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8734daa5303aa76e9f8a074b5f709727cabb58b2
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Installera programmet - H2O mousserande vattenstämplar

Den här artikeln beskriver hur du installerar och kör den [H20 mousserande vattenstämplar](http://www.h2o.ai/) publicerade Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattformen och en lista över tillgängliga oberoende leverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>Om H2O mousserande vattenstämplar

H2O mousserande vattenstämplar är en öppen källkod, distribuerade i minnet machine learning plattform med Linjär skalbarhet. H2O mousserande vattenstämplar kan du kombinera de snabba och skalbara maskininlärningsalgoritmer för H2O med funktionerna i Spark. Med mousserande vattenstämplar enheten användare beräkning från Scala, R och Python med hjälp av H2O flöda Användargränssnittet.

H2O mousserande vattenstämplar innehåller:

* **Lätt att använda WebUI och bekant gränssnitt** – Ställ in och komma igång snabbt med hjälp av antingen H2O intuitiva webbaserade flöda GUI eller programmeringsmiljöer som R, Python, Java, Scala, JSON och H2O-API: er.
* **Data-oberoende stöd för alla vanliga typer av databasen och filen** – enkelt utforska och modellerar Big Data från i Microsoft Excel, R Studio, Tableau och mycket mer. Ansluta till data från HDFS, S3, SQL och NoSQL-datakällor.
* **Mycket skalbar Stordata munging och analys** – H2O stort kopplingar kan utföra 7 x snabbare än R data.table åtgärder och skalas linjärt till 10 miljarder x 10 miljarder raden kopplingar.
* **Realtidsdata bedömningen** – snabbt distribuera modeller till produktionsmiljön med oformaterad gamla Java objects (POJO), modell-optimerad Java objects (MOJO) eller H2O REST API.

### <a name="resource-links"></a>Resurslänkar

* [Översikt över H2O.AI-tekniker](https://jira.h2o.ai/)
* [Startsida för H2O.AI](http://www.h2o.ai/)
* [H2O.AI dokumentation](http://docs.h2o.ai/)
* [H2O.AI Support](https://support.h2o.ai/)
* [H2O.AI öppen källkod Codebase](https://github.com/h2oai/)

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på en ny HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Klustret tier(s): Standard eller Premium
* Kluster-typ: Spark
* Kluster-versioner: 3.5 eller 3,6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Installera H2O mousserande vattenstämplar publicerat program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program läsa [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Starta H2O mousserande vattenstämplar

1. Efter installationen kan du kan börja använda H2O mousserande vattenstämplar (h2o sparklingwater) från ditt kluster i Azure-portalen genom att öppna Jupyter-anteckningsböcker (`https://<ClusterName>.azurehdinsight.net/jupyter`). Du kan också få Jupyter genom att välja **klusterinstrumentpanel** från klustret rutan i portalen, sedan välja **Jupyter-anteckningsbok**. Du uppmanas att ange dina autentiseringsuppgifter. Ange klustrets Hadoop autentiseringsuppgifter som anges på klustret skapas.

2. Jupyter, visas i tre mappar: H2O-PySparkling-exempel, PySpark exempel och Scala exempel. Välj den **H2O-PySparkling-exempel** mapp.

    ![Jupyter-anteckningsböcker hemnätverk](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Det första steget när du skapar en ny anteckningsbok är att konfigurera Spark-miljö. Den här informationen ingår i den **Sentiment_analysis_with_Sparkling_Water** exempel. När du konfigurerar Spark-miljön, måste du använda rätt jar och ange IP-adressen som tillhandahålls av utdata från den första cellen.

    ![Jupyter-anteckningsböcker hemnätverk](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Starta H2O klustret.

    ![Starta klustret](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. När klustret H2O är igång, öppna H2O flöda genom att gå till  **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`** .

    > [!NOTE]
    > Om det inte går att öppna H2O flöda kan du prova att rensa webbläsarens cacheminne. Om du fortfarande inte nå den, du förmodligen inte har tillräckligt med resurser på ditt kluster. Försök att öka antalet arbetarnoder under den **kluster** alternativet i klustret-fönstret.

    ![H2O flöda instrumentpanelen](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Välj den **Million_Songs.flow** exempel på menyn till höger. När du uppmanas att göra med en varning klickar du på **belastningen anteckningsboken**. Den här demon är avsedd att köras på några minuter med hjälp av verkliga data. Målet är att förutsäga från data om Låt gavs ut före eller efter 2004 med hjälp av binär klassificering.

    ![Välj Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Den sökväg som innehåller **milsongs-cls-train.csv.gz**, och Ersätt hela sökvägen med **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz** .

8. Den sökväg som innehåller **milsongs-cls-test.csv.gz** och Ersätt den med **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Om du vill köra alla instruktioner i cellerna bärbara datorer, Välj den **kör alla** i verktygsfältet.

    ![Kör alla](./media/hdinsight-apps-install-h2o/run-all.png)

10. Du bör se utdata som liknar följande efter några minuter.

    ![Resultat](./media/hdinsight-apps-install-h2o/output.png)

Klart! Du har harnessed styrs av datorn i Spark inom några minuter. Du kan nu utforska fler exempel som visar olika typer av maskininlärningsalgoritmer i H2O flöda.

## <a name="next-steps"></a>Nästa steg

* [H2O dokumentation](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserat HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använd tom edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom kantnod för åtkomst till HDInsight-kluster och för att testa och värd för HDInsight-program.
