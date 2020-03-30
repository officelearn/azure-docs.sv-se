---
title: Migrera klustret till en nyare version
titleSuffix: Azure HDInsight
description: Lär dig riktlinjer för att migrera ditt Azure HDInsight-kluster till en nyare version.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023981"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrera HDInsight-kluster till en nyare version

För att dra nytta av de senaste HDInsight-funktionerna rekommenderar vi att HDInsight-kluster regelbundet migreras till den senaste versionen. HDInsight stöder inte uppgraderingar på plats där ett befintligt kluster uppgraderas till en nyare komponentversion. Du måste skapa ett nytt kluster med önskad komponent- och plattformsversion och sedan migrera dina program för att använda det nya klustret. Följ nedanstående riktlinjer för att migrera dina HDInsight-klusterversioner.

> [!NOTE]  
> Information om versioner av HDInsight som stöds finns i [HDInsight-komponentversioner](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Migreringsuppgifter

Arbetsflödet för att uppgradera HDInsight Cluster är följande.
![Arbetsflödesdiagram för HDInsight-uppgradering](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Läs varje avsnitt i det här dokumentet för att förstå ändringar som kan krävas när du uppgraderar HDInsight-klustret.
2. Skapa ett kluster som en test-/kvalitetssäkringsmiljö. Mer information om hur du skapar ett kluster finns i [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiera befintliga jobb, datakällor och sänkor till den nya miljön.
4. Utför valideringstestning för att se till att dina jobb fungerar som förväntat i det nya klustret.

När du har verifierat att allt fungerar som förväntat schemalägger du driftstopp för migreringen. Under den här driftstoppet gör du följande åtgärder:

1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna. Om du till exempel har data som lagras direkt på en huvudnod.
1. [Ta bort det befintliga klustret](./hdinsight-delete-cluster.md).
1. Skapa ett kluster i samma VNET-undernät med den senaste (eller stöds) HDI-versionen med samma standarddatalager som det tidigare klustret använde. På så sätt kan det nya klustret fortsätta arbeta mot dina befintliga produktionsdata.
1. Importera alla tillfälliga data som du säkerhetskopierade.
1. Starta jobb/fortsätt bearbeta med det nya klustret.

## <a name="workload-specific-guidance"></a>Arbetsbelastningsspecifik vägledning

Följande dokument ger vägledning om hur du migrerar specifika arbetsbelastningar:

* [Migrera HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrera Kafka](./kafka/migrate-versions.md)
* [Migrera hive/interaktiv fråga](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Mer information om säkerhetskopiering och återställning av databaser finns i [Återställa en Azure SQL-databas med hjälp av automatiska säkerhetskopieringar av databaser](../sql-database/sql-database-recovery-using-backups.md).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Ansluta till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera ett Linux-baserat kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-viktig information](./hdinsight-version-release.md)
