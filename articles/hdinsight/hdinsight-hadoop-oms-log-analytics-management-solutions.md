---
title: Lägg till lösningar för hantering av HDInsight-kluster i Azure Log Analytics | Microsoft Docs
description: Lär dig hur du skapar anpassade vyer för HDInsight-kluster med hjälp av Azure logganalys.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: caa10682f8419cfbc0837e5069357e02ee1e5f64
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398099"
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Lägg till lösningar för hantering av HDInsight-klustret till logganalys

HDInsight tillhandahåller klusterspecifika hanteringslösningar som du kan lägga till för Azure logganalys. [Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) lägga till funktioner till logganalys ger ytterligare information och verktyg för analys. Dessa lösningar samla in viktig prestandavärden från HDInsight-kluster och ger verktyg för att söka i mått. Dessa lösningar tillhandahåller även visualiseringar och instrumentpaneler för för de flesta klustertyper som stöds i HDInsight. Med det mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar. 

I den här artikeln lär du dig att lägga till klusterspecifika hanteringslösningar logganalys-arbetsytan.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure logganalys. Instruktioner finns i [Använd Azure logganalys med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Lägg till klusterspecifika hanteringslösningar

I det här avsnittet kan du lägga till en hanteringslösning för HBase-kluster till en befintlig logganalys-arbetsyta.

1. Öppna ett HDInsight-kluster i Azure-portalen, klicka på **övervakning**, och klicka sedan på **öppna OMS-instrumentpanelen**.

    ![Öppna Operations Management Suite-instrumentpanelen](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

1. I instrumentpanelen, klickar du på **lösningar galleriet** eller **Vydesigner** ikonen i den vänstra rutan.

    ![Lägg till hanteringslösning i logganalys](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "lägga till hanteringslösning i Operations Management Suite")

2. Klicka på något av följande ikoner i galleriet lösningar:

    - HDInsight Hadoop övervakning
    - HDInsight HBase övervakning (förhandsgranskning)
    - HDInsight Kafka övervakning
    - HDInsight Storm övervakning
    - HDInsight Spark övervakning

3. Klicka på nästa skärm **Lägg till**.  Följande skärmbild visar knappen Lägg till för övervakning av HBase.

     ![Lägg till HBase-hanteringslösning](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "lägga till HBase-hanteringslösning")

4. Du kan se en panel på instrumentpanelen för HBase-hanteringslösning. Om det kluster som du har associerat med Operations Management Suite (som en del av nödvändiga för den här artikeln) är ett HBase-kluster, visar panelen namnet på klustret och antalet noder i klustret.

    ![HBase-hanteringslösning som lagts till](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase-hanteringslösning som lagts till")

## <a name="next-steps"></a>Nästa steg

* [Frågan Azure Log Analytics för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Se också

* [Arbeta med logganalys](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md)
