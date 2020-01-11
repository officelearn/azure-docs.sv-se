---
title: Apache HBase Master inte att starta i Azure HDInsight
description: Apache HBase Master (HMaster) kan inte startas i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887214"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) kan inte startas i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: Atom namnbytes problem

### <a name="issue"></a>Problem

Oväntade filer har identifierats under start processen.

### <a name="cause"></a>Orsak

Under start processen utför HMaster många initierings steg, inklusive att flytta data från scratch-mappen (. tmp) till data-mappen. HMaster tittar också på mappen Write-Ahead-loggar (WAL) för att se om det finns några icke-tillgängliga region servrar.

HMaster har ett grundläggande List kommando i WAL-mapparna. När som helst ser HMaster en oväntad fil i någon av dessa mappar, utlöses ett undantag och startar inte.

### <a name="resolution"></a>Upplösning

Kontrol lera anrops stacken och försök att avgöra vilken mapp som kan orsaka problemet (till exempel kan det vara mappen WAL eller mappen. tmp). Försök sedan att hitta problem filen i Cloud Explorer eller med HDFS-kommandon. Detta är vanligt vis en `*-renamePending.json`-fil. (`*-renamePending.json`-filen är en journal fil som används för att implementera Atomic Rename-åtgärden i WASB-drivrutinen. På grund av buggar i den här implementeringen kan de här filerna lämnas över efter att process kraschar och så vidare.) Framtvinga-ta bort den här filen antingen i Cloud Explorer eller med HDFS-kommandon.

Ibland kan det också finnas en temporär fil som heter något som `$$$.$$$` på den här platsen. Du måste använda HDFS `ls` kommandot för att se den här filen. Du kan inte se filen i Cloud Explorer. Ta bort den här filen genom att använda HDFS-kommandot `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

När du har kört dessa kommandon ska HMaster starta omedelbart.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: ingen server adress visas

### <a name="issue"></a>Problem

Du kan se ett meddelande som anger att `hbase: meta`s tabellen inte är online. Om du kör `hbck` rapporter som `hbase: meta table replicaId 0 is not found on any region.` i HMaster-loggarna kan du se meddelandet: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Orsak

HMaster kunde inte initieras efter omstart av HBase.

### <a name="resolution"></a>Upplösning

1. I HBase-gränssnittet anger du följande kommandon (ändra de faktiska värdena efter vad som är tillämpligt):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Ta bort `hbase: namespace`s posten. Posten kan vara samma fel som rapporteras när `hbase: namespace`s tabellen genomsöks.

1. Starta om Active HMaster från Ambari-ANVÄNDARGRÄNSSNITTET för att hämta HBase i körnings tillstånd.

1. Kör följande kommando i HBase-gränssnittet för att öppna alla offline-tabeller:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: Java. io. IOException: stängningsåtgärd

### <a name="issue"></a>Problem

HMaster tids gräns med allvarligt undantag liknar: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om du har många tabeller och regioner som inte har tömts när du startar om dina HMaster-tjänster. Timeout är ett känt fel med HMaster. Allmänna start åtgärder för klustret kan ta lång tid. HMaster stängs av om inte namn områdes tabellen har tilldelats än. Långa start aktiviteter inträffar när det finns stora mängder data som inte har rensats och tids gränsen på fem minuter inte räcker.

### <a name="resolution"></a>Upplösning

1. I Apache Ambari-ANVÄNDARGRÄNSSNITTET går du till **HBase** > **configs**. I den anpassade `hbase-site.xml`-filen lägger du till följande inställning:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Starta om de tjänster som krävs (HMaster och eventuellt andra HBase-tjänster).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenario: frekventa omstarter av region Server

### <a name="issue"></a>Problem

Noder startar om regelbundet. Från region Server loggarna kan du se poster som liknar:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Orsak

Länge `regionserver` JVM GC Pause. Pausen gör att `regionserver` inte svarar och att det inte går att skicka hjärter till HMaster inom ZK-sessionens tids gräns 40s. HMaster kommer att tro att `regionserver` är död och kommer att avbryta `regionserver` och starta om.

### <a name="resolution"></a>Upplösning

Ändra tids gränsen för Zookeeper-sessionen, inte bara `hbase-site` inställningen `zookeeper.session.timeout` men även Zookeeper `zoo.cfg` inställningen `maxSessionTimeout` måste ändras.

1. Åtkomst till Ambari-gränssnittet, gå till **HBase-> configs – > inställningar**i avsnittet timeouter, ändra värdet för Zookeeper session timeout.

1. Komma åt Ambari-gränssnittet, gå till **Zookeeper-> configs – > anpassad** `zoo.cfg`, Lägg till/ändra följande inställning. Kontrol lera att värdet är detsamma som HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Starta om nödvändiga tjänster.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: logg delnings problem

### <a name="issue"></a>Problem

HMasters kunde inte komma upp i ett HBase-kluster.

### <a name="cause"></a>Orsak

Felkonfigurerat HDFS och HBase-inställningar för ett sekundärt lagrings konto.

### <a name="resolution"></a>Upplösning

Ange HBase. rootdir: wasb://@.blob.core.windows.net/hbase och starta om tjänster på Ambari.

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
