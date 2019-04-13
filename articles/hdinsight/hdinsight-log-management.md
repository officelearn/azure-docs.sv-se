---
title: Hantera loggar för ett HDInsight-kluster – Azure HDInsight
description: Kontrollera de typer och storlekar bevarandeprinciper för loggfiler för HDInsight-aktivitet.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 7ff89e12a1011c0a16644324584eb1610302b1b3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544301"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Hantera loggar för ett HDInsight-kluster

Ett HDInsight-kluster ger en mängd olika loggfiler. Till exempel generera Apache Hadoop och relaterade tjänster, till exempel Apache Spark, detaljerade loggarna för jobbkörning. Hantering av filen är en del av underhållet ett felfritt HDInsight-kluster. Det kan också vara regelkrav för arkivering av loggen.  På grund av antalet och storleken på loggfiler, optimerar lagringen för loggen och arkivering hjälper dig med tjänsten för kostnadshantering.

Hantera loggar för HDInsight-kluster innehåller behåller information om alla aspekter av klustermiljön. Informationen omfattar alla associerade Azure-tjänstens loggar, klusterkonfiguration, jobbinformation för körning, alla feltillstånd och andra data efter behov.

Vanliga steg i hantering av HDInsight är:

* Steg 1: Fastställa principer för kvarhållning av logg
* Steg 2: Hantera kluster tjänstloggar versioner konfiguration
* Steg 3: Hantera loggfiler för klustret jobbet körning
* Steg 4: Skapa prognoser för log volym lagringsstorlekar och kostnader
* Steg 5: Fastställa log Arkiv principer och processer

## <a name="step-1-determine-log-retention-policies"></a>Steg 1: Fastställa principer för kvarhållning av logg

Det första steget i att skapa ett HDInsight-kluster log management strategi är att samla in information om affärsscenarier och lagringskrav för jobbet körning historik.

### <a name="cluster-details"></a>Klusterinformation

Information om följande kluster är användbara i hjälper till att samla in information i din strategi för hantering av loggen. Samla in informationen från alla HDInsight-kluster som du har skapat i en viss Azure-konto.

* Klusternamn
* Kluster-region och Azure-tillgänglighetszon
* Tillstånd för klustret, inklusive information om den senaste tillståndsändringen
* Typ och antal HDInsight-instanser som angetts för master, core och uppgiften noder

Du kan hämta de flesta av informationen på den högsta nivån med Azure portal.  Du kan också använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) att få information om HDInsight-kluster:

