---
title: Skapa Apache Hadoop-kluster med hjälp av en webbläsare – Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark-kluster på Linux för HDInsight med hjälp av en webbläsare och Azure preview portal.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 7ff69d3b79662cff1fc28b0b59777225d0cc439f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189038"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure-portalen är ett webbaserat verktyg för tjänster och resurser som finns i Microsoft Azure-molnet. I den här artikeln lär du dig att skapa Linux-baserade HDInsight-kluster med hjälp av portalen.

## <a name="prerequisites"></a>Förutsättningar
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En modern webbläsare**. Azure-portalen använder HTML5 och Javascript och kanske inte fungerar korrekt i äldre webbläsare.

## <a name="create-clusters"></a>Skapa kluster
Azure-portalen visar de flesta av egenskaperna. Med Azure Resource Manager-mall kan dölja du många detaljer. Mer information finns i [skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **+ skapa en resurs**, klickar du på **Analytics**, och klicka sedan på **HDInsight**.
   
    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "skapa ett nytt kluster i Azure portal")

3. I den **HDInsight** bladet klickar du på **anpassad (storlek, inställningar, appar)**, klickar du på **grunderna**, och ange sedan följande information.

    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "skapa ett nytt kluster i Azure portal")

    * Ange **klusternamn**: Det här namnet måste vara globalt unikt.

    * Från den **prenumeration** listrutan, Välj den prenumeration som används för klustret.

    * Klicka på **Klustertyp**, och välj sedan typ av kluster (Hadoop, Spark, etc.) som du vill skapa. För **operativsystemet**, klickar du på **Linux** och välj sedan en version. Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]
        > HDInsight kluster kommer in en mängd olika typer, vilket motsvarar arbetsbelastning eller teknik som klustret är anpassad till. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster. 
        > 
        > 
        
    * För **användarnamnet för klusterinloggning** och **inloggningslösenordet för klustret**, ange användarnamn och lösenord för administratörsanvändaren.

    * Ange en **SSH-användarnamn** och om du vill ha SSH-lösenordet detsamma som det administratörslösenord du angav tidigare, Välj den **Använd samma lösenord som klusterinloggning** markerar du kryssrutan. Om inte, kan du ange antingen en **lösenord** eller **offentlig nyckel**, som används för att autentisera SSH-användaren. Den rekommenderade metoden är att använda en offentlig nyckel. Spara konfigurationen av autentiseringsuppgifterna genom att klicka på **Välj** längst ned.
   
        Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

    * För **Resursgrupp** anger du om du vill skapa en ny resursgrupp eller använda en befintlig.

    * Ange ett datacenter **plats** där klustret har skapats.

    * Klicka på **Nästa**.

