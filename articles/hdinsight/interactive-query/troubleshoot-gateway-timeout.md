---
title: Undantag när du kör frågor från Apache Ambari Hive View i Azure HDInsight
description: Felsökningssteg när du kör Apache Hive-frågor via Apache Ambari Hive View i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895038"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Undantag när du kör frågor från Apache Ambari Hive View i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör en Apache Hive-fråga från Apache Ambari Hive View visas följande felmeddelande då och då:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Orsak

En timeout för gateway.

Timeout-värdet för gateway är 2 minuter. Frågor från Ambari Hive View `/hive2` skickas till slutpunkten via gatewayen. När frågan har kompilerats och accepterats returnerar HiveServer en `queryid`. Klienter behåller sedan avsökning för frågans status. Om HiveServer inte returnerar ett HTTP-svar inom 2 minuter genererar HDI Gateway ett timeout-fel på 502.3 Gateway till anroparen. Felen kan inträffa när frågan skickas för bearbetning (mer troligt) och även i get status-anropet (mindre troligt). Användarna kunde se någon av dem.

Den http hanterare tråd är tänkt att vara `queryid`snabb: förbereda jobbet och returnera en . På grund av flera orsaker kan dock alla hanterartrådar vara upptagna, vilket resulterar i tidsutgångar för nya frågor och få statusanrop.

### <a name="responsibilities-of-the-http-handler-thread"></a>Ansvar för HTTP-hanterare tråden

När klienten skickar en fråga till HiveServer gör den följande i förgrundstråden:

* Tolka begäran, gör semantisk verifiering
* Hämta lås
* Metastore-sökning om det behövs
* Kompilera frågan (DDL eller DML)
* Förbereda en frågeplan
* Utför auktorisering (Kör alla tillämpliga rangerprinciper i säkra kluster)

## <a name="resolution"></a>Lösning

Några allmänna rekommendationer till dig för att förbättra situationen:

* Om du använder en extern hive-metabutik kontrollerar du DB-måtten och kontrollerar att databasen inte är överbelastad. Överväg att skala metabutiksdatabaslagret.

* Kontrollera att parallell ops är aktiverat (detta gör att HTTP-hanterare trådar att köras parallellt). Om du vill verifiera värdet startar du [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) och navigerar till **Hive** > **Configs** > **Advanced** > Custom**hive-site**. Värdet för `hive.server2.parallel.ops.in.session` ska `true`vara .

* Kontrollera att klustrets VM SKU inte är för liten för belastningen. Överväg att dela upp arbetet mellan flera kluster. Mer information finns i [Välj klustertyp](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Om Ranger är installerat i klustret kontrollerar du om det finns för många Ranger-principer som måste utvärderas för varje fråga. Leta efter dubblett- eller onödiga principer.

* Kontrollera **värdet för HiveServer2 Heap-storlek** från Ambari. Navigera till**Optimering** > av inställningar för >  >  **Hive-konfiguration**.**Configs****Settings** Kontrollera att värdet är större än 10 GB. Justera efter behov för att optimera prestanda.

* Se till att Hive-frågan är väl inställd. Mer information finns i [Optimera Apache Hive-frågor i Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
