---
title: Apache HBase Master startar inte i Azure HDInsight
description: Apache HBase Master (HMaster) startar inte i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887214"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) startar inte i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: Atombytesfel

### <a name="issue"></a>Problem

Oväntade filer som identifierats under startprocessen.

### <a name="cause"></a>Orsak

Under startprocessen utför HMaster många initieringssteg, inklusive att flytta data från scratch-mapp (.tmp) till datamappen. HMaster tittar också på mappen write-ahead logs (WAL) för att se om det finns några servrar som inte svarar.

HMaster gör ett grundläggande listkommando på WAL-mapparna. Om HMaster vid något tillfälle ser en oväntad fil i någon av dessa mappar, genererar den ett undantag och startar inte.

### <a name="resolution"></a>Lösning

Kontrollera anropsstacken och försök att avgöra vilken mapp som kan orsaka problemet (det kan till exempel vara WAL-mappen eller TMP-mappen). Försök sedan att hitta problemfilen i Cloud Explorer eller med HDFS-kommandon. Vanligtvis är detta `*-renamePending.json` en fil. (Filen `*-renamePending.json` är en journalfil som används för att implementera den atomiska namnbytesåtgärden i WASB-drivrutinen. På grund av buggar i den här implementeringen kan dessa filer bli över efter att processen har krascher och så vidare.) Tvinga bort den här filen antingen i Cloud Explorer eller med hjälp av HDFS-kommandon.

Ibland kan det också finnas en `$$$.$$$` tillfällig fil som heter något liknande på den här platsen. Du måste använda `ls` KOMMANDOT HDFS för att se den här filen. Du kan inte se filen i Cloud Explorer. Om du vill ta bort `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`den här filen använder du kommandot HDFS .

När du har kört dessa kommandon bör HMaster starta omedelbart.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: Ingen serveradress listad

### <a name="issue"></a>Problem

Du kan se ett meddelande `hbase: meta` som anger att tabellen inte är online. Kör `hbck` kan `hbase: meta table replicaId 0 is not found on any region.` rapportera att i HMaster loggar, kan `No server address listed in hbase: meta for region hbase: backup <region name>`du se meddelandet: .  

### <a name="cause"></a>Orsak

HMaster kunde inte initiera efter omstart av HBase.

### <a name="resolution"></a>Lösning

1. I HBase-skalet anger du följande kommandon (ändra faktiska värden enligt vad som är tillämpligt):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Ta `hbase: namespace` bort posten. Den här posten kan vara samma fel `hbase: namespace` som rapporteras när tabellen genomsöks.

1. Starta om den aktiva HMaster från Ambari UI för att få upp HBase i körläge.

1. Kör följande kommando för att visa alla offlinetabeller i HBase-skalet:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: java.io.IOException: Timedout

### <a name="issue"></a>Problem

HMaster time out med dödligt undantag som liknar: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om du har många tabeller och regioner som inte har tömts när du startar om HMaster-tjänsterna. Time-out är en känd defekt med HMaster. Allmänna klusterstartuppgifter kan ta lång tid. HMaster stängs av om namnområdestabellen ännu inte har tilldelats. De långa startaktiviteterna inträffar där det finns stora mängder data som inte har flukt och en timeout på fem minuter inte är tillräcklig.

### <a name="resolution"></a>Lösning

1. Från Apache Ambari UI, gå till **HBase** > **Configs**. Lägg till `hbase-site.xml` följande inställning i den anpassade filen:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Starta om de tjänster som krävs (HMaster och eventuellt andra HBase-tjänster).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenario: Frekvent regionserver startar om

### <a name="issue"></a>Problem

Noder startas om med jämna mellanrum. Från regionserverloggarna kan du se poster som liknar:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Orsak

Lång `regionserver` JVM GC-paus. Pausen kommer `regionserver` att orsaka att inte svara och inte kunna skicka hjärtslag till HMaster inom zk session timeout 40s. HMaster kommer `regionserver` att tro är `regionserver` död och kommer att avbryta och starta om.

### <a name="resolution"></a>Lösning

Ändra timeout för Zookeeper-sessionen, inte bara `hbase-site` inställning `zookeeper.session.timeout` utan även Zookeeper-inställningen `zoo.cfg` `maxSessionTimeout` måste ändras.

1. Öppna Ambari UI, gå till **HBase -> Configs -> Inställningar**, i Timeouts avsnitt, ändra värdet för Zookeeper Session Timeout.

1. Öppna Ambari UI, gå till **Zookeeper -> Configs -> Custom** `zoo.cfg`, lägg till/ändra följande inställning. Kontrollera att värdet är detsamma `zookeeper.session.timeout`som HBase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Starta om nödvändiga tjänster.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: Loggdelningsfel

### <a name="issue"></a>Problem

HMasters kunde inte komma upp på ett HBase-kluster.

### <a name="cause"></a>Orsak

Felkonfigurerade HDFS- och HBase-inställningar för ett sekundärt lagringskonto.

### <a name="resolution"></a>Lösning

set hbase.rootdir: wasb://@.blob.core.windows.net/hbase och starta om tjänster på Ambari.

---

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
