---
title: 'Snabbstart: Komma igång med Apache Hadoop och Apache Hive med Azure-portalen – Azure HDInsight'
description: I den här snabbstarten använder du Azure-portalen för att skapa ett HDInsight Hadoop-kluster
keywords: hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: e178326c4a165a9bebbef6b0162cc79c1f9a0727
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459516"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snabbstart: Skapa Apache Hadoop-kluster i Azure HDInsight med hjälp av Azure portal

I den här artikeln får du lära dig hur du skapar [Apache Hadoop](https://hadoop.apache.org/)-kluster i HDInsight med Azure-portalen och sedan kör Apache Hive-jobb i HDInsight. De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I den här artikeln utför du alla tre aktiviteterna.

I den här snabbstarten använder du Azure Portal för att skapa ett HDInsight Hadoop-kluster. Du kan också skapa ett kluster med hjälp av [Azure Resource Manager-mallen](apache-hadoop-linux-tutorial-get-started.md).

För närvarande innehåller HDInsight [sju olika klustertyper](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över komponenter som stöds och som hanteras i HDInsight finns i [Vad är nytt i de Apache Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med hjälp av Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Från Azure-portalen navigerar du till **skapa en resurs** > **Analytics** > **HDInsight**.

    ![Databricks på Azure-portalen](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight.png "Databricks på Azure-portalen")

1. Under **HDInsight** > **Snabbregistrering** > **grunderna**, ange eller Välj följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Klusternamn   | Ange ett namn för Hadoop-klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. De första och sista tecknen i namnet får inte vara bindestreck. |
    |Prenumeration    |  Välj din Azure-prenumeration. |
    |Klustertyp     | Hoppa över detta nu. Du kan ange dessa indata i nästa steg i den här proceduren.|
    |Användarnamnet för klusterinloggning och lösenord    | Standardinloggningsnamnet är **admin**. Lösenordet måste bestå av minst 10 tecken och måste innehålla åtminstone en siffra, en versal, en gemen, ett alfanumeriskt tecken (förutom tecknen ' " ` \). Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |Secure Shell (SSH)-användarnamn | Standardanvändarnamnet är **sshuser**.  Du kan ange ett annat namn som SSH-användarnamn. |
    |Använd inloggningslösenordet för SSH| Markera kryssrutan om du vill använda samma lösenord för SSH-användare som du angav för klusterinloggningsanvändare.|
    |Resursgrupp     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |Location    | Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. |

    ![Komma igång med HDInsight Linux ger grundläggande klustervärden](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics.png "Ange grundläggande värden för att skapa ett HDInsight-kluster")

1. Välj **Klustertyp** att öppna den **klusterkonfiguration** sidan och ange följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Klustertyp     | Välj **Hadoop** |
    |Version     | Välj **Hadoop 2.7.3 (HDI 3.6)**|

    ![Komma igång med HDInsight Linux ger grundläggande klustervärden](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "Ange grundläggande värden för att skapa ett HDInsight-kluster")

    Välj **Välj** och välj sedan **nästa** att gå vidare till lagringsinställningarna.

1. Från den **Storage** fliken, anger du följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Primär lagringstyp    | Välj Azure storage att använda Azure Storage Blob som standardkonto för lagring i den här artikeln. Du kan även använda Azure Data Lake Storage som standardlagringsutrymme. |
    |Urvalsmetod     |  Använd ett lagringskonto från din Azure-prenumeration för den här artikeln genom att välja **Mina prenumerationer**. Om du vill använda ett lagringskonto från någon annan prenumeration väljer du **Åtkomstnyckel** och anger sedan kontots åtkomstnyckel. |
    |Välj ett lagringskonto   | Välj **Välj ett lagringskonto** att välja ett befintligt lagringskonto eller välja **Skapa ny**.|

    Acceptera alla övriga standardvärden och välj sedan **nästa** att gå vidare till sidan Översikt.

    ![Komma igång med HDInsight Linux ger klusterlagringsvärden](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Ange lagringsvärden för att skapa ett HDInsight-kluster")

1. Från den **sammanfattning** kontrollerar de värden du valde i tidigare steg.

    ![Komma igång med HDInsight Linux – klustersammanfattning](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "Komma igång med HDInsight Linux – klustersammanfattning")

1. Välj **Skapa**. Det tar cirka 20 minuter att skapa ett kluster.

1. När klustret har skapats visas en klusteröversiktssida i Azure Portal.

    ![HDInsight Linux komma igång klusterinställningar](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-overview.png "HDInsight-klusteregenskaper")    

    Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-kluster och dess standardkontot för lagring måste samplaceras i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.

    > [!NOTE]  
    > Information om andra kluster metoder för att skapa och förstå de egenskaper som används i den här snabbstarten Se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Köra Apache Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här snabbstarten kan du använda Ambari Hive-vyn från portalen. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Hive-data i HDInsight](hdinsight-use-hive.md).

1. Öppna Ambari genom att välja **Klusterinstrumentpanel** i föregående skärmbild.  Du kan också gå till `https://ClusterName.azurehdinsight.net`, där `ClusterName` är klustret du skapade i föregående avsnitt.

    ![Komma igång med HDInsight Linux – klusterinstrumentpanel](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Komma igång med HDInsight Linux – klusterinstrumentpanel")

2. Ange det Hadoop-användarnamn och -lösenord som du angav när du skapade klustret. Standardanvändarnamnet är **admin**.

3. Öppna **Hive-vy** så som det visas på följande skärmbild:

    ![Välja Ambari-vyer](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "HDInsight Hive Viewer-menyn")

4. På fliken **FRÅGA** klistrar du in följande HiveQL-instruktioner i kalkylbladet:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight Hive-vyer](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "HDInsight Hive visa frågeredigeraren")

5. Välj **Kör**. Fliken **RESULTAT** visas under fliken **FRÅGA** och visar information om jobbet. 

    När frågan har slutförts visas resultatet av åtgärden på fliken **FRÅGA**. En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.

    ![HDInsight Hive-vyer](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "HDInsight Hive visa frågeredigeraren")

6. Upprepa steg 4 och 5 för att köra följande fråga:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Du kan också spara frågans resultat. Välj menyknappen till höger och ange om du vill ladda ned resultatet som en CSV-fil eller lagra den på lagringskontot som är associerat till klustret.

    ![Spara resultatet från Hive-frågan](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Spara resultatet från Hive-frågan")

När du har slutfört ett Hive-jobb kan du [exportera resultaten till Azure SQL Database eller SQL Server-databasen](apache-hadoop-use-sqoop-mac-linux.md) och även [visualisera resultat i Excel](apache-hadoop-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel i en Apache log4j-fil](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kan du ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

> [!NOTE]  
> Om du är *omedelbart* fortsätter till nästa artikel om du vill lära dig att köra ETL-åtgärder med Hadoop på HDInsight, kan du behålla klustret körs. Det beror i självstudien måste du skapa ett Hadoop-kluster igen. Men om du inte går igenom nästa artikel direkt, du måste ta bort klustret nu.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Ta bort klustret och/eller Storage-kontot av standardtyp

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![HDInsight-borttagningskluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Ta bort HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du har lärt dig hur du skapar ett Linux-baserade HDInsight-kluster med en Resource Manager-mall och hur du utför grundläggande Hive-frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med hjälp av interaktiva frågor på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
