---
title: 'Snabb start: Apache Hadoop, Apache Hive & Azure HDInsight-portalen'
description: I den här snabb starten använder du Azure Portal för att skapa ett HDInsight Hadoop-kluster
keywords: hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 35dc4938760ca83a6781d5791c746ee2f74310ab
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031602"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snabb start: skapa Apache Hadoop kluster i Azure HDInsight med Azure Portal

I den här artikeln får du lära dig hur du skapar [Apache Hadoop](https://hadoop.apache.org/)-kluster i HDInsight med Azure-portalen och sedan kör Apache Hive-jobb i HDInsight. De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I den här artikeln utför du alla tre aktiviteterna.

I den här snabbstarten använder du Azure Portal för att skapa ett HDInsight Hadoop-kluster. Du kan också skapa ett kluster med hjälp av [Azure Resource Manager-mallen](apache-hadoop-linux-tutorial-get-started.md).

För närvarande innehåller HDInsight [sju olika klustertyper](../hdinsight-overview.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över komponenter som stöds och som hanteras i HDInsight finns i [Vad är nytt i de Apache Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med hjälp av Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Från Azure Portal går du till **skapa en resurs** > **analys** > **HDInsight**.

    ![Skapa ett resurs-HDInsight-kluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Skapa ett resurs-HDInsight-kluster")

1. Under **grunderna**anger eller väljer du följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Prenumeration    |  Välj din Azure-prenumeration. |
    |Resursgrupp     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |Klusternamn   | Ange ett namn för Hadoop-klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. De första och sista tecknen i namnet får inte vara bindestreck. |
    |plats.    | Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. |
    |Kluster typ| Välj **Välj kluster typ**. Välj sedan **Hadoop** som kluster typ.|
    |Version|Standard versionen för kluster typen anges. Välj i list rutan om du vill ange en annan version.|
    |Användar namn och lösen ord för kluster inloggning    | Standard inloggnings namnet är **admin**. Lösen ordet måste bestå av minst 10 tecken och måste innehålla minst en siffra, en versal och en gemen bokstav, ett icke-alfanumeriskt tecken (förutom tecknen "" "\). Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |Secure Shell (SSH)-användarnamn | Standardanvändarnamnet är **sshuser**.  Du kan ange ett annat namn som SSH-användarnamn. |
    |Använd lösen ord för kluster inloggning för SSH| Markera den här kryss rutan om du vill använda samma lösen ord för SSH-användare som det du angav för kluster inloggnings användaren.|

    ![HDInsight Linux kom igång ger kluster grundläggande värden](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Ange grundläggande värden för att skapa ett HDInsight-kluster")

    Välj **Nästa: lagrings > >** för att gå vidare till lagrings inställningarna.

1. Ange följande värden på fliken **lagring** :

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Primär lagrings typ|Använd standardvärdet **Azure Storage**.|
    |Urvals metod|Använd standardvärdet **Select i list**.|
    |Primärt lagringskonto|Använd List rutan för att välja ett befintligt lagrings konto eller Välj **Skapa nytt**. Om du skapar ett nytt konto måste namnet vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener|
    |Container|Använd det automatiskt ifyllda värdet.|

    ![HDInsight Linux kom igång ger kluster lagrings värden](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Ange lagrings värden för att skapa ett HDInsight-kluster")

    Välj fliken **Granska + skapa** .

1. På fliken **Granska och skapa** kontrollerar du de värden som du valde i de föregående stegen.

    ![HDInsight Linux kom igång-kluster Sammanfattning](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux kom igång-kluster Sammanfattning")

1. Välj **Skapa**. Det tar cirka 20 minuter att skapa ett kluster.

När klustret har skapats visas en klusteröversiktssida i Azure Portal.

![Kluster inställningar för att komma igång med HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Egenskaper för HDInsight-kluster")

Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-kluster och dess standard lagrings konto måste samplaceras i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.

> [!NOTE]  
> För andra metoder för att skapa kluster och förstå de egenskaper som används i den här snabb starten, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Köra Apache Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här snabb starten använder du Ambari Hive-vyn från portalen. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Hive-data i HDInsight](hdinsight-use-hive.md).

1. Öppna Ambari genom att välja **Klusterinstrumentpanel** i föregående skärmbild.  Du kan också bläddra till `https://ClusterName.azurehdinsight.net`, där `ClusterName` är det kluster som du skapade i föregående avsnitt.

    ![HDInsight Linux kom igång-kluster instrument panel](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux kom igång-kluster instrument panel")

2. Ange det Hadoop-användarnamn och -lösenord som du angav när du skapade klustret. Standardanvändarnamnet är **admin**.

3. Öppna **Hive-vy** så som det visas på följande skärmbild:

    ![Välja Hive-vy från Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "HDInsight Hive Viewer-menyn")

4. På fliken **FRÅGA** klistrar du in följande HiveQL-instruktioner i kalkylbladet:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight Hive Visa Frågeredigeraren](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HDInsight Hive Visa Frågeredigeraren")

5. Välj **Kör**. Fliken **RESULTAT** visas under fliken **FRÅGA** och visar information om jobbet. 

    När frågan har slutförts visas resultatet av åtgärden på fliken **FRÅGA**. En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.

    ![HDInsight Apache Hive Visa resultat](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive Visa resultat")

6. Upprepa steg 4 och 5 för att köra följande fråga:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Du kan också spara frågans resultat. Välj menyknappen till höger och ange om du vill ladda ned resultatet som en CSV-fil eller lagra den på lagringskontot som är associerat till klustret.

    ![Spara resultatet av Apache Hive fråga](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Spara resultatet av Apache Hive fråga")

När du har slutfört ett Hive-jobb kan du [exportera resultaten till Azure SQL Database eller SQL Server-databasen](apache-hadoop-use-sqoop-mac-linux.md) och även [visualisera resultat i Excel](apache-hadoop-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel i en Apache log4j-fil](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa artikel och lär dig hur du kör ETL-åtgärder med Hadoop i HDInsight, kanske du vill hålla klustret igång. Detta beror på att du i självstudien måste skapa ett Hadoop-kluster igen. Men om du inte går igenom nästa artikel direkt måste du ta bort klustret nu.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Ta bort klustret och/eller Storage-kontot av standardtyp

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![Azure HDInsight ta bort kluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Ta bort Azure HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Linux-baserat HDInsight-kluster med hjälp av en Resource Manager-mall och hur du utför grundläggande Hive-frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med interaktiv fråga på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
