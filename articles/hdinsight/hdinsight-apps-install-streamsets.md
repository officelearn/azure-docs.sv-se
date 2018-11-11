---
title: Installera publicerad program – StreamSets Data Collector - Azure HDInsight
description: Installera och använda Apache Hadoop-program StreamSets Data Collector från tredje part.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: f963ae53e1396b1ef6279f2bd6502e5ab0cd23a1
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034565"
---
# <a name="install-published-application---streamsets-data-collector"></a>Installera publicerad program – StreamSets Data Collector

Den här artikeln beskrivs hur du installerar och kör den [StreamSets Data Collector för HDInsight](https://streamsets.com/) publicerade Apache Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattform och en lista över tillgängliga oberoende programvaruleverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>Om StreamSets Data Collector

StreamSets Data Collector distribueras ovanpå ett Azure HDInsight-program. StreamSets Data Collector ger en komplett integrerad utvecklingsmiljö (IDE) inmatningspipelines kan du utforma, testa, distribuera och hantera any-to-any. Dessa pipelines nät batchdata och ta med olika omvandlingar i strömmen, allt utan att skriva anpassad kod.

StreamSets Data Collector kan du skapa dataflöden med hjälp av ett stort antal Big Data-komponenter, till exempel HDFS, Kafka, Solr, Hive, HBASE och Kudu. När StreamSets Data Collector körs på en edge-server eller i ditt Hadoop-kluster kan få du realtidsövervakning för såväl data avvikelser som flow åtgärder. Den här övervakning innefattar tröskelbaserade aviseringar, avvikelseidentifiering och automatisk reparation av felposter.

StreamSets Data Collector är utformad för att isolera varje steg i en pipeline logiskt så att du kan uppfylla nya affärsbehov genom att släppa nya processorer och anslutningar utan kodning och med minimal avbrottstid.

### <a name="streamsets-resource-links"></a>StreamSets resurslänkar

* [Dokumentation](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blogg](https://streamsets.com/blog/)
* [Självstudier](https://github.com/streamsets/tutorials)
* [Supportforum för utvecklare](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Kanal för Slack offentliga StreamSets](https://streamsetters.slack.com/)
* [Källkod](https://github.com/streamsets)

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på ett nytt HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Kluster tier(s): Standard eller Premium
* Kluster-versioner: 3.5 och senare

## <a name="install-the-streamsets-data-collector-published-application"></a>Installera StreamSets Data Collector publicerad program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program finns [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Starta StreamSets Data Collector

1. Efter installationen kan du starta StreamSets från ditt kluster i Azure-portalen genom att gå till den **inställningar** och sedan välja **program** under den **Allmänt** kategori. Fönstret installerade appar visar en lista över de installerade programmen.

    ![Installerade StreamSets app](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. När du väljer StreamSets Data Collector, visas en länk till sidan och sökvägen för SSH-slutpunkten. Välj länken för WEBBSIDAN.

3. Använd följande autentiseringsuppgifter för inloggning i dialogrutan inloggning: `admin` och `admin`.

4. På sidan Kom igång **Skapa ny Pipeline**.

    ![Skapa ny pipeline](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Ange ett namn för pipelinen (”Hello World”) i fönstret ny Pipeline, du kan också ange en beskrivning och välj **spara**.

6. Konsolen för datainsamling visas. Panelen Egenskaper Visar egenskaper för pipeline.
 
    ![Data Collector-konsolen](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Du är nu redo att följa den [StreamSets självstudien](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Självstudiekursen innehåller stegvisa anvisningar för hur du skapar din första pipeline.

## <a name="next-steps"></a>Nästa steg

* [StreamSets Data Collector dokumentation](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använda tomma kantnoder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom edge-nod för att komma åt HDInsight-kluster och för testning och som är värd för HDInsight-program.
