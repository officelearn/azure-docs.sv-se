---
title: Hantera loggfiler för ett HDInsight-kluster - Azure HDInsight | Microsoft Docs
description: Fastställa typer, storlek och bevarandeprinciper för HDInsight aktivitet loggfiler.
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
ms.openlocfilehash: d3ca9983eee4db09a68bf772b80c9ef841117872
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Hantera loggar för ett HDInsight-kluster

Ett HDInsight-kluster ger en mängd olika loggfilerna. Till exempel ger Apache Hadoop och relaterade tjänster, till exempel Apache Spark körningsloggar för detaljerad jobbet. Hantering av loggfilen är en del av underhållet ett felfritt HDInsight-kluster. Det kan också finnas krav för arkivering av loggen.  På grund av antalet och storleken för loggfiler optimera lagra loggen och arkivering hjälper dig med tjänsthantering kostnaden.

Hantera HDInsight-kluster loggar innehåller behåller information om alla aspekter av klustermiljön. Informationen omfattar alla tillhörande Azure-och tjänstloggar, klusterkonfiguration, körning jobbinformation, eventuella fel tillstånd och andra data efter behov.

Typiska steg i hantering av HDInsight är:

* Steg 1: Bestäm logga bevarandeprinciper
* Steg 2: Hantera klustret tjänstloggar versioner konfiguration
* Steg 3: Hantera loggfiler för kluster-jobbet körning
* Steg 4: Prognos loggen volym lagringsstorlekar och kostnader
* Steg 5: Bestäm loggen Arkiv principer och processer

## <a name="step-1-determine-log-retention-policies"></a>Steg 1: Bestäm logga bevarandeprinciper

Det första steget i att skapa en hanteringsstrategi för HDInsight-kluster loggen är att samla in information om affärsscenarier och lagringskrav för jobbet körning historik.

### <a name="cluster-details"></a>Information om kluster

Följande information i klustret är användbara i hjälper till att samla in information i din strategi för loggen. Samla in informationen från alla HDInsight-kluster som du har skapat i en viss Azure-konto.

* Klusternamn
* Klustret region och Azure tillgänglighet zon
* Tillstånd för klustret, inklusive information om den senaste tillståndsändringen
* Typ och antal HDInsight-instanser som angetts för master, kärnor och uppgiften noder

Du kan hämta de flesta av informationen på den översta nivån med Azure-portalen.  Du kan också använda Azure CLI för att hämta information om HDInsight-cluster(s):

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Du kan också använda PowerShell för att visa denna information.  Mer information finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Förstå de arbetsbelastningar som körs på ditt kluster

Det är viktigt att förstå de typer av arbetsbelastningar som körs på HDInsight-cluster(s) till lämplig loggning strategier för varje typ av design.

* Är arbetsbelastningen försök (till exempel utveckling eller testning) eller hög kvalitet?
* Hur ofta hög kvalitet arbetsbelastningar normalt kör?
* Är några av arbetsbelastningarna resurskrävande och/eller långvariga?
* Använder någon av arbetsbelastningarna som en komplex uppsättning Hadoop-tjänster som flera typer av loggar produceras?
* Arbetsbelastningar har associerade regelverk körning övriga krav?

### <a name="example-log-retention-patterns-and-practices"></a>Exempel loggen kvarhållning mönster och metoder

* Överväg att bevara data härkomst spårning genom att lägga till en identifierare för varje loggpost eller med andra metoder. På så sätt kan du spåra tillbaka den ursprungliga källan och åtgärden och följa data via varje steg för att förstå dess konsekventa och giltiga.

* Överväg hur du kan samla in loggar från klustret eller från flera kluster och sortera dem för granskning, övervakning, planerings- och aviseringar. Du kan använda en anpassad lösning för att komma åt och hämta loggfilerna på regelbundet och kombinera och analysera dem för att tillhandahålla en instrumentpanelsvy. Du kan också lägga till ytterligare funktioner för avisering för säkerhets- eller upptäcka fel. Du kan skapa dessa verktyg med hjälp av PowerShell, HDInsight SDK eller koden som ansluter till den Azure klassiska distributionsmodellen.

