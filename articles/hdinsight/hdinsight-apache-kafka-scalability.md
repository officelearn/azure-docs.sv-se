---
title: "Apache Kafka öka skala - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du konfigurerar hanterade diskar för Apache Kafka-kluster på Azure HDInsight för att öka skalbarheten."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 84c7048bbd608b0b99215d74f71960dc9d1158ef
ms.contentlocale: sv-se
ms.lasthandoff: 06/16/2017

---

# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurera lagring och skalbarhet för Apache Kafka på HDInsight

Lär dig hur du konfigurerar antalet hanterade diskar som används av Apache Kafka på HDInsight.

Kafka på HDInsight använder den lokala disken för virtuella datorer i HDInsight-klustret. Eftersom Kafka är mycket i/o-stor används [Azure hanterade diskar](../storage/storage-managed-disks-overview.md) för att tillhandahålla hög genomströmning och ger mer lagringsutrymme per nod. Om traditionella virtuella hårddiskar (VHD) användes för Kafka skulle varje nod vara begränsad till 1 TB. Du kan använda flera diskar med hanterade diskar för att uppnå 16 TB för varje nod i klustret.

Följande diagram innehåller en jämförelse mellan Kafka på HDInsight före hanterade diskar och Kafka i HDInsight med hanterade diskar:

![Diagram över Kafka på HDInsight med hjälp av en enda virtuell hårddisk per virtuell dator jämfört med flera hanterade diskar per virtuell dator](./media/hdinsight-apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurera hanterade diskar: Azure-portalen

1. Följ stegen i [Skapa ett HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md) för att förstå de vanliga stegen för att skapa ett kluster med hjälp av portalen. Slutför inte skapandet av portalen.

2. Från bladet __Klusterstorlek__ använder du fältet __Disk per arbetsnod__ att konfigurera antalet diskar.

    > [!NOTE]
    > Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Premiumdiskar används med DS- och GS-serien virtuella datorer. Alla andra typer av virtuella dator använder standard.

    ![Bild av klusterstorleksbladet med diskar per arbetsnod markerat](./media/hdinsight-apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurera hanterade diskar: Resource Manager-mall

Använd följande avsnitt i mallen för att styra antalet diskar som används av arbetarnoder i ett Kafka-kluster:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Du hittar en fullständig mall som visar hur du konfigurerar hanterade diskar på [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Nästa steg

Se följande dokument för mer information om arbete med Kafka på HDInsight:

* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Använda Apache Storm med Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Använda Apache Spark med Kafka på HDInsight](hdinsight-apache-spark-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](hdinsight-apache-kafka-connect-vpn-gateway.md)

* [HDInsight-blogg om hanterade diskar med Kafka](https://azure.microsoft.com/blog/announcing-hdinsight-kafka-public-preview-with-azure-managed-disks)
