---
title: Migrera från Windows-baserade HDInsight till Linux-baserade HDInsight - Azure
description: Lär dig hur du migrerar från ett Windows-baserade HDInsight-kluster till ett Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: 49f55416cb9224736acd7b8ac5eac5b6c5ba5979
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766699"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrera från ett Windows-baserade HDInsight-kluster till ett Linux-baserade kluster

Det här dokumentet innehåller information om skillnaderna mellan HDInsight på Windows och Linux. Den ger även vägledning om hur du migrerar befintliga arbetsbelastningar till ett Linux-baserade kluster.

Medan Windows-baserade HDInsight ger ett enkelt sätt att använda Apache Hadoop i molnet, kan du behöva migrera till ett Linux-baserade kluster. Till exempel att dra nytta av Linux-baserade verktyg och tekniker som krävs för din lösning. Många saker i Hadoop-ekosystemet utvecklas på Linux-baserade system och kanske inte är tillgängliga för användning med Windows-baserade HDInsight. Många böcker, videor och andra utbildningsmaterial förutsätter att du använder en Linux-system när du arbetar med Hadoop.

> [!NOTE]  
> HDInsight-kluster använder långsiktigt stöd för Ubuntu (LTS) som operativsystem för noderna i klustret. Information om versionen av Ubuntu som är tillgängliga med HDInsight, tillsammans med annan information för versionshantering av komponenten finns [HDInsight komponenten versioner](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migration-tasks"></a>Migreringsåtgärder

Det allmänna arbetsflödet för migrering är som följer.

![Diagram över arbetsflödet för migrering](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Läs avsnitten i det här dokumentet för att förstå ändringar som kan krävas när du migrerar.

2. Skapa ett Linux-baserade kluster som en test/kvalitet assurance miljö. Mer information om hur du skapar ett Linux-baserade kluster finns i [skapa Linux-baserade kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Kopiera befintliga jobb, datakällor och mottagare till den nya miljön.

4. Utföra valideringstestning för att se till att dina jobb fungerar som förväntat på det nya klustret.

När du har kontrollerat att allt fungerar som förväntat kan du schemalägga avbrottstiden för migreringen. Under den här avbrott, utför du följande åtgärder:

1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna. Exempel: Om du har data som lagras direkt på en huvudnod.

2. Ta bort Windows-kluster.

3. Skapa ett Linux-baserade kluster med hjälp av samma standard datalager som används för det Windows-baserade klustret. Linux-baserat kluster kan fortsätta arbeta mot dina befintliga produktionsdata.

4. Importera alla tillfälliga säkerhetskopierade data.

5. Starta jobb/fortsätta att bearbeta med hjälp av det nya klustret.

### <a name="copy-data-to-the-test-environment"></a>Kopiera data till testmiljön

Det finns många sätt att kopiera data och jobb, men de två som beskrivs i det här avsnittet är de enklaste metoderna för att direkt flytta filer till ett testkluster.

#### <a name="hdfs-copy"></a>HDFS-kopia

Använd följande steg för att kopiera data från produktionskluster till test-klustret. De här stegen används den `hdfs dfs` verktyg som ingår i HDInsight.

1. Hitta storage-konto och standard behållare informationen om ditt befintliga kluster. I följande exempel använder PowerShell för att hämta den här informationen:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Följ stegen i de skapa Linux-baserade kluster i HDInsight-dokumentet för att skapa en testmiljö. Stoppa innan du skapar klustret och välj i stället **valfri konfiguration**.

3. Välj avsnittet för valfri konfiguration **länkade Storage-konton**.

4. Välj **Lägg till lagringsnyckel**, och när du uppmanas, väljer du det lagringskonto som returnerades av PowerShell-skriptet i steg 1. Klicka på **Välj** i varje avsnitt. Slutligen skapa klustret.

5. När klustret har skapats kan ansluta till den med hjälp av **SSH.** Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

6. Använd följande kommando för att kopiera filer från länkade storage-kontot till nya standardkontot för lagring från SSH-sessionen. Ersätt behållare med container-adressinformation som returneras av PowerShell. Ersätt __konto__ med namnet på kontot. Ersätt sökvägen till data med sökvägen till en datafil.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > Om katalogstrukturen som innehåller data inte finns i testmiljön, kan du skapa den med hjälp av följande kommando:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    Den `-p` växeln gör det möjligt att skapa alla kataloger i sökvägen.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Direct kopiering mellan blobar i Azure Storage

Alternativt kan du använda den `Start-AzStorageBlobCopy` Azure PowerShell-cmdlet för att kopiera BLOB-objekt mellan lagringskonton utanför HDInsight. Mer information finns i hur du hanterar Azure Blobs-avsnittet i med hjälp av Azure PowerShell med Azure Storage.

## <a name="client-side-technologies"></a>Klientsidan tekniker

Klientsidan tekniker som [Azure PowerShell-cmdlets](/powershell/azureps-cmdlets-docs), [klassiska Azure-CLI](../cli-install-nodejs.md), eller [.NET SDK för Apache Hadoop](https://hadoopsdk.codeplex.com/) fortsätter att fungera Linux-baserade kluster. Dessa tekniker är beroende av REST API: er som är gemensamma för båda typerna av klusteroperativsystem.

## <a name="server-side-technologies"></a>Tekniker för serversidan

I följande tabell innehåller råd om migrera server-komponenter som är specifika för Windows.

| Om du använder den här tekniken... | Gör den här åtgärden... |
| --- | --- |
| **PowerShell** (serverskript, inklusive skriptåtgärder användas när klustret skapas) |Omarbetning som Bash-skript. Skriptåtgärder, se [anpassa Linux-baserade HDInsight med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) och [Skriptåtgärdsutveckling för Linux-baserade HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Azure klassiskt CLI** (serverskript) |Den klassiska Azure-CLI är tillgängligt i Linux, kommer det inte förinstallerade på HDInsight-klustrets huvudnoder. Mer information om hur du installerar den klassiska Azure-CLI finns i [Kom igång med klassiska Azure-CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **.NET-komponenter** |.NET stöds på Linux-baserade HDInsight via [Mono](https://mono-project.com). Mer information finns i [migrera .NET-lösningar till Linux-baserade HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Win32-komponenter eller andra endast Windows-teknik** |Vägledning beror på vilken komponent eller teknik. Du kan hitta en version som är kompatibel med Linux. Om inte, måste du hitta en alternativ lösning eller skriva om den här komponenten. |

> [!IMPORTANT]  
> HDInsight-management SDK är inte helt kompatibla med Mono. Använd inte den som en del av lösningar som har distribuerats till HDInsight-klustret.

## <a name="cluster-creation"></a>Skapa kluster

Det här avsnittet innehåller information om skillnader i klustret har skapats.

### <a name="ssh-user"></a>SSH användare

Linux-baserade HDInsight-kluster används den **Secure Shell (SSH)** protokoll som ska ge fjärråtkomst till klusternoderna. Till skillnad från Remote Desktop för Windows-baserade kluster anger de flesta SSH-klienter inte ett grafiskt användargränssnitt. SSH-klienter ger i stället en kommandorad som gör att du kan köra kommandon på klustret. Vissa klienter (till exempel [MobaXterm](https://mobaxterm.mobatek.net/)) tillhandahåller en grafisk filläsare system förutom en fjärransluten kommandorad.

När du skapar klustret måste du ange en SSH-användare och antingen en **lösenord** eller **offentliga nyckelcertifikat** för autentisering.

Vi rekommenderar att du använder certifikat med offentlig nyckel, eftersom det är säkrare än att använda ett lösenord. Autentisering med datorcertifikat fungerar genom att generera ett signerat offentligt/privat nyckelpar och sedan att ange den offentliga nyckeln när du skapar klustret. När du ansluter till servern med SSH, innehåller den privata nyckeln på klienten autentisering för anslutningen.

Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

### <a name="cluster-customization"></a>Klusteranpassning

**Skriptåtgärder** används med Linux-baserade kluster måste skrivas i Bash-skript. Linux-baserade kluster kan använda skriptåtgärder under eller när klustret har skapats. Mer information finns i [anpassa Linux-baserade HDInsight med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) och [Skriptåtgärdsutveckling för Linux-baserade HDInsight](hdinsight-hadoop-script-actions-linux.md).

En annan anpassningsfunktion är **bootstrap**. Windows-kluster kan kan den här funktionen du ange platsen för ytterligare bibliotek för användning med Hive. När klustret har skapats, biblioteken blir automatiskt tillgängliga för användning med Hive-frågor utan att behöva använda `ADD JAR`.

Funktionen Bootstrap för Linux-baserade kluster tillhandahåller inte den här funktionen. Använd i stället skriptåtgärd som beskrivs i [lägga till Apache Hive-bibliotek när klustret skapas](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtuella nätverk

Windows-baserade HDInsight-kluster fungerar bara med klassiska virtuella nätverk, medan Linux-baserade HDInsight-kluster kräver Resource Manager virtuella nätverk. Om du har resurser i ett klassiskt virtuellt nätverk som Linux-HDInsight-klustret måste ansluta till [ansluta ett klassiskt virtuellt nätverk till ett virtuellt nätverk för Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Mer information om krav finns i den [utöka HDInsight-funktioner med hjälp av ett virtuellt nätverk](hdinsight-extend-hadoop-virtual-network.md) dokumentet.

## <a name="management-and-monitoring"></a>Hantering och övervakning

Många av web UIs använt med Windows-baserade HDInsight, till exempel jobbhistorik eller Yarn-Användargränssnittet är tillgängliga via Apache Ambari. Ambari Hive-vyn innehåller också ett sätt att köra Hive-frågor med hjälp av webbläsaren. Ambari-Webbgränssnittet är tillgänglig på Linux-baserade kluster på https://CLUSTERNAME.azurehdinsight.net.

Mer information om hur du arbetar med Ambari finns i följande dokument:

* [Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari-aviseringar

Ambari har en avisering system som kan be dig om potentiella problem med klustret. Aviseringar visas som röda eller gula poster i Ambari-Webbgränssnittet, men du kan även hämta dem via REST API.

> [!IMPORTANT]  
> Ambari aviseringar anger om det *kan* vara ett problem, inte att det finns *är* ett problem. Du kan till exempel får en avisering som att HiveServer2 inte kan nås, även om du normalt kommer åt den.
>
> Många aviseringar implementeras som intervallbaserad frågor mot en tjänst och förväntar sig ett svar inom en viss tidsram. Så aviseringen inte nödvändigtvis att tjänsten är igång, returnerade men det resultat inom den förväntade tidsramen.

## <a name="file-system-locations"></a>Sökvägar för system

Filsystem för Linux-kluster är uppbyggd annorlunda än Windows-baserade HDInsight-kluster. Använd följande tabell för att hitta vanliga filer.

| Jag behöver du hitta... | Det finns... |
| --- | --- |
| Konfiguration |`/etc`. Till exempel, `/etc/hadoop/conf/core-site.xml` |
| Loggfiler |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Det finns två kataloger finns här ett som är den aktuella HDP-versionen och `current`. Den `current` katalogen innehåller symboliska länkar till filer och kataloger finns i katalogen för version. Den `current` directory har angetts som ett bekvämt sätt att komma åt HDP filer sedan ändras versionsnumret som HDP versionen uppdateras. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

I allmänhet om du känner till namnet på filen kan använda du följande kommando från en SSH-session för att hitta sökvägen till filen:

    find / -name FILENAME 2>/dev/null

Du kan också använda jokertecken med filnamnet. Till exempel `find / -name *streaming*.jar 2>/dev/null` returnerar sökvägen i alla jar-filer som innehåller ordet streaming som en del av namnet på filen.

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive, Apache Pig och MapReduce

Pig och MapReduce arbetsbelastningar är liknande på Linux-baserade kluster. Linux-baserade HDInsight-kluster kan dock skapas med nyare versioner av Hadoop Hive och Pig. Dessa skillnader mellan versioner kan införa ändringar i hur din befintliga lösningar-funktion. Mer information om vilka versioner de komponenterna som ingår i HDInsight finns i [versionshantering för HDInsight](hdinsight-component-versioning.md).

Linux-baserade HDInsight ger inte skrivbord fjärrfunktioner. Du kan i stället använda SSH för att fjärransluta till klustrets huvudnoder. Mer information finns i följande dokument:

* [Använda Apache Hive med SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Använda Apache Pig med SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Använda MapReduce med SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> Om du använder en extern Hive metastore, bör du säkerhetskopiera metaarkiv innan du använder den med Linux-baserade HDInsight. Linux-baserade HDInsight är tillgängligt med nyare versioner av Hive, som kan ha inkompatibiliteter med metastores som skapats av tidigare versioner.

Följande diagram innehåller råd om hur du migrerar dina Hive-arbetsbelastningar.

| På Windows-baserade, jag använder... | På Linux-baserade... |
| --- | --- |
| **Hive Editor** |[Apache Hive-vyn i Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` Aktivera Tez |Apache Tez är standard-motorn för körning för Linux-baserade kluster så set-instruktionen är inte längre behövs. |
| C#-användardefinierade funktioner | Information om hur du verifierar C#-komponenter med Linux-baserat HDInsight finns i [migrera .NET-lösningar till Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-filer eller skript på den server som anropas som en del av ett Hive-jobb |Använd Bash-skript |
| `hive` kommando från fjärrskrivbord |Använd [Apache Hive Beeline](hadoop/apache-hadoop-use-hive-beeline.md) eller [Apache Hive från en SSH-session](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| På Windows-baserade, jag använder... | På Linux-baserade... |
| --- | --- |
| C#-användardefinierade funktioner | Information om hur du verifierar C#-komponenter med Linux-baserat HDInsight finns i [migrera .NET-lösningar till Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-filer eller skript på den server som anropas som en del av ett Pig-jobb |Använd Bash-skript |

### <a name="mapreduce"></a>MapReduce

| På Windows-baserade, jag använder... | På Linux-baserade... |
| --- | --- |
| C# mapper och reducer komponenter | Information om hur du verifierar C#-komponenter med Linux-baserat HDInsight finns i [migrera .NET-lösningar till Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-filer eller skript på den server som anropas som en del av ett Hive-jobb |Använd Bash-skript |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> Om du använder en extern Oozie metastore, bör du säkerhetskopiera metaarkiv innan du använder den med Linux-baserade HDInsight. Linux-baserade HDInsight är tillgängligt med nyare versioner av Oozie som kan ha inkompatibiliteter med metastores som skapats av tidigare versioner.

Oozie arbetsflöden kan shell-åtgärder. Shell-åtgärder använder standardgränssnittet för operativsystemet för att köra kommandon på kommandoraden. Om du har Oozie-arbetsflöden som förlitar sig på Windows-gränssnittet, måste du skriva arbetsflöden för att förlita dig på Linux shell-miljön (Bash). Läs mer om hur du använder shell åtgärder med Oozie [Oozie shell åtgärdstillägget](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Om du har ett arbetsflöde som använder ett C#-program kan du verifiera dessa program i en Linux-miljö. Mer information finns i [migrera .NET-lösningar till Linux-baserade HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| På Windows-baserade, jag använder... | På Linux-baserade... |
| --- | --- |
| Storm-instrumentpanelen |Storm-instrumentpanelen är inte tillgänglig. Se [distribuera och hantera Apache Storm-topologier på Linux-baserade HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) för sätt att skicka topologier |
| Storm UI |Storm-Användargränssnittet finns på https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio för att skapa, distribuera och hantera topologier i C# eller hybrid |Visual Studio kan användas för att skapa, distribuera och hantera C# (SCP.NET) eller hybridtopologier på Linux-baserade Storm på HDInsight. Det kan bara användas med kluster som skapas efter 2016/10/28. |

## <a name="apache-hbase"></a>Apache HBase

På Linux-baserade kluster znode överordnade för HBase är `/hbase-unsecure`. Ange ett värde i konfigurationen för alla klienter i Java-program som använder inbyggt HBase Java API.

Se [skapa ett Java-baserade Apache HBase-program](hbase/apache-hbase-build-java-maven-linux.md) för en exempel-klient som anger det här värdet.

## <a name="spark"></a>Spark

Spark-kluster var tillgängliga i Windows-kluster i förhandsversionen. Spark GA är endast tillgänglig med Linux-baserade kluster. Det finns inga migreringsvägen från en Windows-baserad Spark preview-kluster till ett versionen Linux-baserad Spark-kluster.

## <a name="known-issues"></a>Kända problem

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory anpassad .NET-aktiviteter

Azure Data Factory anpassad .NET-aktiviteter stöds inte för närvarande på Linux-baserade HDInsight-kluster. Du bör i stället använda någon av följande metoder för att implementera anpassade aktiviteter som en del av ADF-pipeline.

* Kör .NET-aktiviteter på Azure Batch-pool. Se använda Azure Batch länkad service-avsnittet av [Använd anpassade aktiviteter i en Azure Data Factory-pipeline](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementera aktiviteten som ett MapReduce-aktivitet. Mer information finns i [anropa MapReduce-program från Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Radbrytningar

I allmänhet använder radbrytningar på Windows-baserade system CRLF, medan Linux-baserade system använder LF. Du kan behöva ändra befintliga dataproducenter och konsumenter för att arbeta med LF.

Till exempel returnerar använder Azure PowerShell för att fråga HDInsight i ett Windows-baserade kluster data med CRLF. Samma fråga med en Linux-baserat kluster returnerar LF. Kontrollera om raden slutar orsakar problem med din lösning innan du migrerar till ett Linux-baserade kluster.

Använd alltid LF som rader som slutar för skript som körs på noderna i klustret. Om du använder CRLF, får du felmeddelanden när du kör skripten på ett Linux-baserade kluster.

Om skripten inte innehåller strängar med inbäddade CR-tecken, kan du massimportera ändringen radbrytningar med någon av följande metoder:

* **Innan du laddar upp till klustret**: Använd följande PowerShell-uttryck för att ändra radbrytningar från CRLF till LF innan du laddar upp skriptet till klustret.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **När du har överfört till klustret**: Använd följande kommando från en SSH-session för att Linux-baserat kluster för att ändra skriptet.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Använda SSH för att ansluta till HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera en Linux-baserade kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
