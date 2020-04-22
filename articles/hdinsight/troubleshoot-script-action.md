---
title: Felsöka skriptåtgärder i Azure HDInsight
description: Allmänna felsökningssteg för skriptåtgärder i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771981"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Felsöka skriptåtgärder i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="viewing-logs"></a>Visa loggar

Du kan använda webbgränssnittet Apache Ambari för att visa information som loggas av skriptåtgärder. Om skriptet misslyckas när klustret skapas finns loggar i standardkontot för klusterlagring. Det här avsnittet innehåller information om hur du hämtar loggarna med hjälp av båda dessa alternativ.

### <a name="apache-ambari-web-ui"></a>Apache Ambari webbgränssnitt

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Välj **ops-posten** högst upp på sidan. En lista visar aktuella och tidigare åtgärder som utförs i klustret via Ambari.

    ![Ambari webbgränssnittsfält med ops valt](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Leta reda på de poster som har **\_kört customscriptaction** i kolumnen **Operationer.** Dessa poster skapas när skriptåtgärderna körs.

    ![Åtgärder för apache ambari-skriptåtgärder](./media/troubleshoot-script-action/ambari-script-action.png)

    Om du vill visa UTDATA FÖR **STDOUT** och **STDERR** markerar du posten **run\customscriptaction** och detaljgranskar nedåt genom länkarna. Den här utdata genereras när skriptet körs och kan ha användbar information.

### <a name="default-storage-account"></a>Standardlagringskonto

Om klusterskapande misslyckas på grund av ett skriptfel sparas loggarna i klusterlagringskontot.

* Förvaringsloggarna finns `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`på .

    ![Åtgärdsloggar för skript](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Under den här katalogen ordnas loggarna separat för **headnode,** **arbetarnod**och **zookeeper-nod**. Se följande exempel:

    * **Headnode**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Arbetsnod:**`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper nod:**`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alla **stdout** och **stderr** av motsvarande värd laddas upp till lagringskontot. Det finns en **utdata-\*.txt** och **fel-\*.txt** för varje skriptåtgärd. **Filen output-*.txt** innehåller information om URI-filen för skriptet som kördes på värden. Följande text är ett exempel på denna information:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Det är möjligt att du upprepade gånger skapar ett skriptåtgärdskluster med samma namn. I så fall kan du skilja relevanta loggar baserat på **mappnamnet DATUM.** Mappstrukturen för ett kluster, **mycluster**, som skapats på olika datum, liknar till exempel följande loggposter:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Om du skapar ett skriptåtgärdskluster med samma namn samma dag kan du använda det unika prefixet för att identifiera relevanta loggfiler.

* Om du skapar ett kluster nära 12:00, midnatt, är det möjligt att loggfilerna sträcker sig över två dagar. I så fall visas två olika datummappar för samma kluster.

* Det kan ta upp till fem minuter att överföra loggfiler till standardbehållaren, särskilt för stora kluster. Så om du vill komma åt loggarna bör du inte omedelbart ta bort klustret om en skriptåtgärd misslyckas.

## <a name="ambari-watchdog"></a>Ambari vakthund

Ändra inte lösenordet för Ambari vakthund, hdinsightwatchdog, på din Linux-baserade HDInsight kluster. En lösenordsändring bryter möjligheten att köra nya skriptåtgärder i HDInsight-klustret.

## <a name="cant-import-name-blobservice"></a>Det går inte att importera namn BlobService

__Symptom__. Skriptåtgärden misslyckas. Text som liknar följande fel visas när du visar åtgärden i Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Orsak__. Det här felet uppstår om du uppgraderar Python Azure Storage-klienten som ingår i HDInsight-klustret. HDInsight förväntar sig Azure Storage-klient 0.20.0.

__Upplösning__. Lös det här felet genom att manuellt ansluta `ssh`till varje klusternod med hjälp av . Kör följande kommando för att installera om rätt lagringsklientversion:

```bash
sudo pip install azure-storage==0.20.0
```

Information om hur du ansluter till klustret med SSH finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historiken visar inte de skript som används när klustret skapades

Om klustret skapades före den 15 mars 2016 kanske du inte ser någon post i skriptåtgärdshistoriken. Om du ändrar storlek på klustret visas skripten i skriptåtgärdshistoriken.

Det finns två undantag:

* Klustret skapades före den 1 september 2015. Det här datumet är när skriptåtgärder introducerades. Alla kluster som skapats före det här datumet kunde inte ha använt skriptåtgärder för att skapa kluster.

* Du använde flera skriptåtgärder när klustret skapades. Du har också använt samma namn för flera skript eller samma namn, samma URI, men olika parametrar för flera skript. I dessa fall får du följande felmeddelande:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).