---
title: Hantera loggar för ett HDInsight-kluster – Azure HDInsight
description: Bestäm typer, storlekar och bevarandeprinciper för HDInsight-aktivitetsloggfiler.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 8c3cbf4c18b32a94abfe95e77be768020b44fda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272310"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Hantera loggar för ett HDInsight-kluster

Ett HDInsight-kluster producerar en mängd olika loggfiler. Apache Hadoop och relaterade tjänster, till exempel Apache Spark, producerar till exempel detaljerade jobbkörningsloggar. Hantering av loggfiler är en del av att upprätthålla ett felfritt HDInsight-kluster. Det kan också finnas lagstadgade krav för loggarkivering.  På grund av antalet och storleken på loggfiler, optimera logglagring och arkivering hjälper till med service kostnadshantering.

Hantera HDInsight klusterloggar inkluderar att behålla information om alla aspekter av klustermiljön. Den här informationen omfattar alla associerade Azure Service-loggar, klusterkonfiguration, jobbkörningsinformation, eventuella feltillstånd och andra data efter behov.

Typiska steg i HDInsight-logghantering är:

* Steg 1: Fastställ principer för logglagring
* Steg 2: Hantera konfigurationsloggar för klustertjänstversioner
* Steg 3: Hantera loggfiler för klusterjobbkörning
* Steg 4: Prognosloggvolymlagringsstorlekar och kostnader
* Steg 5: Bestäm principer och processer för loggarkiv

## <a name="step-1-determine-log-retention-policies"></a>Steg 1: Fastställ principer för logglagring

Det första steget i att skapa en HDInsight klusterlogghanteringsstrategi är att samla in information om affärsscenarier och lagringshistoriklagringskrav för jobbkörning.

### <a name="cluster-details"></a>Klusterinformation

Följande klusterinformation är användbar för att samla in information i din logghanteringsstrategi. Samla in den här informationen från alla HDInsight-kluster som du har skapat i ett visst Azure-konto.

* Klusternamn
* Klusterregion och Azure-tillgänglighetszon
* Klustertillstånd, inklusive information om den senaste tillståndsändringen
* Typ och antal HDInsight-instanser som angetts för huvud-, kärn- och uppgiftsnoderna

