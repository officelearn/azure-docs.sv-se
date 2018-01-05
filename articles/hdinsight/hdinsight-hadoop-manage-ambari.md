---
title: "Övervaka och hantera Azure HDInsight med Ambari-Webbgränssnittet | Microsoft Docs"
description: "Lär dig använda Ambari och övervaka och hantera Linux-baserade HDInsight-kluster. I det här dokumentet lär du dig att använda Ambari-Webbgränssnittet som ingår i HDInsight-kluster."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/04/2018
ms.author: larryfr
ms.openlocfilehash: dc1265c7af011b92a9f862cf2e91b47c3998b2e4
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Hantera HDInsight-kluster med Ambari-Webbgränssnittet

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari förenklar hantering och övervakning av ett Hadoop-kluster genom att tillhandahålla ett enkelt att använda webbgränssnittet och REST-API. Ambari ingår i Linux-baserade HDInsight-kluster och används för att övervaka klustret och gör ändringar i konfigurationen.

Lär dig hur du använder Ambari-Webbgränssnittet med ett HDInsight-kluster i det här dokumentet.

## <a id="whatis"></a>Vad är Ambari?

[Apache Ambari](http://ambari.apache.org) förenklar Hadoop-hanteringen genom att tillhandahålla en enkel att använda webbgränssnittet. Du kan använda Ambari för att hantera och övervaka Hadoop-kluster. Utvecklare kan integrera dessa funktioner i sina program med hjälp av den [Ambari REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari-Webbgränssnittet är som standard med HDInsight-kluster som använder Linux-operativsystem.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Anslutning

Ambari-Webbgränssnittet är tillgängligt på ditt HDInsight-kluster på HTTPS://CLUSTERNAME.azurehdidnsight.net, där **KLUSTERNAMN** är namnet på klustret.

> [!IMPORTANT]
> Ansluter till Ambari på HDInsight kräver HTTPS. När du uppmanas för autentisering, Använd admin-kontonamnet och lösenordet du angav när klustret skapades.

## <a name="ssh-tunnel-proxy"></a>SSH-tunnel (proxy)

När Ambari för klustret kan komma åt direkt via Internet, exponeras inte vissa länkar från Ambari-Webbgränssnittet (till exempel JobTracker) på internet. För att komma åt dessa tjänster måste du skapa en SSH-tunnel. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari-webbgränssnittet

> [!WARNING]
> Inte alla funktioner i Ambari-Webbgränssnittet stöds i HDInsight. Mer information finns i [stöds inte operations](#unsupported-operations) i det här dokumentet.

När du ansluter till Ambari-Webbgränssnittet, uppmanas du att autentisera till sidan. Använd kluster administratörsanvändare (standard Admin) och lösenord som du använde när klustret skapas.

När sidan öppnas Observera längst upp. Det här fältet innehåller följande information och kontroller:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari logotypen** -öppnar instrumentpanelen, som kan användas för att övervaka klustret.

* **Klustrets namn # ops** -visar antalet pågående Ambari-åtgärder. Att välja klusternamnet eller **# ops** visar en lista över bakgrundsåtgärder.

* **# aviseringar** -visar varningar eller kritiska varningar för klustret.

* **Instrumentpanelen** -instrumentpanelen visas.

* **Tjänster** - och konfigurationsinformation inställningar för tjänster i klustret.

* **Värdar** -Information och konfiguration av inställningar för noder i klustret.

* **Aviseringar** -en logg över information, varningar och kritiska aviseringar.

* **Admin** -stacken/programtjänster som är installerade på klustret, tjänstens kontoinformation och Kerberos-säkerhet.

* **Admin-knappen** -Ambari hantering, användarinställningar och logga ut.

## <a name="monitoring"></a>Övervakning

### <a name="alerts"></a>Aviseringar

Följande lista innehåller vanliga avisering status som används av Ambari:

* **OKEJ**
* **Varning**
* **KRITISKA**
* **OKÄND**

Aviseringar än **OK** orsaka det **# aviseringar** överst på sidan för att visa antalet aviseringar. Om du markerar den här posten visas aviseringar och deras status.

Aviseringar är uppdelade i flera standardgrupper som kan visas från den **aviseringar** sidan.

![Sidan varningar](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Du kan hantera grupperna med hjälp av den **åtgärder** menyn och välja **hantera avisering grupper**.

![Hantera aviseringar grupper dialogrutan](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Du kan också hantera aviseringar metoder och skapa aviseringar från den **åtgärder** menyn genom att välja __hantera avisering meddelanden__. De aktuella meddelanden visas. Du kan också skapa aviseringar här. Meddelanden kan skickas **e-post** eller **SNMP** när specifika/allvarlighetsgraden för aviseringen kombinationer inträffar. Du kan till exempel skicka ett e-postmeddelande när något av aviseringar i den **YARN standard** grupp har angetts till **kritisk**.

![Skapa varningsruta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Slutligen väljer __Hantera aviseringsinställningar__ från den __åtgärder__ menyn kan du ange antalet gånger som en avisering måste inträffa innan ett meddelande har skickats. Den här inställningen kan användas för att förhindra att meddelanden för tillfälliga fel.

### <a name="cluster"></a>Kluster

Den **mått** på instrumentpanelen innehåller ett antal widgetar som gör det lättare att övervaka status för klustret i korthet. Flera widgetar som **CPU-användning**, innehåller ytterligare information när du klickar på.

![instrumentpanel med mått](./media/hdinsight-hadoop-manage-ambari/metrics.png)

Den **Heatmaps** fliken visar mått som färgade heatmaps som kommer från grönt till rött.

![instrumentpanel med heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Mer information om noder i klustret, väljer **värdar**. Välj den specifika nod som du är intresserad av.

![information om värden](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Tjänster

Den **Services** sidopanelen på instrumentpanelen ger snabb inblick i status för de tjänster som körs på klustret. Olika ikoner används för att ange status eller åtgärder som ska vidtas. Till exempel visas en gul återvinning symbol om en tjänst behöver återvinnas.

![tjänster-sidorutan](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Tjänster som visas skiljer sig åt mellan HDInsight-klustertyper och versioner. De tjänster som visas här kan skilja sig från de tjänster som visas för klustret.

Att välja en tjänst visar mer detaljerad information om tjänsten.

![sammanfattningsinformation för tjänsten](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Snabblänkar

Vissa tjänster visas en **snabblänkar** längst upp på sidan. Detta kan användas för att komma åt tjänstspecifika web användargränssnitt, exempelvis:

* **Jobbhistorik** -MapReduce historiken för datalagerjobb.
* **Hanteraren för filserverresurser** -YARN ResourceManager-Användargränssnittet.
* **NameNode** -Hadoop Distributed File System (HDFS) NameNode Användargränssnittet.
* **Oozie webbgränssnittet** -Oozie-Användargränssnittet.

När du väljer ett av dessa länkar öppnas en ny flik i webbläsaren, som visar den valda sidan.

> [!NOTE]
> Att välja den **snabblänkar** post för en tjänst kan returnera ett ”det gick inte att hitta” serverfel. Om felet uppstår, måste du använda en SSH-tunnel när du använder den **snabblänkar** post för den här tjänsten. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Hantering

### <a name="ambari-users-groups-and-permissions"></a>Ambari användare, grupper och behörigheter

Arbeta med användare, grupper och behörigheter stöds när du använder en [domänanslutna](./domain-joined/apache-domain-joined-introduction.md) HDInsight-kluster. Mer information om med hjälp av Användargränssnittet för hantering av Ambari på en domänansluten klustret finns [hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]
> Ändra inte lösenordet för Ambari watchdog (hdinsightwatchdog) på Linux-baserade HDInsight-kluster. Ändra lösenord bryts möjligheten att använda script actions eller utföra skalning åtgärder med ditt kluster.

### <a name="hosts"></a>Värdar

Den **värdar** sidan listar alla värdar i klustret. Följ dessa steg för att hantera värdar.

![sidan för värdar](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> Lägga till, inaktivering och recommissioning en värd får inte användas med HDInsight-kluster.

1. Välj den värd som du vill hantera.

2. Använd den **åtgärder** -menyn och välj den åtgärd som du vill utföra:

   * **Starta alla komponenter** -starta alla komponenter på värden.

   * **Stoppa alla komponenter** -stoppa alla komponenter på värden.

   * **Starta om alla komponenter** - stoppa och starta alla komponenter på värden.

   * **Aktivera underhållsläget** -Undertrycker aviseringar för värden. Det här läget måste vara aktiverat om du utför åtgärder som genererar aviseringar. Exempelvis stoppa och starta en tjänst.

   * **Inaktivera underhållsläge** -returnerar värden till normal aviseringar.

   * **Stoppa** -stoppar DataNode eller NodeManagers på värden.

   * **Starta** -startar DataNode eller NodeManagers på värden.

   * **Starta om** -stoppar och startar DataNode eller NodeManagers på värden.

   * **Inaktivera** -tar bort en värd från klustret.

     > [!NOTE]
     > Använd inte den här åtgärden på HDInsight-kluster.

   * **Recommission** -lägger till en tidigare inaktiverade värd i klustret.

     > [!NOTE]
     > Använd inte den här åtgärden på HDInsight-kluster.

### <a id="service"></a>Tjänster

Från den **instrumentpanelen** eller **Services** använder den **åtgärder** knappen längst ned i listan över tjänster att stoppa och starta alla tjänster.

![tjänståtgärder](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Medan **lägga till tjänsten** visas i den här menyn den bör inte användas för att lägga till tjänster till HDInsight-klustret. Nya tjänster ska läggas till med en skriptåtgärd under klusteretablering. Mer information om hur du använder skriptåtgärder finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

När den **åtgärder** knappen kan starta om alla tjänster, ofta du vill starta, stoppa eller starta om en specifik tjänst. Använd följande steg för att utföra åtgärder på en enskild tjänst:

1. Från den **instrumentpanelen** eller **Services** väljer du en tjänst.

2. Från början av den **sammanfattning** använder den **tjänståtgärder** och välj åtgärden som ska vidtas. Detta startar om tjänsten på alla noder.

    ![åtgärden på tjänsten](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]
   > Starta om vissa tjänster när klustret är igång kan generera aviseringar. Om du vill undvika aviseringar kan du använda den **tjänståtgärder** för att aktivera **underhållsläge** för tjänsten innan du utför omstarten.

3. När en åtgärd har valts i **# op** post överst på sidan steg att visa att en bakgrundsåtgärden pågår. Om konfigurerad för att visa visas listan över bakgrundsåtgärder.

   > [!NOTE]
   > Om du har aktiverat **underhållsläge** för tjänsten, Kom ihåg att inaktivera med hjälp av den **tjänståtgärder** knappen när åtgärden har slutförts.

Använd följande steg för att konfigurera en tjänst:

1. Från den **instrumentpanelen** eller **Services** väljer du en tjänst.

2. Välj den **konfigurationerna** fliken. Den aktuella konfigurationen visas. En lista över tidigare konfigurationer visas också.

    ![Konfigurationer](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Använd de fält som visas om du vill ändra konfigurationen och välj sedan **spara**. Eller välj en tidigare konfiguration och välj sedan **se aktuella** att återställa de tidigare inställningarna.

## <a name="ambari-views"></a>Ambari-vyer

Ambari Views kan utvecklare plugin-UI-element i en Ambari-Webbgränssnittet med hjälp av den [Ambari vyer Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight innehåller följande vyer med Hadoop-klustertyper:

* Yarn Queue Manager: köhanteraren ger ett enkelt gränssnitt för att visa och ändra YARN köer.

* Hive-vyn: Hive-vy kan du köra Hive-frågor direkt från din webbläsare. Du kan spara frågor, visa resultat, spara resultaten för klusterlagring eller hämta resultaten till din lokala dator. Mer information om hur du använder Hive vyer finns [Använd Hive-vyer med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez vy: Visa Tez kan du bättre förstå och optimera jobb. Du kan visa information om hur Tez-jobb körs och vilka resurser som används.

## <a name="unsupported-operations"></a>Åtgärder som inte stöds

Följande Ambari-åtgärder stöds inte i HDInsight:

* __Flytta mått Collector-tjänsten__. När du visar information på mått Collector-tjänsten är en av åtgärderna som är tillgängliga på menyn tjänståtgärder __flytta mått insamlaren__. Detta stöds inte med HDInsight.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder den [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) med HDInsight.