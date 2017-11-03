---
title: "Skapa en webbläsare - Azure HDInsight Hadoop-kluster | Microsoft Docs"
description: "Lär dig hur du skapar Hadoop, HBase, Storm eller Spark-kluster på Linux för HDInsight med hjälp av en webbläsare och Azure preview portal."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: c9a6040f7115917219143cd4bc8d0e3ac87370cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure-portalen är ett webbaserat verktyg för tjänster och resurser som finns i Microsoft Azure-molnet. I den här artikeln får du lära dig hur du skapar Linux-baserade HDInsight-kluster med hjälp av portalen.

## <a name="prerequisites"></a>Krav
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En modern webbläsare**. Azure-portalen använder HTML5 och Javascript och fungerar inte i äldre webbläsare.

## <a name="create-clusters"></a>Skapa kluster
Azure-portalen visar de flesta egenskaper för klustret. Med Azure Resource Manager-mall kan dölja du en stor mängd information. Mer information finns i [skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på  **+** , klickar du på **Intelligence + analys**, och klicka sedan på **HDInsight**.
   
    ![Skapa ett nytt kluster i Azure portal](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "skapar ett nytt kluster i Azure-portalen")

3. I den **HDInsight** bladet, klickar du på **anpassad (storlek, inställningar, appar)**, klickar du på **grunderna**, och ange följande information.

    ![Skapa ett nytt kluster i Azure portal](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "skapar ett nytt kluster i Azure-portalen")

    * Ange **klusternamnet**: Det här namnet måste vara globalt unikt.

    * Från den **prenumeration** listrutan, Välj den Azure-prenumeration som ska användas för klustret.

    * Klicka på **kluster typen**, och välj sedan:
   
        * **Klustertyp**: Om du inte vet vad du ska välja väljer du **Hadoop**. Det är den mest populära klustertypen.
     
            > [!IMPORTANT]
            > HDInsight-kluster har på en mängd olika typer som motsvarar arbetsbelastning eller teknik som klustret är inställd för. Det finns ingen stöds metod för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase på ett kluster. 
            > 
            > 
        
        * **Operativsystem**: Välj **Linux**.
        
        * **Version**: Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).
        * **Klusternivå**: Azure HDInsight tillhandahåller molntjänster för stordata i två kategorier: Premium- och standardnivå. Mer information finns i [Klusternivåer](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).

    * För **klustret inloggning användarnamn** och **klustret inloggningslösenordet**, ange användarnamn och lösenord för administratören.

    * Ange en **SSH-användarnamn** och om du vill ha SSH-lösenordet samma som det administratörslösenord du angav tidigare, Välj den **använda samma lösenord som klusterinloggning** kryssrutan. Om inte, kan du ange antingen en **lösenord** eller **offentliga nyckel**, som används för att autentisera SSH-användare. Den rekommenderade metoden är att använda en offentlig nyckel. Spara konfigurationen av autentiseringsuppgifterna genom att klicka på **Välj** längst ned.
   
        Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

    * För **resursgruppen**, ange om du vill skapa en ny resursgrupp eller använda en befintlig.

    * Ange ett datacenter **plats** där klustret kommer att skapas.

    * Klicka på **Nästa**.

