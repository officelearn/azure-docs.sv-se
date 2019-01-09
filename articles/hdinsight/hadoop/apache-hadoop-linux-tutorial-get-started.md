---
title: 'Snabbstart: Skapa Apache Hadoop-kluster med hjälp av Resource Manager och fråga efter data med Apache Hive – Azure HDInsight'
description: Lär dig hur du skapar HDInsight-kluster och kör frågor mot data med Hive.
keywords: hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 12/27/2018
ms.openlocfilehash: bec4e4271fa9f1e2333e9414268832fe77b722cb
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811230"
---
# <a name="quickstart-get-started-with-apache-hadoop-and-apache-hive-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Komma igång med Apache Hadoop och Apache Hive i Azure HDInsight med en Resource Manager-mall

I den här artikeln får du lära dig hur du skapar [Adope Hadoop](https://hadoop.apache.org/)-kluster i HDInsight med en Resource Manager-mall och sedan kör Hive-jobb i HDInsight. De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I den här artikeln utför du alla tre aktiviteterna.

I den här snabbstarten använder du en Resource Manager-mall för att skapa ett HDInsight Hadoop-kluster. Du kan även skapa ett kluster med [Azure Portal](apache-hadoop-linux-create-cluster-get-started-portal.md).  Liknande mallar kan visas på [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=hdinsight).

För närvarande innehåller HDInsight [sju olika klustertyper](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över stödda komponenter som hanteras i HDInsight finns i [Vad är nytt i de Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Skapa ett Hadoop-kluster

I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med en Azure Resource Manager-mall. Du behöver inte ha någon erfarenhet av Resource Manager-mallar för att kunna följa den här artikeln. 

1. Klicka på knappen **Distribuera till Azure** om du vill logga in på Azure och öppna Resource Manager-mallen i Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange eller välj de värde som föreslås i följande skärmbild:

    > [!NOTE]  
    > De värden som du anger måste vara unika och bör följa riktlinjerna för namngivning. Mallen utför inte några verifieringskontroller. Om de värden som du anger redan används, eller inte följer riktlinjerna, får du ett felmeddelande när du har skickat mallen.    
    
    ![HDInsight Linux – Komma igång med Resource Manager-mallen i portalen](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuera Hadoop-kluster i HDInsigut med hjälp av Azure-portalen och en mall för resursgrupphantering")

    Ange eller välj följande värden:
    
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Prenumeration**     |  Välj din Azure-prenumeration. |
    |**Resursgrupp**     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |**Plats**     | Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. |
    |**Klusternamn**     | Ange ett namn för Hadoop-klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet får bara innehålla gemena bokstäver, siffror och bindestreck och måste börja med en bokstav.  Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.  Namnet måste också innehålla mellan 3 och 59 tecken. |
    |**Klustertyp**     | Välj **Hadoop**. |
    |**Inloggningsnamn och lösenord för klustret**     | Standardinloggningsnamnet är **admin**. Lösenordet måste bestå av minst 10 tecken och måste innehålla åtminstone en siffra, en versal, en gemen, ett alfanumeriskt tecken (förutom tecknen ' " ` \). Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |**SSH-användarnamn och lösenord**     | Standardanvändarnamnet är **sshuser**.  Du kan byta namn på SSH-användarnamn.  SSH-användarlösenordet har samma krav som lösenordet för klusterinloggning.|
       
    Vissa egenskaper har hårdkodats i mallen.  Du kan konfigurera dessa värden från mallen. Fler förklaringar av dessa egenskaper finns i [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Välj **Jag godkänner villkoren som anges ovan** och välj sedan **Köp**. Du får ett meddelande om att distributionen pågår.  Det tar cirka 20 minuter att skapa ett kluster.

4. När klustret har skapats får du ett meddelande om att **distributionen har lyckats** tillsammans med en länk för att **gå till resursgruppen**.  På sidan **Resursgrupp** visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-klustret och dess lagringskonto av standardtyp måste finnas i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.

> [!NOTE]  
> Mer information om andra metoder för att skapa kluster och förstå de egenskaper som tillämpas i de här självstudierna finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).       


## <a name="use-vscode-to-run-hive-queries"></a>Använda VSCode för att köra Hive-frågor

Hämtningsanvisningar för HDInsight-verktyg i VSCode finns i [Använd Azure HDInsight Tools för Visual Studio Code](../hdinsight-for-vscode.md).

### <a name="submit-interactive-hive-queries"></a>Skicka interaktiva Hive-frågor

Du kan skicka interaktiva Hive-frågor till interaktiva HDInsight-frågekluster med HDInsight-verktyg för VSCode.

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte redan har gjort det.

2. Anslut till ditt Azure-konto och konfigurera sedan standardklustret om du inte redan gjort det.

3. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Högerklicka på skriptredigeraren och välj sedan **HDInsight: Hive Interactive** för att skicka frågan. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. Strax därefter efter visas resultatet av frågan i en ny flik.

   ![Interaktiva Hive-resultat](./media/apache-hadoop-linux-tutorial-get-started/interactive-hive-result.png)

    - Panelen **RESULTAT**: Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil till en lokal sökväg, eller markera flera rader.

    - Panelen **MEDDELANDEN**: När du väljer **Rad**nummer, leder det till den första raden i det aktuella skriptet.

Interaktiva frågor tar mycket kortare tid än [att köra ett Apache Hive-batchjobb](#submit-hive-batch-scripts).

### <a name="submit-hive-batch-scripts"></a>Skicka Hive-batchskript

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte redan har gjort det.

2. Anslut till ditt Azure-konto och konfigurera sedan standardklustret om du inte redan gjort det.

3. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Högerklicka på skriptredigeraren och välj sedan **HDInsight: Hive Batch** för att skicka ett Hive-jobb. 

5. Välj ett kluster som du vill skicka.  

    När du har skickat ett Hive-jobb visas information om resultat och jobb-ID i panelen **UTDATA**. Hive-jobb öppnas också **WEBBLÄSARE**, vilket visar jobbloggar och status i realtid.

   ![skicka resultat om Hive-jobbet](./media/apache-hadoop-linux-tutorial-get-started/submit-Hivejob-result.png)

Det tar mycket kortare tid att [skicka interaktiva Apache Hive-frågor](#submit-interactive-hive-queries) än att skicka ett batchjobb.

## <a name="use-visualstudio-to-run-hive-queries"></a>Använd VisualStudio för att köra Hive-frågor

Hämtningsanvisningar för HDInsight-verktyg i Visual Studie finns i [Använd Data Lake-verktyg för Visual Studio](./apache-hadoop-visual-studio-tools-get-started.md).

### <a name="run-hive-queries"></a>Köra Hive-frågor

Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

Skapa och köra ad-hoc-frågor:

1. I **Server Explorer** väljer du **Azure** > **HDInsight-kluster**.

2. Högerklicka på det kluster där du vill köra frågan och välj sedan **Skriv en Hive-fråga**.  

3. Ange Hive-frågorna. 

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver **SELECT * FROM** visar IntelliSense en lista över alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF.
   
    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 1](./media/apache-hadoop-linux-tutorial-get-started/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 2](./media/apache-hadoop-linux-tutorial-get-started/vs-intellisense-column-name.png "U-SQL IntelliSense")
   
   > [!NOTE]  
   > IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.
   > 
   
4. Välj **Skicka** eller **Skicka (avancerat)**. 
   
    ![Skärmbild av att skicka en hive-fråga](./media/apache-hadoop-linux-tutorial-get-started/vs-batch-query.png)

   Om du väljer det avancerade alternativet för att skicka konfigurerar du **Jobbnamn**, **Argument**, **Ytterligare konfigurationer** och **Statuskatalog** för skriptet:

    ![Skärmbild av HDInsight Hadoop Hive-fråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Skicka frågor")

   Köra interaktiva Hive-frågor

   * klicka på nedåtpilen för att välja **interaktiva**. 
   
   * Klicka på **Kör**.

   ![Skärmbild på att köra interaktiva Hive-frågor](./media/apache-hadoop-linux-tutorial-get-started/vs-execute-hive-query.png)

Så här skapar och kör du en Hive-lösning:

1. På menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
2. Välj **HDInsight** i den vänstra fönsterrutan. I den mellersta fönsterrutan väljer du **Hive-program**. Ange egenskaper och välj sedan **OK**.
   
    ![Skärmbild av ett nytt Hive-projekt i HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Skapa Hive-program från Visual Studio")
3. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna skriptet.
4. Ange Hive-frågorna och skicka. (Se steg 3 och 4 ovan)  



## <a name="run-hive-queries"></a>Köra Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här självstudiekursen ska du använda Ambari Hive-vyn från portalen. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Apache Hive-data i HDInsight](hdinsight-use-hive.md).

1. Välj **Ambari-vyer** för att öppna Ambari från panelen för **klusterinstrumentpaneler**.  Du kan också gå till  **https://&lt;Klusternamn&gt;.azurehdinsight.net** där &lt;Klusternamn&gt; är klustret du skapade i föregående avsnitt.

    ![Komma igång med HDInsight Linux – klusterinstrumentpanel](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Komma igång med HDInsight Linux – klusterinstrumentpanel")

2. Ange det Hadoop-användarnamn och -lösenord som du angav när du skapade klustret. Standardanvändarnamnet är **admin**.

3. Välj **Hive View 2.0** så som det visas på följande skärmbild:
   
    ![Välja Ambari-vyer](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "HDInsight Hive Viewer-menyn")

4. På fliken **FRÅGA** klistrar du in följande HiveQL-instruktioner i kalkylbladet:
   
        SHOW TABLES;

    ![HDInsight Hive-vyer](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "HDInsight Hive visa frågeredigeraren")
   
   > [!NOTE]  
   > Hive kräver semikolon.       


5. Välj **Kör**. Fliken **RESULTAT** visas under fliken **FRÅGA** och visar information om jobbet. 
   
    När frågan har slutförts visas resultatet av åtgärden på fliken **FRÅGA**. En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.
   
    ![HDInsight Hive-vyer](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "HDInsight Hive visa frågeredigeraren")

6. Upprepa steg 4 och 5 för att köra följande fråga:
   
        SELECT * FROM hivesampletable;
   
7. Du kan också spara frågans resultat. Välj menyknappen till höger och ange om du vill ladda ned resultatet som en CSV-fil eller lagra den på lagringskontot som är associerat till klustret.

    ![Spara resultatet från Hive-frågan](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Spara resultatet från Hive-frågan")

När du har slutfört ett Hive-jobb kan du [exportera resultaten till Azure SQL Database eller SQL Server-databasen](apache-hadoop-use-sqoop-mac-linux.md) och även [visualisera resultat i Excel](apache-hadoop-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel i en Apache log4j-fil](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. 

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa kurs för att lära dig hur man kör ETL-åtgärder med Hadoop på HDInsight vill du kanske låta klustret köra vidare. Anledningen till detta är att du i den här självstudiekursen måste skapa ett Hadoop-kluster igen. Men om du inte tänker göra nästa kurs direkt måste du ta bort klustret nu.

**Ta bort klustret och/eller Storage-kontot av standardtyp**

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![HDInsight-borttagningskluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Ta bort HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du fått veta hur du skapar ett Linux-baserat HDInsight-kluster med en Resource Manager-mall och hur du utför grundläggande Hive-frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med Apache Hive på HDInsight ](../hdinsight-analyze-flight-delay-data-linux.md)

Om du är redo att börja arbeta med dina egna data och vill veta mer om hur data lagras i HDInsight eller om att hämta data till HDInsight, hittar du mer information i följande artiklar:

* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Information om hur du skapar ett HDInsight-kluster med Data Lake Storage finns i [Snabbstart: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](../hdinsight-upload-data.md).

Mer information om att analysera data med HDInsight finns i följande artiklar:

* Mer information om att använda Hive med HDInsight, inklusive hur du utför Hive-frågor från Visual Studio, finns i [Använda Apache Hive med HDInsight](hdinsight-use-hive.md).
* Du kan läsa mer om Pig, ett språk som används för att omvandla data, i [Använda Apache Pig med HDInsight](hdinsight-use-pig.md).
* Du kan läsa mer om MapReduce, ett sätt att skriva appar som bearbetar data i Hadoop, i [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md).
* Du kan läsa mer om hur du använder HDInsight Tools för Visual Studio för att analysera data i HDInsight i [Komma igång med Visual Studio Hadoop-verktyg för HDInsight](apache-hadoop-visual-studio-tools-get-started.md).
* Du kan läsa mer om hur du använder HDInsight Tools för VSCode för att analysera data i HDInsight i [Använda HDInsight-verktyg för Visual Studio-kod](../hdinsight-for-vscode.md).


Mer information om att skapa eller hantera HDInsight-kluster hittar du i följande artiklar:

* Mer information om att hantera ditt Linux-baserade HDInsight-kluster finns i [Hantera HDInsight-kluster med Apache Ambari](../hdinsight-hadoop-manage-ambari.md).
* Mer information om de alternativ som du kan välja när du skapar ett HDInsight-kluster finns i [Skapa HDInsight i Linux med anpassade alternativ](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