```azurecli
    az hdinsight list --resource-group <ResourceGroup>
    az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Du kan också använda PowerShell för att visa denna information.  Mer information finns i [Apache hantera Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Förstå de arbetsbelastningar som körs i ditt kluster

Det är viktigt att förstå de typer av arbetsbelastningar som körs på HDInsight-kluster till lämplig loggning strategier för varje typ av design.

* Är arbetsbelastningarna experimentella (till exempel utveckling eller testning) eller hög kvalitet?
* Hur ofta arbetsbelastningarna med hög kvalitet normalt kör?
* Är något av arbetsbelastningarna resurskrävande och/eller långvariga?
* Använd någon av arbetsbelastningarna som en komplex uppsättning Hadoop-tjänster som flera typer av loggar produceras?
* Något av arbetsbelastningarna har associerat föreskrifter körning härkomst krav?

### <a name="example-log-retention-patterns-and-practices"></a>Exempel log kvarhållning mönster och metoder

* Överväg att underhålla datahärkomst spårning genom att lägga till en identifierare till varje loggpost eller via andra tekniker. På så sätt kan du spåra tillbaka den ursprungliga källan och åtgärden och följ data via varje steg för att förstå dess konsekvens och giltighet.

* Överväg hur du kan samla in loggar från klustret eller från flera kluster och sortera dem för exempelvis granskning, övervakning, planering och varningar. Du kan använda en anpassad lösning för att komma åt och ladda ned filerna med jämna mellanrum, och kombinera och analysera dem för att tillhandahålla en instrumentpanelsvy. Du kan också lägga till ytterligare funktioner för avisering för säkerhets- eller felidentifiering. Du kan skapa dessa verktyg med PowerShell, HDInsight SDK: er eller koden som ansluter till den klassiska distributionsmodellen.

* Överväg om en övervakning lösning eller tjänst skulle vara en användbar fördel. Microsoft System Center innehåller en [HDInsight hanteringspaket](https://www.microsoft.com/download/details.aspx?id=42521). Du kan också använda verktyg från tredje part, till exempel Apache Chukwa och Ganglia för att samla in och centralisera loggar. Många företag erbjuder tjänster för att övervaka Hadoop-baserade big data-lösningar, till exempel: Centerity, Compuware APM, Sematext SPM och Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Steg 2: Hantera service-kluster-versioner och visa loggar skriptåtgärd

En typisk HDInsight-klustret använder flera tjänster och program med öppen källkod-paket (till exempel Apache HBase, Apache Spark och så vidare). För vissa arbetsbelastningar, till exempel bioinformatik, kan du behöva behålla service configuration logghistorik förutom loggarna för jobbkörning.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa inställningar för klustrets med Ambari UI

Apache Ambari förenklar hantering, konfiguration och övervakning i ett HDInsight-kluster genom att tillhandahålla en web UI och ett REST-API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj den **Klusterinstrumentpanel** fönstret på Azure HDInsight portalsidan att öppna den **Klusterinstrumentpaneler** länksida.  Välj sedan den **HDInsight-klusterinstrumentpanel** fönstret för att öppna Ambari UI.  Du uppmanas att ange dina autentiseringsuppgifter för klusterinloggning.

Om du vill öppna en lista över service-vyerna, Välj den **Ambari-vyer** rutan på sidan för Azure portal för HDInsight.  Den här listan varierar beroende på vilka bibliotek som du har installerat.  Du kan till exempel se köhanteraren YARN, Hive-vyerna och Tez.  Välj en länk för tjänsten att se konfigurations- och tjänstinformation.  Ambari UI **Stack och Version** sidan innehåller information om kluster-tjänsternas konfiguration och versionshistorik för tjänsten. Gå till det här avsnittet av Ambari UI, välja den **Admin** menyn och sedan **stackar och versioner**.  Välj den **versioner** fliken för att se information om tjänstens version.

![Stack och versioner](./media/hdinsight-log-management/stack-versions.png)

Du kan med hjälp av Ambari UI, för att hämta konfigurationen för alla (eller alla) tjänster som körs på en viss värd (eller noden) i klustret.  Välj den **värdar** -menyn, sedan på länken för värden i närheten. På den värd-sidan väljer den **värden åtgärder** knappen och sedan **ladda ned klienten Peeringkonfigurationer**. 

![Klient-konfigurationer för värd](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Visa skript åtgärdsloggar

HDInsight [skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) köra skript i ett kluster, antingen manuellt eller när angetts. Skriptåtgärder kan till exempel användas för att installera ytterligare programvara på klustret eller ändra konfigurationsinställningarna från standardvärdena. Skriptet åtgärdsloggar kan ge insikter om fel som uppstod under installationen av klustret och ändringar i av konfigurationsinställningar som kan påverka klusterprestanda och tillgänglighet.  Om du vill se status för en skriptåtgärd, Välj den **ops** knappen på din Ambari UI, eller åtkomst status loggar in standardkontot för lagring. Storage-loggar finns på `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Steg 3: Hantera loggfiler för klustret jobbet körning

Nästa steg är Granska loggfilerna för jobbet körning för de olika tjänsterna.  Tjänsterna kan omfatta Apache HBase, Apache Spark och mycket mer. Ett Hadoop-kluster ger ett stort antal utförliga loggar så kan vara tidskrävande att bestämma vilka loggar är användbara (och som inte är).  Förstå loggning system är viktigt för riktade hantering av loggfiler.  Följande är en exempel-loggfil.

