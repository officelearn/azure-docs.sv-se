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
ms.date: 11/08/2017
ms.author: nitinme
ms.openlocfilehash: dc959f763e9a84199130bae845cb62c493676977
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Lägg till lösningar för hantering av HDInsight-klustret till logganalys

HDInsight tillhandahåller klusterspecifika hanteringslösningar som du kan lägga till för Azure logganalys. [Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) lägger till funktioner i OMS, tillhandahåller ytterligare data och analysverktyg i Log Analytics. Dessa lösningar samla in viktig prestandavärden från HDInsight-kluster och ger verktyg för att söka i mått. Dessa lösningar tillhandahåller även visualiseringar och instrumentpaneler för för de flesta klustertyper som stöds i HDInsight. Med det mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar. 

I den här artikeln lär du dig att lägga till klusterspecifika hanteringslösningar till en OMS-arbetsyta.

## <a name="prerequisites"></a>Krav

* Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure logganalys. Instruktioner finns i [Använd Azure logganalys med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Lägg till klusterspecifika hanteringslösningar

I det här avsnittet kan du lägga till en hanteringslösning för HBase-kluster till en befintlig OMS-arbetsyta.

1. Öppna ett HDInsigt kluster i Azure-portalen, klicka på **övervakning**, och klicka sedan på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

1. I OMS-instrumentpanelen, klickar du på **lösningar galleriet** eller **Vydesigner** ikonen i den vänstra rutan.

    ![Lägg till hanteringslösning i OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "lägga till hanteringslösning i OMS")

2. Klicka på något av följande ikoner i galleriet lösningar:

    - HDInsight Hadoop övervakning
    - HDInsight HBase övervakning (förhandsgranskning)
    - HDInsight Kafka övervakning
    - HDInsight Storm övervakning
    - HDInsight Spark övervakning

3. Klicka på nästa skärm **Lägg till**.  Följande skärmbild visar knappen Lägg till för övervakning av HBase.

     ![Lägg till HBase-hanteringslösning](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "lägga till HBase-hanteringslösning")

4. En panel visas på OMS instrumentpanel för HBase-hanteringslösning. Om det kluster som du har associerat med OMS (som en del av nödvändiga för den här artikeln) är ett HBase-kluster på panelen visar namnet på klustret och antalet noder i klustret.

    ![HBase-hanteringslösning som lagts till](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase-hanteringslösning som lagts till")

## <a name="next-steps"></a>Nästa steg

* [Frågan Azure Log Analytics för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Se även

* [Arbeta med OMS logganalys](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md)
