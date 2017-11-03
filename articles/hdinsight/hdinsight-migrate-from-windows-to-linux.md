---
title: "Migrera från Windows-baserade HDInsight till Linux-baserat HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du migrerar från en Windows-baserade HDInsight-kluster till ett Linux-baserade HDInsight-kluster."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: f2695d4f15fe984cd02cba9ff66033b90d0a4dc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrera från ett Windows-baserade HDInsight-kluster till ett Linux-baserade kluster

Det här dokumentet innehåller information om skillnaderna mellan HDInsight på Windows och Linux. Den ger även vägledning om hur du migrerar befintliga arbetsbelastningar till ett Linux-baserade kluster.

När Windows-baserade HDInsight erbjuder ett enkelt sätt att använda Hadoop i molnet, kan du behöva migrera till ett Linux-baserade kluster. Till exempel för att dra nytta av Linux-baserat verktyg och tekniker som krävs för din lösning. Många saker i Hadoop-ekosystemet utvecklas på Linux-baserade system och får inte vara tillgängliga för användning med Windows-baserade HDInsight. Många böcker, videor och andra utbildningsmaterial förutsätter att du använder ett Linux-system när du arbetar med Hadoop.

> [!NOTE]
> HDInsight-kluster använder Ubuntu långsiktigt stöd (LTS) som operativsystem för noder i klustret. Information om versionen av Ubuntu tillgänglig med HDInsight, tillsammans med andra komponenten versionsinformation finns [HDInsight komponenten versioner](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Migreringsåtgärder

Det allmänna arbetsflödet för migrering är som följer.

![Diagram över arbetsflödet för migrering](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Läs avsnitten i det här dokumentet att förstå ändringar som kan krävas när du migrerar.

2. Skapa ett Linux-baserade kluster som en test/kvalitet försäkran miljö. Mer information om hur du skapar ett Linux-baserade kluster finns [skapa Linux-baserade kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Kopiera befintliga jobb, datakällor och sänkor till den nya miljön.

4. Utföra verifiering tester för att se till att dina jobb fungerar som förväntat på det nya klustret.

När du har kontrollerat att allt fungerar som förväntat, schemalägga avbrottstiden för migreringen. Under den här driftstopp, utför följande åtgärder:

1. Säkerhetskopiera alla tillfälligt data som lagras lokalt på klusternoderna. Till exempel om du har data som lagras direkt på en huvudnod.

2. Ta bort Windows-baserade kluster.

3. Skapa ett Linux-baserade kluster med hjälp av samma standard datalager som används av Windows-baserade kluster. Linux-baserade kluster kan fortsätta arbeta mot ditt befintliga produktionsdata.

4. Importera alla tillfälligt säkerhetskopierade data.

5. Starta jobb/fortsätta att bearbeta med det nya klustret.

### <a name="copy-data-to-the-test-environment"></a>Kopiera data till testmiljön

Det finns många sätt att kopiera data och jobb, men de två som beskrivs i det här avsnittet är de enklaste metoderna för att direkt flytta filer till ett testkluster.

#### <a name="hdfs-copy"></a>HDFS-kopia

Använd följande steg för att kopiera data från produktionskluster till test-cluster. Använd de här stegen på `hdfs dfs` verktyg som ingår i HDInsight.

1. Hitta storage-konto och standard behållaren informationen för ett befintligt kluster. I följande exempel använder PowerShell för att hämta den här informationen:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Följ stegen i Skapa Linux-baserade kluster i HDInsight-dokumentet för att skapa en testmiljö. Stoppa innan du skapar klustret och i stället väljer **valfri konfiguration**.

3. Konfigurationsavsnittet valfria Välj **länkade Lagringskonton**.

4. Välj **lägga till en nyckel för säkerhetslagring**, och när du uppmanas, Välj lagringskontot som returnerades av PowerShell-skriptet i steg 1. Klicka på **Välj** i varje avsnitt. Skapa slutligen klustringen.

5. När klustret har skapats kan ansluta till den med hjälp av **SSH.** Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

6. Använd följande kommando för att kopiera filer från länkade storage-konto till nya standardkontot för lagring från SSH-session. Ersätta BEHÅLLAREN med behållaren informationen som returneras av PowerShell. Ersätt __konto__ med namnet på kontot. Ersätt sökvägen till data med sökvägen till en fil.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Om katalogstrukturen som innehåller data inte finns i testmiljön, kan du skapa den med följande kommando:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    Den `-p` växel kan skapa alla kataloger i sökvägen.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Direkt kopiera mellan blobbar i Azure Storage

Du kanske också vill använda den `Start-AzureStorageBlobCopy` Azure PowerShell-cmdlet för att kopiera BLOB mellan lagringskonton utanför HDInsight. Mer information finns i Hantera Azure BLOB-avsnittet i med hjälp av Azure PowerShell med Azure Storage.

## <a name="client-side-technologies"></a>Tekniker på klientsidan

Klientsidans tekniker som [Azure PowerShell-cmdlets](/powershell/azureps-cmdlets-docs), [Azure CLI](../cli-install-nodejs.md), eller [.NET SDK för Hadoop](https://hadoopsdk.codeplex.com/) fortsätter att fungera Linux-baserade kluster. Dessa tekniker förlitar sig på REST API: er som är samma för både klustertyper OS.

## <a name="server-side-technologies"></a>Tekniker för serversidan

Följande tabell innehåller råd om migrera server-komponenter som är specifika för Windows.

| Om du använder den här tekniken... | Åtgärda... |
| --- | --- |
| **PowerShell** (-skript, inklusive skriptåtgärder användas när klustret skapas) |Skriv om som Bash-skript. Skriptåtgärder, se [anpassa Linux-baserade HDInsight med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) och [för Linux-baserade HDInsight utveckling av skriptåtgärder](hdinsight-hadoop-script-actions-linux.md). |
| **Azure CLI** (serversidan skript) |Azure CLI är tillgängliga på Linux, kommer det inte förinstallerat på HDInsight-klustrets huvudnoder. Mer information om hur du installerar Azure CLI finns [Kom igång med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **.NET-komponenter** |.NET stöds på Linux-baserat HDInsight via [Mono](https://mono-project.com). Mer information finns i [migrera .NET lösningar på Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Win32-komponenter eller andra endast Windows-teknik** |Vägledning beror på vilken komponent eller teknik. Du kanske kan hitta en version som är kompatibel med Linux. Om inte, måste du söka efter en annan lösning eller skriva om den här komponenten. |

> [!IMPORTANT]
> Hantering av HDInsight SDK är inte helt kompatibel med Mono. Använd inte det som en del av lösningar som distribueras till HDInsight-klustret.

## <a name="cluster-creation"></a>Skapa ett kluster

Det här avsnittet innehåller information om skillnaderna i klustret har skapats.

### <a name="ssh-user"></a>SSH användare

Linux-baserade HDInsight-kluster används den **SSH (Secure Shell)** protokoll för att ge fjärråtkomst till klusternoderna. Till skillnad från Remote Desktop för Windows-baserade kluster anger de flesta SSH-klienter inte ett grafiskt användargränssnitt. SSH-klienter innehåller en kommandorad som gör att du kan köra kommandon på klustret. Vissa klienter (exempelvis [MobaXterm](http://mobaxterm.mobatek.net/)) ger ett grafiskt Filhanteraren system förutom en fjärransluten kommandorad.

När klustret skapas måste du ange en SSH-användare och antingen en **lösenord** eller **offentliga nyckelcertifikat** för autentisering.

Du bör använda certifikat med offentlig nyckel, eftersom det är säkrare än att använda ett lösenord. Autentisering med datorcertifikat fungerar genom att generera ett signerat offentliga/privata nyckelpar och sedan tillhandahålla den offentliga nyckeln när du skapar klustret. När du ansluter till servern med SSH, ger den privata nyckeln på klienten autentisering för anslutningen.

Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

### <a name="cluster-customization"></a>Anpassning av kluster

**Script åtgärder** används med Linux-baserade kluster måste skrivas i Bash-skript. Linux-baserade kluster kan använda script actions under eller efter att klustret har skapats. Mer information finns i [anpassa Linux-baserade HDInsight med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) och [för Linux-baserade HDInsight utveckling av skriptåtgärder](hdinsight-hadoop-script-actions-linux.md).

En annan anpassningsfunktion är **bootstrap**. För Windows-kluster kan den här funktionen du ange platsen för ytterligare bibliotek för användning med Hive. När klustret skapas dessa bibliotek är automatiskt tillgängliga för användning med Hive-frågor utan att behöva använda `ADD JAR`.

Funktionen Bootstrap för Linux-baserade kluster ger inte den här funktionen. Använd i stället skriptåtgärd dokumenterade i [lägga till Hive-bibliotek när klustret skapas](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtuella nätverk

Windows-baserade HDInsight-kluster endast fungerar med klassiska virtuella nätverk, medan Linux-baserade HDInsight-kluster kräver Resource Manager virtuella nätverk. Om du har resurser i ett klassiskt virtuellt nätverk som Linux-HDInsight-klustret måste ansluta till Se [ansluta ett klassiskt virtuellt nätverk till ett nätverk för virtuella Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Mer information om konfigurationskraven finns i [utöka HDInsight funktioner med hjälp av ett virtuellt nätverk](hdinsight-extend-hadoop-virtual-network.md) dokumentet.

## <a name="management-and-monitoring"></a>Hantering och övervakning

Många av web användargränssnitt som du har använt med Windows-baserade HDInsight, till exempel jobbhistorik eller Yarn-Användargränssnittet är tillgängliga via Ambari. Dessutom kan Ambari Hive-vy du köra Hive-frågor med hjälp av webbläsaren. Ambari-Webbgränssnittet är tillgänglig på Linux-baserade kluster på https://CLUSTERNAME.azurehdinsight.net.

Mer information om hur du arbetar med Ambari finns i följande dokument:

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari aviseringar

Ambari har ett system för varning som anger du eventuella problem med klustret. Aviseringar visas som röd eller gul poster i Ambari-Webbgränssnittet, men du kan också hämta dem via REST API.

> [!IMPORTANT]
> Ambari-aviseringar anger om det *kan* vara ett problem, inte att det *är* problem. Exempelvis kan du få en varning att HiveServer2 inte kan nås, även om du har åtkomst till den normalt.
>
> Många aviseringar implementeras som intervallbaserad frågor mot en tjänst och förväntar sig ett svar inom en viss tidsperiod. Så varningen innebär inte nödvändigtvis att tjänsten är igång, returnerade men det resultat inom den förväntade tidsperiod.

## <a name="file-system-locations"></a>Sökvägar för system

Filsystem för Linux-kluster är placerade annorlunda än Windows-baserade HDInsight-kluster. Använd följande tabell för att hitta vanliga filer.

| Jag vill hitta... | Det finns... |
| --- | --- |
| Konfiguration |`/etc`. Till exempel, `/etc/hadoop/conf/core-site.xml` |
| Loggfiler |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Det finns två kataloger finns här, ett som är den aktuella versionen av HDP och `current`. Den `current` katalogen innehåller symboliska länkar till filer och kataloger finns i katalogen för version. Den `current` katalog som är ett bekvämt sätt att komma åt HDP filer eftersom ändras versionsnumret som HDP version är uppdaterad. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

I allmänhet om du känner till namnet på filen använda du följande kommando från en SSH-session för att hitta sökvägen till filen:

    find / -name FILENAME 2>/dev/null

Du kan också använda jokertecken med namnet. Till exempel `find / -name *streaming*.jar 2>/dev/null` returnerar sökvägen till jar-filer som innehåller ordet strömning som en del av namnet på filen.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig och MapReduce

Pig och MapReduce arbetsbelastningar är liknande på Linux-baserade kluster. Linux-baserade HDInsight-kluster kan dock skapas med nyare versioner av Hadoop Hive och Pig. Dessa skillnader mellan versioner införa ändringar i hur din befintliga lösningar-funktion. Mer information om vilka versioner av komponenter som ingår i HDInsight finns [HDInsight component-versioning](hdinsight-component-versioning.md).

Linux-baserade HDInsight ger inte remote desktop funktioner. Använd istället SSH för att fjärransluta till head klusternoderna. Mer information finns i följande dokument:

* [Använda Hive med SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Använda Pig med SSH](hdinsight-hadoop-use-pig-ssh.md)
* [Använda MapReduce med SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Om du använder en extern Hive metastore bör du säkerhetskopiera metastore innan du använder med Linux-baserade HDInsight. Linux-baserat HDInsight finns i nyare versioner av Hive, som kan ha inkompatibiliteter med metastores som skapats i tidigare versioner.

Följande diagram ger vägledning om hur du migrerar dina Hive-arbetsbelastningar.

| På Windows-baserade jag använda... | På Linux-baserade... |
| --- | --- |
| **Hive-redigeraren** |[Hive-vyn i Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`Så här aktiverar du Tez |Tez är standard Körningsmotor för Linux-baserade kluster så set-satsen är inte längre behövs. |
| C# användardefinierade funktioner | Mer information om verifiering C#-komponenter med Linux-baserat HDInsight finns [migrera .NET lösningar på Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-filer eller skript på den server som anropas som en del av ett Hive-jobb |Använd Bash-skript |
| `hive`kommando från fjärrskrivbord |Använd [Beeline](hdinsight-hadoop-use-hive-beeline.md) eller [Hive från en SSH-session](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| På Windows-baserade jag använda... | På Linux-baserade... |
| --- | --- |
| C# användardefinierade funktioner | Mer information om verifiering C#-komponenter med Linux-baserat HDInsight finns [migrera .NET lösningar på Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-filer eller skript på den server som anropas som en del av ett Pig-jobb |Använd Bash-skript |

### <a name="mapreduce"></a>MapReduce

| På Windows-baserade jag använda... | På Linux-baserade... |
| --- | --- |
| C# mapper och reducer komponenter | Mer information om verifiering C#-komponenter med Linux-baserat HDInsight finns [migrera .NET lösningar på Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-filer eller skript på den server som anropas som en del av ett Hive-jobb |Använd Bash-skript |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Om du använder en extern Oozie metastore måste säkerhetskopiera du metastore innan du använder med Linux-baserade HDInsight. Linux-baserat HDInsight finns i nyare versioner av Oozie som kan ha inkompatibiliteter med metastores som skapats i tidigare versioner.

Shell-åtgärder för att tillåta Oozie arbetsflöden. Shell-åtgärder använder standardgränssnittet för operativsystemet för att köra kommandon på kommandoraden. Om du har Oozie arbetsflöden som förlitar sig på Windows-gränssnittet, måste du skriva om arbetsflöden och förlitar sig på Linux shell-miljö (Bash). Läs mer om hur du använder shell åtgärder med Oozie [Oozie shell-tillägg för åtgärd](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Om du har ett arbetsflöde som använder en C#-program kan du verifiera dessa program i en Linux-miljö. Mer information finns i [migrera .NET lösningar på Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| På Windows-baserade jag använda... | På Linux-baserade... |
| --- | --- |
| Storm-instrumentpanelen |Storm-instrumentpanelen är inte tillgänglig. Se [distribuera och hantera Storm-topologier på Linux-baserade HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) för sätt att skicka topologier |
| Storm UI |Storm-Användargränssnittet är tillgänglig på https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio för att skapa, distribuera och hantera topologier för C# eller hybrid |Visual Studio kan användas för att skapa, distribuera och hantera C# (SCP.NET) eller hybridtopologier på Linux-baserade Storm på HDInsight. Det kan bara användas med kluster som skapas efter 2016/10/28. |

## <a name="hbase"></a>HBase

På Linux-baserade kluster znode överordnade för HBase är `/hbase-unsecure`. Ange ett värde i konfigurationen för alla Java-klienter program som använder interna HBase Java API.

Se [skapar ett Java-baserade HBase-program](hdinsight-hbase-build-java-maven.md) för en exempel-klient som anger det här värdet.

## <a name="spark"></a>Spark

Spark-kluster var tillgängliga på Windows-kluster under förhandsgranskningen. Spark GA är bara tillgänglig med Linux-baserade kluster. Det finns inga migreringsvägen från en förhandsversion Windows-baserad Spark-kluster till ett versionen Linux-baserade Spark-kluster.

## <a name="known-issues"></a>Kända problem

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory anpassade .NET-aktiviteter

Azure Data Factory anpassade .NET aktiviteter stöds inte på Linux-baserade HDInsight-kluster. Du bör i stället använda någon av följande metoder för att implementera anpassade aktiviteter som en del av din ADF-pipeline.

* Köra .NET-aktiviteter i Azure Batch-pool. Se Använd Azure Batch länkad tjänst-avsnittet i [använda anpassade aktiviteter i ett Azure Data Factory-pipelinen](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementera aktiviteten som en MapReduce activity. Mer information finns i [anropa MapReduce program från Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Radbrytningar

I allmänhet använder radbrytningar på Windows-datorer CRLF, medan Linux-baserade datorer använder LF. Du kan behöva ändra befintlig dataproducenter och konsumenter för att arbeta med LF.

Till exempel returnerar använda Azure PowerShell för att fråga HDInsight på Windows-baserade kluster data med CRLF. Samma fråga med ett Linux-baserade kluster returnerar LF. Kontrollera om raden avslutas orsakar problem med din lösning innan du migrerar till ett Linux-baserade kluster.

Använd alltid LF som rader som slutar för skript som körs på klusternoderna. Om du använder CRLF får du felmeddelanden när du kör skript på en Linux-baserade kluster.

Om skripten inte innehåller strängar med inbäddade CR tecken, kan du massimportera ändra radbrytningar genom att använda någon av följande metoder:

* **Innan du laddar upp till klustret**: använder du följande PowerShell-uttryck för att ändra radbrytningar CRLF till LF innan du laddar upp skriptet till klustret.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **När du överför till klustret**: använder du följande kommando från en SSH-session till Linux-baserade kluster för att ändra skriptet.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Nästa steg

* [Lär dig att skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Använda SSH för att ansluta till HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera en Linux-baserade kluster med Ambari](hdinsight-hadoop-manage-ambari.md)
