---
title: Uppgradera HDInsight-kluster till en nyare version – Azure
description: Läs rikt linjerna för att uppgradera ditt Azure HDInsight-kluster till en nyare version.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951164"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uppgradera HDInsight-kluster till en nyare version

För att kunna dra nytta av de senaste HDInsight-funktionerna rekommenderar vi att HDInsight-kluster uppgraderas till den senaste versionen. Följ rikt linjerna nedan för att uppgradera dina HDInsight-kluster versioner.

> [!NOTE]  
> Information om vilka versioner av HDInsight som stöds finns i [versioner av HDInsight-komponenter](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Uppgraderings uppgifter

Arbets flödet för att uppgradera HDInsight-kluster är följande.
arbets flödes diagram för ![HDInsight-uppgradering](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Läs varje avsnitt av det här dokumentet om du vill veta vilka ändringar som kan krävas när du uppgraderar ditt HDInsight-kluster.
2. Skapa ett kluster som test/kvalitets säkrings miljö. Mer information om hur du skapar ett kluster finns i [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiera befintliga jobb, data källor och mottagare till den nya miljön.
4. Genomför verifierings testning för att se till att dina jobb fungerar som förväntat på det nya klustret.

När du har kontrollerat att allt fungerar som förväntat schemalägger du stillestånds tiden för migreringen. Under den här stillestånds tiden utför du följande åtgärder:

1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna. Om du till exempel har data som lagras direkt på en head-nod.
1. [Ta bort det befintliga klustret](./hdinsight-delete-cluster.md).
1. Skapa ett kluster i samma VNET-undernät med den senaste (eller stödda) HDI-versionen med samma standard data lager som det tidigare klustret använde. Detta gör att det nya klustret kan fortsätta att arbeta med dina befintliga produktions data.
1. Importera alla övergående data som du säkerhetskopierar.
1. Starta jobb/Fortsätt bearbeta med det nya klustret.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Ansluta till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera ett Linux-baserat kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Viktig information om HDInsight](./hdinsight-version-release.md)