Du kan få den mesta informationen på den översta nivån med Hjälp av Azure-portalen.  Du kan också använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) för att få information om dina HDInsight-kluster:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Du kan också använda PowerShell för att visa den här informationen.  Mer information finns i [Apache Hantera Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Förstå arbetsbelastningarna som körs i klustren

Det är viktigt att förstå vilka arbetsbelastningstyper som körs på dina HDInsight-kluster för att utforma lämpliga loggningsstrategier för varje typ.

* Är arbetsbelastningarna experimentella (t.ex. utveckling eller test) eller produktionskvalitet?
* Hur ofta körs arbetsbelastningarna av produktionskvalitet normalt?
* Är någon av arbetsbelastningarna resurskrävande och/eller tidskrävande?
* Använder någon av arbetsbelastningarna en komplex uppsättning Hadoop-tjänster för vilka flera typer av loggar produceras?
* Har någon av arbetsbelastningarna tillhörande lagstadgade körningslinjekrav?

### <a name="example-log-retention-patterns-and-practices"></a>Exempel på loggkvarhållningsmönster och metoder

* Överväg att underhålla spårning av data härstamning genom att lägga till en identifierare i varje loggpost eller via andra tekniker. På så sätt kan du spåra den ursprungliga källan till data och åtgärden och följa data genom varje steg för att förstå dess konsekvens och giltighet.

* Fundera över hur du kan samla in loggar från klustret eller från mer än ett kluster och sortera dem för ändamål som granskning, övervakning, planering och aviseringar. Du kan använda en anpassad lösning för att komma åt och hämta loggfilerna regelbundet och kombinera och analysera dem för att tillhandahålla en instrumentpanelsvisning. Du kan också lägga till ytterligare funktioner för aviseringar för identifiering av säkerhet eller fel. Du kan skapa dessa verktyg med PowerShell, HDInsight SDK:er eller kod som har åtkomst till Azures klassiska distributionsmodell.

* Fundera över om en övervakningslösning eller tjänst skulle vara en användbar fördel. Microsoft System Center tillhandahåller ett [HDInsight-hanteringspaket](https://www.microsoft.com/download/details.aspx?id=42521). Du kan också använda verktyg från tredje part som Apache Chukwa och Ganglia för att samla in och centralisera loggar. Många företag erbjuder tjänster för att övervaka Hadoop-baserade stordatalösningar, till exempel: Centerity, Compuware APM, Sematext SPM och Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Steg 2: Hantera klustertjänstversioner och visa loggar

Ett typiskt HDInsight-kluster använder flera tjänster och programpaket med öppen källkod (till exempel Apache HBase, Apache Spark och så vidare). För vissa arbetsbelastningar, till exempel bioinformatik, kan du behöva behålla logghistorik för tjänstkonfiguration utöver jobbkörningsloggar.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa klusterkonfigurationsinställningar med Ambari-användargränssnittet

Apache Ambari förenklar hanteringen, konfigurationen och övervakningen av ett HDInsight-kluster genom att tillhandahålla ett webbgränssnitt och ett REST API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj fönstret **Klusterinstrumentpanel** på sidan HDInsight i Azure Portal för att öppna länksidan **klusterinstrumentpaneler.**  Välj sedan **instrumentpanelsfönstret hdinsight-kluster** för att öppna användargränssnittet i Ambari.  Du uppmanas att ange inloggningsuppgifter för klustret.

Om du vill öppna en lista över tjänstvyer väljer du fönstret **Ambari-vyer** på Azure-portalsidan för HDInsight.  Den här listan varierar beroende på vilka bibliotek du har installerat.  Du kan till exempel se YARN Queue Manager, Hive View och Tez View.  Välj en tjänstlänk för att se konfigurations- och tjänstinformation.  Sidan Ambari UI **Stack and Version** innehåller information om klustertjänsternas konfigurations- och tjänstversionshistorik. Om du vill navigera till det här avsnittet i Ambari-användargränssnittet väljer du **admin-menyn** och sedan **staplar och versioner**.  Välj fliken **Versioner** om du vill visa information om tjänstversioner.

![Apache Ambari admin Stack och versioner](./media/hdinsight-log-management/ambari-stack-versions.png)

Med hjälp av Ambari-användargränssnittet kan du hämta konfigurationen för alla (eller alla) tjänster som körs på en viss värd (eller nod) i klustret.  Välj **hosts-menyn** och länken för värden av intresse. På värdens sida väljer du knappen **Värdåtgärder** och hämtar sedan **klientkonfigurationer**.

![Apache Ambari ladda ner värd klient configs](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Visa skriptåtgärdsloggarna

[HDInsight-skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) kör skript i ett kluster, antingen manuellt eller när det anges. Skriptåtgärder kan till exempel användas för att installera ytterligare programvara i klustret eller för att ändra konfigurationsinställningarna från standardvärdena. Skriptåtgärdsloggar kan ge insikt i fel som uppstod under installationen av klustret och även konfigurationsinställningarnas ändringar som kan påverka klustrets prestanda och tillgänglighet.  Om du vill se status för en skriptåtgärd väljer du **ops-knappen** i ditt Ambari-användargränssnitt eller öppnar statusloggarna i standardlagringskontot. Förvaringsloggarna finns `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`på .

### <a name="view-ambari-alerts-status-logs"></a>Visa statusloggar för Ambari-aviseringar

Apache Ambari skriver varningsstatusändringar till `ambari-alerts.log`. Den fullständiga `/var/log/ambari-server/ambari-alerts.log`sökvägen är . Om du vill aktivera felsökning för loggen ändrar du en egenskap i `/etc/ambari-server/conf/log4j.properties.` Ändra och sedan posten under `# Log alert state changes` från:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Steg 3: Hantera loggfilerna för klusterjobbkörning

Nästa steg är att granska loggfilerna för jobbkörning för de olika tjänsterna.  Tjänster kan omfatta Apache HBase, Apache Spark och många andra. Ett Hadoop-kluster producerar ett stort antal utförliga loggar, så att avgöra vilka loggar som är användbara (och vilka som inte är det) kan vara tidskrävande.  Att förstå loggningssystemet är viktigt för riktad hantering av loggfiler.  Följande bild är en exempelloggfil.

![EXEMPEL PÅ exempel på exempelloggfilsutdata](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Öppna Hadoop-loggfilerna

HDInsight lagrar sina loggfiler både i klusterfilsystemet och i Azure Storage. Du kan undersöka loggfiler i klustret genom att öppna en [SSH-anslutning](hdinsight-hadoop-linux-use-ssh-unix.md) till klustret och bläddra i filsystemet, eller genom att använda statusportalen Hadoop YARN på fjärrhuvudnodsservern. Du kan granska loggfilerna i Azure Storage med något av de verktyg som kan komma åt och hämta data från Azure Storage. Exempel är [AzCopy,](../storage/common/storage-use-azcopy.md) [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)och Visual Studio Server Explorer. Du kan också använda PowerShell- och Azure Storage-klientbiblioteken, eller Azure .NET SDK:er, för att komma åt data i Azure blob-lagring.

Hadoop kör jobbets arbete som *aktivitetsförsök* på olika noder i klustret. HDInsight kan initiera spekulativa uppgiftsförsök och avsluta alla andra uppgiftsförsök som inte slutförs först. Detta genererar betydande aktivitet som loggas till styrenheten, stderr och syslog loggfiler on-the-fly. Dessutom körs flera aktivitetsförsök samtidigt, men en loggfil kan bara visa resultat linjärt.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight-loggar skrivna till Azure Blob-lagring

HDInsight-kluster är konfigurerade för att skriva uppgiftsloggar till ett Azure Blob-lagringskonto för alla jobb som skickas med Azure PowerShell-cmdlets eller .NET-jobböverförings-API:erna.  Om du skickar jobb via SSH till klustret lagras körningsloggningsinformationen i Azure-tabellerna som beskrivs i föregående avsnitt.

Förutom de grundläggande loggfiler som genereras av HDInsight, installerade tjänster som YARN också generera loggfiler jobbkörning.  Antalet och typen av loggfiler beror på vilka tjänster som är installerade.  Vanliga tjänster är Apache HBase, Apache Spark och så vidare.  Undersök jobbloggkörningsfilerna för varje tjänst för att förstå de övergripande loggningsfiler som är tillgängliga i klustret.  Varje tjänst har sina egna unika metoder för loggning och platser för lagring av loggfiler.  Som ett exempel diskuteras information om hur du kommer åt de vanligaste tjänstloggfilerna (från YARN) i följande avsnitt.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight loggar som genereras av YARN

YARN sammanställer loggar över alla behållare på en arbetsnod och lagrar dessa loggar som en aggregerad loggfil per arbetsnod. Loggen lagras i standardfilsystemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod aggregeras alltid till en enda fil. Det finns bara en logg per arbetsnod som används av ditt program. Loggaggregering är aktiverad som standard på HDInsight-kluster version 3.0 och senare. Aggregerade loggar finns i standardlagring för klustret.

```
/app-logs/<user>/logs/<applicationId>
```

De aggregerade loggarna är inte direkt läsbara, eftersom de är skrivna i ett binärt TFile-format som indexeras efter behållare. Använd YARN ResourceManager-loggarna eller CLI-verktygen för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

#### <a name="yarn-cli-tools"></a>YARN CLI-verktyg

Om du vill använda YARN CLI-verktygen måste du först ansluta till HDInsight-klustret med SSH. Ange `<applicationId>`, `<user-who-started-the-application>` `<containerId>`, `<worker-node-address>` och information när du kör dessa kommandon. Du kan visa loggarna som oformaterad text med något av följande kommandon:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>GARN ResourceManager UI

YARN ResourceManager-användargränssnittet körs på klusterhuvudnoden och nås via webbgränssnittet Ambari. Gör så här för att visa YARN-loggarna:

1. Gå till `https://CLUSTERNAME.azurehdinsight.net` i en webbläsare. Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.
2. Välj YARN i listan över tjänster till vänster.
3. I listrutan Snabblänkar väljer du en av klusterhuvudnoderna och väljer sedan **ResourceManager-loggar**. Du presenteras med en lista med länkar till YARN loggar.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Steg 4: Prognosloggvolymlagringsstorlekar och kostnader

När du har slutfört föregående steg har du en förståelse för vilka typer och volymer av loggfiler som dina HDInsight-kluster producerar.

Analysera sedan volymen av loggdata på viktiga logglagringsplatser under en tidsperiod. Du kan till exempel analysera volym och tillväxt över 30-60-90 dagars perioder.  Spela in den här informationen i ett kalkylblad eller använd andra verktyg som Visual Studio, Azure Storage Explorer eller Power Query för Excel. Mer information finns i [Analysera HDInsight-loggar](hdinsight-debug-jobs.md).  

Du har nu tillräckligt med information för att skapa en logghanteringsstrategi för nyckelloggarna.  Använd kalkylbladet (eller verktyget för val) för att prognostisera både loggstorlekstillväxt och logga lagringskostnader för Azure-tjänster framöver.  Överväg även eventuella krav på loggkvarhållning för den uppsättning loggar som du undersöker.  Nu kan du omrapportera framtida logglagringskostnader, efter att ha fastställt vilka loggfiler som kan tas bort (om sådana finns) och vilka loggar som ska behållas och arkiveras till billigare Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Steg 5: Bestäm principer och processer för loggarkiv

När du har bestämt vilka loggfiler som kan tas bort kan du justera loggningsparametrarna för många Hadoop-tjänster för att automatiskt ta bort loggfiler efter en angiven tidsperiod.

För vissa loggfiler kan du använda en alternativ arkiveringsmetod för loggfiler till lägre priser. För Azure Resource Manager-aktivitetsloggar kan du utforska den här metoden med hjälp av Azure-portalen.  Konfigurera arkivering av Resource Manager-loggarna genom att välja länken **Aktivitetslogg** i Azure-portalen för din HDInsight-instans.  Högst upp på söksidan för aktivitetsloggen väljer du **menyalternativet Exportera** för att öppna **loggfönstret Exportera aktivitet.**  Fyll i prenumerationen, regionen, om du vill exportera till ett lagringskonto och hur många dagar du vill behålla loggarna. I samma fönsterruta kan du också ange om du vill exportera till en händelsenav.

![Förhandsversion av förhandsversionen av azure portal-exportaktivitetsloggen](./media/hdinsight-log-management/hdi-export-log-files.png)

Du kan också arkivera skriptlogg med PowerShell.  Ett exempel på PowerShell-skript finns i [Arkivera Azure Automation-loggar till Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Komma åt Azure Storage-mått

Azure Storage kan konfigureras för att logga lagringsåtgärder och åtkomst. Du kan använda dessa mycket detaljerade loggar för kapacitetsövervakning och planering och för granskningsbegäranden till lagring. Den loggade informationen innehåller latensinformation, så att du kan övervaka och finjustera prestanda för dina lösningar.
Du kan använda .NET SDK för Hadoop för att undersöka loggfilerna som genereras för Azure Storage som innehåller data för ett HDInsight-kluster.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Styra storleken och antalet säkerhetskopieringsindex för gamla loggfiler

Om du vill styra storleken och antalet loggfiler som `RollingFileAppender`behålls anger du följande egenskaper för:

* `maxFileSize`är den kritiska storleken på filen, ovanför vilken filen rullas. Standardvärdet är 10 MB.
* `maxBackupIndex`anger antalet säkerhetskopior som ska skapas, standard 1.

### <a name="other-log-management-techniques"></a>Andra tekniker för logghantering

För att undvika att diskutrymmet börjar ta kan du använda vissa operativsystemverktyg, till exempel [logrotate](https://linux.die.net/man/8/logrotate) för att hantera hanteringen av loggfiler. Du kan `logrotate` konfigurera att köras dagligen, komprimera loggfiler och ta bort gamla. Din metod beror på dina behov, till exempel hur länge loggfilerna ska behållas på lokala noder.  

Du kan också kontrollera om FELSÖKNINGSloggning är aktiverat för en eller flera tjänster, vilket avsevärt ökar utdataloggstorleken.  

Om du vill samla in loggarna från alla noder till en central plats kan du skapa ett dataflöde, till exempel att inta alla loggposter i Solr.

## <a name="next-steps"></a>Nästa steg

* [Övervakning och loggningsövning för HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Få tillgång till Apache Hadoop YARN-programloggar i Linux-baserade HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Så här styr du storleken på loggfiler för olika Apache Hadoop-komponenter](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
