---
title: Felsöka ett långsamt eller inte alls HDInsight-kluster - Azure HDInsight | Microsoft Docs
description: Diagnostisera och felsöka ett långsamt eller inte alls HDInsight-kluster.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: bd026169062788fa7587cb6357b3764fbfdb24fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415919"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Felsöka ett långsamt eller felaktigt HDInsight-kluster

Om ett HDInsight-kluster långsam eller misslyckas med en felkod, har du flera alternativ för felsökning. Om dina jobb tar längre tid att köra än väntat eller om du ser långa svarstider i allmänhet, kan det finnas fel uppströms från klustret, till exempel de tjänster som körs på klustret. Den vanligaste orsaken till att dessa fördröjningar är dock inte tillräckligt skalning. När du skapar ett nytt HDInsight-kluster, Välj lämpliga [storlekar för virtuella datorer](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

Samla in information om alla aspekter av miljön, till exempel tillhörande Azure-tjänster, klusterkonfigurationen och information för körning av jobbet för att diagnostisera ett långsamt eller inte alls kluster. En bra diagnostik är att försöka återskapa feltillstånd på ett annat kluster.

* Steg 1: Samla in data om problemet
* Steg 2: Validera klustermiljön HDInsight 
* Steg 3: Visa ditt kluster hälsa
* Steg 4: Granska miljö stack och versioner
* Steg 5: Kontrollera loggfilerna för kluster
* Steg 6: Kontrollera konfigurationsinställningarna
* Steg 7: Återskapa felet på ett annat kluster 

## <a name="step-1-gather-data-about-the-issue"></a>Steg 1: Samla in data om problemet

HDInsight innehåller många verktyg som du kan använda för att identifiera och felsöka problem med kluster. Följande steg när du går igenom de här verktygen och ger förslag på lokalisera problemet.

### <a name="identify-the-problem"></a>Identifiera problem

Du kan identifiera problemet, beakta följande frågor:

* Vad jag förväntar mig att hända? Vad hände i stället?
* Hur lång tid processen tog för att köra? Hur lång tid ska den har köras?
* Har mina aktiviteter körs alltid långsamt på detta kluster? De körde snabbare på ett annat kluster?
* När det här problemet först förekommer? Hur ofta har det skett sedan?
* Har något ändras i min klusterkonfiguration?

### <a name="cluster-details"></a>Information om kluster

Innehåller viktig information om klustret:

* Klusternamnet.
* Kluster region - Sök efter [region avbrott](https://azure.microsoft.com/status/).
* Typ av HDInsight-kluster och version.
* Typ och antal HDInsight-instanser som angetts för noder head- och arbetsroller.

Azure-portalen kan tillhandahålla den här informationen:

![HDInsight Azure-portalen Information](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Du kan också använda Azure CLI:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Ett annat alternativ är att använda PowerShell. Mer information finns i [hantera Hadoop-kluster i HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Steg 2: Validera klustermiljön HDInsight

Varje HDInsight-kluster förlitar sig på olika Azure-tjänster och program med öppen källa, till exempel Apache HBase och Apache Spark. HDInsight-kluster kan också anropa på andra Azure-tjänster, till exempel virtuella Azure-nätverk.  Ett klusterfel kan orsakas av något av tjänsterna som körs på klustret eller av en extern tjänst.  En konfigurationsändring för kluster-tjänsten kan också medföra klustret slutar fungera.

### <a name="service-details"></a>Tjänstinformation

* Kontrollera versionen versioner bibliotek med öppen källkod
* Sök efter [avbrott i Azure-tjänst](https://azure.microsoft.com/status/) 
* Kontrollera om gränserna för Resursanvändning i Azure-tjänsten 
* Kontrollera konfigurationen för Azure Virtual Network-undernät 

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa inställningar för klustrets med Ambari UI

Apache Ambari ger hantering och övervakning av ett HDInsight-kluster med en webbgränssnittet och en REST-API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj den **Klusterinstrumentpanel** rutan på Azure HDInsight portalsidan.  Välj den **instrumentpanelen för HDInsight-klustret** fönstret för att öppna Ambari UI och ange inloggningsuppgifter för klustret.  

![Ambari UI](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Om du vill öppna en lista över servicevyer, Välj **Ambari Views** på sidan för Azure-portalen.  Den här listan beror på vilka bibliotek är installerade. Du kan till exempel se YARN Queue Manager, Hive och Tez.  Välj en tjänst-länk för att visa konfigurations- och tjänstinformation.

#### <a name="check-for-azure-service-outages"></a>Kontrollera att Azure-tjänstavbrott

HDInsight är beroende av flera Azure-tjänster. Virtuella servrar körs på Azure HDInsight lagrar data och skript i Azure Blob storage eller Azure DataLake Store och index loggfiler i Azure Table storage. Avbrott i tjänsterna, kan även om det är sällsynt, orsaka problem i HDInsight. Om du har oväntade fördröjningar eller fel i klustret, kontrollera den [Azure Status instrumentpanelen](https://azure.microsoft.com/status/). Statusen för varje tjänst anges per region. Kontrollera ditt kluster region och områden för eventuella relaterade tjänster.

#### <a name="check-azure-service-usage-limits"></a>Kontrollera gränserna för Resursanvändning i Azure-tjänst

Om du är att starta en stor klustret eller ha startats många kluster samtidigt, kan ett kluster misslyckas om du har överskridit en gräns för Azure-tjänsten. Tjänstbegränsningarna varierar beroende på din Azure-prenumeration. Mer information finns i [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Du kan begära att Microsoft öka antalet HDInsight-resurser som är tillgängliga (till exempel VM kärnor och VM-instanser) med en [Resource Manager kärnor ökar kvotförfrågan](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Kontrollera versionen

Jämför klusterversionen med den senaste versionen i HDInsight. Varje HDInsight-version innehåller nya program, funktioner, korrigeringsfiler och felkorrigeringar. Det problem som påverkar ditt kluster kan ha korrigerats i den senaste versionen. Om möjligt köra klustret använder den senaste versionen av HDInsight och associerade bibliotek, till exempel Apache HBase, Apache Spark och andra igen.

#### <a name="restart-your-cluster-services"></a>Starta om klustret-tjänster

Om det uppstår fördröjningar i klustret, kan du överväga att starta om dina tjänster via Ambari UI eller Azure CLI. Klustret kan ha problem med tillfälliga fel och omstart är det snabbaste sättet att hålla din miljö och förbättra eventuellt prestandan.

## <a name="step-3-view-your-clusters-health"></a>Steg 3: Visa ditt kluster hälsa

HDInsight-kluster består av olika typer av noder som kör på virtuella instanser. Varje nod kan övervakas för negativa effekter på resurser, problem med nätverksanslutningen och andra problem som kan sakta ned klustret. Varje kluster innehåller två huvudnoderna och de flesta klustertyper innehåller en kombination av arbetare och kant-noder. 

En beskrivning av varje typ av kluster använder olika noder finns [ställa in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](hdinsight-hadoop-provision-linux-clusters.md).

I följande avsnitt beskrivs hur du kontrollerar hälsotillståndet för varje nod och övergripande klustret.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Hämta en ögonblicksbild av kluster med Ambari UI-instrumentpanelen

Den [Ambari UI instrumentpanelen](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) ger en översikt över kluster hälsotillstånd, till exempel drifttid, minne, nätverket och CPU-användning, HDFS diskanvändning och så vidare. Använd avsnittet värdar i Ambari om du vill visa resurser på en värdnivå. Du kan även stoppa och starta om tjänsterna.

### <a name="check-your-webhcat-service"></a>Kontrollera din WebHCat-tjänst

Ett vanligt scenario för Hive, Pig och Sqoop jobb misslyckas är ett fel med den [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (eller *Templeton*) service. WebHCat är en REST-gränssnitt för fjärr jobbkörningen, till exempel Hive, Pig, dig mer och MapReduce. WebHCat översätter jobbet skicka förfrågningar till YARN program och returnerar status som härletts från YARN programstatus.  I följande avsnitt beskrivs vanliga WebHCat HTTP-statuskoder.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 statuskod)

Detta är ett allmänt meddelande från gateway-noder och är de vanligaste statuskoden för felet. En möjlig orsak till detta är tjänsten WebHCat som anges i den aktiva huvudnoden. Använd kommandot CURL för att söka efter denna möjlighet:

```bash
$ curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari visas ett varningsmeddelande som visar de värdar som WebHCat-tjänsten har stoppats. Du kan försöka att få igång tjänsten WebHCat säkerhetskopiera genom att starta om tjänsten på värden.

![Starta om servern WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Om en WebHCat-server inte kommer fortfarande, kontrollerar du operations loggen för misslyckade meddelanden. Mer information finns i `stderr` och `stdout` filer som refereras på noden.

#### <a name="webhcat-times-out"></a>WebHCat timeout

En Gateway för HDInsight timeout svar som tar längre tid än två minuter returnerar `502 BadGateway`. WebHCat frågar YARN-tjänster för jobbstatus och om YARN tar längre tid än två minuter för svar, som begär kan timeout.

I det här fallet i loggarna följande i den `/var/log/webhcat` directory:

* **webhcat.log** är log4j loggen till vilken server skriver loggar
* **webhcat console.log** är stdout av servern när igång
* **webhcat-konsolen-error.log** är stderr för serverprocessen

> [!NOTE]
> Varje `webhcat.log` återställs dagligen, genererar filer med namnet `webhcat.log.YYYY-MM-DD`. Välj filen för det tidsintervall du undersöker.

I följande avsnitt beskrivs möjliga orsaker för WebHCat-timeout.

##### <a name="webhcat-level-timeout"></a>WebHCat nivå timeout

Det tar längre tid att etablera nya socketanslutningar, vilket kan leda till en tidsgräns när WebHCat är under belastning med fler än 10 öppna socketar. Om du vill visa nätverksanslutningar till och från WebHCat använder `netstat` på den aktuella aktiva headnode:

```bash
$ netstat | grep 30111
```

30111 är WebHCat lyssnar på porten. Antalet öppna uttag bör vara mindre än 10.

Om det inte finns några öppna sockets, skapar inte ett resultat i det föregående kommandot. Kontrollera om Templeton är igång och lyssnar på port 30111, Använd:

```bash
$ netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN nivå timeout

Templeton anropar YARN köra jobb och kommunikationen mellan Templeton och YARN kan orsaka en tidsgräns.

Det finns två typer av timeout på YARN-nivå:

1. Skickar ett YARN-jobb kan ta tillräckligt lång för att göra en tidsgräns.

    Om du öppnar den `/var/log/webhcat/webhcat.log` loggfilen och Sök efter ”köade jobb” du kan se flera poster där körningstiden är för lång (> 2000 ms), med poster som visar öka väntetid.

    Tid för jobb i kö fortsätter att öka eftersom den hastighet som nya jobb hämta skickats är högre än den som de gamla jobben har slutförts. När YARN-minne är 100% används, den *joblauncher kön* kan inte längre att låna kapacitet från den *standardkön*. Därför kan inga fler nya jobb accepteras i joblauncher kön. Detta kan medföra väntetiden blir längre och längre, orsakar ett timeout-fel som vanligtvis följs av många andra.

    Följande bild visar köns joblauncher 714.4% överanvända. Detta är acceptabelt så länge som det finns fortfarande kapacitet i standardkön låna från. Men när klustret är fullt utnyttjade och YARN-minne är 100% kapacitet, måste nya jobb vänta, vilket till slut gör timeout.

    ![Joblauncher kön](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Det finns två sätt att lösa problemet: antingen minska tiden för nya jobb som skickas eller öka hastigheten för förbrukning av gamla jobb genom att skala upp klustret.

2. YARN bearbetningen kan ta lång tid, vilket kan orsaka timeout.

    * Visa alla jobb: Detta är en tidskrävande anrop. Det här anropet räknar upp program från YARN ResourceManager och för varje färdiga programmet, hämtar status från YARN JobHistoryServer. Med ett högre antal jobb kan det här anropet timeout.

    * Visa jobb som är äldre än sju dagar: I HDInsight YARN JobHistoryServer är konfigurerad för att behålla slutförda jobbinformation under sju dagar (`mapreduce.jobhistory.max-age-ms` värde). Försök att räkna upp borttagna jobb resulterar i en tidsgräns.

Att diagnostisera problemen:

    1. Fastställa UTC tidsintervallet för att felsöka
    2. Välj lämpliga `webhcat.log` filerna
    3. Leta efter varna och felmeddelanden under den tiden

#### <a name="other-webhcat-failures"></a>Andra WebHCat-fel

1. HTTP-statuskod 500

    I de flesta fall där WebHCat returnerar 500-felmeddelandet innehåller information om felet. Annars titta igenom `webhcat.log` för varna och felmeddelanden.

2. Jobbfel

    Det kan finnas fall där interaktioner med WebHCat är lyckades, men jobb misslyckas.

    Templeton samlar in konsolens jobbutdata som `stderr` i `statusdir`, vilket ofta är användbart för felsökning. `stderr` innehåller YARN program-ID för den faktiska frågan.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Steg 4: Granska miljö stack och versioner

Ambari UI **stacken och Version** innehåller information om klustret services konfigurations- och tidigare versioner.  Felaktig Hadoop service library versioner kan vara en orsak till klusterfelet.  I Ambari UI, väljer du den **Admin** menyn och sedan **stackar och versioner**.  Välj den **versioner** fliken på sidan för att visa versionsinformation för tjänsten:

![Stacken och versioner](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Steg 5: Kontrollera loggfilerna

Det finns många typer av loggar som genereras från många tjänster och komponenter som utgör ett HDInsight-kluster. [Loggfiler för WebHCat](#check-your-webhcat-service) beskrivs tidigare. Det finns flera andra användbara loggfiler som du kan undersöka om du vill begränsa problem med ditt kluster, enligt beskrivningen i följande avsnitt.

![Exempel på HDInsight loggfil](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

* HDInsight-kluster som består av flera noder, de flesta uppgifter om du vill köra skickade jobb. Jobb körs samtidigt, men loggfiler kan bara visa resultat linjärt. HDInsight kör nya aktiviteter avslutar andra som misslyckas med att slutföra först. Den här aktiviteten är inloggad på `stderr` och `syslog` filer.

* Loggfiler för skriptet åtgärd visar fel eller oväntat konfigurationsändringar under skapar kluster.

* Hadoop steg loggar identifiera Hadoop-jobb startades som en del av ett steg som innehåller fel.

### <a name="check-the-script-action-logs"></a>Åtgärden skript loggarna

HDInsight [skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md) skript körs på klustret som anges när eller manuellt. Skriptåtgärder kan till exempel användas för att installera ytterligare programvara på klustret eller ändra konfigurationsinställningarna från standardvärdena. Kontrollera skript åtgärd loggar ger kunskaper om fel som uppstod under klustret installation och konfiguration.  Du kan visa statusen för en skriptåtgärd genom att välja den **ops** i Ambari UI eller genom att öppna loggfilerna från standardkontot för lagring.

Skriptet åtgärd loggar finns i den `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` directory.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Kontrollera händelseloggarna för HDInsight med Ambari snabblänkar

HDInsight Ambari Användargränssnittet innehåller ett antal **snabblänkar** avsnitt.  För att komma åt loggen länkarna för en viss tjänst i ditt HDInsight-kluster, öppna Ambari UI för klustret, och välj sedan service-länken i listan till vänster. Välj den **snabblänkar** listrutan sedan HDInsight noden intresse och välj sedan länken för dess associerade logg.

Till exempel för HDFS-loggar:

![Ambari snabblänkar loggfiler](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Visa loggfiler som genererats av Hadoop

Ett HDInsight-kluster genererar loggar som skrivs till Azure-tabeller och Azure Blob storage. YARN skapar sin egen körningsloggar. Mer information finns i [hantera loggar för ett HDInsight-kluster](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Granska heap minnesdumpar

Heap Dumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler som helst som är användbara för att diagnostisera problem som uppstår vid körning. Mer information finns i [aktivera heap Dumpar för Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Steg 6: Kontrollera konfigurationsinställningarna

HDInsight-kluster är förkonfigurerade med standardinställningar för relaterade tjänster, till exempel Hadoop, Hive, HBase och så vidare. Beroende på vilken typ av kluster, dess maskinvarukonfiguration, dess antal noder, vilka typer av jobb du använder, och data du arbetar med (och hur dessa data bearbetas) du kan behöva optimera din konfiguration.

Detaljerade instruktioner om hur du optimerar prestandakonfigurationer för de flesta fall finns [optimera klusterkonfigurationer med Ambari](hdinsight-changing-configs-via-ambari.md). När du använder Spark, se [optimera Spark jobb för prestanda](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Steg 7: Återskapa felet på ett annat kluster

Starta ett nytt kluster med samma konfiguration för att diagnosticera källan för ett kluster, och skicka det misslyckade jobbet steg i taget. Kontrollera resultatet av varje steg innan nästa. Den här metoden ger dig möjlighet att korrigera och kör ett enda steg som misslyckades. Den här metoden har också nytta av endast läsa in inkommande data en gång.

1. Skapa ett nytt testkluster med samma konfiguration som misslyckade klustret.
2. Skicka jobbet första steget i test-klustret.
3. När steget har slutförts bearbetning, söka efter fel i loggfilerna steg. Anslut till test-klustrets huvudnod och granskar du loggfilerna. Loggfilerna steg visas endast när steget körs under en viss tid har slutförts eller misslyckas.
4. Om det första steget när du kör nästa steg. Om fel uppstod, Undersök fel i loggfilerna. Om det är ett fel i koden, korrigera och kör steget igen. 
5. Fortsätt tills alla steg körs utan fel.
6. När du är klar felsökning testklustret, ta bort den.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Analysera HDInsight loggar](hdinsight-debug-jobs.md)
* [Åtkomst YARN programloggen på Linux-baserat HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap Dumpar för Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Kända problem för Apache Spark-kluster i HDInsight](hdinsight-apache-spark-known-issues.md)
