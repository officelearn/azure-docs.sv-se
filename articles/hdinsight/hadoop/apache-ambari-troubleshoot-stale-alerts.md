---
title: Apache Ambari inaktuella aviseringar i Azure HDInsight
description: Diskussion och analys av möjliga orsaker och lösningar för Apache Ambari inaktuella varningar i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539118"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari inaktuella aviseringar i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I Apache Ambari-användargränssnittet kan du se en avisering som denna:

![Apache Ambari inaktuella varningsexempel](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Orsak

Ambari agenter övervakar kontinuerligt hälsan hos många resurser. *Aviseringar* kan konfigureras för att meddela dig om specifika klusteregenskaper ligger inom förutbestämda tröskelvärden. När varje resurskontroll körs, om varningsvillkoret är uppfyllt, rapporterar Ambari-agenter statusen tillbaka till Ambari-servern och utlöser en avisering. Om en avisering inte kontrolleras enligt intervallet i dess varningsprofil utlöser servern en *Ambari Server Stale Alerts-avisering.*

Det finns olika orsaker till att en hälsokontroll kanske inte körs med det definierade intervallet:

* Värdarna är under tung användning (hög CPU-användning), så att Ambari-agenten inte kan få tillräckligt med systemresurser för att köra aviseringarna i tid.

* Klustret är upptagen med att köra många jobb eller tjänster under en period av tung belastning.

* Ett litet antal värdar i klustret är värd för många komponenter och krävs därför för att köra många aviseringar. Om antalet komponenter är stort kan aviseringsjobb missa sina schemalagda intervall.

## <a name="resolution"></a>Lösning

Prova följande metoder för att lösa problem med Ambari inaktuella aviseringar.

### <a name="increase-the-alert-interval-time"></a>Öka varningsintervalltiden

Du kan öka värdet för ett enskilt varningsintervall, baserat på klustrets svarstid och belastning:

1. Välj fliken **Aviseringar** i Apache Ambari-användargränssnittet.
1. Välj önskat aviseringsdefinitionsnamn.
1. Välj **Redigera**i definitionen .
1. Öka värdet **Kontrollera intervall** och välj sedan **Spara**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Öka varningsintervalltiden för Ambari-servervarningar

1. Välj fliken **Aviseringar** i Apache Ambari-användargränssnittet.
1. Välj **AMBARI Standard**i listrutan **Grupper** .
1. Välj **aviseringen Ambari-servervarningar.**
1. Välj **Redigera**i definitionen .
1. Öka värdet **för kontrollintervallet.**
1. Öka **värdet för multiplikator för intervallet** och välj sedan **Spara**.

### <a name="disable-and-reenable-the-alert"></a>Inaktivera och återaktivera aviseringen

Om du vill ignorera en inaktuell avisering inaktiverar du och kan sedan återaktivera den:

1. Välj fliken **Aviseringar** i Apache Ambari-användargränssnittet.
1. Välj önskat aviseringsdefinitionsnamn.
1. Välj **Aktiverad** längst till höger i användargränssnittet i definitionen.
1. I popup-fönstret **Bekräftelse** väljer du **Bekräfta inaktivera**.
1. Vänta några sekunder innan alla "instanser" som visas på sidan ska rensas.
1. Välj **Inaktiverad** längst till höger i användargränssnittet i definitionen.
1. Välj **Bekräfta aktivera**i popup-fönstret **Bekräftelse** .

### <a name="increase-the-alert-grace-period"></a>Öka respitperioden för avisering

Det finns en respitperiod innan en Ambari-agent rapporterar att en konfigurerad avisering missade schemat. Om aviseringen missade sin schemalagda tid men kördes inom respitperioden genereras inte den inaktuella aviseringen.

Standardvärdet `alert_grace_period` är 5 sekunder. Du kan konfigurera den här inställningen i /etc/ambari-agent/conf/ambari-agent.ini. För värdar där inaktuella aviseringar inträffar med jämna mellanrum kan du försöka öka värdet till 10. Starta sedan om Ambari-agenten.

## <a name="next-steps"></a>Nästa steg

Om ditt problem inte nämndes här eller om du inte kan lösa det besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter på [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med på Twitter. Det här är det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Den kopplar Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp skickar du en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Om du vill komma dit väljer du Hjälp (**?**) på portalmenyn eller öppnar **supportfönstret Hjälp +.** Mer information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). 

  Support för prenumerationshantering och fakturering ingår i din Microsoft Azure-prenumeration. Teknisk support är tillgänglig via [Azure Support Plans](https://azure.microsoft.com/support/plans/).
