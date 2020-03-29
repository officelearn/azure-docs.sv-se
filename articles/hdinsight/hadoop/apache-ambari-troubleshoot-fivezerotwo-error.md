---
title: Apache Ambari UI 502-fel i Azure HDInsight
description: Apache Ambari UI 502-fel när du försöker komma åt ditt Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895762"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: Apache Ambari UI 502 fel i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du försöker komma åt Apache Ambari-användargränssnittet för ditt HDInsight-kluster får du ett meddelande som liknar: "502 - Webbservern fick ett ogiltigt svar när du agerade som en gateway eller proxyserver."

## <a name="cause"></a>Orsak

I allmänhet innebär HTTP 502-statuskoden att Ambari-servern inte körs korrekt på den aktiva huvudnoden. Det finns några möjliga grundorsaker.

## <a name="resolution"></a>Lösning

I de flesta fall, för att minska problemet, kan du starta om den aktiva headnode. Eller välj en större VM-storlek för din headnode.

### <a name="ambari-server-failed-to-start"></a>Ambari-servern kunde inte starta

Du kan kontrollera ambari-server loggar för att ta reda på varför Ambari servern inte kunde starta. En vanlig orsak är databasen konsekvenskontroll fel. Du kan ta reda på `/var/log/ambari-server/ambari-server-check-database.log`detta i den här loggfilen: .

Om du har gjort några ändringar i klusternoden ångrar du dem. Använd alltid Ambari UI för att ändra eventuella Hadoop/Spark-relaterade konfigurationer.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari-server tar 100% CPU-användning

I sällsynta situationer har vi sett ambari-server processen har nära 100% CPU-användning hela tiden. Som en lindring kan du ssh till den aktiva headnode, och döda Ambari serverprocessen och starta den igen.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari server dödad av oom-killer

I vissa fall tar din headnode på minne, och Linux oom-killer börjar plocka processer för att döda. Du kan verifiera den här situationen genom att söka i AmbariServer-process-ID: t, som inte bör hittas. Titta sedan `/var/log/syslog`på din , och leta efter något liknande:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Identifiera sedan vilka processer som tar minnen och försöker ytterligare grundorsak.

### <a name="other-issues-with-ambari-server"></a>Andra problem med Ambari server

Sällan Ambari servern inte kan hantera den inkommande begäran, kan du hitta mer info genom att titta på ambari-server loggar för eventuella fel. Ett sådant fall är ett fel som detta:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
