---
title: Övervaka och hantera Azure HDInsight med hjälp av Ambari-Webbgränssnittet
description: Lär dig mer om att använda Ambari och övervaka och hantera Linux-baserade HDInsight-kluster. I detta dokument kan du lära dig hur du använder Ambari-Webbgränssnittet som medföljer HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 738ef5df0b2e2a7f31a7316a1d2ef4395168d41e
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576964"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari förenklar hantering och övervakning av ett Apache Hadoop-kluster genom att tillhandahålla ett enkelt sätt att använda webbgränssnittet och REST API. Ambari ingår i Linux-baserade HDInsight-kluster och används för att övervaka klustret och gör ändringar i konfigurationen.

Lär dig hur du använder Ambari-Webbgränssnittet med ett HDInsight-kluster i det här dokumentet.

## <a id="whatis"></a>Vad är Apache Ambari?

[Apache Ambari](https://ambari.apache.org) förenklar Hadoop-hanteringen genom att tillhandahålla en enkel att använda webbgränssnittet. Du kan använda Ambari för att hantera och övervaka Hadoop-kluster. Utvecklare kan integrera de här funktionerna i sina program med hjälp av den [Ambari REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari-Webbgränssnittet är som standard med HDInsight-kluster som använder Linux-operativsystem.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Anslutning

Ambari-Webbgränssnittet finns i ditt HDInsight-kluster på HTTPS://CLUSTERNAME.azurehdinsight.net, där **CLUSTERNAME** är namnet på klustret.

> [!IMPORTANT]  
> Ansluta till Ambari på HDInsight kräver HTTPS. När du uppmanas att autentisera, använda namnet på administratörskontot och lösenordet du angav när klustret har skapats.

## <a name="ssh-tunnel-proxy"></a>SSH-tunnel (proxy)

Ambari för ditt kluster är tillgängliga direkt via Internet, visas inte vissa länkar från Ambari-Webbgränssnittet (till exempel JobTracker) på internet. För att komma åt dessa tjänster måste du skapa en SSH-tunnel. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari-webbgränssnittet

> [!WARNING]  
> Inte alla funktioner i Ambari-Webbgränssnittet stöds i HDInsight. Mer information finns i den [stöds inte operations](#unsupported-operations) i det här dokumentet.

När du ansluter till Ambari-Webbgränssnittet, uppmanas du att autentisera till sidan. Använd kluster administratörsanvändare (standard administratör) och lösenordet som du använde när klustret skapas.

När du öppnar sidan Observera fältet högst upp. Det här fältet innehåller följande information och kontroller:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari-logotypen** -öppnas instrumentpanelen, som kan användas för att övervaka klustret.

* **Kluster namnet # ops** – visar antalet pågående Ambari-åtgärder. Att välja klusternamnet eller **# ops** visar en lista över bakgrundsåtgärder.

* **# aviseringar** -visar varningar eller kritiska varningar för klustret.

* **Instrumentpanelen** -instrumentpanelen visas.

* **Tjänster** -Information och konfiguration av inställningar för tjänster i klustret.

* **Värdar** -Information och vilka konfigurationsinställningar för noderna i klustret.

* **Aviseringar** – en logg över information, varningar och kritiska aviseringar.

* **Administratören** -stack/Programvarutjänster som är installerade på klustret, tjänstens kontoinformation och Kerberos-säkerhet.

* **Admin-knappen** -Ambari hantering, användarinställningar och utloggning.

## <a name="monitoring"></a>Övervakning

### <a name="alerts"></a>Aviseringar

I följande lista innehåller vanliga avisering statusar som används av Ambari:

* **OK**
* **Varning**
* **CRITICAL**
* **UNKNOWN**

Aviseringar än **OK** orsaka den **# aviseringar** överst på sidan för att visa antalet aviseringar. Om du väljer den här posten visas aviseringarna och deras status.

Aviseringar är uppdelade i flera standardgrupper som kan visas från den **aviseringar** sidan.

![Sidan varningar](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Du kan hantera grupperna med hjälp av den **åtgärder** menyn och välja **Hantera aviseringar grupper**.

![Hantera aviseringar grupper dialogrutan](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Du kan också hantera aviseringar metoder och skapa en avisering från den **åtgärder** menyn genom att välja __hantera avisering meddelanden__. Alla aktuella meddelanden visas. Du kan också skapa aviseringar här. Meddelanden kan skickas **e-post** eller **SNMP** när specifika/allvarlighetsgraden för aviseringen kombinationer inträffar. Du kan till exempel skicka ett e-postmeddelande när någon av aviseringar i den **YARN standard** gruppen angetts till **kritisk**.

![Skapa varningsruta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Slutligen väljer __Hantera aviseringsinställningar__ från den __åtgärder__ menyn kan du ange hur många gånger som en avisering måste inträffa innan ett meddelande har skickats. Den här inställningen kan användas för att förhindra att meddelanden för tillfälliga fel.

### <a name="cluster"></a>Kluster

Den **mått** fliken instrumentpanelen innehåller en serie widgetar som gör det enkelt att övervaka status för ditt kluster på ett ögonblick. Flera widgetar som **CPU-användning**, ger ytterligare information när du klickar på.

![instrumentpanel med mått](./media/hdinsight-hadoop-manage-ambari/metrics.png)

Den **termiska kartor** fliken visar mått som färgade termiska kartor, kommer från grön till röd.

![instrumentpanel med termiska kartor](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

För mer information om noder i klustret, väljer **värdar**. Välj sedan den specifika noden som du är intresserad av.

![värd-information](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Tjänster

Den **Services** sidopanelen på instrumentpanelen ger snabb inblick i status för de tjänster som körs i klustret. Olika ikoner används för att ange status eller åtgärder som ska vidtas. Till exempel visas en gul återvinning symbol om en tjänst behöver återvinnas.

![tjänster sidofältet](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> De tjänster som visas skiljer sig åt mellan HDInsight-klustertyper och versioner. De tjänster som visas här kan skilja sig från de tjänster som visas för ditt kluster.

Att välja en tjänst visar mer detaljerad information om tjänsten.

![sammanfattningsinformation för tjänsten](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Snabblänkar

Vissa tjänster visas en **snabblänkar** länken längst upp på sidan. Detta kan användas för att komma åt tjänstspecifika web UIs, som:

* **Jobbhistorik** -MapReduce jobbets historik.
* **Resource Manager** -YARN ResourceManager UI.
* **NameNode** -Hadoop Distributed File System (HDFS) NameNode Användargränssnittet.
* **Oozie webbgränssnittet** -Oozie-Användargränssnittet.

Om du väljer någon av dessa länkar öppnas en ny flik i webbläsaren som visar den valda sidan.

> [!NOTE]  
> Att välja den **snabblänkar** post för en tjänst kan returnera ett ”det gick inte att hitta” serverfel. Om felet uppstår, måste du använda en SSH-tunnel när du använder den **snabblänkar** posten för den här tjänsten. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Hantering

### <a name="ambari-users-groups-and-permissions"></a>Ambari-användare, grupper och behörigheter

Arbeta med användare, grupper och behörigheter kan användas med en [domänanslutna](./domain-joined/apache-domain-joined-introduction.md) HDInsight-kluster. Information om hur du använder Ambari Management UI på ett domänanslutet kluster finns i [hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]  
> Ändra inte lösenordet för Ambari-watchdog (hdinsightwatchdog) på Linux-baserade HDInsight-klustret. Ändra lösenordet delar möjligheten att använda skriptåtgärder eller utföra skalningsåtgärder med klustret.

### <a name="hosts"></a>Värdar

Den **värdar** sidan listar alla värdar i klustret. Följ dessa steg för att hantera värdar.

![sidan för värdar](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> Lägga till, ta ur drift och recommissioning en värd bör inte användas med HDInsight-kluster.

1. Välj den värd som du vill hantera.

2. Använd den **åtgärder** menyn för att markera den åtgärd som du vill utföra:

   * **Starta alla komponenter** -startar alla komponenter på värden.

   * **Stoppa alla komponenter** -stoppa alla komponenter på värden.

   * **Starta om alla komponenter** – stoppa och starta alla komponenter på värden.

   * **Aktivera underhållsläget** -Undertrycker aviseringar för värden. Det här läget måste vara aktiverad om du utför åtgärder som genererar aviseringar. Exempel: stoppa och starta en tjänst.

   * **Inaktivera underhållsläge** -returnerar värden till normal aviseringar.

   * **Stoppa** -stoppas DataNode eller NodeManagers på värden.

   * **Starta** -startar DataNode eller NodeManagers på värden.

   * **Starta om** -stoppar och startar DataNode eller NodeManagers på värden.

   * **Inaktivera** – tar bort en värd från klustret.

     > [!NOTE]  
     > Använd inte den här åtgärden på HDInsight-kluster.

   * **Recommission** -lägger till en tidigare inaktiverade värd i klustret.

     > [!NOTE]  
     > Använd inte den här åtgärden på HDInsight-kluster.

### <a id="service"></a>Tjänster

Från den **instrumentpanelen** eller **Services** kan du använda den **åtgärder** knappen längst ned i listan över tjänster att stoppa och starta alla tjänster.

![tjänståtgärder](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> Medan **Lägg till tjänst** visas i den här menyn den bör inte användas för att lägga till tjänster i HDInsight-klustret. Nya tjänster ska läggas till med en skriptåtgärd under klusteretablering. Mer information om hur du använder skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Medan den **åtgärder** knappen kan starta om alla tjänster, ofta du vill starta, stoppa eller starta om en specifik tjänst. Använd följande steg för att utföra åtgärder på en enskild tjänst:

1. Från den **instrumentpanelen** eller **Services** väljer du en tjänst.

2. Högst upp på den **sammanfattning** fliken den **tjänståtgärder** och välj åtgärden som ska vidtas. Detta startar om tjänsten på alla noder.

    ![tjänsteåtgärd](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Starta om vissa tjänster medan klustret är igång kan generera aviseringar. Om du vill undvika aviseringar kan du använda den **tjänståtgärder** knappen för att aktivera **underhållsläge** för tjänsten innan du utför omstarten.

3. När en åtgärd har valts, den **# op** post överst på sidan steg att visa att en bakgrundsåtgärden sker. Om konfigurerat för att visa visas listan över bakgrundsåtgärder.

   > [!NOTE]  
   > Om du har aktiverat **underhållsläge** för tjänsten, Kom ihåg att inaktivera den genom att använda den **tjänståtgärder** knappen när åtgärden har slutförts.

Om du vill konfigurera en tjänst, använder du följande steg:

1. Från den **instrumentpanelen** eller **Services** väljer du en tjänst.

2. Välj den **Peeringkonfigurationer** fliken. Den aktuella konfigurationen visas. En lista över tidigare konfigurationer visas också.

    ![Konfigurationer](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Använd de fält som visas för att ändra konfigurationen och välj sedan **spara**. Eller välj en tidigare konfiguration och välj sedan **gör aktuell** att återställa de tidigare inställningarna.

## <a name="ambari-views"></a>Ambari-vyer

Ambari-vyer kan utvecklare plugin-UI-element i Ambari-Webbgränssnittet med den [Apache Ambari-vyer Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight innehåller följande vyer med Hadoop-klustertyper:


* Hive-vyn: Hive-vyn kan du köra Hive-frågor direkt från din webbläsare. Du kan spara frågor, visa resultat, spara resultaten för klusterlagring eller hämta resultaten till din lokala dator. Mer information om hur du använder Hive-vyer finns i [använda Apache Hive-vyer med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez vy: Tez-vy kan du bättre förstå och optimera jobb. Du kan visa information om hur Tez-jobb körs och vilka resurser som används.

## <a name="unsupported-operations"></a>Åtgärder som inte stöds

Följande Ambari-åtgärder stöds inte på HDInsight:

* __Flytta mått Collector-tjänsten__. När du visar information på mått Collector-tjänsten är en av åtgärderna som är tillgängliga på menyn tjänståtgärder __flytta mått insamlaren__. Detta stöds inte med HDInsight.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder den [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) med HDInsight.
