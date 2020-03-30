---
title: Felsöka ett långsamt eller misslyckat jobb i Azure HDInsight-klustret
description: Diagnostisera och felsöka ett långsamt eller misslyckat jobb i ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895322"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Felsöka ett jobb som är långsamt eller som inte fungerar i ett HDInsight-kluster

Om ett program som bearbetar data i ett HDInsight-kluster antingen körs långsamt eller misslyckas med en felkod har du flera felsökningsalternativ. Om dina jobb tar längre tid att köra än förväntat, eller om du ser långsamma svarstider i allmänhet, kan det finnas fel uppströms från klustret, till exempel de tjänster som klustret körs. Den vanligaste orsaken till dessa nedgångar är dock otillräcklig skalning. När du skapar ett nytt HDInsight-kluster väljer du lämpliga [storlekar för virtuella datorer](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Om du vill diagnostisera ett långsamt eller misslyckat kluster samlar du in information om alla aspekter av miljön, till exempel associerade Azure Services, klusterkonfiguration och jobbkörningsinformation. En användbar diagnostik är att försöka återskapa feltillståndet i ett annat kluster.

* Steg 1: Samla in data om problemet.
* Steg 2: Validera HDInsight-klustermiljön.
* Steg 3: Visa klustrets hälsa.
* Steg 4: Granska miljöstacken och versioner.
* Steg 5: Undersök klusterloggfilerna.
* Steg 6: Kontrollera konfigurationsinställningarna.
* Steg 7: Återskapa felet i ett annat kluster.

## <a name="step-1-gather-data-about-the-issue"></a>Steg 1: Samla in data om problemet

HDInsight innehåller många verktyg som du kan använda för att identifiera och felsöka problem med kluster. Följande steg guidar dig genom dessa verktyg och ger förslag för att identifiera problemet.

### <a name="identify-the-problem"></a>Identifiera problemet

Tänk på följande frågor för att identifiera problemet:

* Vad förväntade jag mig att hända? Vad hände istället?
* Hur lång tid tog det att köra processen? Hur länge skulle det ha gått?
* Har mina uppgifter alltid köras långsamt i det här klustret? Körde de snabbare i ett annat kluster?
* När uppstod det här problemet först? Hur ofta har det hänt sedan dess?
* Har något ändrats i min klusterkonfiguration?

### <a name="cluster-details"></a>Klusterinformation

Viktig klusterinformation omfattar:

* Klusternamn.
* Klusterregion - sök efter [region avbrott](https://azure.microsoft.com/status/).
* HDInsight klustertyp och version.
* Typ och antal HDInsight-instanser som angetts för huvud- och arbetsnoderna.

Azure-portalen kan tillhandahålla den här informationen:

![Information om HDInsight Azure-portal](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Du kan också använda [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Ett annat alternativ är att använda PowerShell. Mer information finns i [Hantera Apache Hadoop-kluster i HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Steg 2: Validera hdinsight-klustermiljön

Varje HDInsight-kluster är beroende av olika Azure-tjänster och på programvara med öppen källkod som Apache HBase och Apache Spark. HDInsight-kluster kan också anropa andra Azure-tjänster, till exempel Virtuella Azure-nätverk.  Ett klusterfel kan orsakas av någon av de tjänster som körs i klustret eller av en extern tjänst.  En konfigurationsändring för klustertjänsten kan också orsaka att klustret misslyckas.

### <a name="service-details"></a>Information om tjänsten

* Kontrollera utgivningsversionerna för bibliotek med öppen källkod.
* Sök efter [avbrott i Azure-tjänsten](https://azure.microsoft.com/status/).  
* Sök efter användningsgränser för Azure-tjänsten. 
* Kontrollera azure Virtual Network-undernätskonfigurationen.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa klusterkonfigurationsinställningar med Ambari-användargränssnittet

Apache Ambari tillhandahåller hantering och övervakning av ett HDInsight-kluster med ett webbgränssnitt och ett REST API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj fönstret **Klusterinstrumentpanel** på sidan HDInsight för Azure Portal.  Välj **instrumentpanelsfönstret i HDInsight-kluster** för att öppna användargränssnittet i Ambari och ange inloggningsuppgifterna för klustret.  

![Översikt över Apache Ambari-instrumentpanelen](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Om du vill öppna en lista över tjänstvyer väljer du **Ambari-vyer** på Azure-portalsidan.  Den här listan beror på vilka bibliotek som är installerade. Du kan till exempel se YARN Queue Manager, Hive View och Tez View.  Välj en tjänstlänk för att se konfigurations- och tjänstinformation.

#### <a name="check-for-azure-service-outages"></a>Sök efter avbrott i Azure-tjänsten

HDInsight är beroende av flera Azure-tjänster. Den kör virtuella servrar på Azure HDInsight, lagrar data och skript på Azure Blob storage eller Azure Data Lake Storage och indexerar loggfiler i Azure Table storage. Störningar i dessa tjänster, även om sällsynta, kan orsaka problem i HDInsight. Om du har oväntade nedgångar eller fel i klustret kontrollerar du [Azure Status Dashboard](https://azure.microsoft.com/status/). Statusen för varje tjänst visas efter region. Kontrollera om det finns några relaterade tjänster i klustret och regioner.

#### <a name="check-azure-service-usage-limits"></a>Kontrollera användningsgränser för Azure-tjänst

Om du startar ett stort kluster, eller har startat många kluster samtidigt, kan ett kluster misslyckas om du har överskridit en Azure-tjänstgräns. Tjänstgränserna varierar beroende på din Azure-prenumeration. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Du kan begära att Microsoft ökar antalet tillgängliga HDInsight-resurser (till exempel VM-kärnor och VM-instanser) med en begäran om att öka [resurshanterarens kärnkvotsökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Kontrollera versionen av versionen

Jämför klusterversionen med den senaste HDInsight-versionen. Varje HDInsight-utgåva innehåller förbättringar som nya program, funktioner, patchar och buggfixar. Problemet som påverkar klustret kan ha åtgärdats i den senaste versionen. Om möjligt kör du klustret igen med den senaste versionen av HDInsight och associerade bibliotek som Apache HBase, Apache Spark och andra.

#### <a name="restart-your-cluster-services"></a>Starta om klustertjänsterna

Om du upplever avmattning i klustret kan du överväga att starta om dina tjänster via Ambari-användargränssnittet eller Azure Classic CLI. Klustret kan uppleva tillfälliga fel, och omstart är det snabbaste sättet att stabilisera din miljö och eventuellt förbättra prestanda.

## <a name="step-3-view-your-clusters-health"></a>Steg 3: Visa klustrets hälsa

HDInsight-kluster består av olika typer av noder som körs på instanser av virtuella datorer. Varje nod kan övervakas för resurssvält, problem med nätverksanslutningen och andra problem som kan göra klustret långsammare. Varje kluster innehåller två huvudnoder och de flesta klustertyper innehåller en kombination av arbets- och kantnoder. 

En beskrivning av de olika noderna som varje klustertyp använder finns [i Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

I följande avsnitt beskrivs hur du kontrollerar hälsotillståndet för varje nod och för det övergripande klustret.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Få en ögonblicksbild av klusterhälsan med hjälp av instrumentpanelen för Ambari-användargränssnittet

[Ambari UI-instrumentpanelen](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) ger en översikt över klusterhälsa, till exempel drifttid, minne, nätverks- och CPU-användning, HDFS-diskanvändning och så vidare. Använd avsnittet Värdar i Ambari för att visa resurser på värdnivå. Du kan också stoppa och starta om tjänster.

### <a name="check-your-webhcat-service"></a>Kontrollera din WebHCat-tjänst

Ett vanligt scenario för Apache Hive, Apache Pig eller Apache Sqoop jobb misslyckas är ett misslyckande med [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (eller *Templeton)* tjänsten. WebHCat är ett REST-gränssnitt för fjärrkörning av jobb, till exempel Hive, Pig, Scoop och MapReduce. WebHCat översätter jobbinlämningsbegäranden till Apache Hadoop YARN-program och returnerar en status som härleds från YARN-programstatusen.  I följande avsnitt beskrivs vanliga WEBHCat HTTP-statuskoder.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 statuskod)

Den här koden är ett allmänt meddelande från gateway-noder och är de vanligaste statuskoderna för fel. En möjlig orsak till detta är att WebHCat-tjänsten är nere på den aktiva huvudnoden. För att kontrollera om detta är möjligt, använd följande CURL-kommando:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari visar en avisering som visar värdarna där WebHCat-tjänsten är nere. Du kan försöka återställa WebHCat-tjänsten genom att starta om tjänsten på värden.

![Apache Ambari Starta webhcat-server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Om en WebHCat-server fortfarande inte kommer upp kontrollerar du om det finns felmeddelanden i driftloggen. Mer detaljerad information finns `stderr` `stdout` i och filer som refereras på noden.

#### <a name="webhcat-times-out"></a>WebHCat time out

En HDInsight Gateway time out svar som tar `502 BadGateway`längre tid än två minuter, återvänder . WebHCat frågar YARN-tjänster för jobbstatus, och om YARN tar längre tid än två minuter att svara kan den begäran time out.

I så fall granskar du följande `/var/log/webhcat` loggar i katalogen:

* **webhcat.log** är log4j loggen till vilken server skriver loggar
* **webhcat-console.log** är stackarn på servern när den startas
* **webhcat-console-error.log** är stderr av serverprocessen

> [!NOTE]  
> Var `webhcat.log` och en rullas över `webhcat.log.YYYY-MM-DD`dagligen, genererar filer som heter . Välj lämplig fil för det tidsintervall som du undersöker.

I följande avsnitt beskrivs några möjliga orsaker till WebHCat-timeout.

##### <a name="webhcat-level-timeout"></a>Timeout på WebHCat-nivå

När WebHCat är under belastning, med mer än 10 öppna uttag, tar det längre tid att upprätta nya socketanslutningar, vilket kan resultera i en timeout. Om du vill visa nätverksanslutningarna till `netstat` och från WebHCat använder du den aktuella aktiva huvudnoden:

```bash
netstat | grep 30111
```

30111 är porten WebHCat lyssnar på. Antalet öppna uttag bör vara mindre än 10.

Om det inte finns några öppna uttag ger det föregående kommandot inget resultat. Om du vill kontrollera om Templeton är uppe och lyssnar i port 30111 använder du:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>TIDSGRÄNSEN FÖR YARN-nivå

Templeton kallar YARN för att köra jobb, och kommunikationen mellan Templeton och YARN kan orsaka en timeout.

På YARN-nivån finns det två typer av timeout:

1. Att skicka in ett YARN-jobb kan ta tillräckligt lång tid för att orsaka en timeout.

    Om du `/var/log/webhcat/webhcat.log` öppnar loggfilen och söker efter "köat jobb" kan du se flera poster där körningstiden är för lång (>2000 ms), med poster som visar ökande väntetider.

    Tiden för de köade jobben fortsätter att öka eftersom den takt med vilken nya jobb skickas är högre än den takt med vilken de gamla jobben slutförs. När YARN-minnet har använts på 100 % kan *jobbstarterkön* inte längre låna kapacitet från *standardkön*. Därför kan inga fler nya jobb accepteras i jobbstarterkön. Detta kan orsaka att väntetiden blir längre och längre, vilket orsakar ett timeout-fel som vanligtvis följs av många andra.

    Följande bild visar jobbstarterkön på 714,4 % överutnyttjad. Detta är acceptabelt så länge det fortfarande finns fri kapacitet i standardkön att låna från. Men när klustret är fullt utnyttjat och YARN-minnet har 100 % kapacitet måste nya jobb vänta, vilket så småningom orsakar timeout.

    ![Kövyn hdinsight jobbstarter](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Det finns två sätt att lösa problemet: antingen minska hastigheten på nya jobb som skickas in eller öka förbrukningshastigheten för gamla jobb genom att skala upp klustret.

2. YARN bearbetning kan ta lång tid, vilket kan orsaka timeout.

    * Lista alla jobb: Detta är ett tidskrävande samtal. Det här anropet räknar upp programmen från YARN ResourceManager och för varje slutfört program får status från YARN JobHistoryServer. Med högre antal jobb kan det här samtalet ta time out.

    * Lista jobb äldre än sju dagar: HDInsight YARN JobHistoryServer är konfigurerad`mapreduce.jobhistory.max-age-ms` för att behålla slutförd jobbinformation i sju dagar (värde). Försöker räkna upp rensade jobb resulterar i en timeout.

Så här diagnostiserar du dessa problem:

1. Bestäm det UTC-tidsintervall som ska felsökas
2. Välj rätt `webhcat.log` fil(er)
3. Leta efter VARNING och FELMEDDELANDEn under den tiden

#### <a name="other-webhcat-failures"></a>Andra WebHCat-fel

1. HTTP-statuskod 500

    I de flesta fall där WebHCat returnerar 500 innehåller felmeddelandet information om felet. Annars kan `webhcat.log` du titta igenom warn- och felmeddelanden.

2. Fel på jobbet

    Det kan finnas fall där interaktioner med WebHCat lyckas, men jobben misslyckas.

    Templeton samlar in jobbkonsolutdata som `stderr` i `statusdir`, vilket ofta är användbart för felsökning. `stderr`innehåller YARN-programidentifieraren för den faktiska frågan.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Steg 4: Granska miljöstacken och versioner

Sidan Ambari UI **Stack and Version** innehåller information om konfiguration av klustertjänster och tjänstversionshistorik.  Felaktiga Hadoop-tjänstbiblioteksversioner kan vara en orsak till klusterfel.  I Ambari-användargränssnittet väljer du **admin-menyn** och sedan **staplar och versioner**.  Välj fliken **Versioner** på sidan om du vill visa information om tjänstversion:

![Apache Ambari Stack och versioner](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Steg 5: Undersök loggfilerna

Det finns många typer av loggar som genereras från de många tjänster och komponenter som utgör ett HDInsight-kluster. [WebHCat-loggfiler](#check-your-webhcat-service) beskrivs tidigare. Det finns flera andra användbara loggfiler som du kan undersöka för att begränsa problem med klustret, enligt beskrivningen i följande avsnitt.

* HDInsight-kluster består av flera noder, varav de flesta har till uppgift att köra inskickade jobb. Jobb körs samtidigt, men loggfiler kan bara visa resultat linjärt. HDInsight utför nya uppgifter och avslutar andra som inte slutförs först. All denna aktivitet loggas `stderr` `syslog` till filerna och.

* Skriptåtgärdsloggfilerna visar fel eller oväntade konfigurationsändringar under klustrets skapandeprocess.

* Hadoop-stegloggarna identifierar Hadoop-jobb som startats som en del av ett steg som innehåller fel.

### <a name="check-the-script-action-logs"></a>Kontrollera skriptåtgärdsloggarna

[HDInsight-skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) kör skript i klustret manuellt eller när de anges. Skriptåtgärder kan till exempel användas för att installera ytterligare programvara i klustret eller för att ändra konfigurationsinställningarna från standardvärdena. Om du kontrollerar skriptåtgärdsloggarna kan du få en inblick i fel som uppstod under klusterkonfigurationen och konfigurationen.  Du kan visa status för en skriptåtgärd genom att välja **ops-knappen** i Ambari-användargränssnittet eller genom att komma åt loggarna från standardlagringskontot.

Skriptåtgärdsloggarna finns `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` i katalogen.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Visa HDInsight-loggar med Ambari-snabblänkar

HDInsight Ambari UI innehåller ett antal **snabblänkar** avsnitt.  Om du vill komma åt logglänkarna för en viss tjänst i ditt HDInsight-kluster öppnar du Ambari-användargränssnittet för klustret och väljer sedan tjänstlänken från listan till vänster. Välj listrutan **Snabblänkar,** sedan HDInsight-noden av intresse och välj sedan länken för den associerade loggen.

Till exempel för HDFS-loggar:

![Ambari snabblänkar till loggfiler](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Visa Hadoop-genererade loggfiler

Ett HDInsight-kluster genererar loggar som skrivs till Azure-tabeller och Azure Blob-lagring. YARN skapar egna körningsloggar. Mer information finns i [Hantera loggar för ett HDInsight-kluster](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Granska heap dumpar

Heap-dumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler på den tiden, vilket är användbart för att diagnostisera problem som uppstår vid körning. Mer information finns i [Aktivera heap-dumpar för Apache Hadoop-tjänster på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Steg 6: Kontrollera konfigurationsinställningar

HDInsight-kluster är förkonfigurerade med standardinställningar för relaterade tjänster, till exempel Hadoop, Hive, HBase och så vidare. Beroende på vilken typ av kluster, dess maskinvarukonfiguration, antalet noder, vilka typer av jobb du kör och de data du arbetar med (och hur dessa data bearbetas) kan du behöva optimera konfigurationen.

Detaljerade instruktioner om hur du optimerar prestandakonfigurationer för de flesta scenarier finns i [Optimera klusterkonfigurationer med Apache Ambari](hdinsight-changing-configs-via-ambari.md). När du använder Spark läser du [Optimera Apache Spark-jobb för prestanda](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Steg 7: Återskapa felet i ett annat kluster

För att diagnostisera källan till ett klusterfel startar du ett nytt kluster med samma konfiguration och skickar sedan om det misslyckade jobbets steg en efter en. Kontrollera resultatet av varje steg innan du bearbetar nästa. Den här metoden ger dig möjlighet att korrigera och köra ett enda misslyckat steg igen. Den här metoden har också fördelen att du bara läser in indata en gång.

1. Skapa ett nytt testkluster med samma konfiguration som det misslyckade klustret.
2. Skicka det första jobbsteget till testklustret.
3. När bearbetningen är klar kontrollerar du om det finns fel i stegloggfilerna. Anslut till testklustrets huvudnod och visa loggfilerna där. Stegloggfilerna visas bara när steget körs under en viss tid, är klar eller misslyckas.
4. Om det första steget lyckades kör du nästa steg. Om det fanns fel undersöker du felet i loggfilerna. Om det var ett fel i koden gör du korrigeringen och kör steget igen.
5. Fortsätt tills alla steg körs utan fel.
6. När du är klar med felsökningen av testklustret tar du bort det.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Få tillgång till Apache Hadoop YARN-programtecken i Linux-baserad HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap dumpar för Apache Hadoop tjänster på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Kända problem för Apache Spark-kluster på HDInsight](hdinsight-apache-spark-known-issues.md)
