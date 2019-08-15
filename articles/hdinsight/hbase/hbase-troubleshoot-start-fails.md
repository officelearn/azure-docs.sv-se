---
title: Apache HBase Master inte att starta i Azure HDInsight
description: Apache HBase Master (HMaster) kan inte startas i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935411"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) kan inte startas i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: Atomiska namnbytes försök

### <a name="issue"></a>Problem

Oväntade filer har identifierats under start processen.

### <a name="cause"></a>Orsak

Under start processen utför HMaster många initierings steg, inklusive att flytta data från scratch-mappen (. tmp) till data-mappen. HMaster tittar också på WALs-mappen (Skriv loggar i förväg) för att se om det finns några servrar för död region. Under alla dessa situationer görs ett grundläggande `list` kommando i dessa mappar. Om det när som helst ser en oväntad fil i någon av dessa mappar, kommer den att utlösa ett undantag och därför inte starta.

### <a name="resolution"></a>Lösning

I så fall kan du kontrol lera i anrops stacken om du vill se vilken mapp som kan orsaka problem (t. ex. mappen WALs mapp eller TMP). Leta sedan upp problem filen via Cloud Explorer eller HDFS-kommandon. Problem filen är vanligt vis en `*-renamePending.json` fil (en journal fil som används för att implementera Atom namnbyte i WASB-drivrutinen). På grund av buggar i den här implementeringen kan sådana filer lämnas över i händelse av process krasch. Framtvinga borttagning av den här filen via Cloud Explorer. Dessutom kan det finnas en temporär fil av typen $ på den här platsen. Filen kan inte visas via Cloud Explorer och endast via HDFS `ls` -kommandot. Du kan ta bort den `hdfs dfs -rm //\$\$\$.\$\$\$` här filen genom att använda HDFS-kommandot.

När problem filen har tagits bort ska HMaster starta omedelbart.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: Ingen server adress visas

### <a name="issue"></a>Problem

HMaster log visar ett fel meddelande som liknar "ingen server adress visas i HBase: meta för region xxx."

### <a name="cause"></a>Orsak

HMaster kunde inte initieras efter omstart av HBase.

### <a name="resolution"></a>Lösning

1. Kör följande kommandon i HBase-gränssnittet (ändra de faktiska värdena efter vad som är tillämpligt):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Ta bort posten för HBase: Namespace som samma fel kan rapporteras vid skanning av HBase: namespace-tabellen.

1. Starta om Active HMaster från Ambari-ANVÄNDARGRÄNSSNITTET för att hämta HBase i körnings tillstånd.

1. Kör följande kommando på HBase-gränssnittet för att hämta alla offline-tabeller:

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: Java. io. IOException: Stängningsåtgärd

### <a name="issue"></a>Problem

HMaster tids gräns med allvarligt undantag som `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Orsak

Timeout är ett känt fel med HMaster. Allmänna start åtgärder för klustret kan ta lång tid. HMaster stängs av om inte namn områdes tabellen har tilldelats än. Långa start aktiviteter inträffar när det finns stora mängder data som inte har rensats och tids gränsen på fem minuter inte räcker.

### <a name="resolution"></a>Lösning

1. Kom åt Ambari-användargränssnittet, gå till HBase-> configs, `hbase-site.xml` i anpassad Lägg till följande inställning:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Starta om nödvändiga tjänster (huvudsakligen HMaster och eventuellt andra HBase-tjänster).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Scenario: Frekventa regionserver-omstarter

### <a name="issue"></a>Problem

Noder startar om regelbundet. Från regionserver-loggarna kan du se poster som liknar:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Orsak

Lång regionserver JVM GC Pause. Pausen gör att regionserver inte svarar och att det inte går att skicka hjärter till HMaster inom ZK sessions-timeout 40s. HMaster tror att regionserver är död och kommer att avbryta regionserver och starta om.

### <a name="resolution"></a>Lösning

Ändra tids gränsen för Zookeeper-sessionen, inte bara HBase, `zookeeper.session.timeout` men även Zookeeper Zoo. cfg- `maxSessionTimeout` inställningen måste ändras.

1. Åtkomst till Ambari-gränssnittet, gå till **HBase-> configs – > inställningar**i avsnittet timeouter, ändra värdet för Zookeeper session timeout.

1. Komma åt Ambari-gränssnittet, gå till **Zookeeper-> configs-> anpassad** Zoo. cfg, Lägg till/ändra följande inställning. Kontrol lera att värdet är detsamma som HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Starta om nödvändiga tjänster.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: Logg delnings problem

### <a name="issue"></a>Problem

HMasters kunde inte komma upp i ett HBase-kluster.

### <a name="cause"></a>Orsak

Felkonfigurerat HDFS och HBase-inställningar för ett sekundärt lagrings konto.

### <a name="resolution"></a>Lösning

Ange HBase. rootdir: wasb://@.blob.core.windows.net/hbase och starta om tjänsterna på Ambari.

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