4. På den **lagring** bladet, ange om du vill Azure Storage (WASB) eller Data Lake Store som standardlagring. Titta på tabellen nedan för mer information.

    ![Skapa ett nytt kluster i Azure portal](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "skapar ett nytt kluster i Azure-portalen")

    | Lagring                                      | Beskrivning |
    |----------------------------------------------|-------------|
    | **Azure Storage-Blobbar som standardlagring**   | <ul><li>För **primära lagringstyp**väljer **Azure Storage**. Efter det för **urvalsmetod**, kan du välja **Mina prenumerationer** om du vill ange ett lagringskonto som är en del av din Azure-prenumeration och välj sedan lagringskontot. Annars klickar du på **åtkomstnyckeln** och ange information för det lagringskonto som du vill välja från utanför Azure-prenumerationen.</li><li>För **standardbehållaren**, kan du gå med standardnamnet för behållaren som föreslås i portalen eller ange egna.</li><li>Om du använder WASB som standardlagring kan du (valfritt) på **ytterligare Lagringskonton** att ange ytterligare lagringskonton ska associeras med klustret. I den **Azure Lagringsnycklar** bladet klickar du på **lägga till en nyckel för säkerhetslagring**, och du kan ange ett lagringskonto från dina Azure-prenumerationer eller andra prenumerationer (genom att tillhandahålla lagring kontoåtkomst nyckel).</li><li>Om du använder WASB som standardlagring kan du (valfritt) på **Data Lake Store-åtkomst** att ange Azure Data Lake Store som ytterligare lagringsutrymme. Mer information finns i [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Azure Data Lake Store som standardlagring** | För **primära lagringstyp**väljer **Datasjölager** och finns i artikel [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) anvisningar. |
    | **Externa metastores**                      | Alternativt kan ange du en SQL-databas för att spara Hive och Oozie-metadata som associeras med klustret. För **Välj en SQL-databas för Hive** Välj en SQL-databas och ange sedan det användarnamn/lösenordet för databasen. Upprepa dessa steg för Oozie-metadata.<br><br>Vissa överväganden när du använder Azure SQL-databas för metastores. <ul><li>Azure SQL-databasen som används för metastore måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. Klicka på namnet på servern på Azure SQL database instrumentpanelen till höger. Detta är den server som kör SQL-databasinstansen. När du är på server-vy klickar du på **konfigurera**, och sedan för **Azure Services**, klickar du på **Ja**, och klicka sedan på **spara**.</li><li>När du skapar en metastore kan du inte använda ett databasnamn som innehåller tankstreck eller bindestreck, eftersom detta kan orsaka klusterskapandeprocessen misslyckas.</li></ul>                                                                                                                                                                       |

    Klicka på **Nästa**. 

    > [!WARNING]
    > Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

5. Du kan också klicka på **program** att installera program som fungerar med HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv. Mer information finns i [installera HDInsight-program](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Klicka på **klusterstorleken** att visa information om de noder som kommer att skapas för det här klustret. Ange antalet arbetarnoder som du behöver för klustret. Den uppskattade kostnaden för klustret visas på bladet.
   
    ![Noden priser nivåer bladet](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "ange antalet noder i klustret")
   
   > [!IMPORTANT]
   > Om du planerar att mer än 32 arbetarnoder i klustret har skapats eller genom att skala klustret när den har skapats, måste du välja en huvudnod storlek med minst 8 kärnor och 14GB RAM-minne.
   > 
   > Mer information om noden storlekar och relaterade kostnader finns [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Klicka på **nästa** spara noden priser konfiguration.

7. Klicka på **avancerade inställningar** att konfigurera andra valfria inställningar, till exempel med hjälp av **skriptåtgärder** att anpassa ett kluster för att installera anpassade komponenter eller ansluta till en **virtuellt nätverk**. Titta på tabellen nedan för mer information.

    ![Noden priser nivåer bladet](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "ange antalet noder i klustret")

    | Alternativ | Beskrivning |
    |--------|-------------|
    | **Skriptåtgärder** | Använd det här alternativet om du vill använda ett anpassat skript för att anpassa ett kluster, när klustret skapas. Mer information om skriptåtgärder finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Virtual Network** | Välj ett virtuellt Azure-nätverk och undernätet om du vill att klustret i ett virtuellt nätverk. Information om hur du använder HDInsight med ett virtuellt nätverk, inklusive specifika konfigurationskrav för det virtuella nätverket finns [utöka HDInsight funktioner med hjälp av ett Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). |

    Klicka på **Nästa**.

8. På den **sammanfattning** bladet kontrollerar du informationen som du angav tidigare och klicka sedan på **skapa**.

    ![Noden priser nivåer bladet](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "ange antalet noder i klustret")
    
    > [!NOTE]
    > Det tar en stund innan klustret skapas vanligtvis cirka 15 minuter. Använd panelen på startsidan eller **meddelanden** post till vänster på sidan för att kontrollera etableringsprocessen.
    > 
    > 
12. När processen är klar klickar du på ikonen för klustret från startsidan att starta klusterbladet. Klusterbladet innehåller följande information.
    
    ![Klusterbladet](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "egenskaper för kluster")
    
    Använd följande för att förstå ikonerna längst upp i det här bladet.
    
    * **Översikt över** innehåller alla viktig information om klustret, till exempel namnet, den resursgrupp som det tillhör, platsen, operativsystem, URL: en för instrumentpanelen klustret, osv.
    * **Instrumentpanelen** leder till Ambari-portalen som associeras med klustret.
    * **Secure Shell**: Information som behövs för att få åtkomst till klustret via SSH.
    * **Kluster för** kan du öka antalet arbetarnoder som associeras med klustret.
    * **Ta bort**: tar bort HDInsight-klustret.
    

## <a name="customize-clusters"></a>Anpassa kluster
* Se [anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Se [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett HDInsight-kluster, kan du använda följande för att lära dig hur du arbetar med ditt kluster:

### <a name="hadoop-clusters"></a>Hadoop-kluster
* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-kluster
* [Kom igång med HBase på HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för HBase i HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-kluster
* [Utveckla Java-topologier för Storm på HDInsight](hdinsight-storm-develop-java-topology.md)
* [Använda Python komponenter i Storm på HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Storm på HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark-kluster
* [Skapa ett fristående program med hjälp av Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](hdinsight-apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](hdinsight-apache-spark-eventhub-streaming.md)

