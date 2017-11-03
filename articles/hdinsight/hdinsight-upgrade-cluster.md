---
title: Uppgradera HDInsight-kluster till en nyare version-Azure | Microsoft Docs
description: "Lär dig hur du uppgraderar HDInsight-kluster till en nyare version."
services: hdinsight
documentationcenter: 
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: bhanupr
ms.openlocfilehash: fa2e37bd922690322ccc3d8f68128180d013b701
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uppgradera HDInsight-kluster till en nyare version
Om du vill dra nytta av de senaste funktionerna för HDInsight, rekommenderar vi att HDInsight-kluster uppgraderas till den senaste versionen. Följ den nedan riktlinjer för att uppgradera ditt HDInsight-kluster versioner.

> [!NOTE]
> HDInsight-kluster version 3.2 eller 3.3 snart datumet för tillbakadragandet. Information om HDInsight version som stöds finns [HDInsight komponenten versioner](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Uppgraderingsuppgifter
Arbetsflöde för att uppgradera HDInsight-kluster är som följer.

![Uppgradera arbetsflödesdiagram](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Läs avsnitten i det här dokumentet att förstå ändringar som kan krävas när du uppgraderar ditt HDInsight-kluster.
2. Skapa ett kluster som en test/kvalitet försäkran miljö. Mer information om hur du skapar ett kluster finns [Lär dig att skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiera befintliga jobb, datakällor och sänkor till den nya miljön. Se [kopiera Data till testmiljö](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) för mer information.
4. Utföra verifiering tester för att se till att dina jobb fungerar som förväntat på det nya klustret.


När du har kontrollerat att allt fungerar som förväntat, schemalägga avbrottstiden för migreringen. Under den här driftstörningen gör du följande åtgärder:

1.  Säkerhetskopiera alla tillfälligt data som lagras lokalt på klusternoderna. Till exempel om du har data som lagras direkt på en huvudnod.
2.  Ta bort det befintliga klustret.
3.  Skapa ett kluster i samma undernät för virtuellt nätverk med senaste (eller stöds) HDI-version med samma standard datalager som används för tidigare kluster. Detta gör det nya klustret kan fortsätta arbeta mot ditt befintliga produktionsdata.
4.  Importera alla tillfälligt säkerhetskopierade data.
5.  Starta jobb/fortsätta att bearbeta med det nya klustret.

## <a name="next-steps"></a>Nästa steg
* [Lär dig att skapa Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Ansluta till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera en Linux-baserade kluster med Ambari](hdinsight-hadoop-manage-ambari.md)

