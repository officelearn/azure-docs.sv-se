---
title: Klusterskapande misslyckas med DomainNotFound-fel i Azure HDInsight
description: Felsökning av steg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776240"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: Klusterskapande misslyckas med DomainNotFound-fel i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Hdi Secure (Enterprise Security Package) `DomainNotFound` kluster skapande misslyckas med felmeddelande.

## <a name="cause"></a>Orsak

Felaktiga DNS-inställningar.

## <a name="resolution"></a>Lösning

När domänens anslutna kluster distribueras skapar HDI ett internt användarnamn och lösenord i AAD DS (för varje kluster) och ansluter alla klusternoder till den här domänen. Domänkopplingen sker med Samba-verktyg. Se till att följande förutsättningar är uppfyllda:

* Domännamnet ska matchas via DNS.
* IP-adressen för domänkontrollanterna bör anges i DNS-inställningarna för det virtuella nätverket där klustret distribueras.
* Om det virtuella nätverket är peered med det virtuella nätverket av AAD DS, då det måste göras manuellt.
* Om du använder DNS-vidarebefordrare måste domännamnet matchas korrekt i det virtuella nätverket.
* Säkerhetsprinciper (NSG) bör inte blockera domänkopplingen.

### <a name="additional-debugging-steps"></a>Ytterligare felsökningssteg

* Distribuera en virtuell dator för Windows i samma undernät, domän ansluta till datorn med ett användarnamn och lösenord (detta kan göras via kontrollpanelens användargränssnitt), eller

* Distribuera en virtuell ubuntu-dator i samma undernät och domän ansluta till datorn
  * SSH i maskinen
  * sudo su (sudo su)
  * Kör skriptet med användarnamn och lösenord
  * Skriptet kommer att pinga, skapa de nödvändiga konfigurationsfilerna och sedan domänen. Om det lyckas är DNS-inställningarna bra.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
