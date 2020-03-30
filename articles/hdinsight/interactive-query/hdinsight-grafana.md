---
title: Använda Grafana på Azure HDInsight
description: Lär dig hur du kommer åt Grafana-instrumentpanelen med Apache Hadoop-kluster i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082871"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Använda Grafana i Azure HDInsight

[Grafana](https://grafana.com/) är en populär, öppen källkod graf och instrumentpanel byggare. Grafana är funktionen rik; inte bara låter det användare skapa anpassningsbara och delbara instrumentpaneler, det erbjuder också mallade / skript instrumentpaneler, LDAP integration, flera datakällor och mycket mer.

I Azure HDInsight stöds Grafana med klustertyperna Spark, HBase, Kafka och Interactive Query. Det stöds inte för kluster med Enterprise Security Pack aktiverat.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md). För **klustertyp**väljer du **Spark**, **Kafka**, **HBase**eller **Interaktiv fråga**.

## <a name="access-the-grafana-dashboard"></a>Få tillgång till Grafana-instrumentpanelen

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/grafana/` den plats där CLUSTERNAME är namnet på klustret.

1. Ange användarautentiseringsuppgifterna för Hadoop-kluster.

1. Grafana-instrumentpanelen visas och ser ut så här:

    ![HdInsight Grafana webbinstrumentpanel](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Instrumentpanelen HDInsight Grafana")

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort klustret som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan över HDInsight-kluster som visas väljer du **...** bredvid klustret som du skapade.

1. Välj **Ta bort**. Välj **Ja**.

## <a name="next-steps"></a>Nästa steg

Mer information om att analysera data med HDInsight finns i följande artiklar:

* [Använd Apache Hive med HDInsight](../hadoop/hdinsight-use-hive.md).

* [Använd MapReduce med HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Kom igång med Visual Studio Hadoop-verktyg för HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