4. I **säkerhet och nätverk**, du kan ansluta ditt kluster till ett virtuellt nätverk med hjälp av den angivna listrutan. Välj en Azure-nätverket och undernätet om du vill placera kluster till ett virtuellt nätverk. Information om hur du använder HDInsight med ett virtuellt nätverk, inklusive konfigurationskraven för det virtuella nätverket, finns i [utöka HDInsight-funktioner med hjälp av Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Om du vill använda den **Enterprise Security Package**, du kan även följa instruktionerna här: [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

5. För **Storage**, ange om du vill att Azure Storage (WASB) eller Data Lake Storage som standardlagring. Titta på tabellen nedan för mer information.

     ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "skapa ett nytt kluster i Azure portal")

     | Storage                                      | Beskrivning |
     |----------------------------------------------|-------------|
     | **Azure Storage-Blobar som standardlagring**   | <ul><li>För **primär lagringstyp**väljer **Azure Storage**. Efter det för **urvalsmetod**, du kan välja **Mina prenumerationer** om du vill ange ett lagringskonto som är en del av din Azure-prenumeration och välj sedan lagringskontot. Annars klickar du på **åtkomstnyckel** och ange information för det lagringskonto som du vill välja från utanför din Azure-prenumeration.</li><li>För **standardbehållaren**, kan du gå med standard-behållarnamn som föreslås av portalen eller ange dina egna.</li><li>Om du använder WASB som standardlagring, kan du (valfritt) Klicka **ytterligare Lagringskonton** ange ytterligare lagringskonton ska associeras med klustret. För **Azure Storage-nycklar**, klickar du på **Lägg till lagringsnyckel**, och du kan ange ett lagringskonto från dina Azure-prenumerationer eller från andra prenumerationer (genom att tilldela åtkomstnyckeln för lagringskontot).</li><li>Om du använder WASB som standardlagring, kan du (valfritt) Klicka **Data Lake Store-åtkomst** att ange Azure Data Lake Storage som ytterligare lagringsutrymme. Mer information finns i [snabbstarten: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage som standardlagring** | För **primär lagringstyp**väljer **Azure Data Lake Storage Gen1** eller **Azure Data Lake Storage Gen2 (förhandsversion)** och finns i artikeln [Snabbstart : Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) anvisningar. |
     | **Externa metastores**                      | Alternativt kan du ange en SQL-databas för att spara Hive och Oozie-metadata som associeras med klustret. För **Välj en SQL-databas för Hive** Välj en SQL-databas och ange sedan användarnamn/lösenord för databasen. Upprepa dessa steg för Oozie-metadata.<br><br>Vissa överväganden när du använder Azure SQL-databas för metastores. <ul><li>Azure SQL-databasen som används för metastore måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. Klicka på servernamnet på Azure SQL database-instrumentpanelen till höger. Det här är den server som kör SQL-databasinstans. När du är på server-vy klickar du på **konfigurera**, och sedan för **Azure Services**, klickar du på **Ja**, och klicka sedan på **spara**.</li><li>När du skapar ett metaarkiv kan du inte Använd ett databasnamn som innehåller tankstreck eller bindestreck, eftersom detta kan medföra att klusterskapningsprocessen misslyckas.</li></ul> |

     Klicka på **Nästa**. 

     > [!WARNING]
     > Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

6. Du kan också klicka på **program** att installera program som fungerar med HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv. Mer information finns i [installera HDInsight-program](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Klicka på **klusterstorlek** att visa information om de noder som används för det här klustret. Ange antalet arbetsnoder som ska användas för klustret. Den uppskattade kostnaden för att köra klustret visas också.
   
    ![Nodprisnivåer](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "anger antalet klusternoder")
   
   > [!IMPORTANT]
   > Om du planerar att mer än 32 arbetarnoder i klustret har skapats eller skala klustret när du har skapat, måste du välja en head nodstorlek med minst 8 kärnor och 14 GB RAM-minne.
   > 
   > Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Klicka på **nästa** att spara konfigurationen av nodpris.

8. I **skriptåtgärder**, du kan anpassa ett kluster för att installera anpassade komponenter.  Använd det här alternativet om du vill använda ett anpassat skript för att anpassa ett kluster när klustret skapas. Mer information om skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).
Klicka på **Nästa**.

9. För **sammanfattning**, kontrollerar du informationen som du angav tidigare och klicka sedan på **skapa**.

     ![Nodprisnivåer](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "anger antalet klusternoder")
    
    > [!NOTE]
    > Det tar en stund för klustret skapas, vanligen cirka 15 minuter. Använd ikonen på startsidan eller **meddelanden** post till vänster på sidan för att kontrollera etableringsprocessen.
    > 
    > 
10. När processen är klar klickar du på panelen för klustret från startsidan. Fönstret klustret innehåller följande information.
    
    ![Kluster-gränssnittet](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "Klusteregenskaper")
    
    Använd följande för att förstå ikonerna längst upp.
    
    * **Översikt över** fliken innehåller viktig information om klustret, till exempel namnet, resursgrupp som den tillhör, plats, operativsystem, URL: en för instrumentpanelen för klustret, osv.
    * **Instrumentpanelen** dirigerar dig till Ambari-portalen som är kopplat till klustret.
    * **Secure Shell**: Information som behövs för att få åtkomst till klustret med SSH.
    * **Skala kluster** kan du öka antalet arbetsnoder som är kopplat till klustret.
      * **Ta bort**: Tar bort HDInsight-klustret.
    

## <a name="customize-clusters"></a>Anpassa kluster
* Se [anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett HDInsight-kluster, Använd följande för att lära dig hur du arbetar med ditt kluster:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [UseApache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda Apache Hadoop MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster
* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-kluster
* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark-kluster
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](spark/apache-spark-machine-learning-mllib-ipython.md)

