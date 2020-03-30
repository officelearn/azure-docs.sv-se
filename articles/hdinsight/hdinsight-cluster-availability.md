---
title: 'Övervakning: Apache Ambari & Azure Monitor-loggar - Azure HDInsight'
description: Lär dig hur du använder Ambari- och Azure Monitor-loggar för att övervaka klusterhälsa och tillgänglighet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060190"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Så här övervakar du klustertillgänglighet med Apache Ambari- och Azure Monitor-loggar

HDInsight-kluster inkluderar både Apache Ambari, som ger hälsoinformation med ett ögonkast och fördefinierade aviseringar, samt Azure Monitor-loggintegration, som tillhandahåller frågebara mått och loggar, samt konfigurerbara aviseringar.

Det här dokumentet visar hur du använder dessa verktyg för att övervaka ditt kluster och går igenom några exempel för att konfigurera en Ambari-avisering, övervaka nodtillgänglighetsfrekvensen och skapa en Azure Monitor-avisering som utlöses när ett pulsslag inte har tagits emot från en eller flera noder om fem timmar.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Instrumentpanel

Ambari-instrumentpanelen kan nås genom att välja **Ambari-hemlänken** i avsnittet **Klusterinstrumentpaneler** i HDInsight Översikt i Azure-portalen som visas nedan. Alternativt kan den nås genom att `https://CLUSTERNAME.azurehdinsight.net` navigera till i en webbläsare där CLUSTERNAME är namnet på klustret.

