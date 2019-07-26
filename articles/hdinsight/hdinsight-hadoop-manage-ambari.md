---
title: Övervaka och hantera Azure HDInsight med Ambari-webbgränssnitt
description: Lär dig hur du använder Ambari för att övervaka och hantera Linux-baserade HDInsight-kluster. I det här dokumentet får du lära dig hur du använder Ambari-webbgränssnittet som ingår i HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: d0641a1c058db59acd5e9a64b10bb57b334f82bd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442060"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Hantera HDInsight-kluster med hjälp av webbgränssnittet Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari fören klar hanteringen och övervakningen av ett Apache Hadoop kluster genom att tillhandahålla ett enkelt sätt att använda webb gränssnitt och REST API. Ambari ingår i HDInsight-kluster och används för att övervaka klustret och göra konfigurations ändringar.

I det här dokumentet får du lära dig hur du använder Ambari-webbgränssnittet med ett HDInsight-kluster.

## <a id="whatis"></a>Vad är Apache Ambari?

[Apache Ambari](https://ambari.apache.org) fören klar Hadoop-hanteringen genom att tillhandahålla ett LÄTTANVÄNT webb gränssnitt. Du kan använda Ambari för att hantera och övervaka Hadoop-kluster. Utvecklare kan integrera dessa funktioner i sina program med hjälp av [AMBARI REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Anslutningar

Ambari-webbgränssnittet är tillgängligt i ditt HDInsight- `https://CLUSTERNAME.azurehdinsight.net`kluster på `CLUSTERNAME` , där är namnet på klustret.

> [!IMPORTANT]  
> För att ansluta till Ambari i HDInsight krävs HTTPS. När du tillfrågas om autentisering använder du det administratörs konto namn och lösen ord som du angav när klustret skapades.

## <a name="ssh-tunnel-proxy"></a>SSH-tunnel (proxy)

Medan Ambari för klustret kan nås direkt via Internet, visas inte vissa länkar från Ambari-webbgränssnittet (till exempel till JobTracker) på Internet. För att få åtkomst till dessa tjänster måste du skapa en SSH-tunnel. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari webb gränssnitt

> [!WARNING]  
> Det finns inte stöd för alla funktioner i Ambari-webbgränssnittet i HDInsight. Mer information finns i avsnittet [åtgärder som inte stöds](#unsupported-operations) i det här dokumentet.

När du ansluter till Ambari-webbgränssnittet uppmanas du att autentisera till sidan. Använd administratörs användaren för kluster (standard administratör) och lösen ord som du använde när du skapade klustret.

När sidan öppnas noterar du fältet överst. Det här fältet innehåller följande information och kontroller:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

|Objekt |Beskrivning |
|---|---|
|Ambari-logotyp|Öppnar instrument panelen som kan användas för att övervaka klustret.|
|Kluster namn # OPS|Visar antalet pågående Ambari-åtgärder. Om du väljer kluster namnet eller **# Ops** visas en lista över bakgrunds åtgärder.|
|antal aviseringar|Visar eventuella varningar eller kritiska aviseringar för klustret.|
|Instrumentpanel|Visar instrument panelen.|
|Tjänster|Information och konfigurations inställningar för tjänsterna i klustret.|
|Värdar|Information och konfigurations inställningar för noderna i klustret.|
|Aviseringar|En logg med information, varningar och viktiga aviseringar.|
|Admin|Program varu stack/tjänster som är installerade i klustret, tjänst konto information och Kerberos-säkerhet.|
|Knappen administratör|Hantering av Ambari, användar inställningar och utloggning.|

## <a name="monitoring"></a>Övervakning

### <a name="alerts"></a>Aviseringar

Följande lista innehåller de vanliga aviserings statusarna som används av Ambari:

* **OK**
* **Honom**
* **MINDRE**
* **OKÄND**

Andra aviseringar än **OK** leder till posten **# Alerts** överst på sidan för att visa antalet aviseringar. Om du väljer den här posten visas aviseringarna och deras status.

Aviseringar organiseras i flera standard grupper som kan visas från sidan **aviseringar** .

![Sidan aviseringar](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Du kan hantera grupper med hjälp av **åtgärder** -menyn och välja **Hantera aviserings grupper**.

![dialog rutan hantera aviserings grupper](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Du kan också hantera aviserings metoder och skapa aviserings meddelanden från menyn **åtgärder** genom att välja __Hantera aviserings meddelanden__. Alla aktuella meddelanden visas. Du kan också skapa meddelanden härifrån. Aviseringar kan skickas via **e-post** eller **SNMP** när vissa kombinationer av aviseringar/allvarlighets grader inträffar. Du kan till exempel skicka ett e-postmeddelande när någon av aviseringarna i **standard gruppen garn** är inställd på **kritisk**.

![Dialog rutan skapa avisering](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Slutligen kan du välja __Hantera aviserings inställningar__ på menyn __åtgärder__ för att ange hur många gånger en avisering måste inträffa innan ett meddelande skickas. Den här inställningen kan användas för att förhindra meddelanden om tillfälliga fel.

### <a name="cluster"></a>Kluster

Fliken **mått** på instrument panelen innehåller en serie med widgetar som gör det enkelt att snabbt övervaka status för klustret. Flera widgetar, till exempel **processor användning**, ger ytterligare information när du klickar på den.

![instrument panel med mått](./media/hdinsight-hadoop-manage-ambari/metrics.png)

Fliken **termiska kartor** visar mått som färgad termiska kartor, från grönt till rött.

![instrument panel med termiska kartor](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Om du vill ha mer information om noderna i klustret väljer du **värdar**. Välj sedan den angivna noden som du är intresse rad av.

![värd information](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Tjänster

Sid panelen **tjänster** på instrument panelen ger snabb insyn i statusen för de tjänster som körs i klustret. Olika ikoner används för att indikera status eller åtgärder som ska vidtas. Till exempel visas en gul åter användnings symbol om en tjänst behöver återvinnas.

![tjänstens sid List](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> De tjänster som visas skiljer sig mellan olika typer av HDInsight-kluster och-versioner. De tjänster som visas här kan skilja sig från de tjänster som visas för klustret.

Om du väljer en tjänst visas mer detaljerad information om tjänsten.

![tjänst sammanfattnings information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Snabblänkar

Vissa tjänster visar länken **länkar** överst på sidan. Detta kan användas för att få åtkomst till tjänstspecifika webb-UIs, till exempel:

* **Jobb historik** – jobb historik för MapReduce.
* **Resource Manager** -garn-RESOURCEMANAGER-gränssnitt.
* NameNode-gränssnitt för **NameNode** -HADOOP DISTRIBUTED File System (HDFS).
* **Oozie Web UI** – Oozie UI.

Om du väljer någon av dessa länkar öppnas en ny flik i webbläsaren som visar den valda sidan.

> [!NOTE]  
> Om du väljer posten **snabb länkar** för en tjänst kan du returnera felet "servern hittades inte". Om det här felet uppstår måste du använda en SSH-tunnel när du använder posten **snabb länkar** för den här tjänsten. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Hantering

### <a name="ambari-users-groups-and-permissions"></a>Ambari användare, grupper och behörigheter

Att arbeta med användare, grupper och behörigheter stöds när du [använder ett domänanslutna](./domain-joined/hdinsight-security-overview.md) HDInsight-kluster. Information om hur du använder gränssnittet för hantering av Ambari på ett domänanslutet kluster finns i [Hantera domänanslutna HDInsight-kluster](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Ändra inte lösen ordet för Ambari-hdinsightwatchdog () på ditt Linux-baserade HDInsight-kluster. Om du ändrar lösen ordet bryts möjligheten att använda skript åtgärder eller utföra skalnings åtgärder med klustret.

### <a name="hosts"></a>Värdar

Sidan **värdar** visar alla värdar i klustret. Följ dessa steg om du vill hantera värdar.

![Sidan värdar](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> Det ska inte användas med HDInsight-kluster för att lägga till, ta bort och ställa av en värd.

1. Välj den värd som du vill hantera.

2. Använd menyn **åtgärder** för att välja den åtgärd som du vill utföra:

    |Objekt |Beskrivning |
    |---|---|
    |Starta alla komponenter|Starta alla komponenter på värden.|
    |Stoppa alla komponenter|Stoppa alla komponenter på värden.|
    |Starta om alla komponenter|Stoppa och starta alla komponenter på värden.|
    |Aktivera underhålls läge|Ignorerar aviseringar för värden. Det här läget bör vara aktiverat om du utför åtgärder som genererar aviseringar. Till exempel stoppa och starta en tjänst.|
    |Inaktivera underhålls läge|Returnerar värden till normal avisering.|
    |Stoppa|Stoppar DataNode eller) Nodemanagers på värden.|
    |Start|Startar DataNode eller) Nodemanagers på värden.|
    |Starta om|Stoppar och startar DataNode eller) Nodemanagers på värden.|
    |Inaktivera|Tar bort en värd från klustret. **Använd inte den här åtgärden på HDInsight-kluster.**|
    |Reprovision|Lägger till en tidigare inaktive rad värd i klustret. **Använd inte den här åtgärden på HDInsight-kluster.**|

### <a id="service"></a>Terminal

På sidan **instrument panel** eller **tjänster** använder du knappen **åtgärder** längst ned i listan över tjänster för att stoppa och starta alla tjänster.

![tjänst åtgärder](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> När du **lägger till tjänsten** i listan i den här menyn bör den inte användas för att lägga till tjänster i HDInsight-klustret. Nya tjänster ska läggas till med en skript åtgärd under kluster etableringen. Mer information om hur du använder skript åtgärder finns i [Anpassa HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Medan knappen **åtgärder** kan starta om alla tjänster, ofta vill du starta, stoppa eller starta om en speciell tjänst. Använd följande steg för att utföra åtgärder på en enskild tjänst:

1. På sidan **instrument panel** eller **tjänster** väljer du en tjänst.

2. Överst på fliken **Sammanfattning** använder du knappen **service åtgärder** och väljer den åtgärd som ska vidtas. Detta startar om tjänsten på alla noder.

    ![tjänst åtgärd](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Att starta om vissa tjänster medan klustret körs kan generera aviseringar. För att undvika aviseringar kan du använda knappen **service åtgärder** för att aktivera **underhålls läget** för tjänsten innan du utför omstarten.

3. När en åtgärd har valts visas posten **# op** överst på sidan för att visa att en bakgrunds åtgärd inträffar. Om den är konfigurerad att Visa visas listan över bakgrunds åtgärder.

   > [!NOTE]  
   > Om du har aktiverat **underhålls läge** för tjänsten måste du komma ihåg att inaktivera det genom att använda knappen för **tjänst åtgärder** när åtgärden har slutförts.

Använd följande steg för att konfigurera en tjänst:

1. På sidan **instrument panel** eller **tjänster** väljer du en tjänst.

2. Välj den **Peeringkonfigurationer** fliken. Den aktuella konfigurationen visas. En lista över tidigare konfigurationer visas också.

    ![konfigurationer](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Använd fälten som visas för att ändra konfigurationen och välj sedan **Spara**. Eller Välj en tidigare konfiguration och välj sedan **gör aktuell** för att återställa till föregående inställningar.

## <a name="ambari-views"></a>Ambari-vyer

Med Ambari vyer kan utvecklare koppla GRÄNSSNITTs element till Ambari-webbgränssnittet med [Apache Ambari views](https://cwiki.apache.org/confluence/display/AMBARI/Views)-ramverket. HDInsight tillhandahåller följande vyer med Hadoop-kluster typer:

* Hive-vy: I Hive-vyn kan du köra Hive-frågor direkt från webbläsaren. Du kan spara frågor, Visa resultat, Spara resultat i kluster lagringen eller ladda ned resultat till det lokala systemet. Mer information om hur du använder Hive-vyer finns i [använda Apache Hive vyer med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez vy: Med vyn Tez kan du bättre förstå och optimera jobb. Du kan visa information om hur Tez-jobb körs och vilka resurser som används.

## <a name="unsupported-operations"></a>Åtgärder som inte stöds

Följande Ambari-åtgärder stöds inte i HDInsight:

* __Flyttar mått insamlings tjänsten__. När du visar information på mått insamlings tjänsten, flyttas en av de åtgärder som är tillgängliga från menyn tjänst åtgärder till __mått__insamlaren. Detta stöds inte med HDInsight.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) med HDInsight.