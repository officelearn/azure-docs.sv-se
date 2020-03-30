---
title: Aktivera automatisk skapande av ämnen i Apache Kafka - Azure HDInsight
description: Lär dig hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa ämnen. Du kan konfigurera Kafka genom att ange auto.create.topics.enable till true via Ambari eller under klusterskapande via PowerShell- eller Resource Manager-mallar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242356"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Konfigurera Apache Kafka på HDInsight för att automatiskt skapa ämnen

Som standard aktiverar [Apache Kafka](https://kafka.apache.org/) på HDInsight inte automatisk skapande av ämnen. Du kan aktivera automatisk skapande av ämnen för befintliga kluster med [Apache Ambari](https://ambari.apache.org/). Du kan också aktivera automatisk skapande av ämnen när du skapar ett nytt Kafka-kluster med hjälp av en Azure Resource Manager-mall.

## <a name="apache-ambari-web-ui"></a>Apache Ambari webbgränssnitt

Så här aktiverar du automatisk skapande av ett ämne i ett befintligt kluster via webbgränssnittet i Ambari:

1. Välj ditt Kafka-kluster i [Azure-portalen.](https://portal.azure.com)

1. Välj **Ambari home**i **Klusterinstrumentpaneler**.

    ![Bild av portalen med klusterinstrumentpanelen markerad](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    När du uppmanas att autentisera med inloggningsuppgifterna (admin) för klustret. Alternativt kan du ansluta till Amabri direkt från `https://CLUSTERNAME.azurehdinsight.net/` där `CLUSTERNAME` är namnet på din Kafka kluster.

1. Välj Kafka-tjänsten i listan till vänster på sidan.

    ![Fliken Apache Ambari-tjänstlista](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Välj Configs i mitten av sidan.

    ![Fliken Apache Ambari-tjänstkonfigurationer](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. Ange värdet `auto.create`.

    ![Sökfilterfält för Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Detta filtrerar listan över `auto.create.topics.enable` egenskaper och visar inställningen.

1. Ändra värdet `auto.create.topics.enable` på `true`till och välj sedan **Spara**. Lägg till en anteckning och välj sedan **Spara** igen.

    ![Bild av posten auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Välj Kafka-tjänsten, välj __Starta om__och välj sedan Starta om __alla berörda__. När du uppmanas till det väljer du __Bekräfta omstart av alla__.

    ![Apache Ambari starta om alla drabbade](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Du kan också ställa in Ambari-värden via Ambari REST API. Detta är i allmänhet svårare, eftersom du måste göra flera REST-anrop för att hämta den aktuella konfigurationen, ändra den, etc. Mer information finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API-dokumentet.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

När du skapar ett Kafka-kluster med hjälp `auto.create.topics.enable` av en Azure `kafka-broker`Resource Manager-mall kan du direkt ange det genom att lägga till det i en . Följande JSON-kodavsnitt visar hur du ställer `true`in det här värdet på:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Efterföljande moment

I det här dokumentet har du lärt dig hur du aktiverar automatiska ämnesskapande för Apache Kafka på HDInsight. Mer information om hur du arbetar med Kafka finns i följande länkar:

* [Analysera Apache Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Apache Kafka-kluster](apache-kafka-mirroring.md)
