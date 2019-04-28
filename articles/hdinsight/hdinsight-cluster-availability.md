---
title: Så här övervakar du klustret med Ambari och Azure Monitor-loggar
description: Lär dig mer om att använda Ambari och Azure Monitor-loggar att övervaka hälsotillstånd hos kluster och tillgänglighet.
keywords: övervakning, ambari, övervaka, logganalys, varning, tillgänglighet, hälsa
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097068"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Så här övervakar du klustret med Ambari och Azure Monitor-loggar

HDInsight-kluster är både Apache Ambari, som ger hälsoinformation på ett ögonblick och fördefinierade aviseringar, samt Azure Monitor-loggar integration, som tillhandahåller frågningsbart mått och loggar, samt konfigurerbara aviseringar.

Det här dokumentet visar hur du använder dessa verktyg för att övervaka ditt kluster och går igenom några exempel för att konfigurera en Ambari-avisering, övervakning noden ordinarie taxan och skapa en Azure Monitor-avisering utlöses när ett pulsslag har tagits emot från en eller flera noder i fem timmar.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Instrumentpanel

Ambari-instrumentpanelen kan nås genom att klicka på den **Ambari home** länken i den **Klusterinstrumentpaneler** på HDInsight översikt bladet i Azure-portalen som visas nedan. Du kan också den kan nås genom att ange följande URL i en webbläsare [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight resource portal-vy](media/hdinsight-cluster-availability/portal-overview.png)

Du uppmanas sedan användarnamnet för klusterinloggning och lösenord. Ange autentiseringsuppgifterna du angav när du skapade klustret.

Du kommer sedan att tas till Ambari-instrumentpanelen, som innehåller widgetar som visar en handfull mått för att ge dig en snabb översikt över HDInsight-klustrets hälsotillstånd. Dessa widgetar visa mått, till exempel antalet live DataNodes (arbetsnoder) och JournalNodes (zookeeper-nod), NameNodes (huvudnoderna) drifttid som och mått som är specifika för vissa typer av klustret, t.ex. YARN ResourceManager drifttid för Spark och Hadoop-kluster.