![Resursportalvyn HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Du kommer då att bli tillfrågad om ett användarnamn och lösenord för klusterinloggning. Ange de autentiseringsuppgifter du valde när du skapade klustret.

Du kommer sedan att tas till Ambari-instrumentpanelen, som innehåller widgetar som visar en handfull mätvärden för att ge dig en snabb översikt över din HDInsight-klusters hälsa. Dessa widgetar visar mått som antalet live DataNodes (arbetarnoder) och JournalNodes (zookeeper-nod), NameNodes (huvudnoder) drifttid, samt mått som är specifika för vissa klustertyper, till exempel YARN ResourceManager-drifttid för Spark- och Hadoop-kluster.

![Apache Ambari använder instrumentpanelsvisning](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Värdar – visa individuell nodstatus

Du kan också visa statusinformation för enskilda noder. Välj fliken **Värdar** om du vill visa en lista över alla noder i klustret och se grundläggande information om varje nod. Den gröna kontrollen till vänster om varje nodnamn anger att alla komponenter finns uppe på noden. Om en komponent är nere på en nod visas en röd varningstriangel i stället för den gröna kontrollen.

![HDInsight Apache Ambari värd vy](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Du kan sedan välja på **namnet** på en nod för att visa mer detaljerade värdmått för den specifika noden. I den här vyn visas status/tillgänglighet för varje enskild komponent.

![Apache Ambari är värd för ennodvy](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari varningar

Ambari erbjuder också flera konfigurerbara varningar som kan ge meddelanden om vissa händelser. När aviseringar utlöses visas de i det övre vänstra hörnet av Ambari i ett rött märke som innehåller antalet aviseringar. Om du väljer det här märket visas en lista över aktuella aviseringar.

![Apache Ambari aktuella aviseringar räknas](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Om du vill visa en lista över varningsdefinitioner och deras status väljer du fliken **Aviseringar,** som visas nedan.

![Ambari-varningsdefinitioner visas](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari erbjuder många fördefinierade aviseringar relaterade till tillgänglighet, inklusive:

| Aviseringsnamn                        | Beskrivning   |
|---|---|
| Hälsosammanfattning för DataNode           | Den här aviseringen på tjänstnivå utlöses om det finns felaktiga DataNodes|
| NameNode hög tillgänglighet Hälsa | Den här aviseringen på tjänstnivå utlöses om antingen Active NameNode eller Standby NameNode inte körs.|
| Procentjournalnoder tillgängliga    | Den här aviseringen utlöses om antalet ned journalnoder i klustret är större än det konfigurerade kritiska tröskelvärdet. Det sammanställer resultaten av JournalNode processkontroller. |
| Procentdatanoder tillgängliga       | Den här aviseringen utlöses om antalet neddatanoder i klustret är större än det konfigurerade kritiska tröskelvärdet. Det sammanställer resultaten av DataNode processkontroller.|

En fullständig lista över Ambari-varningar som hjälper till att övervaka tillgängligheten för ett kluster finns [här,](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)

Om du vill visa information om ett aviserings- eller ändringsvillkor väljer du **namnet** på aviseringen. Ta **DataNode Health Summary** som ett exempel. Du kan se en beskrivning av aviseringen samt de specifika kriterier som utlöser en "varning" eller "kritisk" avisering och kontrollintervallet för kriterierna. Om du vill redigera konfigurationen markerar du knappen **Redigera** i det övre högra hörnet i rutan Konfiguration.

![Apache Ambari varningskonfiguration](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Här kan du redigera beskrivningen och, ännu viktigare, kontrollintervallet och tröskelvärdena för varning eller kritiska aviseringar.

![Redigeringsvyn för Ambari-varningskonfigurationer](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

I det här exemplet kan du göra 2 felaktiga DataNodes utlösa en kritisk avisering och 1 ohälsosam dataNod bara utlösa en varning. Välj **Spara** när du är klar med redigeringen.

### <a name="email-notifications"></a>E-postmeddelanden

Du kan också konfigurera e-postmeddelanden för Ambari-aviseringar. Det gör du när **Alerts** du klickar på knappen **Åtgärder** längst upp till vänster och sedan **hanterar meddelanden.**

![Ambari hantera meddelandeåtgärder](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

En dialogruta för hantering av varningsmeddelanden öppnas. Välj **+** längst ned i dialogrutan och fyll i de fält som krävs för att förse Ambari med information om e-postservern som du kan skicka e-post från.

> [!TIP]
> Ställa in Ambari e-postmeddelanden kan vara ett bra sätt att få varningar på ett ställe när du hanterar många HDInsight kluster.

## <a name="azure-monitor-logs-integration"></a>Integrering av Azure Monitor-loggar

Azure Monitor-loggar gör det möjligt att samla in och aggregeras data som genereras av flera resurser, till exempel HDInsight-kluster, på ett ställe för att uppnå en enhetlig övervakningsupplevelse.

Som en förutsättning behöver du en Log Analytics Workspace för att lagra insamlade data. Om du inte redan har skapat en kan du följa instruktionerna här: [Skapa en logganalysarbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Aktivera integrering av HDInsight Azure Monitor-loggar

Välj **Azure Monitor**på resurssidan för HDInsight-kluster i portalen . Välj sedan **aktivera** och välj arbetsytan Log Analytics i listrutan.

![HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Frågemått och loggtabeller

När Azure Monitor-loggintegrering är aktiverad (det kan ta några minuter), navigera till din **Log Analytics Workspace-resurs** och välj **Loggar**.

![Logganalysarbetsyta loggar](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Loggar lista ett antal exempelfrågor, till exempel:

| Namn på fråga                      | Beskrivning                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Datorer tillgänglighet idag    | Diagram antalet datorer som skickar loggar, varje timme                     |
| Lista hjärtslag                 | Lista alla datorslagslag från den senaste timmen                           |
| Sista pulsslaget på varje dator | Visa de senaste pulsslag som skickas av varje dator                             |
| Datorer som inte är tillgängliga           | Lista alla kända datorer som inte har skickat pulsslag under de senaste 5 timmarna |
| Tillgänglighetspris               | Beräkna tillgänglighetshastigheten för varje ansluten dator                |

Kör till exempel exempel exempelfungermplet **För tillgänglighetsfrekvens** genom att välja **Kör** på den frågan, vilket visas i skärmbilden ovan. Detta visar tillgänglighetsfrekvensen för varje nod i klustret i procent. Om du har aktiverat flera HDInsight-kluster för att skicka mått till samma Log Analytics-arbetsyta visas tillgänglighetsfrekvensen för alla noder i dessa kluster.

![Exempelfrågan om logganalysarbetsytaloggar "tillgänglighetsfrekvens"](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Tillgänglighetshastigheten mäts under en 24-timmarsperiod, så ditt kluster måste köras i minst 24 timmar innan du ser korrekta tillgänglighetspriser.

Du kan fästa den här tabellen på en delad instrumentpanel genom att klicka på **Pin** i det övre högra hörnet. Om du inte har några skrivbara delade instrumentpaneler kan du se hur du skapar en här: [Skapa och dela instrumentpaneler i Azure-portalen](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Azure Monitor-aviseringar

Du kan också ställa in Azure Monitor-aviseringar som utlöses när värdet för ett mått eller resultatet av en fråga uppfyller vissa villkor. Låt oss till exempel skapa en avisering för att skicka ett e-postmeddelande när en eller flera noder inte har skickat ett pulsslag på 5 timmar (dvs. antas vara otillgänglig).

Kör exempelfrågan **för otillgängliga datorer** i **Loggar**genom att välja **Kör** på den frågan, som visas nedan.

![Exempel på logganalysarbetsytaloggar som inte är tillgängliga för datorer](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Om alla noder är tillgängliga bör den här frågan returnera noll resultat för tillfället. Klicka på **Ny aviseringsregel** om du vill börja konfigurera aviseringen för den här frågan.

![Logg Analytics arbetsyta ny varningsregel](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Det finns tre komponenter till en avisering: *den resurs* som regeln ska skapas för (log Analytics-arbetsytan i det här fallet), *villkoret* för att utlösa aviseringen och *de åtgärdsgrupper* som avgör vad som ska hända när aviseringen utlöses.
Klicka på **villkorsrubriken**, som visas nedan, för att slutföra konfigurationen av signallogiken.

![Regelvillkor för portalavisering](media/hdinsight-cluster-availability/portal-condition-title.png)

Detta öppnar **Konfigurera signallogik**.

Ange avsnittet **Varningslogik** på följande sätt:

*Baserat på: Antal resultat, Villkor: Större än, Tröskel: 0.*

Eftersom den här frågan endast returnerar otillgängliga noder som resultat, om antalet resultat är allt större än 0, bör aviseringen avfyras.

I avsnittet **Utvärderad baserat på** anger du **perioden** och **frekvensen** baserat på hur ofta du vill söka efter otillgängliga noder.

I den här aviseringen vill du vara säker på **Period=Frekvens.** Mer information om period-, frekvens- och andra varningsparametrar finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Välj **Klar** när du är klar med konfigurationen av signallogiken.

![Varningsregel konfigurerar signallogik](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Om du inte redan har en befintlig åtgärdsgrupp klickar du på **Skapa ny** under avsnittet **Åtgärdsgrupper.**

![Varningsregel skapar ny åtgärdsgrupp](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Detta öppnar **Lägg till åtgärdsgrupp**. Välj ett **åtgärdsgruppnamn,** **kortnamn,** **prenumeration**och **resursgrupp.** Under avsnittet **Åtgärder** väljer du ett **åtgärdsnamn** och väljer **E-post/SMS/Push/Voice** som **åtgärdstyp.**

> [!NOTE]
> Det finns flera andra åtgärder som en avisering kan utlösa förutom en e-post/SMS/Push/Voice, till exempel en Azure-funktion, LogicApp, Webhook, ITSM och Automation Runbook. [Lära sig mer.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Detta kommer att öppna **e-post / SMS / Push / Voice**. Välj ett **namn** för mottagaren, **markera** rutan **E-post** och skriv en e-postadress som du vill att aviseringen ska skickas till. Välj **OK** i **E-post/SMS/Push/Voice**och sedan i **Lägg till åtgärdsgrupp** för att slutföra konfigurationen av din åtgärdsgrupp.

![Varningsregel skapar lägga till åtgärdsgrupp](media/hdinsight-cluster-availability/portal-add-action-group.png)

När de här bladen har stängts bör du se din åtgärdsgrupp listad under avsnittet **Åtgärdsgrupper.** Slutför slutligen avsnittet **Varningsinformation** genom att skriva ett namn och en beskrivning av **varningsregeln** och välja en **allvarlighetsgrad**. **Description** Klicka på **Skapa aviseringsregel** för att slutföra.

![Portal skapar klarning av varningsregel](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Möjligheten att ange **allvarlighetsgrad** är ett kraftfullt verktyg som kan användas när du skapar flera aviseringar. Du kan till exempel skapa en avisering för att höja en varning (Sev 1) om en enda huvudnod går ner och en annan avisering som höjer Kritisk (Sev 0) i den osannolika händelsen att båda huvudnoderna går nedåt.

När villkoret för den här varningen är uppfyllt kommer varningen att avfyras och du får ett e-postmeddelande med varningsinformationen så här:

![E-postexempel för Azure Monitor-avisering](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Du kan också visa alla aviseringar som har avfyrats, grupperat efter **allvarlighetsgrad,** genom att gå till Aviseringar i **logganalysarbetsytan**.

![Logganalysarbetsytavarningar](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Om du väljer en allvarlighetsgradsgruppering (dvs. **Sev 1,** som markerats ovan) visas poster för alla aviseringar om den allvarlighetsgrad som har avfyrats som nedan:

![Logg Analytics arbetsyta sev en varning](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Nästa steg

- [Tillgänglighet och tillförlitlighet för Apache Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md)
