---
title: Uppgradera HDInsight-kluster till en nyare version – Azure
description: Lär dig hur du uppgraderar HDInsight-kluster till en nyare version.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: v-yiso
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 04/04/2017
ms.date: 02/04/2019
ms.openlocfilehash: b5048266fe17bc16fba8228f7cc17d0ee9f3bc0b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763986"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uppgradera HDInsight-kluster till en nyare version
Om du vill dra nytta av de senaste funktionerna för HDInsight, rekommenderar vi att HDInsight-kluster uppgraderas till den senaste versionen. Följ den nedan riktlinjer för att uppgradera din HDInsight-kluster versioner.

> [!NOTE]
> Information om vilka versioner av HDInsight finns i [HDInsight komponenten versioner](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Uppgifter
Arbetsflöde för att uppgradera HDInsight-kluster är som följer.

![Uppgradera arbetsflödesdiagram](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Läs avsnitten i det här dokumentet för att förstå ändringar som kan krävas när du uppgraderar din HDInsight-kluster.
2. Skapa ett kluster som en test/kvalitet assurance miljö. Mer information om hur du skapar ett kluster finns i [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiera befintliga jobb, datakällor och mottagare till den nya miljön. Se [kopiera Data till testmiljö](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) för mer information.
4. Utföra valideringstestning för att se till att dina jobb fungerar som förväntat på det nya klustret.


När du har kontrollerat att allt fungerar som förväntat kan du schemalägga avbrottstiden för migreringen. Under den här avbrott, gör du följande åtgärder:

1.  Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna. Exempel: Om du har data som lagras direkt på en huvudnod.
2.  Ta bort det befintliga klustret.
3.  Skapa ett kluster i samma undernät för virtuellt nätverk med senaste (eller stöds) HDI-version med hjälp av samma standard datalager som används i föregående kluster. På så sätt kan det nya klustret att fortsätta arbeta mot dina befintliga produktionsdata.
4.  Importera alla tillfälliga säkerhetskopierade data.
5.  Starta jobb/fortsätta att bearbeta med hjälp av det nya klustret.

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Ansluta till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera en Linux-baserade kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)

