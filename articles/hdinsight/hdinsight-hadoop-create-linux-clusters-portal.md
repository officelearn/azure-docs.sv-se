---
title: Skapa Apache Hadoop-kluster med hjälp av en webbläsare, Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark-kluster på Linux för HDInsight med hjälp av en webbläsare och Azure preview portal.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: 9da9c6c438aac2c160a9ec7bc658e5d7b4ea207e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715182"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure-portalen är ett webbaserat verktyg för tjänster och resurser som finns i Microsoft Azure-molnet. I den här artikeln lär du dig att skapa Linux-baserade Azure HDInsight-kluster med hjälp av portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [så här hämtar du Azure kostnadsfria utvärderingsversionen för att testa Hadoop i HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En modern webbläsare**. Azure-portalen använder HTML5 och JavaScript. Det kanske inte fungerar korrekt i äldre webbläsare.

## <a name="create-clusters"></a>Skapa kluster
Azure-portalen visar de flesta av egenskaperna. Med hjälp av Azure Resource Manager-mallar kan dölja du många detaljer. Mer information finns i [skapa Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **+ Skapa en resurs** från menyn till vänster.

1.  Under **Azure Marketplace**väljer **Analytics**.

1.  Under **Aktuella** väljer du **HDInsight**.
   
    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "skapa ett nytt kluster i Azure portal")

1. På den **HDInsight** väljer **anpassad (storlek, inställningar, appar)**.

1. Välj **1 grunderna**. Ange följande information.

    ![Konfigurera grundläggande inställningar](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "skapa ett nytt kluster i Azure portal")

    * Ange den **klusternamn**. Det här namnet måste vara globalt unikt.

    * Från den **prenumeration** listrutan väljer du den prenumeration som används för klustret.

    * Välj **Klustertyp**. Välj sedan typ av kluster som du vill skapa. Exempel är Hadoop och Apache Spark. Den **operativsystemet** blir **Linux**. Välj sedan en version av programtyp för klustret. Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > HDInsight kluster kommer in en mängd olika typer. De motsvarar arbetsbelastning eller teknik som klustret är anpassad till. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer. Exempel är Storm och HBase på ett kluster.
        
    * För **användarnamnet för klusterinloggning** och **inloggningslösenordet för klustret**, ange användarnamn och lösenord för administratörsanvändaren.

    * Ange en **SSH-användarnamn**. Om du vill att samma SSH-lösenord som administratörslösenord som du angav tidigare, väljer du den **Använd samma lösenord som klusterinloggning** markerar du kryssrutan. Om inte, kan du ange antingen en **lösenord** eller **offentlig nyckel** att autentisera SSH-användaren. En offentlig nyckel är den metod som rekommenderas. Välj **Välj** längst ned för att spara konfigurationen av autentiseringsuppgifterna.
   
        Mer information finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * För **Resursgrupp** anger du om du vill skapa en ny resursgrupp eller använda en befintlig.

    * Ange ett datacenter **plats** där klustret har skapats.

    * Välj **nästa** att flytta till nästa sida.

