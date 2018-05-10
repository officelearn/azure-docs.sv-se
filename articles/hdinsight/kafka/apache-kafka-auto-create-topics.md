---
title: Aktivera automatisk avsnittet skapas i Apache Kafka - Azure HDInsight | Microsoft Docs
description: Lär dig hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa avsnitt. Du kan konfigurera Kafka genom att auto.create.topics.enable till true via Ambari eller när klustret skapas via PowerShell eller Resource Manager-mallar.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa avsnitt

Som standard kan Kafka på HDInsight inte skapa en automatisk avsnittet. Du kan aktivera avsnittet ska skapas automatiskt för befintliga kluster med Ambari. Du kan också aktivera avsnittet ska skapas automatiskt när du skapar ett nytt Kafka kluster med en Azure Resource Manager-mall.

## <a name="ambari-web-ui"></a>Ambari-webbgränssnittet

Om du vill aktivera automatisk avsnittet skapas i ett befintligt kluster med Ambari-Webbgränssnittet, använder du följande steg:

1. Från den [Azure-portalen](https://portal.azure.com), Välj Kafka klustret.

2. Från den __översikt över kluster__väljer __klusterinstrumentpanel__. 

    ![Bild av portalen med instrumentpanelen i klustret markerat](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Välj sedan __instrumentpanelen för HDInsight-klustret__. När du uppmanas du autentisera med inloggningsuppgifterna (admin) för klustret.

    ![Bild av HDInsight-kluster instrumentpanelen post](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Välj tjänsten Kafka i listan till vänster på sidan.

    ![Tjänstlista](./media/apache-kafka-auto-create-topics/service-list.png)

4. Välj konfigurationerna i mitten på sidan.

    ![Config-fliken för tjänsten](./media/apache-kafka-auto-create-topics/service-config.png)

5. Ange värdet i fältet Filter `auto.create`. 

    ![Bild av filterfältet](./media/apache-kafka-auto-create-topics/filter.png)

    Detta filtrerar listan över egenskaper och visar den `auto.create.topics.enable` inställningen.

6. Ändra värdet för `auto.create.topics.enable` till `true`, och sedan klicka på Spara. Lägga till en anteckning och välj sedan spara igen.

    ![Bild av posten auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Välj tjänsten Kafka __starta om__, och välj sedan __starta om alla berörda__. När du uppmanas, Välj __bekräfta starta om alla__.

    ![Bild av omstart markering](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Du kan också ange Ambari värden via Ambari REST API. Detta är vanligtvis svårare, eftersom du behöver göra flera REST-anrop hämta den aktuella konfigurationen genom att ändra den osv. Mer information finns i [hantera HDInsight-kluster med Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentet.

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

När du skapar ett Kafka kluster med en Azure Resource Manager-mall kan du direkt kan ange `auto.create.topics.enable` genom att lägga till den i en `kafka-broker`. Följande JSON-utdrag visar hur du det här värdet till `true`:

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

I det här dokumentet beskrivs hur du aktiverar automatisk avsnittet för att skapa för Kafka på HDInsight. Mer information om hur du arbetar med Kafka, se följande länkar:

* [Analysera Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Kafka-kluster](apache-kafka-mirroring.md)