* Överväg om en övervakning lösning eller tjänsten skulle vara en användbar fördel. Microsoft System Center innehåller en [HDInsight hanteringspaketet](https://www.microsoft.com/download/details.aspx?id=42521). Du kan också använda verktyg från tredje part, till exempel Chukwa och Ganglia för att samla in och centralisera loggar. Många företag erbjuda tjänster till övervaka Hadoop-baserade stordatalösningar, till exempel Centerity, Compuware APM och Sematext SPM Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Steg 2: Hantera cluster service-versioner och visa skriptåtgärd loggar

En typisk HDInsight-kluster använder flera tjänster och program med öppen källa paket (till exempel Apache HBase, Apache Spark och så vidare). För vissa arbetsbelastningar, till exempel bioinformatik, kan du behöva behålla service configuration logghistorik förutom körningsloggar för jobbet.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa inställningar för klustrets med Ambari UI

Apache Ambari förenklar hanteringen, konfiguration och övervakning av ett HDInsight-kluster genom att tillhandahålla en webbplats användargränssnitt och en REST-API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj den **Klusterinstrumentpanel** rutan på Azure HDInsight portalsidan att öppna den **' Klusterinstrumentpaneler** länk sidan.  Välj sedan den **instrumentpanelen för HDInsight-klustret** fönstret för att öppna Ambari UI.  Du ombeds ange dina inloggningsuppgifter för klustret.

Om du vill öppna en lista över servicevyer, Välj den **Ambari Views** rutan på sidan för Azure portal för HDInsight.  Den här listan varierar beroende på vilka bibliotek som du har installerat.  Du kan till exempel se YARN Queue Manager, Hive och Tez.  Välj alla service-länk för att visa konfigurations- och tjänstinformation.  Ambari UI **stacken och Version** sidan innehåller information om kluster-tjänsternas konfiguration och versionshistorik för tjänsten. Om du vill navigera till den här delen av Ambari UI, Välj den **Admin** menyn och sedan **stackar och versioner**.  Välj den **versioner** fliken för att se versionsinformation för tjänsten.

![Stacken och versioner](./media/hdinsight-log-management/stack-versions.png)

Du kan hämta konfigurationen för alla (eller alla) tjänster som körs på en viss värd (eller nod) i klustret med Ambari UI.  Välj den **värdar** -menyn och klicka sedan på länken för värden av intresse. Den värden på sidan Välj den **värden åtgärder** knappen och sedan **hämtar klienten konfigurationerna**. 

![Värden konfigurationerna för klienten](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Visa skript åtgärd loggar

HDInsight [skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md) köra skript i ett kluster, antingen manuellt eller när angetts. Skriptåtgärder kan till exempel användas för att installera ytterligare programvara på klustret eller ändra konfigurationsinställningarna från standardvärdena. Skriptet åtgärd loggar ger kunskaper om fel som uppstod under installationen av klustret och konfigurationsinställningar för ändringar som kan påverka klustret prestanda och tillgänglighet.  Om du vill se status för en skriptåtgärd, Välj den **ops** knappen på Ambari UI eller åtkomst status loggar in standardkontot för lagring. Storage-loggar finns på `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Steg 3: Hantera loggfiler för kluster-jobbet körning

Nästa steg är Granska loggfilerna jobbet körningen för olika tjänster.  Tjänster kan inbegripa Apache HBase, Apache Spark och många andra. Ett Hadoop-kluster genererar ett stort antal detaljerade loggarna så att fastställa vilka loggar är användbara (och som inte är) kan ta lång tid.  Förstå loggning system är viktigt för riktade hantering av loggfiler.  Följande är en loggfil som exempel.

![Exempel på HDInsight loggfil](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Åtkomst till Hadoop-loggfiler

HDInsight lagrar loggfilerna både i filsystemet för klustret och i Azure-lagring. Du kan undersöka loggfiler i klustret genom att öppna en SSH-anslutning till klustret och surfning filsystemet eller med hjälp av Hadoop YARN Status portal på fjärranslutna huvudnod-servern. Du kan undersöka loggfiler i Azure storage med hjälp av någon av de verktyg som kan komma åt och hämta data från Azure storage. Exempel är AZCopy, CloudXplorer och Visual Studio Server Explorer. Du kan också använda PowerShell och Azure Storage-klientbibliotek eller Azure .NET SDK, för att komma åt data i Azure blob storage.

Hadoop kör jobb som arbete *uppgift försök* på olika noder i klustret. HDInsight kan initiera spekulativ uppgiften försök avsluta andra uppgiften försök som inte utför du först. Detta genererar betydande aktivitet som loggas av domänkontrollant, stderr och syslog log-filer på direkt. Dessutom kan flera försök att aktiviteten körs samtidigt, men en loggfil kan bara visa resultat linjärt.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight-loggar som skrivs till Azure Blob storage

HDInsight-kluster är konfigurerade för att skriva uppgiften loggar till ett Azure Blob storage-konto för alla jobb som skickas med hjälp av Azure PowerShell-cmdlets eller jobbet .NET API: er.  Om du skickar jobb via SSH till klustret lagras loggningsinformation körning i Azure-tabeller som beskrivs i föregående avsnitt.

Installerat services förutom core loggfiler som genererats av HDInsight, såsom YARN också skapa jobbet körning loggfiler.  Antalet och typen av loggfiler beror på de tjänster som installeras.  Vanliga tjänster är Apache HBase, Apache Spark och så vidare.  Undersök jobbet körning loggfilerna för varje tjänst att förstå den övergripande loggningen filer tillgänglig på klustret.  Varje tjänst har sin egen unika metoder för loggning och platser för att lagra loggfiler.  Exempelvis beskrivs information för att komma åt i de vanligaste loggfilerna (från YARN) i följande avsnitt.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight-loggar som genereras av YARN

YARN aggregerar loggar över alla behållare på en arbetsnod och lagrar dessa loggar som en sammansatt loggfil per arbetsnoden. Loggen är lagrade på filsystemet när ett program har slutförts. Programmet kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod aggregeras alltid till en fil. Det finns endast en logg per arbetsnoden som används av ditt program. Aggregering av loggen är aktiverad som standard på HDInsight-kluster av version 3.0 och senare. Sammanställda loggarna finns i standardlagring för klustret.

```
    /app-logs/<user>/logs/<applicationId>
```

Sammanställda loggarna är inte direkt läsbar, eftersom de skrivs i TFile binärformat indexeras av behållare. Använda YARN ResourceManager loggarna eller CLI-verktygen för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

#### <a name="yarn-cli-tools"></a>YARN CLI-verktygen

Om du vill använda YARN CLI-verktygen måste du först ansluta till HDInsight-klustret via SSH. Ange den `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, och `<worker-node-address>` information när du kör kommandona. Du kan visa loggarna som oformaterad text med något av följande kommandon:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager-Användargränssnittet

YARN ResourceManager-Användargränssnittet körs på klustrets huvudnod och nås via Ambari-webbgränssnittet. Använd följande steg om du vill visa YARN-loggar:

1. I en webbläsare, navigerar du till `https://CLUSTERNAME.azurehdinsight.net`. Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.
2. Välj YARN i listan över tjänster till vänster.
3. Välj en av klusternoderna head snabblänkar-listrutan och välj sedan **ResourceManager loggar**. Visas en lista med länkar till YARN-loggar.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Steg 4: Prognos loggen volym lagringsstorlekar och kostnader

När du har slutfört föregående steg, har du förstå vilka typer och mängder loggfiler som ger upphov till HDInsight-cluster(s).

Därefter analysera volymen av loggdata i lagringsplatser för nyckel logg över en tidsperiod. Du kan till exempel analysera volymen och tillväxt över 30-60-90 dagarna.  Registrera den här informationen i ett kalkylblad eller använda andra verktyg som Visual Studio, Azure Lagringsutforskaren eller Power Query för Excel. Mer information finns i [analysera HDInsight loggar](hdinsight-debug-jobs.md).  

Nu har du tillräckligt med information för att skapa en strategi för logg för viktiga loggarna.  Använd kalkylbladet (eller verktyget för) för att göra prognoser för båda loggen storlekstillväxt och logga Azure service lagringskostnader framöver.  Överväg också loggen kvarhållning kraven för en uppsättning loggar som du undersöker.  Du kan nu reforecast framtida loggen lagringskostnader, efter att fastställa vilka loggfiler kan tas bort (om sådan finns) och som loggar ska bevaras och arkiveras till billigare Azure-lagring.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Steg 5: Bestäm loggen Arkiv principer och processer

När du har bestämt vilka loggfiler kan tas bort kan du justera parametrarna för loggning på många Hadoop-tjänster för att ta bort loggfilerna automatiskt efter en angiven tidsperiod.

Du kan använda en lägre priser loggfil arkivering tillvägagångssätt för vissa loggfiler. Du kan utforska den här metoden använder Azure portal för Azure Resource Manager aktivitetsloggar.  Konfigurera arkivering av ARM-loggarna genom att välja den **aktivitetsloggen**' länken i Azure-portalen för din HDInsight-instans.  Överst på sidan för aktivitetsloggen sökning, Välj den **exportera** menyalternativet för att öppna den **Export aktivitetsloggen** fönstret.  Fyll i prenumerationen, region, om du vill exportera till ett lagringskonto och hur många dagar till Behåll loggarna. På samma rutan kan ange du också om du vill exportera till en händelsehubb. 

![Exportera loggfiler](./media/hdinsight-log-management/archive.png)

Du kan också skript loggen arkivering med PowerShell.  Ett exempel PowerShell-skript finns [Arkiv Azure Automation loggar till Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Åtkomst till Azure storage-mätvärden

Azure-lagring kan konfigureras att loggen lagringsåtgärder och åtkomst. Du kan använda dessa mycket detaljerade loggar för kapacitet övervakning och planering och granskning begäranden till lagring. Loggade informationen innehåller latens information så att du kan övervaka och finjustera prestanda för dina lösningar.
Du kan använda .NET SDK för Hadoop för att undersöka loggfiler som genererats för Azure-lagring som innehåller data för ett HDInsight-kluster.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Styra storleken och antalet säkerhetskopiering index för gamla loggfiler

Ange följande egenskaper för att kontrollera storlek och antal loggfiler som lagras i `RollingFileAppender`:

* `maxFileSize` är kritiska storleken på filen som filen återställs. Standardvärdet är 10 MB.
* `maxBackupIndex` Anger hur många säkerhetskopior som ska skapas standardvärdet 1.

### <a name="other-log-management-techniques"></a>Andra tekniker för hantering av logg

För att undvika diskutrymme tar slut kan du använda vissa OS-verktyg som `logrotate` att hantera hantering av loggfiler. Du kan konfigurera `logrotate` dagligen måste komprimera logga filer och ta bort gamla. Din metod beror på dina krav som hur lång tid för att behålla loggfilerna på lokala noder. 

Du kan också kontrollera om felsökningsloggning är aktiverat för en eller flera tjänster som ökar utdata loggfilens storlek. 

Du kan skapa ett dataflöde, till exempel vill föra in alla loggposter i Solr för att samla in loggar från alla noder till en central plats.

## <a name="next-steps"></a>Nästa steg

* [Övervakning och loggning för HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Åtkomst YARN programloggen på Linux-baserat HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Så här kontrollerar du storleken för loggfiler för olika Hadoop-komponenter](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