4. Från **2 säkerhet + nätverk**, du kan ansluta ditt kluster till ett virtuellt nätverk med hjälp av den angivna nedrullningsbara menyn. Välj en Azure-nätverket och undernätet om du vill placera kluster till ett virtuellt nätverk. Information om hur du använder HDInsight med ett virtuellt nätverk finns i [utöka HDInsight-funktioner med hjälp av Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Artikeln innehåller specifika konfigurationskrav för det virtuella nätverket. 

    Om du vill använda den **Enterprise Security Package**, följer du dessa instruktioner: [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Välj **nästa** att flytta till nästa sida.


5. Från **3 Storage**, ange om du vill att Azure Storage eller Azure Data Lake Storage som standardlagring. Mer information finns i följande tabell.

     ![Ställ in Lagringsinställningar](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "skapa ett nytt kluster i Azure portal")

     | Storage                                      | Beskrivning |
     |----------------------------------------------|-------------|
     | **Azure Storage-blobar som standardlagring**   | <ul><li>För **primär lagringstyp**väljer **Azure Storage**. För **urvalsmetod**, Välj **Mina prenumerationer** om du vill ange ett lagringskonto som är en del av din Azure-prenumeration. Välj sedan lagringskontot. Annars väljer **åtkomstnyckel**. Ange sedan information för det lagringskonto som du vill välja från utanför din Azure-prenumeration.</li><li>För **standardbehållaren**, välja standard-behållarnamn som föreslås av portalen eller ange dina egna.</li><li>Om Azure Blob storage är din standardlagring, kan du också välja **ytterligare Lagringskonton** ange ytterligare lagringskonton ska associeras med klustret. För **Azure Storage-nycklar**väljer **Lägg till lagringsnyckel**. Du kan ange ett lagringskonto från dina Azure-prenumerationer eller från andra prenumerationer. Ange åtkomstnyckel för lagringskontot.</li><li>Om Blob storage är din standardlagring, kan du också välja **Data Lake Storage åtkomst** att ange Azure Data Lake Storage som ytterligare lagringsutrymme. Mer information finns i [ Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage som standardlagring** | För **primär lagringstyp**väljer **Azure Data Lake Storage Gen1** eller **Azure Data Lake Storage Gen2**. Sedan finns i artikeln [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) anvisningar. |
     | **Externa metastores**                      | Som ett alternativ kan du ange en SQL-databas för att spara Apache Hive och Apache Oozie-metadata som associeras med klustret. För **Välj en SQL-databas för Hive**, Välj en SQL-databas. Ange användarnamnet och lösenordet för databasen. Upprepa dessa steg för Oozie-metadata.<br><br>Vissa överväganden om hur du använder Azure SQL-databas för metastores är följande: <ul><li>Azure SQL-databasen som används för metastore måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. På höger sida av instrumentpanelen för Azure SQL-databas, väljer du namnet på servern. Den här servern är den som SQL database-instans som körs på. När du arbetar i server-vy, väljer **konfigurera**. Sedan för **Azure Services**väljer **Ja**. Välj sedan **Spara**.</li><li>När du skapar ett metaarkiv kan inte namnge en databas med tankstreck eller bindestreck. Följande tecken kan orsaka klusterskapningsprocessen misslyckas.</li></ul> |

     > [!WARNING]  
     > Med ett annat lagringskonto i en annan plats än HDInsight-kluster stöds inte.

     Välj **nästa** att flytta till nästa sida.


6. Från **4 program (valfritt)**, Välj alla program som du vill. Microsoft, oberoende programvaruleverantörer (ISV) eller om du kan utveckla programmen. Mer information finns i [installera program när klustret skapas](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Välj **nästa** att flytta till nästa sida.


6. **5 klusterstorleken** visar information om de noder som används för det här klustret. Ange antalet arbetsnoder som ska användas för klustret. Den uppskattade kostnaden för att köra klustret visas också.
   
    ![Ange nodprisnivåer](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "anger antalet klusternoder")
   
   > [!IMPORTANT]  
   > Om du planerar att mer än 32 arbetsnoder, väljer du en head nodstorlek med minst åtta kärnor och 14 GB RAM-minne. Planera noderna i klustret har skapats eller skala klustret när du har skapat. 
   > 
   > Läs mer på nodstorlekar och relaterade kostnader, [priser för Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Välj **nästa** att flytta till nästa sida.

8. Från **6 skriptåtgärder**, du kan anpassa ett kluster för att installera anpassade komponenter. Det här alternativet fungerar om du vill använda ett anpassat skript för att anpassa ett kluster när klustret skapas. Mer information om skriptåtgärder finns i [anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

   Välj **nästa** att flytta till nästa sida.

9. Från **7 sammanfattning**, kontrollerar du informationen som du angav tidigare. Välj sedan **Skapa**.

     ![Bekräfta konfigurationer](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "anger antalet klusternoder")
    
    > [!NOTE]  
    > Det tar lite tid att skapa klustret, normalt cirka 20 minuter. Övervaka **meddelanden** att läsa på etableringsprocessen.

10. När processen är klar väljer du **gå till resurs** från den **distributionen lyckades** meddelande. Fönstret klustret innehåller följande information.
    
    ![Kluster-gränssnittet](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Klusteregenskaper")
    
    Ikonerna i fönstret beskrivs på följande sätt:
    
    * Den **översikt** fliken innehåller viktig information om klustret. Exempel är namnet på resursgruppen som den tillhör, platsen, operativsystemet och URL-Adressen för instrumentpanelen för klustret.
    * **Instrumentpanelen** dirigerar dig till Ambari-portalen som är kopplat till klustret.
    * **Secure Shell** innehåller information som behövs för åtkomst till klustret med hjälp av SSH.
    * Med hjälp av **skala kluster**, du kan öka antalet arbetsnoder som är kopplat till klustret.
    * **Ta bort** tar bort HDInsight-klustret.
    

## <a name="customize-clusters"></a>Anpassa kluster
* [Anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Ta bort klustret
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg
Du har nu skapat ett HDInsight-kluster. Nu ska du lära dig hur du arbetar med ditt kluster.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

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
* [Apache Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](spark/apache-spark-machine-learning-mllib-ipython.md)

