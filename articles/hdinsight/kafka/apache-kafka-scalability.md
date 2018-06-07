---
title: Apache Kafka öka skala - Azure HDInsight | Microsoft Docs
description: Lär dig hur du konfigurerar hanterade diskar för Apache Kafka-kluster på Azure HDInsight för att öka skalbarheten.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/30/2018
ms.author: larryfr
ms.openlocfilehash: 1a104f4b4dee340f43c1dba01b83cb80e138a9ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626739"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurera lagring och skalbarhet för Apache Kafka på HDInsight

Lär dig hur du konfigurerar antalet hanterade diskar som används av Apache Kafka på HDInsight.

Kafka på HDInsight använder den lokala disken för virtuella datorer i HDInsight-klustret. Eftersom Kafka är mycket i/o-stor används [Azure hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md) för att tillhandahålla hög genomströmning och ger mer lagringsutrymme per nod. Om traditionella virtuella hårddiskar (VHD) användes för Kafka skulle varje nod vara begränsad till 1 TB. Du kan använda flera diskar med hanterade diskar för att uppnå 16 TB för varje nod i klustret.

Följande diagram innehåller en jämförelse mellan Kafka på HDInsight före hanterade diskar och Kafka i HDInsight med hanterade diskar:

![Diagram över Kafka på HDInsight med hjälp av en enda virtuell hårddisk per virtuell dator jämfört med flera hanterade diskar per virtuell dator](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurera hanterade diskar: Azure-portalen

1. Följ stegen i [Skapa ett HDInsight-kluster](../hdinsight-hadoop-create-linux-clusters-portal.md) för att förstå de vanliga stegen för att skapa ett kluster med hjälp av portalen. Slutför inte skapandet av portalen.

2. Från avsnittet __Klusterstorlek__ använder du fältet __Disk per arbetsnod__ att konfigurera antalet diskar.

    > [!NOTE]
    > Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Premiumdiskar används med DS- och GS-serien virtuella datorer. Alla andra typer av virtuella dator använder standard.

    ![Bild av klusterstorleksavsnittet med diskar per arbetsnod markerat](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurera hanterade diskar: Resource Manager-mall

Använd följande avsnitt i mallen för att styra antalet diskar som används av arbetarnoder i ett Kafka-kluster:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Du hittar en fullständig mall som visar hur du konfigurerar hanterade diskar på [ https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json ](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Nästa steg

Se följande dokument för mer information om arbete med Kafka på HDInsight:

* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](apache-kafka-mirroring.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Använda Apache Spark med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)

* [HDInsight-blogg om hanterade diskar med Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)