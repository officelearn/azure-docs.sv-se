---
title: Apache Ambari UI 502-fel i Azure HDInsight
description: Apache Ambari UI 502-fel vid försök att komma åt ditt Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: d4bcb8475f822675d39ca8e542155779384eacf1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087832"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: Apache Ambari UI 502-fel i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du försöker komma åt Apache Ambari-ANVÄNDARGRÄNSSNITTET för ditt HDInsight-kluster får du ett meddelande som liknar: "502-webb servern tog emot ett ogiltigt svar när den fungerade som gateway eller proxyserver."

## <a name="cause"></a>Orsak

I allmänhet innebär HTTP 502-status koden att Ambari-servern inte körs korrekt på den aktiva huvudnoden. Det finns några möjliga rotor orsaker.

## <a name="resolution"></a>Lösning

I de flesta fall kan du starta om den aktiva huvudnoden för att undvika problemet. Eller Välj en större storlek för virtuella datorer för din huvudnoden.

### <a name="ambari-server-failed-to-start"></a>Det gick inte att starta Ambari-servern

Du kan kontrol lera Ambari-Server-loggarna för att ta reda på varför Ambari-servern inte kunde starta. En vanlig orsak är att konsekvens kontroll av databasen är fel. Du hittar detta i den här logg filen: `/var/log/ambari-server/ambari-server-check-database.log`.

Om du har gjort ändringar i klusternoden kan du ångra dem. Använd alltid Ambari-ANVÄNDARGRÄNSSNITTET för att ändra alla Hadoop/Spark-relaterade konfigurationer.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari server tar 100% processor användning

I sällsynta fall har vi sett att Ambari-Server-processen har nära 100% processor användning konstant. Som en minskning kan du utföra SSH till den aktiva huvudnoden och avsluta Ambari-servern och starta den igen.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari-servern har stoppats av OOM-Killer

I vissa fall tar huvudnoden slut på minne och Linux-OOM-Killer börjar att välja processer att avsluta. Du kan kontrol lera den här situationen genom att söka i AmbariServer process-ID: t, som inte ska hittas. Titta sedan på ditt `/var/log/syslog`och leta efter något som liknar detta:

```
Jul 27 15:29:30 hn0-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Identifiera sedan vilka processer som tar minnen och försök att ytterligare rotor saken.

### <a name="other-issues-with-ambari-server"></a>Andra problem med Ambari-servern

Sällan Ambari-servern kan inte hantera inkommande begäran. du hittar mer information genom att titta på Ambari-serverns loggar för alla fel. Ett sådant fall är ett fel som detta:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
