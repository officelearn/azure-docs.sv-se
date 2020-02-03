---
title: Apache Ambari inaktuella aviseringar i Azure HDInsight
description: Diskussioner och analys av möjliga orsaker och lösningar för inaktuella Apache Ambari-aviseringar i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722816"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari inaktuella aviseringar i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I Apache Ambari-ANVÄNDARGRÄNSSNITTET kan du se en avisering som liknar följande bild:

![Exempel på Apache Ambari-inaktuell avisering](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Orsak

Ambari-agenter utför ständigt hälso kontroller för att övervaka hälso tillståndet för många resurser. Varje avisering har kon figurer ATS för att köras vid fördefinierade tidsintervall. Efter att varje avisering har körts, rapporterar Ambari-agenterna tillbaka statusen till Ambari-servern. I det här läget om Ambari-servern upptäcker att någon av aviseringarna inte kördes inom rimlig tid, utlöser den en "Ambari Server-aviseringar". Det finns olika orsaker till varför en hälso kontroll kanske inte kan utföras enligt det definierade intervallet:

* Om värdarna är under tung belastning (hög processor) är det en risk att Ambari-agenten inte kunde få tillräckligt med system resurser för att kunna köra aviseringarna i rimlig tid.

* Klustret är upptaget med att köra många jobb/tjänster under hög belastning.

* Några värdar i klustret kan vara värdar för många komponenter och kan därför krävas för att köra många aviseringar. Om antalet komponenter är stort är det möjligt att aviserings jobben kan missa sina schemalagda intervall

## <a name="resolution"></a>Lösning

### <a name="increase-alert-interval-time"></a>Öka tid för aviserings intervall

Du kan välja att öka värdet för ett enskilt aviserings intervall baserat på svars tiden för klustret och dess belastning.

1. Välj fliken **aviseringar** i Apache AMBARI-användargränssnittet.
1. Välj önskad aviserings definitions namn.
1. Från definitionen väljer du **Redigera**.
1. Ändra värdet för **kontrol lera intervall** som du vill och välj sedan **Spara**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Öka tiden för aviserings intervall för Ambari Server-aviseringar

1. Välj fliken **aviseringar** i Apache AMBARI-användargränssnittet.
1. I list rutan **grupper** väljer du **AMBARI default**.
1. Välj avisering **Ambari Server-aviseringar**.
1. Från definitionen väljer du **Redigera**.
1. Ändra värdet för **kontrol lera intervall** som du vill.
1. Ändra värdet för **intervall multiplikatorn** efter behov och välj sedan **Spara**.

### <a name="disable-and-enable-the-alert"></a>Inaktivera och aktivera aviseringen

Du kan inaktivera och sedan aktivera aviseringen igen för att ta bort eventuella inaktuella aviseringar.

1. Välj fliken **aviseringar** i Apache AMBARI-användargränssnittet.
1. Välj önskad aviserings definitions namn.
1. Från definitionen väljer du **aktive rad** längst till höger.
1. Från **bekräftelse** -popup-fönstret väljer du **Bekräfta inaktive ring**.
1. Vänta några sekunder för alla varnings instanser som visas på sidan raderas.
1. Från definitionen väljer du **inaktive rad** längst till höger.
1. Välj **Bekräfta aktivering**i popup-fönstret för **bekräftelse** .

### <a name="increase-alert-grace-time"></a>Öka Grace-tid för avisering

Innan Ambari-agenten rapporterar att en konfigurerad avisering missade sitt schema, finns det en Grace-tid. Även om aviseringen missade sin schemalagda tid men utlöstes inom återställnings tiden för aviseringen, utlöses inte inaktuell avisering.

Standard `alert_grace_period` svärdet är 5 sekunder. Den här `alert_grace_period` inställningen kan konfigureras i `/etc/ambari-agent/conf/ambari-agent.ini`. Försök att öka till ett värde på 10 för de värdar från vilka inaktuella aviseringar utlöses med jämna mellanrum. Starta sedan om Ambari-agenten

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
