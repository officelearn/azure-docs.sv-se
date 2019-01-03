---
title: Aktivera automatisk ämnet skapas i Apache Kafka – Azure HDInsight
description: Lär dig hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa ämnen. Du kan konfigurera Kafka genom att ange auto.create.topics.enable till true via Ambari eller när klustret skapas via PowerShell eller Resource Manager-mallar.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: b9f17ad0ccfd5e58d5b93bde91e6f9c537a15fa6
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606902"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa ämnen

Som standard [Apache Kafka](https://kafka.apache.org/) på HDInsight kan inte skapa automatiska ämne. Du kan aktivera att skapa ämnen automatiskt för befintliga kluster med hjälp av [Apache Ambari](https://ambari.apache.org/). Du kan också aktivera skapa ämnen automatiskt när du skapar ett nytt Kafka-kluster med en Azure Resource Manager-mall.

## <a name="apache-ambari-web-ui"></a>Apache Ambari-webbgränssnittet

Om du vill aktivera automatisk ämnet skapas i ett befintligt kluster via Ambari-Webbgränssnittet, använder du följande steg:

1. Från den [Azure-portalen](https://portal.azure.com), Välj Kafka-klustret.

2. Från den __översikt över kluster__väljer __klusterinstrumentpanel__. 

    ![Bild av på portalen med instrumentpanelen för klustret valt](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Välj sedan __HDInsight-klusterinstrumentpanel__. När du uppmanas, kan du autentisera med autentiseringsuppgifterna för inloggning (admin) för klustret.

    ![Bild av posten HDInsight-kluster instrumentpanel](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Välj Kafka-tjänsten i listan till vänster på sidan.

    ![Tjänstlista](./media/apache-kafka-auto-create-topics/service-list.png)

4. Välj Peeringkonfigurationer mitt på sidan.

    ![Tjänsten config-fliken](./media/apache-kafka-auto-create-topics/service-config.png)

5. I fältet Filter anger du värdet `auto.create`. 

    ![Bild av filterfältet](./media/apache-kafka-auto-create-topics/filter.png)

    Detta filtrerar listan över egenskaper och visar den `auto.create.topics.enable` inställningen.

6. Ändra värdet för `auto.create.topics.enable` till `true`, och välj sedan spara. Lägga till en anteckning och välj sedan spara igen.

    ![Bild av posten auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Välj Kafka-tjänsten, Välj __starta om__, och välj sedan __starta om alla berörda__. När du uppmanas, väljer __bekräfta starta om alla__.

    ![Bild av val av omstart](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Du kan också ange Ambari värden via Ambari REST API. Detta är vanligtvis svårare, eftersom du behöver göra flera REST-anrop för att hämta den aktuella konfigurationen, ändra den, osv. Mer information finns i den [hantera HDInsight-kluster med Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentet.

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

När du skapar ett Kafka-kluster med en Azure Resource Manager-mall kan du direkt kan ange `auto.create.topics.enable` genom att lägga till den i en `kafka-broker`. Följande JSON-kodfragmentet visar hur du ställer in det här värdet `true`:

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

I det här dokumentet lärde du dig att skapa automatiska ämne för Apache Kafka på HDInsight. Mer information om hur du arbetar med Kafka finns följande länkar:

* [Analysera Apache Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Apache Kafka-kluster](apache-kafka-mirroring.md)
