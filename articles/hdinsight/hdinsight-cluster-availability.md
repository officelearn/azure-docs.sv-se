---
title: Övervaka kluster tillgänglighet med Ambari och Azure Monitor loggar
description: Lär dig hur du använder Ambari och Azure Monitor loggar för att övervaka kluster hälsa och tillgänglighet.
keywords: övervakning, Ambari, övervakning, logg analys, avisering, tillgänglighet, hälsa
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 07b82f475074f5b55a2a5a93f7a59008476233c8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934323"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Övervaka kluster tillgänglighet med Ambari och Azure Monitor loggar

HDInsight-kluster omfattar både Apache Ambari, som ger hälso information på en överskådlig och fördefinierad avisering, samt Azure Monitor loggar integrering, som tillhandahåller frågor som kan ha frågor och loggar, samt konfigurerbara aviseringar.

Det här dokumentet visar hur du använder dessa verktyg för att övervaka klustret och går igenom några exempel på hur du konfigurerar en Ambari avisering, övervakar tillgänglighets takten för noden och skapar en Azure Monitor avisering som utlöses när ett pulsslag inte har tagits emot från en eller flera noder på fem timmar.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Instrumentpanel

Ambari-instrumentpanelen kan nås genom att klicka på **Start länken Ambari** i avsnittet **kluster instrument paneler** i bladet HDInsight-översikt i Azure Portal som visas nedan. Du kan också komma åt den genom att ange följande URL i en webbläsare [https://\<kluster\>namn. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Vyn HDInsight-resurs Portal](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Du uppmanas sedan att ange ett användar namn och lösen ord för kluster inloggning. Ange de autentiseringsuppgifter som du valde när du skapade klustret.

Du kommer sedan till Ambari-instrumentpanelen som innehåller widgetar som visar en fåtal med mått för att ge dig en snabb överblick över ditt HDInsight-klusters hälsa. Dessa widgetar visar mått som antalet Live-DataNodes (arbetsnoder) och Journalnodes available (Zookeeper Node), NameNodes (huvudnoder) drift tid, samt mått som är specifika för vissa kluster typer, t. ex. garn-ResourceManager drift tid för Spark-och Hadoop-kluster.

![Ambari-instrumentpanel](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Värdar – Visa status för enskilda noder

Du kan också visa statusinformation för enskilda noder. Klicka på fliken **värdar** om du vill visa en lista över alla noder i klustret och se grundläggande information om varje nod. Den gröna kontrollen till vänster om varje nodnamn visar att alla komponenter är upp på noden. Om en komponent finns i en nod visas en röd aviserings triangel i stället för den gröna kontrollen.

![Vy över Ambari-värdar](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Sedan kan du klicka på **namnet** på en nod om du vill visa mer detaljerade värd mått för just den noden. I den här vyn visas status/tillgänglighet för varje enskild komponent.

![Ambari är värd för vyn enskild nod](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari aviseringar

Ambari erbjuder också flera konfigurerbara aviseringar som kan ge meddelanden om vissa händelser. När aviseringar utlöses visas de i det övre vänstra hörnet av Ambari i en röd skylt som innehåller antalet aviseringar. Om du klickar på den här skylten visas en lista över aktuella aviseringar.

![Antal aviseringar för Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Om du vill visa en lista över aviserings definitioner och deras status klickar du på fliken **aviseringar** , som visas nedan.

![Vy över Ambari aviserings definitioner](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari erbjuder många fördefinierade aviseringar relaterade till tillgänglighet, inklusive:

| Aviseringsnamn                        | Beskrivning                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode hälso översikt           | Den här aviseringen på tjänst nivå utlöses om det finns DataNodes som inte är felfria                                                                                                                |
| NameNode-hälsa för hög tillgänglighet | Den här aviseringen på tjänst nivå utlöses om antingen den aktiva NameNode eller vänte läges NameNode inte körs.                                                                              |
| Procent Journalnodes available tillgängliga    | Den här aviseringen utlöses om antalet Journalnodes available i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av JournalNode process-kontroller. |
| Procent DataNodes tillgängliga       | Den här aviseringen utlöses om antalet DataNodes i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av DataNode process-kontroller.       |

En fullständig lista över Ambari-aviseringar som hjälper dig att övervaka tillgängligheten för ett kluster finns [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Om du vill visa information om en avisering eller ändra villkor klickar du på **namnet** på aviseringen. Ta **DataNode Health Summary** som ett exempel. Du kan se en beskrivning av aviseringen samt de villkor som utlöser en varnings avisering eller kritisk avisering och kontroll intervallet för kriterierna. Redigera konfigurationen genom att klicka på knappen **Redigera** i det övre högra hörnet i rutan konfiguration.

![Aviserings konfiguration för Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Här kan du redigera beskrivningen och, viktigare, kontroll intervall och tröskelvärden för varningar eller kritiska aviseringar.

![Ambari aviserings konfiguration redigera vy](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

I det här exemplet kan du göra 2 DataNodes som utlöser en kritisk varning och 1 DataNode som endast utlöser en varning. Klicka på **Spara** när du är klar med redigeringen.

### <a name="email-notifications"></a>E-postmeddelanden

Du kan också välja att konfigurera e-postaviseringar för Ambari-aviseringar. Det gör du genom att klicka på knappen **åtgärder** längst upp till vänster i fliken **aviseringar** och sedan **Hantera meddelanden.**

![Ambari-åtgärd för att hantera meddelanden](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

En dialog ruta för att hantera aviseringar öppnas. **+** Klicka på längst ned i dialog rutan och fyll i de obligatoriska fälten för att tillhandahålla Ambari med information om e-postservern som e-postmeddelanden ska skickas från.

> [!TIP]
> Att ställa in Ambari e-postaviseringar kan vara ett bra sätt att få aviseringar på en plats när du hanterar många HDInsight-kluster.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor loggar integrering

Azure Monitor loggar aktiverar data som genereras av flera resurser, till exempel HDInsight-kluster, som ska samlas in och aggregeras på ett och samma ställe för att uppnå en enhetlig övervaknings upplevelse.

Som en förutsättning behöver du en Log Analytics arbets yta för att lagra insamlade data. Om du inte redan har skapat en, kan du följa anvisningarna här: [Skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Aktivera integrering av HDInsight Azure Monitor-loggar

Klicka på bladet **Operations Management Suite** på sidan HDInsight-kluster resurs i portalen. Klicka sedan på **Aktivera** och välj din Log Analytics arbets yta i list rutan.

![Bladet HDInsight Operations Management Suite](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Fråga mått och logga tabeller i bladet loggar

När Azure Monitor logg integrering har Aktiver ATS (det här kan ta några minuter) navigerar du till din **Log Analytics arbets ytans** resurs och klickar på bladet **loggar**

![Bladet för Log Analytics arbets ytans loggar](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

På bladet **loggar** visas ett antal exempel frågor, till exempel:

| Frågenamn                      | Beskrivning                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Tillgänglighet för datorer idag    | Rita ett diagram över antalet datorer som skickar loggar, varje timme                     |
| Visa pulsslag                 | Visa en lista över alla dator pulsslag från den senaste timmen                           |
| Senaste pulsslag för varje dator | Visa det sista pulsslaget som skickats av varje dator                             |
| Ej tillgängliga datorer           | Visa en lista med alla kända datorer som inte skickade några pulsslag under de senaste 5 timmarna |
| Tillgänglighets hastighet               | Beräkna tillgänglighets frekvensen för varje ansluten dator                |

Du kan till exempel köra frågan **tillgänglighets frekvens** genom att klicka på **Kör** på den frågan, som visas i skärm bilden ovan. Då visas tillgänglighets takten för varje nod i klustret i procent. Om du har aktiverat flera HDInsight-kluster för att skicka mått till samma Log Analytics arbets yta, visas tillgänglighets takten för alla noder i de kluster som visas.

![Exempel fråga för bladet tillgänglighets frekvens i bladet Log Analytics arbets yta](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Tillgänglighets takten mäts under en 24-timmarsperiod, så klustret måste köras i minst 24 timmar innan du kan se korrekt tillgänglighets taxa.

Du kan fästa den här tabellen på en delad instrument panel genom att klicka på **Fäst** i det övre högra hörnet. Om du inte har några skrivbara, delade instrument paneler kan du se hur du skapar en här: [Skapa och dela instrument paneler i Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Azure Monitor aviseringar

Du kan också ställa in Azure Monitor aviseringar som ska utlösas när värdet för ett mått eller resultatet av en fråga uppfyller vissa villkor. Vi kan till exempel skapa en avisering för att skicka ett e-postmeddelande när en eller flera noder inte har skickat ett pulsslag på 5 timmar (dvs. förmodas vara otillgänglig).

Från bladet **loggar** kör du exempel frågan **otillgängliga datorer** genom att klicka på **Kör** på den frågan, som visas nedan.

![Exempel frågan för bladet "ej tillgängliga datorer" på arbets ytan Log Analytics](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Om alla noder är tillgängliga ska den här frågan returnera 0 resultat för tillfället. Klicka på **ny varnings regel** för att börja konfigurera aviseringen för den här frågan.

![Log Analytics arbets ytans nya aviserings regel](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Det finns tre komponenter i en avisering: den *resurs* för vilken du vill skapa regeln (Log Analytics arbets ytan i det här fallet), *villkoret* för att utlösa aviseringen och de *Åtgärds grupper* som avgör vad som ska hända när aviseringen är utlöses.

Klicka på **villkors rubriken**som visas nedan för att slutföra konfigurationen av signal logiken.

![Villkor för varnings regel](media/hdinsight-cluster-availability/portal-condition-title.png)

Då öppnas bladet **Konfigurera signal logik** .

Ange **aviserings logik** avsnittet enligt följande:

*Baserat på: Antal resultat, villkor: Större än, tröskel: 0,0.*

Eftersom den här frågan bara returnerar otillgängliga noder som resultat, bör aviseringen utlösas om antalet resultat någonsin är större än 0.

I avsnittet **utvärdera baserat på** anger du **period** och **frekvens** baserat på hur ofta du vill söka efter otillgängliga noder.

Observera att för den här aviseringen ska du se till att **period = frekvens.** Mer information om period, frekvens och andra aviserings parametrar hittar du [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Klicka på **klart** när du är färdig med konfigurationen av signal logiken.

![Varnings regel konfigurera signal logik](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Om du inte redan har en befintlig åtgärds grupp klickar du på **Skapa ny** under avsnittet **Åtgärds grupper** .

![Ny åtgärds grupp för aviserings regel](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Då öppnas bladet **Lägg till åtgärds grupp** . Välj ett **Åtgärds grupp namn**, **kort namn**, **prenumeration**och **resurs grupp.** Under avsnittet **åtgärder** väljer du ett **Åtgärds namn** och väljer **e-post/SMS/push/röst** som **Åtgärds typ.**

> [!NOTE]
> Det finns flera andra åtgärder som en avisering kan utlösa förutom e-post/SMS/push/Voice, till exempel en Azure Function-, LogicApp-, webhook-, ITSM-och Automation-Runbook. [Lära sig mer.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Då öppnas bladet **e-post/SMS/push/röst** . Välj ett **namn** för mottagaren, **Markera** rutan **e-** postadress och skriv en e-postadress som du vill att aviseringen ska skickas till. Klicka på **OK** i bladet **e-post/SMS/push/Voice** och Slutför konfigurationen av din åtgärds grupp i bladet **Lägg till åtgärds grupp** .

![Åtgärds grupp för att lägga till aviserings regel](media/hdinsight-cluster-availability/portal-add-action-group.png)

När bladen stängs bör du se din åtgärds grupp i avsnittet **Åtgärds grupper** . Slutligen fyller du i avsnittet **aviserings information** genom att ange ett namn och en **Beskrivning** för **varnings regeln** och välja en **allvarlighets grad**.
Klicka på **skapa aviserings regel** för att slutföra.

![Skapa varnings regel slutförd](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Möjligheten att ange **allvarlighets grad** är ett kraftfullt verktyg som kan användas när du skapar flera aviseringar. Du kan till exempel skapa en avisering för att utlösa en varning (allvarlighets grad 1) om en enda Head-nod går ned och en annan avisering som aktiverar kritisk (allvarlighets grad 0) i förmodad händelse att båda huvudnoderna går ned.

När villkoret för den här aviseringen uppfylls, utlöses aviseringen och du får ett e-postmeddelande med aviserings informationen så här:

![Azure Monitor e-postavisering](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Du kan också Visa alla aviseringar som har utlösts, grupperade efter allvarlighets grad, genom att gå till bladet **aviseringar** i **arbets ytan Log Analytics**.

![Aviseringar för Log Analytics arbets ytan](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Om du klickar på en allvarlighets grad gruppering (t. ex. **allvarlighets grad 1 som är** markerad ovan) visas poster för alla aviseringar med den allvarlighets grad som har utlösts som nedan:

![Allvarlighets grad 1-aviseringar för arbets ytan Log Analytics](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Nästa steg
- [Tillgänglighet och tillförlitlighet för Apache Hadoop kluster i HDInsight](hdinsight-high-availability-linux.md)
