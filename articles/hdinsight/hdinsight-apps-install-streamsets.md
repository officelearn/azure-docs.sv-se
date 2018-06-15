---
title: Installera publicerade program - StreamSets datainsamlaren - Azure HDInsight | Microsoft Docs
description: Installera och använda Hadoop-programmet StreamSets datainsamlaren från tredje part.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e433de82576f8b943988881ed0b6673c0dccd77e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401031"
---
# <a name="install-published-application---streamsets-data-collector"></a>Installera publicerade program - StreamSets datainsamlaruppsättning

Den här artikeln beskriver hur du installerar och kör den [StreamSets datainsamlaren för HDInsight](https://streamsets.com/) publicerade Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattformen och en lista över tillgängliga oberoende leverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>Om StreamSets datainsamlaruppsättning

Datainsamlaren StreamSets distribuerar ovanpå ett Azure HDInsight-program. StreamSets datainsamlaren innehåller en komplett integrerad utvecklingsmiljö (IDE) som kan du utforma, testa, distribuera och hantera alla-till-alla infognings-pipelines. Dessa pipelines kan nät dataströmmen och batch data och omfattar en mängd-stream omformningar, allt utan att behöva skriva egen kod.

StreamSets datainsamlaren kan du skapa dataflöden med flera Stordata komponenter såsom HDFS, Kafka, Solr, Hive, HBASE och Kudu. När StreamSets datainsamlaren körs på en gränsserver eller i Hadoop-kluster kan hämta du realtidsövervakning för både data avvikelser och data flödet åtgärder. Denna övervakning innehåller tröskelvärdesbaserad aviseringar avvikelseidentifiering och automatisk reparation av felposter.

StreamSets datainsamlaren är utformat för att isolera varje steg i en pipeline logiskt så att du kan uppfylla nya affärsbehov genom att släppa i nya processorer och kopplingar utan kodning och med minimal avbrottstid.

### <a name="streamsets-resource-links"></a>StreamSets resurslänkar

* [Dokumentation](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blogg](https://streamsets.com/blog/)
* [Självstudier](https://github.com/streamsets/tutorials)
* [Supportforum för utvecklare](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slack offentliga StreamSets kanal](https://streamsetters.slack.com/)
* [Källkoden](https://github.com/streamsets)

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på en ny HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Klustret tier(s): Standard eller Premium
* Kluster-versioner: 3.5 och senare

## <a name="install-the-streamsets-data-collector-published-application"></a>Installera StreamSets datainsamlaren publicerat program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program läsa [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Starta datainsamlaruppsättningen StreamSets

1. Efter installationen kan du starta StreamSets från ditt kluster i Azure-portalen genom att gå till den **inställningar** rutan, sedan välja **program** under den **allmänna** kategori. Den installerade appar rutan visas de installerade programmen.

    ![Installerade StreamSets app](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. När du väljer StreamSets datainsamlaren finns en länk till webbsidan och sökväg för SSH-slutpunkt. Välj länken för WEBBSIDAN.

3. Använd följande autentiseringsuppgifter i dialogrutan Logga in för att logga in: `admin` och `admin`.

4. På sidan komma igång **Skapa ny Pipeline**.

    ![Skapa ny pipeline](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Ange ett namn för pipelinen (”Hello World”) i fönstret ny Pipeline, om du vill ange en beskrivning och välj **spara**.

6. Datainsamlaren konsolen visas. Panelen Egenskaper visar pipeline-egenskaper.
 
    ![Data Collector konsolen](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Du är nu redo att följa den [StreamSets kursen](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Guiden innehåller stegvisa anvisningar för att skapa din första pipeline.

## <a name="next-steps"></a>Nästa steg

* [StreamSets datainsamlaren dokumentationen](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserat HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använd tom edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom kantnod för åtkomst till HDInsight-kluster och för att testa och värd för HDInsight-program.