![Ambari-instrumentpanel](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>-Värdar – visa status för enskild nod

Du kan också visa statusinformation för enskilda noder. Klicka på den **värdar** flik för att visa en lista över alla noder i klustret och visa grundläggande information om varje nod. Grön bock till vänster om varje nodnamnet anger alla komponenter som är igång på noden. Om en komponent inte är tillgänglig i en nod, visas en röd avisering triangel i stället för grön bock.

![Visa Ambari-värdar](media/hdinsight-cluster-availability/ambari-hosts.png)

Du kan klicka på den **namn** för en nod för att visa mer detaljerad värdmått för just den noden. Den här vyn visar status/tillgängligheten av varje komponent.

![Ambari värdar enkel nodvy](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-aviseringar

Ambari erbjuder även flera konfigurerbara aviseringar som kan upplyser om vissa händelser. När aviseringarna utlöses visas de i det övre vänstra hörnet av Ambari i en röd Aktivitetsikon som innehåller antalet aviseringar. Klicka på den här skylten visar en lista över aktuella aviseringar.

![Ambari varningsräkning](media/hdinsight-cluster-availability/ambari-alerts.png)

Om du vill visa en lista över varningsdefinitioner och deras status, klickar du på den **aviseringar** fliken enligt nedan.

![Ambari Aviseringsvy definitioner](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari erbjuder många fördefinierade aviseringar som rör tillgängliga, inklusive:

| Aviseringsnamn                        | Beskrivning                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sammanfattning av DataNode hälsotillstånd           | Den här tjänstnivå aviseringen utlöses om det finns felaktiga DataNodes                                                                                                                |
| NameNode High Availability Health | Den här tjänstnivå aviseringen utlöses om den aktiva NameNode eller vänteläge NameNode inte körs.                                                                              |
| Procent JournalNodes som är tillgängliga    | Den här aviseringen utlöses om antalet ned JournalNodes i klustret är större än det konfigurera kritiska tröskelvärdet. Det aggregerar resultaten av JournalNode processen kontroller. |
| Procent DataNodes som är tillgängliga       | Den här aviseringen utlöses om antalet ned DataNodes i klustret är större än det konfigurera kritiska tröskelvärdet. Det aggregerar resultaten av DataNode processen kontroller.       |

En fullständig lista över Ambari aviseringar som hjälp med att övervaka tillgängligheten för ett kluster finns [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Om du vill visa information om en avisering eller ändra kriterier, klickar du på den **namn** för aviseringen. Ta **sammanfattning av hälsotillstånd för DataNode** som exempel. Du kan se en beskrivning av aviseringen, samt specifika villkor som utlöser en avisering om ”varning” eller ”kritisk” och kontrollera intervallet för villkoret. Om du vill redigera konfigurationen klickar du på den **redigera** knappen i övre högra hörnet av rutan konfiguration.

![Ambari Aviseringskonfiguration](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Här kan du kan redigera beskrivningen och viktigast av allt, kontrollen intervall och tröskelvärden för varningar eller kritiska varningar.

![Ambari varningskonfigurationen redigeringsvyn](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

I det här exemplet kan du göra 2 feltillstånd DataNodes utlösa en kritisk varning och 1 feltillstånd DataNode endast utlösa en varning. Klicka på **spara** när du är klar redigering.

### <a name="email-notifications"></a>E-postmeddelanden

Du kan även konfigurera e-postaviseringar Ambari. Att göra det, när på den **aviseringar** klickar du på den **åtgärder** knappen i det övre vänstra, sedan **hantera meddelanden.**

![Ambari hantera meddelanden åtgärd](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

En dialogruta för att hantera aviseringar öppnas. Klicka på den **+** längst ned i dialogrutan och Fyll i de obligatoriska fälten att tillhandahålla Ambari med e-postadress serverinformation som skickar e-post.

> [!TIP]
> Konfigurera Ambari kan e-postmeddelanden vara ett bra sätt att få aviseringar på en plats när du hanterar många HDInsight-kluster.

## <a name="azure-monitor-logs-integration"></a>Integrering med Azure Monitor-loggar

Azure Monitor loggar aktiverar data som genereras av flera resurser, till exempel HDInsight-kluster som samlas in och aggregeras i en plats för att uppnå en enhetlig upplevelse för övervakning.

Som ett krav behöver du en Log Analytics-arbetsyta för att lagra insamlade data. Om du inte redan har skapat en kan du följa instruktionerna här: [Skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Aktivera integrering av HDInsight Azure Monitor-loggar

HDInsight-kluster resource sidan i portalen klickar du på den **Operations Management Suite** bladet. Klicka sedan på **aktivera** och välj Log Analytics-arbetsytan i listrutan.

![HDInsight Operations Management Suite-bladet](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Fråga mått och loggar tabeller i bladet loggar

När Azure Monitor log-integrering aktiverat (Detta kan ta några minuter), navigera till din **Log Analytics-arbetsytan** resursen och klicka på den **loggar** bladet

![Log Analytics-arbetsytebladet loggar](media/hdinsight-cluster-availability/portal-logs.png)

Den **loggar** bladet innehåller ett antal exempelfrågor, till exempel:

| Frågans namn                      | Beskrivning                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Datorer tillgänglighet idag    | Skapa diagram över hur många datorer som skickar loggar, varje timme                     |
| Lista pulsslag                 | Lista över alla datorn pulsslag från den senaste timmen                           |
| Sista pulsslaget för varje dator | Visa det sista pulsslaget som skickas av varje dator                             |
| Otillgängliga datorerna           | Lista över alla kända datorer som inte skickar ett pulsslag under de senaste 5 timmarna |
| Ordinarie taxan               | Beräkna ordinarie taxan för varje dator som är anslutna                |

Till exempel kör den **ordinarie taxan** exempelfråga genom att klicka på **kör** på den frågan, som visas i skärmbilden ovan. Detta visar ordinarie taxan för varje nod i klustret som en procentandel. Om du har aktiverat flera HDInsight-kluster att skicka mått till samma Log Analytics-arbetsyta, visas den ordinarie taxan för alla noder i dessa kluster visas.

![Logga Analytics-arbetsyta loggar bladet ”ordinarie taxan' exempelfråga](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Ordinarie taxan är mätt under en 24-timmarsperiod så att klustret måste köra minst 24 timmar innan du se priser för korrekt tillgänglighet.

Du kan fästa den här tabellen till en delad instrumentpanel genom att klicka på **PIN-kod** i det övre högra hörnet. Om du inte har någon skrivbar delade instrumentpaneler, kan du se hur du skapar ett här: [Skapa och dela instrumentpaneler i Azure-portalen](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Azure Monitor-aviseringar

Du kan också ställa in Azure Monitor-aviseringar som utlöses när värdet för ett mått eller en frågas resultat uppfyller vissa villkor. Exempelvis kan vi skapa en avisering om att skicka ett e-postmeddelande när en eller flera noder som inte har skickat ett pulsslag under 5 timmar (d.v.s. anses vara ej tillgänglig).

Från den **loggar** bladet kör den **otillgängliga datorerna** exempelfråga genom att klicka på **kör** på den frågan, enligt nedan.

![Logga exempelfråga i Analytics-arbetsyta loggar bladet ”otillgänglig datorer”](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Om alla noder är tillgängliga, bör den här frågan returnerar 0 resultat för tillfället. Klicka på **ny aviseringsregel** att börja konfigurera aviseringen för den här frågan.

![Log Analytics-arbetsyta ny aviseringsregel](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Det finns tre komponenter på en avisering: den *resource* som du vill skapa regeln (Log Analytics-arbetsytan i det här fallet), den *villkor* att utlösa aviseringen och *åtgärdsgrupper*  som fastställer vad som händer när en avisering utlöses.

Klicka på den **villkoret rubrik**, som visas nedan, för att slutföra konfigurationen av signallogiken.

![Varningsregeln villkor](media/hdinsight-cluster-availability/portal-condition-title.png)

Då öppnas det **konfigurera signallogiken** bladet.

Ange den **Avisera logic** avsnittet på följande sätt:

*Baserat på: Antal resultat, villkor: Över tröskeln: 0.*

Eftersom den här frågan returnerar endast otillgänglig noder som resultat, om antalet resultat är allt större än 0, ska aviseringen utlösas.

I den **Evaluated utifrån** anger den **period** och **frekvens** baserat på hur ofta du vill söka efter tillgängliga noder.

Observera att i den här aviseringen du vill kontrollera **Period = frekvens.** Mer information om period, frekvens och andra aviseringsparametrar finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Klicka på **klar** när du har konfigurerat signallogiken.

![Varningsregeln konfigurera signallogiken](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Om du inte redan har en befintlig åtgärdsgrupp, klickar du på **Skapa ny** under den **åtgärdsgrupper** avsnittet.

![Varningsregeln ny åtgärdsgrupp](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Då öppnas det **Lägg till åtgärdsgrupp** bladet. Välj en **namn på åtgärdsgrupp**, **kortnamnet**, **prenumeration**, och **resursgrupp.** Under den **åtgärder** väljer en **åtgärdsnamn** och välj **e-post/SMS/Push/röst** som den **åtgärdstyp.**

> [!NOTE]
> Det finns flera andra åtgärder som en avisering kan utlösa förutom en e-post/SMS/Push/röst, till exempel en Azure-funktion, LogicApp, Webhook, ITSM och Automation-Runbook. [Lära sig mer.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Då öppnas det **e-post/SMS/Push/röst** bladet. Välj en **namn** till mottagaren, **Kontrollera** den **e-post** rutan och Skriv en e-postadress som du vill att aviseringen ska skickas. Klicka på **OK** i den **e-post/SMS/Push/röst** bladet i den **Lägg till åtgärdsgrupp** bladet för att slutföra konfigurationen av din åtgärdsgruppen.

![Varningsregeln Lägg till åtgärdsgrupp](media/hdinsight-cluster-availability/portal-add-action-group.png)

När dessa blad stänger du bör se din åtgärdsgrupp som visas under den **åtgärdsgrupper** avsnittet. Slutför den **Aviseringsinformationen** avsnittet genom att skriva en **avisering Regelnamn** och **beskrivning** och välja en **allvarlighetsgrad**.
Klicka på **skapa varningsregel** ska slutföras.

![Skapa aviseringsregel Slutför](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Möjligheten att ange **allvarlighetsgrad** är ett kraftfullt verktyg som kan användas när du skapar flera aviseringar. Exempel: du kan skapa en avisering om du vill generera en varning (allvarlighetsgrad 1) om bara en huvudnod går ned och en ny avisering som genererar en kritisk (Sev 0) i det osannolika skulle att båda huvudnoder gå.

När villkoret för aviseringen är uppfyllt, aviseringen utlöses och du får ett e-postmeddelande med aviseringsinformation så här:

![Azure Monitor e-postavisering](media/hdinsight-cluster-availability/alert-email.png)

Du kan också visa alla aviseringar som har aktiverats, grupperade efter allvarlighetsgrad, genom att gå till den **aviseringar** -bladet i din **Log Analytics-arbetsytan**.

![Log Analytics-arbetsytan aviseringar](media/hdinsight-cluster-availability/portal-alerts.png)

När du klickar på en allvarlighetsgrad gruppering (d.v.s. **allvarlighetsgrad 1,** som markerade ovan) visar poster för alla aviseringar för den allvarlighetsgrad som har aktiverats som nedan:

![Log Analytics-arbetsyta sev 1-aviseringar](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Nästa steg
- [Tillgänglighet och tillförlitlighet för Apache Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md)
