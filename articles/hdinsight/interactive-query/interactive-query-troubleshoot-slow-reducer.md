---
title: Minskning är långsam i Azure HDInsight
description: Minskning är långsam i Azure HDInsight från möjlig data skevning
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895171"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: en minskning är långsam i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör en fråga, till exempel `insert into table1 partition(a,b) select a,b,c from table2` en frågeplan, startar en mängd olika Minskare, men data från varje partition går till en enda minsknings punkt. Detta gör att frågan blir så låg som den tid det tar för den största partitionens minskning.

## <a name="cause"></a>Orsak

Öppna [Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) och kontrol lera värdet för set `hive.optimize.sort.dynamic.partition` .

Värdet för den här variabeln är avsett att ställas in på True/false baserat på data typen.

Om partitionerna i inmatnings tabellen är mindre (t. ex. mindre än 10), och så är antalet utgående partitioner, och variabeln är inställd på `true` , så gör det att data globalt sorteras och skrivs med hjälp av en enda minskning per partition. Även om antalet tillgängliga minsknings bara enheter är större, kan det hända att ett fåtal dämpare slutar på grund av data skevningen och att maximal parallellitet inte kan uppnås. När det ändras till `false` kan mer än en minskning hantera en enda partition och flera mindre filer skrivs ut, vilket resulterar i snabbare infogning. Detta kan påverka ytterligare frågor på grund av förekomsten av mindre filer.

Värdet är `true` meningsfullt när antalet partitioner är större och data inte är sneda. I sådana fall skrivs resultatet av kart fasen ut så att varje partition hanteras av en enda minskning som resulterar i bättre efterföljande frågeresultat.

## <a name="resolution"></a>Lösning

1. Försök att partitionera om data för att normalisera till flera partitioner.

1. Om #1 inte är möjligt ställer du in värdet för config på false i Beeline-sessionen och försöker köra frågan igen. `set hive.optimize.sort.dynamic.partition=false`. Att ställa in värdet på false på en kluster nivå rekommenderas inte. Värdet för `true` är optimalt och anger parametern efter behov baserat på typen av data och frågor.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
