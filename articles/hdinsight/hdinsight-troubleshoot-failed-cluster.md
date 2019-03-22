---
title: Felsöka ett långsamt eller felaktigt HDInsight-kluster – Azure HDInsight
description: Diagnostisera och felsöka ett långsamt eller felaktigt HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 685731aee9396efbfa9f7bb554ec7ce20270935f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314747"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Felsöka ett långsamt eller felaktigt HDInsight-kluster

Om ett HDInsight-kluster långsam eller misslyckas med felkoden, har du flera alternativ för felsökning. Om dina jobb tar längre tid än förväntat eller om du ser långa svarstider i allmänhet, kan det finnas fel uppströms från ditt kluster, till exempel de tjänster som körs på klustret. Den vanligaste orsaken till dessa fördröjningar är dock inte tillräckligt med skalning. När du skapar ett nytt HDInsight-kluster väljer du lämplig [storlekar för virtuella datorer](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Samla in information om alla aspekter av miljön, till exempel tillhörande Azure-tjänster och konfiguration av klustrets jobbinformation för körning för att diagnostisera ett långsamt eller felaktigt kluster. En bra diagnostik är att försöka återskapa feltillstånd i ett annat kluster.

* Steg 1: Samla in data om problemet.
* Steg 2: Validera miljön för HDInsight-kluster.
* Steg 3: Visa hälsotillstånd för ditt kluster.
* Steg 4: Granska miljö stack och versioner.
* Steg 5: Granska loggfilerna för klustret.
* Steg 6: Kontrollera konfigurationsinställningarna.
* Steg 7: Återskapa felet på ett annat kluster.

## <a name="step-1-gather-data-about-the-issue"></a>Steg 1: Samla in data om problemet

HDInsight innehåller många verktyg som du kan använda för att identifiera och Felsök problem med kluster. Följande steg hjälper dig att dessa verktyg och ge förslag för att lokalisera problemet.

### <a name="identify-the-problem"></a>Identifiera problemet

För att identifiera problemet, beakta följande frågor:

* Vad jag förvänta mig ske? Vad hände istället?
* Hur lång tid processen tog för att köra? Hur lång tid ska den ha köras?
* Har mina aktiviteter alltid körs långsamt i det här klustret? De kördes snabbare på ett annat kluster?
* När det här problemet första gången? Hur ofta har det skett sedan?
* Ändrats något i min klusterkonfiguration?

### <a name="cluster-details"></a>Klusterinformation

Viktig information om klustret innehåller:

* Klustrets namn.
* Kluster region - Sök efter [region avbrott](https://azure.microsoft.com/status/).
* Typ av HDInsight-kluster och version.
* Typ och antal HDInsight-instanser som angetts för huvud- och worker-noder.

Azure-portalen kan ge den här informationen:

![HDInsight-Azure-portalen Information](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Du kan också använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Ett annat alternativ med hjälp av PowerShell. Mer information finns i [hantera Apache Hadoop-kluster i HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Steg 2: Validera miljön för HDInsight-kluster

Varje HDInsight-kluster förlitar sig på olika Azure-tjänster och programvara med öppen källkod, till exempel Apache HBase- och Apache Spark. HDInsight-kluster kan du även anropa på andra Azure-tjänster, till exempel virtuella Azure-nätverk.  Ett klusterfel kan orsakas av någon av tjänsterna som körs i klustret eller genom att en extern tjänst.  En konfigurationsändring för kluster-tjänsten kan också orsaka klustret slutar fungera.

### <a name="service-details"></a>Tjänstinformation

* Kontrollera slutversionerna bibliotek med öppen källkod.
* Sök efter [Azure tjänstavbrott](https://azure.microsoft.com/status/).  
* Sök efter Azure-tjänsten användningsbegränsningar. 
* Kontrollera konfigurationen för Azure Virtual Network-undernät.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa inställningar för klustrets med Ambari UI

Apache Ambari ger hantering och övervakning av ett HDInsight-kluster med ett webbgränssnitt och ett REST-API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj den **Klusterinstrumentpanel** fönstret på Azure HDInsight portalsidan.  Välj den **HDInsight-klusterinstrumentpanel** fönstret för att öppna Ambari UI och ange autentiseringsuppgifterna för klusterinloggning.  

![Ambari-Användargränssnittet](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Om du vill öppna en lista över service-vyerna, Välj **Ambari-vyer** på sidan för Azure portal.  Den här listan beror på vilka bibliotek som är installerade. Du kan till exempel se köhanteraren YARN, Hive-vyerna och Tez.  Välj en tjänst-länk för att se konfigurations- och tjänstinformation.

#### <a name="check-for-azure-service-outages"></a>Sök efter Azure tjänstavbrott

HDInsight är beroende av flera Azure-tjänster. Den körs virtuella servrar på Azure HDInsight lagrar data och skript på Azure Blob storage eller Azure Data Lake Storage och index loggfiler i Azure Table storage. Avbrott i tjänsterna, kan även om det är sällsynt, orsaka problem i HDInsight. Om du har oväntat kapacitetssänkningar eller fel i klustret och kontrollera den [instrumentpanelen för Azure](https://azure.microsoft.com/status/). Status för varje tjänst anges per region. Kontrollera ditt kluster region, och som också regioner för eventuella relaterade tjänster.

#### <a name="check-azure-service-usage-limits"></a>Kontrollera Azure-tjänsten användningsbegränsningar

Om du startar ett stort kluster, eller har lanserat många kluster samtidigt, kan ett kluster misslyckas om du har överskridit gränsen för en Azure-tjänsten. Tjänstbegränsningar varierar beroende på din Azure-prenumeration. Mer information finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Du kan begära att Microsoft öka antalet HDInsight-resurser som är tillgängliga (till exempel Virtuella kärnor och VM-instanser) med en [Resource Manager core ökning kvotförfrågan](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Kontrollera den utgivna versionen

Jämför klusterversionen med den senaste versionen av HDInsight. Varje HDInsight-versionen innehåller nya program, funktioner, korrigeringar och felkorrigeringar. Det problem som påverkar ditt kluster kanske har åtgärdats i den senaste versionen. Om möjligt kör klustret använder den senaste versionen av HDInsight och associerade bibliotek som Apache HBase, Apache Spark och andra.

#### <a name="restart-your-cluster-services"></a>Starta om din klustertjänster

Överväg att starta om dina tjänster via Ambari UI eller den klassiska Azure-CLI om du upplever fördröjningar i klustret. Klustret kan bero på tillfälliga fel och starta om är det snabbaste sättet att stabilisera din miljö och eventuellt förbättra prestanda.

## <a name="step-3-view-your-clusters-health"></a>Steg 3: Visa din klusterhälsa

HDInsight-kluster består av olika typer av noder som körs på instanser av virtuella datorer. Varje nod kan övervakas för negativa effekter på resurser, problem med nätverksanslutningen och andra problem som kan sakta ned klustret. Varje kluster innehåller två huvudnoder och de flesta klustertyper innehålla en kombination av worker och kantnoder. 

En beskrivning av de olika noderna som används av varje typ av kluster finns i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

I följande avsnitt beskrivs hur du kontrollerar hälsotillståndet för varje nod och i hela klustret.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>En ögonblicksbild av klusterhälsa med hjälp av Ambari UI-instrumentpanel

Den [Ambari UI instrumentpanelen](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) innehåller en översikt över klusterhälsa, till exempel drifttid, minne, nätverks- och CPU-användning, HDFS diskanvändning och så vidare. Använd avsnittet värdar i Ambari om du vill visa resurser på en värdnivå. Du kan även stoppa och starta om tjänsterna.

### <a name="check-your-webhcat-service"></a>Kontrollera din WebHCat-tjänst

Ett vanligt scenario för Apache Hive, Apache Pig eller Apache Sqoop jobb misslyckas uppstår ett fel med den [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (eller *Templeton*) service. WebHCat är ett REST-gränssnitt för körning av Fjärrjobb, till exempel Hive, Pig, dig mer och MapReduce. WebHCat översätter jobbet inskickade önskemål till Apache Hadoop YARN-program och returnerar en status som härletts från statusen för YARN-programmet.  I följande avsnitt beskrivs vanliga WebHCat HTTP-statuskoder.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 statuskod)

Detta är ett allmänt meddelande från gateway-noder och är de vanligaste statuskoden för felet. En möjlig orsak till detta är den WebHCat-tjänst som håller ned på den aktiva huvudnoden. Om du vill söka efter den här risken, använder du följande CURL-kommando:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari visas en avisering som visar de värdar där WebHCat-tjänsten har stoppats. Du kan försöka att återställa tjänsten WebHCat säkerhetskopiera genom att starta om tjänsten på dess värd.

![Restart WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Om en server med WebHCat fortfarande inte kommer, kontrollerar du åtgärdsloggen för felmeddelanden. Mer detaljerad information, kontrollera den `stderr` och `stdout` filer som refereras till på noden.

#### <a name="webhcat-times-out"></a>WebHCat tidsgränsen uppnås

En HDInsight-Gateway tidsgränsen svar som tar längre tid än två minuter returnerar `502 BadGateway`. WebHCat frågar YARN-tjänster för jobbstatus och om YARN tar längre tid än två minuter att svara, som begär kan timeout.

I det här fallet granska följande loggar i den `/var/log/webhcat` directory:

* **webhcat.log** är log4j-loggen vilka skrivningar serverloggar
* **webhcat console.log** är stdout för servern när startade
* **webhcat-konsolen-error.log** är stderr för serverprocessen

> [!NOTE]  
> Varje `webhcat.log` perioder varje dag, genereras filerna med namnet `webhcat.log.YYYY-MM-DD`. Välj filen för det tidsintervall som du undersöker.

I följande avsnitt beskrivs några orsaker till WebHCat-timeout.

##### <a name="webhcat-level-timeout"></a>Tidsgräns för WebHCat-nivå

När WebHCat är under belastning med fler än 10 öppna sockets, tar det längre tid att etablera nya socketanslutningar, vilket kan resultera i en tidsgräns. Om du vill visa nätverksanslutningar till och från WebHCat använder `netstat` på den aktuella aktiva huvudnoden:

```bash
netstat | grep 30111
```

30111 är WebHCat lyssnar på porten. Antalet öppna sockets bör vara mindre än 10.

Om det finns inga öppna sockets, genererar inte ett resultat i det föregående kommandot. Kontrollera om Templeton är igång och lyssnar på port 30111, Använd:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Tidsgräns för YARN-nivå

Templeton anropar YARN att köra jobb och kommunikationen mellan Templeton och YARN kan orsaka en tidsgräns.

Det finns två typer av tidsgränser på YARN-nivå:

1. Skicka en YARN-jobbet kan ta tillräckligt lång för att orsaka en tidsgräns.

    Om du öppnar den `/var/log/webhcat/webhcat.log` loggfilen och Sök efter ”köade jobb” du kan se flera poster där körningstiden är för lång (> 2 000 ms), med poster som visar ökar väntetid.

    Tid för jobb i kö fortsätter att öka eftersom den hastighet som nya jobb får skickas är högre än den som de gamla jobb har slutförts. När det YARN-minnet är 100% används, den *joblauncher kö* kan inte längre att låna kapacitet från den *standardkö*. Därför kan inga fler nya jobb godkännas i kön joblauncher. Detta kan medföra väntetiden ska bli längre och längre, orsakar ett tidsgränsfel som vanligtvis följs av många andra.

    Följande bild visar joblauncher kön 714.4% överutnyttjas. Detta är acceptabelt så länge det finns fortfarande kapacitet i standardkön som ska lånar från. När klustret har utnyttjats och YARN-minne är 100% kapacitet, måste nya jobb däremot vänta, vilket till slut gör timeout-fel.

    ![Joblauncher kö](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Det finns två sätt att lösa problemet: antingen minska hastighet nya jobb som skickas eller öka hastigheten för användning av den äldre jobb genom att skala klustret.

2. YARN-bearbetning kan ta lång tid, vilket kan orsaka tidsgränser.

    * Lista över alla jobb: Det här är en tidskrävande anrop. Det här anropet räknar upp program från YARN ResourceManager och för varje färdiga programmet, hämtar status från YARN-JobHistoryServer. Med ett högre antal jobb kan det här anropet timeout.

    * Lista jobb är äldre än sju dagar: HDInsight YARN JobHistoryServer är konfigurerad för att bevara slutfört jobb informationen i sju dagar (`mapreduce.jobhistory.max-age-ms` värde). Försök att räkna upp borttagna jobb resulterar i en tidsgräns.

Diagnostisera problemen:

1. Intervall för UTC-tid att felsöka
2. Välj lämplig `webhcat.log` fil (er)
3. Leta efter WARN och felmeddelanden under den tiden

#### <a name="other-webhcat-failures"></a>Andra fel med WebHCat

1. HTTP-statuskoden 500

    I de flesta fall där WebHCat returnerar 500 innehåller felmeddelandet information om felet. I annat fall titta igenom `webhcat.log` för WARN och felmeddelanden.

2. Misslyckade jobb

    Det kan finnas fall där interaktioner med WebHCat är lyckades, men jobb som misslyckas.

    Templeton samlar in jobbet konsolens utdata som `stderr` i `statusdir`, vilket ofta är användbart för felsökning. `stderr` innehåller YARN-program-ID för den faktiska frågan.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Steg 4: Granska miljö stack och versioner

Ambari UI **Stack och Version** sidan innehåller information om klustret tjänster konfigurations- och tidigare versioner.  Felaktig Hadoop biblioteket tjänstversioner kan vara en orsak till klusterfelet.  I Ambari UI, väljer du den **Admin** menyn och sedan **stackar och versioner**.  Välj den **versioner** fliken på sidan för att se information om tjänstens version:

![Stack och versioner](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Steg 5: Granska loggfilerna

Det finns många typer av loggarna som genereras från de många tjänsterna och komponenterna som utgör ett HDInsight-kluster. [Loggfiler för WebHCat](#check-your-webhcat-service) beskrivs tidigare. Det finns flera andra användbara loggfiler som du kan undersöka om du vill begränsa problem med ditt kluster, enligt beskrivningen i följande avsnitt.

* HDInsight-kluster består av flera noder, de flesta är har behörighet för att köra skickade jobb. Jobb körs samtidigt, men loggfiler kan bara visa resultat linjärt. HDInsight kör nya aktiviteter avslutas andra som inte slutföras först. Den här aktiviteten loggas i den `stderr` och `syslog` filer.

* Loggfiler i skriptet visar fel eller oväntat konfigurationsändringar under skapandeprocessen för ditt kluster.

* Hadoop steg loggarna identifiera Hadoop-jobb startas som en del av ett steg som innehåller fel.

### <a name="check-the-script-action-logs"></a>Kontrollera skriptet åtgärdsloggar

HDInsight [skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) köra skript på klustret som manuellt eller när anges. Skriptåtgärder kan till exempel användas för att installera ytterligare programvara på klustret eller ändra konfigurationsinställningarna från standardvärdena. Kontrollerar åtgärdsloggar skriptet ger kunskaper om fel som uppstått under konfiguration och konfiguration.  Du kan visa statusen för en skriptåtgärd genom att välja den **ops** knappen i Ambari UI, eller genom att komma åt loggarna från standardkontot för lagring.

Åtgärdsloggar skript finns i den `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` directory.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Visa loggar för HDInsight med hjälp av Ambari snabblänkar

HDInsight Ambari UI innehåller ett antal **snabblänkar** avsnitt.  För att komma åt log-länkar för en viss tjänst i ditt HDInsight-kluster, öppna Ambari UI för klustret och välj sedan länken tjänsten i listan till vänster. Välj den **snabblänkar** listrutan sedan noden HDInsight Orienteringspunkter och välj sedan länken för dess associerade logg.

Till exempel för HDFS-loggar:

![Ambari snabblänkar till loggfiler](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Visa loggfiler som genererats av Hadoop

Ett HDInsight-kluster genererar loggar som skrivs till Azure-tabeller och Azure Blob storage. YARN skapar en egen körningsloggar. Mer information finns i [hantera loggar för ett HDInsight-kluster](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Granska heapdumpar

Heapdumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler vid den tidpunkten, vilket är användbart för att diagnostisera problem som uppstår vid körning. Mer information finns i [aktivera heapdumpar för Apache Hadoop-tjänster på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Steg 6: Kontrollera konfigurationsinställningarna

HDInsight-kluster är förkonfigurerad med standardinställningarna för relaterade tjänster, till exempel Hadoop, Hive, HBase och så vidare. Beroende på vilken typ av kluster, dess maskinvarukonfiguration, dess antalet noder, vilka typer av jobb du kör, och data du arbetar med (och hur dessa data bearbetas) du kan behöva optimera din konfiguration.

Detaljerade anvisningar om hur du optimerar prestandakonfigurationer för de flesta scenarier finns i [optimera klusterkonfigurationer med Apache Ambari](hdinsight-changing-configs-via-ambari.md). När du använder Spark, se [optimera Apache Spark-jobb för prestanda](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Steg 7: Återskapa felet på ett annat kluster

Starta ett nytt kluster med samma konfiguration för att diagnostisera källan till ett klusterfel, och sedan skicka det misslyckade jobbet steg en i taget. Kontrollera resultatet av varje steg innan nästa. Den här metoden ger dig möjlighet att korrigera och kör ett enda steg som misslyckades. Den här metoden har även fördelen med bara inläsning av dina indata en gång.

1. Skapa ett nytt testkluster med samma konfiguration som misslyckade klustret.
2. Skicka det första steget i test-klustret.
3. När steget har slutförts bearbetning, Sök efter fel i loggfilerna steg. Anslut till test-klustrets huvudnod och visa det loggfilerna. Loggfilerna steg visas endast när steget körs under en period har slutförts eller misslyckas.
4. Om det första steget lyckades, kör du nästa steg. Om det finns fel, Undersök fel i loggfilerna. Om det uppstod ett fel i koden, korrigera och kör steget.
5. Fortsätt tills alla steg körs utan fel.
6. När du är klar felsökning test-klustret, ta bort den.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Åtkomst Apache Hadoop YARN-program för inloggning i Linux-baserat HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap dumps för Apache Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Kända problem för Apache Spark-kluster i HDInsight](hdinsight-apache-spark-known-issues.md)
