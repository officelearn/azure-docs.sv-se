---
title: 'Snabbstart: Apache Hadoop, Apache Hive & Azure HDInsight-portal'
description: I den här snabbstarten använder du Azure-portalen för att skapa ett HDInsight Hadoop-kluster
keywords: hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 8939d9b342094342d576c00cf02e622286c8fc0f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130576"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snabbstart: Skapa Apache Hadoop-kluster i Azure HDInsight med Azure-portalen

I den här artikeln får du lära dig hur du skapar Apache Hadoop-kluster i HDInsight med Azure-portalen och sedan kör Apache Hive-jobb i HDInsight. De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I den här artikeln utför du alla tre aktiviteterna. Detaljerade förklaringar av tillgängliga konfigurationer finns i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur portalen används för att skapa kluster finns [i Skapa kluster i portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

I den här snabbstarten använder du Azure Portal för att skapa ett HDInsight Hadoop-kluster. Du kan också skapa ett kluster med hjälp av [Azure Resource Manager-mallen](apache-hadoop-linux-tutorial-get-started.md).

För närvarande levereras HDInsight med [sju olika klustertyper](../hdinsight-overview.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över komponenter som stöds och som hanteras i HDInsight finns i [Vad är nytt i de Apache Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med hjälp av Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj + Skapa **en resurs**på den övre menyn .

    ![Skapa ett HDInsight-kluster för resurs](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Skapa ett HDInsight-kluster för resurs")

1. Välj **Analytics** > **Azure HDInsight** för att gå till **sidan Skapa HDInsight-kluster.**

1. Ange följande information på fliken **Grunderna:**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Prenumeration    |  Välj den Azure-prenumeration som används för klustret i listrutan. |
    |Resursgrupp     | Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
    |Klusternamn   | Ange ett globalt unikt namn. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. Det första och sista tecknet i namnet kan inte vara bindestreck. |
    |Region    | Välj en region där klustret skapas i listrutan.  Välj en plats närmare så får du bättre prestanda. |
    |Klustertyp| Välj **Välj klustertyp**. Välj sedan **Hadoop** som klustertyp.|
    |Version|Välj en **version**i listrutan . Använd standardversionen om du inte vet vad du ska välja.|
    |Användarnamn och lösenord för klusterinloggning    | Standardinloggningsnamnet är **admin**. Lösenordet måste vara minst 10 tecken långt och innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (utom tecknen ' " ' . \) Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |Secure Shell (SSH)-användarnamn | Standardanvändarnamnet är **sshuser**.  Du kan ange ett annat namn som SSH-användarnamn. |
    |Använda lösenord för klusterinloggning för SSH| Markera den här kryssrutan om du vill använda samma lösenord för SSH-användare som det du angav för klusterinloggningsanvändaren.|

    ![HDInsight Linux komma igång ger kluster grundläggande värden](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Ange grundläggande värden för att skapa ett HDInsight-kluster")

    Välj **nästa: Lagring >>** för att gå vidare till lagringsinställningarna.

1. Ange följande värden på fliken **Lagring:**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Primär lagringstyp|Använd standardvärdet **Azure Storage**.|
    |Urvalsmetod|Använd standardvärdet **Välj från listan**.|
    |Primärt lagringskonto|Använd listrutan för att välja ett befintligt lagringskonto eller välj **Skapa nytt**. Om du skapar ett nytt konto måste namnet vara mellan 3 och 24 tecken långt och kan innehålla siffror och gemener endast|
    |Container|Använd det automatiskt befolkade värdet.|

    ![HDInsight Linux komma igång ger klusterlagringsvärden](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Ange lagringsvärden för att skapa ett HDInsight-kluster")

    Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste samlokaliseras i samma Azure-region. Om du tar bort kluster tas inte lagringskontot bort.

    Välj fliken **Granska + skapa.**

1. Kontrollera de värden som du valde i de tidigare stegen på fliken **Granska + skapa.**

    ![HDInsight Linux komma igång kluster sammanfattning](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux komma igång kluster sammanfattning")

1. Välj **Skapa**. Det tar cirka 20 minuter att skapa ett kluster.

    När klustret har skapats visas en klusteröversiktssida i Azure Portal.

    ![HDInsight Linux komma igång klusterinställningar](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Egenskaper för HDInsight-kluster")

## <a name="run-apache-hive-queries"></a>Köra Apache Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här snabbstarten använder du vyn Ambari Hive från portalen. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Hive-data i HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive View är inte tillgängligt i HDInsight 4.0.

1. Öppna Ambari genom att välja **Klusterinstrumentpanel** i föregående skärmbild.  Du kan också `https://ClusterName.azurehdinsight.net` `ClusterName` bläddra till var är klustret som du skapade i föregående avsnitt.

    ![HDInsight Linux komma igång kluster instrumentpanel](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux komma igång kluster instrumentpanel")

2. Ange det Hadoop-användarnamn och -lösenord som du angav när du skapade klustret. Standardanvändarnamnet är **admin**.

3. Öppna **Hive-vy** så som det visas på följande skärmbild:

    ![Välja Hive View från Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menyn HDInsight Hive Viewer")

4. På fliken **FRÅGA** klistrar du in följande HiveQL-instruktioner i kalkylbladet:

    ```sql
    SHOW TABLES;
    ```

    ![Frågeredigeraren för HDInsight Hive View](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Frågeredigeraren för HDInsight Hive View")

5. Välj **Kör**. Fliken **RESULTAT** visas under fliken **FRÅGA** och visar information om jobbet. 

    När frågan är klar visar fliken **QUERY** resultatet av åtgärden. En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.

    ![HdInsight Apache Hive visa resultat](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HdInsight Apache Hive visa resultat")

6. Upprepa steg 4 och 5 för att köra följande fråga:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Du kan också spara frågans resultat. Välj menyknappen till höger och ange om du vill ladda ned resultatet som en CSV-fil eller lagra den på lagringskontot som är associerat till klustret.

    ![Spara resultatet av Apache Hive-frågan](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Spara resultatet av Apache Hive-frågan")

När du har slutfört ett Hive-jobb kan du [exportera resultaten till Azure SQL Database eller SQL Server-databasen](apache-hadoop-use-sqoop-mac-linux.md), du kan också [visualisera resultaten med Excel](apache-hadoop-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel i en Apache log4j-fil](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa artikel för att lära dig hur du kör ETL-åtgärder med Hadoop på HDInsight, kanske du vill hålla klustret igång. Detta beror på att du i självstudien måste skapa ett Hadoop-kluster igen. Men om du inte går igenom nästa artikel direkt måste du ta bort klustret nu.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Ta bort klustret och/eller Storage-kontot av standardtyp

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![Ta bort Azure HDInsight-kluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Ta bort Azure HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Linux-baserat HDInsight-kluster med hjälp av en Resource Manager-mall och hur du utför grundläggande Hive-frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med interaktiv fråga på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
