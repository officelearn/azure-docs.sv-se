---
title: Aktivera automatisk generering av ämnen i Apache Kafka – Azure HDInsight
description: Lär dig hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa ämnen. Du kan konfigurera Kafka genom att ställa in Auto. Create. topics. Aktivera sant via Ambari eller skapa kluster via PowerShell eller Resource Manager-mallar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242356"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Så här konfigurerar du Apache Kafka på HDInsight för att automatiskt skapa ämnen

[Apache Kafka](https://kafka.apache.org/) i HDInsight aktiverar som standard inte automatisk generering av ämne. Du kan aktivera skapande av automatiskt ämne för befintliga kluster med [Apache Ambari](https://ambari.apache.org/). Du kan också aktivera skapande av automatiskt ämne när du skapar ett nytt Kafka-kluster med hjälp av en Azure Resource Manager-mall.

## <a name="apache-ambari-web-ui"></a>Apache Ambari-webbgränssnitt

Gör så här om du vill aktivera automatisk generering av ämnen i ett befintligt kluster genom Ambari-webbgränssnittet:

1. Välj ditt Kafka-kluster från [Azure Portal](https://portal.azure.com).

1. Välj **Ambari start**från **kluster instrument paneler**.

    ![Bild av portalen med valt kluster instrument panel](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    När du uppmanas autentiserar du med inloggnings uppgifterna (admin) för klustret. Alternativt kan du ansluta till Amabri direkt från `https://CLUSTERNAME.azurehdinsight.net/` där `CLUSTERNAME` är namnet på ditt Kafka-kluster.

1. Välj Kafka-tjänsten i listan till vänster på sidan.

    ![Flik för Apache Ambari service list](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Välj config i mitten av sidan.

    ![Fliken Konfiguration av Apache Ambari-tjänst](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. I fältet Filter anger du ett värde för `auto.create`.

    ![Sök filter fält för Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Detta filtrerar listan över egenskaper och visar `auto.create.topics.enable`-inställningen.

1. Ändra värdet för `auto.create.topics.enable` till `true`och välj sedan **Spara**. Lägg till en anteckning och välj sedan **Spara** igen.

    ![Bild av filen Auto. Create. topics. enable Entry](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Välj Kafka-tjänsten, Välj __starta om__och välj sedan __starta om alla berörda__. När du uppmanas väljer du __Bekräfta omstart av alla__.

    ![Apache Ambari-omstart alla påverkade](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Du kan också ange Ambari-värden via Ambari-REST API. Detta är i allmänhet svårare eftersom du måste göra flera REST-anrop för att hämta den aktuella konfigurationen, ändra den osv. Mer information finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) Document.

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

När du skapar ett Kafka-kluster med hjälp av en Azure Resource Manager-mall kan du direkt ange `auto.create.topics.enable` genom att lägga till det i en `kafka-broker`. Följande JSON-kodfragment visar hur du ställer in det här värdet på `true`:

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

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du aktiverar automatisk generering av ämnen för Apache Kafka i HDInsight. Mer information om hur du arbetar med Kafka finns i följande länkar:

* [Analysera Apache Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Apache Kafka-kluster](apache-kafka-mirroring.md)
