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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Lägg till lösningar för hantering av HDInsight-klustret till logganalys (förhandsgranskning)

HDInsight tillhandahåller klusterspecifika hanteringslösningar som du kan lägga till för Azure logganalys. [Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) lägger till funktioner i OMS, tillhandahåller ytterligare data och analysverktyg i Log Analytics. Dessa lösningar samla in viktig prestandavärden från HDInsight-kluster och ger verktyg för att söka i mått. Dessa lösningar tillhandahåller även visualiseringar och instrumentpaneler för för de flesta klustertyper som stöds i HDInsight. Med det mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar. 

I den här artikeln lär du dig att lägga till klusterspecifika hanteringslösningar till en OMS-arbetsyta.

## <a name="prerequisites"></a>Krav

* Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure logganalys. Instruktioner finns i [Använd Azure logganalys med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Lägg till klusterspecifika hanteringslösningar

I det här avsnittet kan du lägga till en hanteringslösning för HBase-kluster till en befintlig OMS-arbetsyta. Liknande lösningar för andra typer av HDInsight-klustret blir snart tillgänglig.

1. Öppna OMS-instrumentpanelen. Öppna bladet HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen, klicka på fliken övervakning, och klicka på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

1. I OMS-instrumentpanelen, klickar du på **lösningar galleriet** eller ikonen View Designer i den vänstra rutan.

    ![Lägg till hanteringslösning i OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "lägga till hanteringslösning i OMS")

2. I galleriet lösningar hitta **HDInsight HBase övervakning**, och klickar sedan på ikonen.

    ![Hitta lösning för hantering av HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "hitta HBase lösning")

3. Klicka på nästa skärm **Lägg till**.

     ![Lägg till HBase-hanteringslösning](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "lägga till HBase-hanteringslösning")

4. Du bör nu se en panel på instrumentpanelen för OMS för HBase-hanteringslösning. Om det kluster som du har associerat med OMS (som en del av nödvändiga för den här artikeln) är ett HBase-kluster på panelen visar namnet på klustret och antalet noder i klustret.

    ![HBase-hanteringslösning som lagts till](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase-hanteringslösning som lagts till")

## <a name="next-steps"></a>Nästa steg

* [Frågan Azure Log Analytics för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Se även

* [Arbeta med OMS logganalys](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md)
