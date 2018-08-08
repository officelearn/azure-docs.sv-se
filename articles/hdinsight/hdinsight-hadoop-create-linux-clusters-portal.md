---
title: Skapa Hadoop-kluster med hjälp av en webbläsare – Azure HDInsight
description: Lär dig hur du skapar Hadoop, HBase, Storm eller Spark-kluster på Linux för HDInsight med hjälp av en webbläsare och Azure preview portal.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 210ece6825393917f77e213939770c4ee867fd20
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600854"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure-portalen är ett webbaserat verktyg för tjänster och resurser som finns i Microsoft Azure-molnet. I den här artikeln lär du dig att skapa Linux-baserade HDInsight-kluster med hjälp av portalen.

## <a name="prerequisites"></a>Förutsättningar
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En modern webbläsare**. Azure-portalen använder HTML5 och Javascript och kanske inte fungerar korrekt i äldre webbläsare.

## <a name="create-clusters"></a>Skapa kluster
Azure-portalen visar de flesta av egenskaperna. Med Azure Resource Manager-mall kan dölja du många detaljer. Mer information finns i [skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **+**, klickar du på **information + analys**, och klicka sedan på **HDInsight**.
   
    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "skapa ett nytt kluster i Azure portal")

3. I den **HDInsight** bladet klickar du på **anpassad (storlek, inställningar, appar)**, klickar du på **grunderna**, och ange sedan följande information.

    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "skapa ett nytt kluster i Azure portal")

    * Ange **klusternamnet**: Det här namnet måste vara globalt unikt.

    * Från den **prenumeration** listrutan, Välj den prenumeration som används för klustret.

    * Klicka på **Klustertyp**, och välj sedan typ av kluster (Hadoop, Spark, etc.) som du vill skapa. För **operativsystemet**, klickar du på **Linux** och välj sedan en version. Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).

        För Hadoop, Spark och Interactive Query klustertyper, kan du också välja för att installera den **Enterprise Security Package**. Enterprise Security Package säkerhetsvillkoren som Azure Active Directory-integrering och Apache Ranger för klustren. Mer information finns i [säkerhetspaketet för företag i Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

        ![Aktivera Enterprise Security Package](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-enable-enterprise-security-package.png "aktivera Enterprise Security Package")
     
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

4. För **Storage**, ange om du vill att Azure Storage (WASB) eller Data Lake Storage som standardlagring. Titta på tabellen nedan för mer information.

    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "skapa ett nytt kluster i Azure portal")

    | Storage                                      | Beskrivning |
    |----------------------------------------------|-------------|
    | **Azure Storage-Blobar som standardlagring**   | <ul><li>För **primär lagringstyp**väljer **Azure Storage**. Efter det för **urvalsmetod**, du kan välja **Mina prenumerationer** om du vill ange ett lagringskonto som är en del av din Azure-prenumeration och välj sedan lagringskontot. Annars klickar du på **åtkomstnyckel** och ange information för det lagringskonto som du vill välja från utanför din Azure-prenumeration.</li><li>För **standardbehållaren**, kan du gå med standard-behållarnamn som föreslås av portalen eller ange dina egna.</li><li>Om du använder WASB som standardlagring, kan du (valfritt) Klicka **ytterligare Lagringskonton** ange ytterligare lagringskonton ska associeras med klustret. För **Azure Storage-nycklar**, klickar du på **Lägg till lagringsnyckel**, och du kan ange ett lagringskonto från dina Azure-prenumerationer eller från andra prenumerationer (genom att tilldela åtkomstnyckeln för lagringskontot).</li><li>Om du använder WASB som standardlagring, kan du (valfritt) Klicka **Data Lake Store-åtkomst** att ange Azure Data Lake Storage som ytterligare lagringsutrymme. Mer information finns i [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | **Azure Data Lake Storage som standardlagring** | För **primär lagringstyp**väljer **Azure Data Lake Storage Gen1** eller **Azure Data Lake Storage Gen2 (förhandsversion)** och finns i artikeln [Snabbstart : Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) anvisningar. |
    | **Externa metastores**                      | Alternativt kan du ange en SQL-databas för att spara Hive och Oozie-metadata som associeras med klustret. För **Välj en SQL-databas för Hive** Välj en SQL-databas och ange sedan användarnamn/lösenord för databasen. Upprepa dessa steg för Oozie-metadata.<br><br>Vissa överväganden när du använder Azure SQL-databas för metastores. <ul><li>Azure SQL-databasen som används för metastore måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. Klicka på servernamnet på Azure SQL database-instrumentpanelen till höger. Det här är den server som kör SQL-databasinstans. När du är på server-vy klickar du på **konfigurera**, och sedan för **Azure Services**, klickar du på **Ja**, och klicka sedan på **spara**.</li><li>När du skapar ett metaarkiv kan du inte Använd ett databasnamn som innehåller tankstreck eller bindestreck, eftersom detta kan medföra att klusterskapningsprocessen misslyckas.</li></ul> |

    Klicka på **Nästa**. 

    > [!WARNING]
    > Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

5. Du kan också klicka på **program** att installera program som fungerar med HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv. Mer information finns i [installera HDInsight-program](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Klicka på **klusterstorlek** att visa information om de noder som används för det här klustret. Ange antalet arbetsnoder som ska användas för klustret. Den uppskattade kostnaden för att köra klustret visas också.
   
    ![Nodprisnivåer](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "anger antalet klusternoder")
   
   > [!IMPORTANT]
   > Om du planerar att mer än 32 arbetarnoder i klustret har skapats eller skala klustret när du har skapat, måste du välja en head nodstorlek med minst 8 kärnor och 14 GB RAM-minne.
   > 
   > Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Klicka på **nästa** att spara konfigurationen av nodpris.

7. Klicka på **avancerade inställningar** att konfigurera andra valfria inställningar, till exempel med hjälp av **skriptåtgärder** att anpassa ett kluster för att installera anpassade komponenter eller koppla en **virtuellt nätverk**. Titta på tabellen nedan för mer information.

    ![Nodprisnivåer](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "anger antalet klusternoder")

    | Alternativ | Beskrivning |
    |--------|-------------|
    | **Skriptåtgärder** | Använd det här alternativet om du vill använda ett anpassat skript för att anpassa ett kluster när klustret skapas. Mer information om skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Virtual Network** | Välj en Azure-nätverket och undernätet om du vill placera kluster till ett virtuellt nätverk. Information om hur du använder HDInsight med ett virtuellt nätverk, inklusive konfigurationskraven för det virtuella nätverket, finns i [utöka HDInsight-funktioner med hjälp av Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). |

    Klicka på **Nästa**.

8. För **sammanfattning**, kontrollerar du informationen som du angav tidigare och klicka sedan på **skapa**.

    ![Nodprisnivåer](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "anger antalet klusternoder")
    
    > [!NOTE]
    > Det tar en stund för klustret skapas, vanligen cirka 15 minuter. Använd ikonen på startsidan eller **meddelanden** post till vänster på sidan för att kontrollera etableringsprocessen.
    > 
    > 
12. När processen är klar klickar du på panelen för klustret från startsidan. Fönstret klustret innehåller följande information.
    
    ![Kluster-gränssnittet](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "Klusteregenskaper")
    
    Använd följande för att förstå ikonerna längst upp.
    
    * **Översikt över** fliken innehåller viktig information om klustret, till exempel namnet, resursgrupp som den tillhör, plats, operativsystem, URL: en för instrumentpanelen för klustret, osv.
    * **Instrumentpanelen** dirigerar dig till Ambari-portalen som är kopplat till klustret.
    * **Secure Shell**: Information som behövs för att få åtkomst till klustret med SSH.
    * **Skala kluster** kan du öka antalet arbetsnoder som är kopplat till klustret.
    * **Ta bort**: tar bort HDInsight-klustret.
    

## <a name="customize-clusters"></a>Anpassa kluster
* Se [anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett HDInsight-kluster, Använd följande för att lära dig hur du arbetar med ditt kluster:

### <a name="hadoop-clusters"></a>Hadoop-kluster
* [Använda Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-kluster
* [Kom igång med HBase i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-kluster
* [Utveckla Java-topologier för Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark-kluster
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](spark/apache-spark-machine-learning-mllib-ipython.md)