![Exempel på HDInsight en loggfil](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Få åtkomst till loggfilerna för Hadoop

HDInsight lagrar loggfilerna både i filsystemet kluster och i Azure storage. Du kan undersöka loggfiler i klustret genom att öppna en [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) anslutningen till klustret och bläddra i filsystemet eller med hjälp av Hadoop YARN Status-portalen på fjärranslutna huvudnoden-servern. Du kan granska loggfilerna i Azure storage med någon av de verktyg som kan komma åt och hämta data från Azure storage. Exempel är [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer), och Visual Studio Server Explorer. Du kan också använda PowerShell och Azure Storage-klientbibliotek eller Azure .NET SDK för att komma åt data i Azure blob storage.

Hadoop körs verk som tillhör jobb som *uppgift försök* på olika noder i klustret. HDInsight kan initiera spekulativ uppgift försök Avsluta några andra uppgiften försök som inte slutförs först. Detta genererar betydande aktivitet som loggas i domänkontrollanten, stderr och syslog log-filer i farten. Dessutom kan flera försök att uppgiften körs samtidigt, men en loggfil kan bara visa resultat linjärt.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight-loggar som skrivs till Azure Blob storage

HDInsight-kluster har konfigurerats för att skriva uppgift loggar till ett Azure Blob storage-konto för alla jobb som skickas med hjälp av Azure PowerShell-cmdlets eller jobböverföring .NET API: er.  Om du skickar in jobb via SSH till klustret, lagras loggningsinformation körning i Azure-tabeller som beskrivs i föregående avsnitt.

Installerat services förutom core loggfiler som genererats av HDInsight, t.ex. YARN också skapa jobb loggfiler för körning.  Antalet och typen av loggfiler beror på de tjänster som är installerad.  Vanliga tjänster är Apache HBase, Apache Spark och så vidare.  Undersök jobbet körning loggfilerna för varje tjänst att förstå den övergripande loggningen replikeringsfiler i klustret.  Varje tjänst har sin egen unika metoder för loggning och platser för att lagra loggfiler.  Till exempel beskrivs information för att komma åt de vanligaste service-loggfilerna (från YARN) i följande avsnitt.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight-loggar som genereras av YARN

YARN sammanställer loggar över alla behållare på en underordnad nod och lagrar dessa loggar som en sammansatt loggfil per arbetsnod. Loggen lagras på standardfilsystemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod alltid räknas avgifter för en enskild fil. Det finns bara en logg per arbetsnod som används av ditt program. Log aggregering är aktiverat som standard på HDInsight-kluster version 3.0 och senare. Sammanställda loggfiler finns i Standardlagringsutrymmet för klustret.

```
    /app-logs/<user>/logs/<applicationId>
```

Sammanställda loggfiler är inte direkt läsbar, eftersom de är skrivna i TFile binärformat indexeras av behållare. Använda loggar för YARN ResourceManager eller CLI-verktyg för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

#### <a name="yarn-cli-tools"></a>YARN CLI-verktyg

Om du vill använda YARN CLI-verktyg, måste du först ansluta till HDInsight-kluster med SSH. Ange den `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, och `<worker-node-address>` information när du kör dessa kommandon. Du kan visa loggarna som oformaterad text med något av följande kommandon:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI körs på klustrets huvudnod och nås via Ambari-webbgränssnittet. Använd följande steg för att visa YARN-loggar:

1. I en webbläsare, navigerar du till `https://CLUSTERNAME.azurehdinsight.net`. Ersätt CLUSTERNAME med namnet på ditt HDInsight-kluster.
2. Listan över tjänster till vänster, Välj YARN.
3. Välj någon av klustrets huvudnoder listrutan snabblänkar och välj sedan **ResourceManager loggar**. Visas en lista med länkar till YARN-loggar.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Steg 4: Skapa prognoser för log volym lagringsstorlekar och kostnader

När du har slutfört föregående steg, har du en förståelse för vilka typer och volymer med loggfiler som som ger upphov till HDInsight-kluster.

Sedan analysera loggdata lagringsplatser för nyckel log under en viss tidsperiod. Du kan till exempel analysera volym och tillväxt över 30 – 60 – 90 dagarna.  Registrera den här informationen i ett kalkylblad eller använda andra verktyg som Visual Studio, Azure Storage Explorer eller Power Query för Excel. Mer information finns i [analysera HDInsight loggar](hdinsight-debug-jobs.md).  

Nu har du tillräcklig information för att skapa en strategi för hantering av loggen för viktiga loggarna.  Använd kalkylbladet (eller verktyg) för att skapa prognoser för båda log storlekstillväxt och logga Azure-tjänsten lagringskostnader framöver.  Överväg att även eventuella kvarhållning loggkraven för loggar som du undersöker.  Du kan nu reforecast framtida log kostnader för lagring, när du har fastställt vilken loggfiler kan tas bort (om sådan finns) och vilka loggar ska behållas och arkiveras till billigare Azure storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Steg 5: Fastställa log Arkiv principer och processer

När du har bestämt vilka loggfiler kan tas bort kan du justera parametrar på många Hadoop-tjänster för att automatiskt ta bort loggfiler efter en angiven tidsperiod.

Du kan använda ett billigare loggfilen arkivering metod för vissa loggfiler. Aktivitetsloggar i Azure Resource Manager, kan du utforska den här metoden med Azure portal.  Konfigurera arkivering av ARM-loggarna genom att välja den **aktivitetsloggen**' länken i Azure-portalen för din HDInsight-instans.  Överst på sidan för sökning av aktivitetsloggen, väljer den **exportera** menyalternativet att öppna den **exportera aktivitetslogg** fönstret.  Fyll i prenumerationen, region, om du vill exportera till ett lagringskonto och hur många dagar att behålla loggarna. På det här samma fönstret kan ange du också om du vill exportera till en händelsehubb. 

![Exportera loggfiler](./media/hdinsight-log-management/archive.png)

Du kan också skript log arkivering med PowerShell.  Ett exempel PowerShell-skript finns i [Arkiv Azure Automation loggar till Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Åtkomst till Azure storage-mått

Azure storage kan konfigureras att loggen lagringsåtgärder och åtkomst. Du kan använda dessa mycket detaljerade loggar för övervakning och planering och för granskning förfrågningar till storage. Loggade informationen innehåller information om svarstider, så att du kan övervaka och finjustera prestanda för dina lösningar.
Du kan använda .NET SDK för Hadoop för att undersöka loggfiler som genererats för Azure-lagring som innehåller data för ett HDInsight-kluster.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Kontrollera storleken och antalet säkerhetskopiering index för gamla loggfiler

Ange följande egenskaper för att kontrollera storlek och antal loggfiler som bevaras den `RollingFileAppender`:

* `maxFileSize` är kritisk storleken på filen, ovan, som filen återställs. Standardvärdet är 10 MB.
* `maxBackupIndex` Anger hur många säkerhetskopior som ska skapas standardvärdet 1.

### <a name="other-log-management-techniques"></a>Andra metoder för hantering av logg

För att undvika körs slut på diskutrymme kan du använda några OS-verktyg som [logrotate](https://linux.die.net/man/8/logrotate) att hantera hantering av loggfiler. Du kan konfigurera `logrotate` dagligen måste komprimera logga filer och ta bort gamla. Din beror på dina behov, till exempel hur länge för att hålla loggfilerna på lokala noder.  

Du kan också kontrollera om felsökningsloggning är aktiverat för en eller flera tjänster, vilket ökar loggstorleken utdata.  

För att samla in loggar från alla noder till en central plats, kan du skapa ett dataflöde, till exempel mata in alla loggposter i Solr.

## <a name="next-steps"></a>Nästa steg

* [Övervakning och loggning för HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Åtkomst Apache Hadoop YARN-programloggar i Linux-baserat HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Så här kontrollerar du storleken på loggfiler för olika Apache Hadoop-komponenter](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
