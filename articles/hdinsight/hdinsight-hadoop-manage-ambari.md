---
title: Övervaka och hantera Azure HDInsight med Ambari Web UI
description: Lär dig hur du använder Ambari för att övervaka och hantera Linux-baserade HDInsight-kluster. I det här dokumentet får du lära dig hur du använder webbgränssnittet i Ambari som ingår i HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: bf780897317d41c7da85140f64313546cf5c31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064683"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari förenklar hanteringen och övervakningen av ett Apache Hadoop-kluster genom att tillhandahålla ett lättanvänt webbgränssnitt och REST API. Ambari ingår i HDInsight-kluster och används för att övervaka klustret och göra konfigurationsändringar.

I det här dokumentet får du lära dig hur du använder Ambari Web UI med ett HDInsight-kluster.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Vad är Apache Ambari?

[Apache Ambari](https://ambari.apache.org) förenklar Hadoop-hanteringen genom att tillhandahålla ett lättanvänt webbgränssnitt. Du kan använda Ambari för att hantera och övervaka Hadoop-kluster. Utvecklare kan integrera dessa funktioner i sina program med hjälp av [Ambari REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Anslutning

Ambari-webbgränssnittet är tillgängligt i ditt `https://CLUSTERNAME.azurehdinsight.net`HDInsight-kluster på , där `CLUSTERNAME` är namnet på klustret.

> [!IMPORTANT]  
> Att ansluta till Ambari på HDInsight kräver HTTPS. När du uppmanas att ange autentisering använder du administratörskontots namn och lösenord som du angav när klustret skapades. Om du inte uppmanas att ange autentiseringsuppgifter kontrollerar du nätverksinställningarna för att bekräfta att det inte finns något anslutningsproblem mellan klienten och Azure HDInsight-kluster.

## <a name="ssh-tunnel-proxy"></a>SSH-tunnel (proxy)

Medan Ambari för ditt kluster är tillgängligt direkt via Internet, är vissa länkar från Ambari Web UI (till exempel till JobTracker) inte exponeras på Internet. För att komma åt dessa tjänster måste du skapa en SSH-tunnel. Mer information finns i [Använda SSH-tunnel med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari webbgränssnitt

> [!WARNING]  
> Alla funktioner i Ambari Web UI stöds inte på HDInsight. Mer information finns i avsnittet [Åtgärder som inte stöds](#unsupported-operations) i det här dokumentet.

När du ansluter till webbgränssnittet i Ambari uppmanas du att autentisera till sidan. Använd den användare av klusteradministratör (standardadministratör) och lösenord som du använde när klustret skapades.

När sidan öppnas noterar du fältet högst upp. Det här fältet innehåller följande information och kontroller:

![Översikt över Apache Ambari-instrumentpanelen](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Objekt |Beskrivning |
|---|---|
|Ambari-logotyp|Öppnar instrumentpanelen, som kan användas för att övervaka klustret.|
|Klusternamn # ops|Visar antalet pågående Ambari-operationer. Om du väljer klusternamnet eller **# ops** visas en lista över bakgrundsåtgärder.|
|# varningar|Visar varningar eller eventuella kritiska aviseringar för klustret.|
|Instrumentpanel|Visar instrumentpanelen.|
|Tjänster|Information och konfigurationsinställningar för tjänsterna i klustret.|
|Värdar|Information och konfigurationsinställningar för noderna i klustret.|
|Aviseringar|En logg över information, varningar och kritiska aviseringar.|
|Admin|Programvarustack/tjänster som är installerade på klustret, tjänstkontoinformationen och Kerberos-säkerheten.|
|Knappen Admin|Ambari-hantering, användarinställningar och logga ut.|

## <a name="monitoring"></a>Övervakning

### <a name="alerts"></a>Aviseringar

Följande lista innehåller de vanliga varningsstatusar som används av Ambari:

* **OK**
* **Varning**
* **Kritiska**
* **Okänd**

Andra aviseringar än **OK** gör att posten **# varningar** högst upp på sidan visar antalet aviseringar. Om du väljer den här posten visas aviseringarna och deras status.

Aviseringar är ordnade i flera standardgrupper, som kan visas från sidan **Aviseringar.**

![Apache Ambari varnar sida sammanfattning](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Du kan hantera grupperna genom att använda **menyn Åtgärder** och välja **Hantera aviseringsgrupper**.

![Apache Ambari hanterar varningsgrupper](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Du kan också hantera aviseringar och skapa aviseringar på **Menyn Åtgärder** genom att välja __Hantera meddelanden__. Alla aktuella meddelanden visas. Du kan också skapa aviseringar härifrån. Meddelanden kan skickas via **e-post** eller **SNMP** när specifika kombinationer av aviseringar/allvarlighetsgrad inträffar. Du kan till exempel skicka ett e-postmeddelande när någon av aviseringarna i gruppen **YARN Standard** är inställd **på Kritisk**.

![Apache Ambari skapa varningsmeddelande](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Slutligen kan du ange hur många gånger en avisering måste inträffa innan ett meddelande skickas om du väljer __Hantera aviseringsinställningar__ på menyn __Åtgärder.__ Den här inställningen kan användas för att förhindra meddelanden om tillfälliga fel.

En självstudiekurs av ett aviseringsmeddelande med ett kostnadsfritt [SendGrid-konto](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)finns [i Konfigurera Apache Ambari-e-postmeddelanden i Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Kluster

Fliken Mått på instrumentpanelen innehåller en serie widgetar som gör det enkelt att övervaka status för **klustret** med ett ögonkast. Flera widgetar, till exempel **CPU-användning,** ger ytterligare information när du klickar på den.

![Apache Ambari-instrumentpanel med mått](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Fliken **Heatmaps** visar mätvärden som färgade heatmaps, från grönt till rött.

![Apache Ambari instrumentbräda med heatmaps](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Om du vill ha mer information om noderna i klustret väljer du **Värdar**. Välj sedan den specifika noden som du är intresserad av.

![Apache Ambari värd sammanfattning detaljer](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Tjänster

Sidofältet **Tjänster** på instrumentpanelen ger snabb inblick i statusen för de tjänster som körs i klustret. Olika ikoner används för att ange status eller åtgärder som ska vidtas. En gul återvinningssymbol visas till exempel om en tjänst behöver återvinnas.

![Apache Ambari tjänster sidofält](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Tjänsterna som visas skiljer sig åt mellan HDInsight-klustertyper och versioner. Tjänsterna som visas här kan skilja sig från de tjänster som visas för klustret.

Om du väljer en tjänst visas mer detaljerad information om tjänsten.

![Apache Ambari tjänst sammanfattning information](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Snabblänkar

Vissa tjänster visar en **snabblänk** längst upp på sidan. Detta kan användas för att komma åt tjänstspecifika webb-UIs, till exempel:

* **Jobbhistorik** - MapReduce jobbhistorik.
* **Resurshanteraren** - YARN ResourceManager UI.
* **NameNode** - Hadoop Distributed File System (HDFS) NameNode UI.
* **Oozie Web UI** - Oozie UI.

Om du väljer någon av dessa länkar öppnas en ny flik i webbläsaren, som visar den valda sidan.

> [!NOTE]  
> Om du väljer **posten Snabblänkar** för en tjänst kan felet "server inte hittas". Om du stöter på det här felet måste du använda en SSH-tunnel när du använder **snabblänkposten** för den här tjänsten. Mer information finns i [Använda SSH-tunnel med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Hantering

### <a name="ambari-users-groups-and-permissions"></a>Ambari-användare, grupper och behörigheter

Att arbeta med användare, grupper och behörigheter stöds när du använder en [domän ansluten](./domain-joined/hdinsight-security-overview.md) HDInsight-kluster. Information om hur du använder användargränssnittet för Ambari-hantering i ett domänanslutet kluster finns i [Hantera domänanslutna HDInsight-kluster](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Ändra inte lösenordet för Ambari vakthund (hdinsightwatchdog) på din Linux-baserade HDInsight kluster. Om du ändrar lösenordet bryts möjligheten att använda skriptåtgärder eller utföra skalningsåtgärder med klustret.

### <a name="hosts"></a>Värdar

På sidan **Värdar** visas alla värdar i klustret. Gör så här om du vill hantera värdar.

![Apache Ambari värd sida översikt](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Lägga till, inaktivera och återbeställa en värd bör inte användas med HDInsight-kluster.

1. Välj den värd som du vill hantera.

2. Använd **menyn Åtgärder** för att välja den åtgärd som du vill utföra:

    |Objekt |Beskrivning |
    |---|---|
    |Starta alla komponenter|Starta alla komponenter på värden.|
    |Stoppa alla komponenter|Stoppa alla komponenter på värden.|
    |Starta om alla komponenter|Stoppa och starta alla komponenter på värden.|
    |Aktivera underhållsläge|Undertrycker aviseringar för värden. Det här läget bör aktiveras om du utför åtgärder som genererar aviseringar. Till exempel stoppa och starta en tjänst.|
    |Inaktivera underhållsläge|Returnerar värden till normal avisering.|
    |Stoppa|Stoppar DataNode eller NodeManagers på värden.|
    |Start|Startar DataNode eller NodeManagers på värden.|
    |Starta om|Stoppar och startar DataNode eller NodeManagers på värden.|
    |Inaktiverar|Tar bort en värd från klustret. **Använd inte den här åtgärden på HDInsight-kluster.**|
    |Återtagande|Lägger till en tidigare inaktiverad värd i klustret. **Använd inte den här åtgärden på HDInsight-kluster.**|

### <a name="services"></a><a id="service"></a>Tjänster

På sidan **Instrumentpanel** eller **Tjänster** använder du knappen **Åtgärder** längst ned i listan över tjänster för att stoppa och starta alla tjänster.

![Apache Ambari tjänst åtgärder lista](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Lägg **till tjänst** visas på den här menyn, men den bör inte användas för att lägga till tjänster i HDInsight-klustret. Nya tjänster bör läggas till med hjälp av en skriptåtgärd under klusteretablering. Mer information om hur du använder skriptåtgärder finns i [Anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Medan **knappen Åtgärder** kan starta om alla tjänster, vill du ofta starta, stoppa eller starta om en viss tjänst. Gör så här för att utföra åtgärder på en enskild tjänst:

1. Välj en tjänst på sidan **Instrumentpanel** eller **Tjänster.**

2. Högst upp på fliken **Sammanfattning** använder du knappen **Serviceåtgärder** och väljer den åtgärd som ska vidtas. Detta startar om tjänsten på alla noder.

    ![Apache Ambari enskilda serviceåtgärder](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Om du startar om vissa tjänster medan klustret körs kan det generera aviseringar. För att undvika aviseringar kan du använda knappen **Serviceåtgärder** för att aktivera **underhållsläge** för tjänsten innan du gör omstarten.

3. När en åtgärd har valts ökar **posten # op** högst upp på sidan för att visa att en bakgrundsåtgärd inträffar. Om den är konfigurerad för att visas visas listan över bakgrundsåtgärder.

   > [!NOTE]  
   > Om du har aktiverat **underhållsläge** för tjänsten, kom ihåg att inaktivera den med hjälp av knappen **Serviceåtgärder** när åtgärden är klar.

Så här konfigurerar du en tjänst:

1. Välj en tjänst på sidan **Instrumentpanel** eller **Tjänster.**

2. Välj fliken **Configs.** Den aktuella konfigurationen visas. En lista över tidigare konfigurationer visas också.

    ![Apache Ambari-tjänstkonfiguration](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Använd de fält som visas för att ändra konfigurationen och välj sedan **Spara**. Eller välj en tidigare konfiguration och välj sedan **Gör aktuellt** för att återställa till föregående inställningar.

## <a name="ambari-views"></a>Ambari visningar

Ambari Views tillåter utvecklare att ansluta gränssnittselement till Ambari Web UI med hjälp av [Apache Ambari Views Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight innehåller följande vyer med Hadoop-klustertyper:

* Hive View: Med Hive-vyn kan du köra Hive-frågor direkt från din webbläsare. Du kan spara frågor, visa resultat, spara resultat i klusterlagringen eller hämta resultat till ditt lokala system. Mer information om hur du använder Hive-vyer finns i [Använda Apache Hive-vyer med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez View: The Tez View kan du bättre förstå och optimera jobb. Du kan visa information om hur Tez-jobb körs och vilka resurser som används.

## <a name="unsupported-operations"></a>Åtgärder som inte stöds

Följande Ambari-åtgärder stöds inte på HDInsight:

* __Flytta tjänsten Metrics Collector__. När du visar information om tjänsten Metrics Collector är en av de åtgärder som är tillgängliga på menyn Tjänståtgärder __Samlare__av Flytta mått . Detta stöds inte med HDInsight.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) med HDInsight.
