---
title: Det går inte att skapa kluster med DomainNotFound-fel i Azure HDInsight
description: Fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76776240"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: det går inte att skapa kluster med DomainNotFound-fel i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att skapa ett HDI-kluster (Enterprise Security Package) med `DomainNotFound` fel meddelande.

## <a name="cause"></a>Orsak

Felaktiga DNS-inställningar.

## <a name="resolution"></a>Lösning

När domänanslutna kluster distribueras, skapar HDI ett internt användar namn och lösen ord i AAD DS (för varje kluster) och ansluter alla klusternoder till den här domänen. Domän anslutningen görs med hjälp av Samba-verktyg. Se till att följande krav är uppfyllda:

* Domän namnet måste matcha DNS.
* IP-adressen för domän kontrol Lanterna ska anges i DNS-inställningarna för det virtuella nätverk där klustret ska distribueras.
* Om det virtuella nätverket är peer-kopplat med det virtuella nätverket AAD DS måste det göras manuellt.
* Om du använder DNS-vidarebefordrare måste domän namnet lösas korrekt i det virtuella nätverket.
* Säkerhets principer (NSG: er) blockerar inte domän anslutningen.

### <a name="additional-debugging-steps"></a>Ytterligare fel söknings steg

* Distribuera en virtuell Windows-dator i samma undernät, domän Anslut datorn med ett användar namn och lösen ord (detta kan göras via kontroll panelens gränssnitt), eller

* Distribuera en virtuell Ubuntu-dator i samma undernät och domän ansluta till datorn
  * SSH till datorn
  * sudo su
  * Kör skriptet med användar namn och lösen ord
  * Skriptet kommer att pinga, skapa nödvändiga konfigurationsfiler och sedan domän. Om det lyckas är dina DNS-inställningar lämpliga.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
