---
title: "Lägg till lösningar för hantering av HDInsight-kluster i Azure Log Analytics | Microsoft Docs"
description: "Lär dig hur du skapar anpassade vyer för HDInsight-kluster med hjälp av Azure logganalys."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: f8a073fbf40feaef97053bb1165d63bbca3e3feb
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Lägg till lösningar för hantering av HDInsight-klustret till logganalys

HDInsight tillhandahåller klusterspecifika hanteringslösningar som du kan lägga till för Azure logganalys. [Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) lägga till funktionen i [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), vilket ger ytterligare data och analysverktyg till logganalys. Dessa lösningar samla in viktig prestandavärden från HDInsight-kluster och ger verktyg för att söka i mått. Dessa lösningar tillhandahåller även visualiseringar och instrumentpaneler för för de flesta klustertyper som stöds i HDInsight. Med det mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar. 

I den här artikeln lär du dig att lägga till klusterspecifika hanteringslösningar till en Operations Management Suite-arbetsyta.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure logganalys. Instruktioner finns i [Använd Azure logganalys med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Lägg till klusterspecifika hanteringslösningar

I det här avsnittet kan du lägga till en hanteringslösning för HBase-kluster till en befintlig Operations Management Suite-arbetsyta.

1. Öppna ett HDInsigt kluster i Azure-portalen, klicka på **övervakning**, och klicka sedan på **öppna OMS-instrumentpanelen**.

    ![Öppna Operations Management Suite-instrumentpanelen](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

1. I OMS-instrumentpanelen, klickar du på **lösningar galleriet** eller **Vydesigner** ikonen i den vänstra rutan.

    ![Lägg till hanteringslösning i Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "lägga till hanteringslösning i Operations Management Suite")

2. Klicka på något av följande ikoner i galleriet lösningar:

    - HDInsight Hadoop Monitoring
    - HDInsight HBase övervakning (förhandsgranskning)
    - HDInsight Kafka Monitoring
    - HDInsight Storm övervakning
    - HDInsight Spark övervakning

3. Klicka på nästa skärm **Lägg till**.  Följande skärmbild visar knappen Lägg till för övervakning av HBase.

     ![Lägg till HBase-hanteringslösning](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "lägga till HBase-hanteringslösning")

4. Du kan se en panel på OMS instrumentpanel för HBase-hanteringslösning. Om det kluster som du har associerat med Operations Management Suite (som en del av nödvändiga för den här artikeln) är ett HBase-kluster, visar panelen namnet på klustret och antalet noder i klustret.

    ![HBase-hanteringslösning som lagts till](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase-hanteringslösning som lagts till")

## <a name="next-steps"></a>Nästa steg

* [Frågan Azure Log Analytics för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Se också

* [Arbeta med Operations Management Suite logganalys](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md)
