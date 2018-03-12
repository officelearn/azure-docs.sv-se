---
title: "Komma igång med Hadoop och Hive i Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du skapar HDInsight-kluster och kör frågor mot data med Hive."
keywords: "hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 11c0a583ab671b27a5c5a65c4bb36032a8582466
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Hadoop-självstudiekurs: Komma igång med Hadoop i HDInsight

Lär dig hur du skapar [Hadoop](http://hadoop.apache.org/)-kluster i HDInsight och hur du kör Hive-jobb i HDInsight. [Apache Hive](https://hive.apache.org/) är den populäraste komponenten i Hadoop-ekosystemet. För närvarande innehåller HDInsight [sju olika klustertyper](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över stödda komponenter som hanteras i HDInsight finns i [Vad är nytt i de Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här vägledningen måste du ha:

* **Azure-prenumeration**: Gå till  [azure.microsoft.com/free](https://azure.microsoft.com/free) för att skapa ett kostnadsfritt provkonto för en månad.

## <a name="create-cluster"></a>Skapa kluster

De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med en [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md). Du behöver inte ha någon erfarenhet av Resource Manager-mallar för att kunna följa de här självstudierna. Mer information om andra metoder för att skapa kluster och förstå de egenskaper som tillämpas i de här självstudierna finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

Resource Manager-mallen som används i den här självstudien finns på [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Klicka på följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange eller välj följande värden:

    * **Prenumeration**: Välj din Azure-prenumeration.
    * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en behållare med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. 
    * **Plats**: Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. 
    * **Klustertyp**: Välj **hadoop** för den här självstudien.
    * **Klusternamn**: Ange ett namn för Hadoop-klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. Observera att det första och sista tecknet i namnet inte kan vara ett bindestreck.
    * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard **admin**. Lösenordet måste bestå av minst 10 tecken och måste innehålla åtminstone en siffra, en versal, en gemen, ett alfanumeriskt tecken (förutom tecknen ' " ` \). 
    * **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn på SSH-användarnamn.  SSH-användarlösenordet har samma krav som lösenordet för klusterinloggning.   
   
    ![HDInsight Linux komma igång med Resource Manager-mall i portalen](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuera Hadoop-kluster i HDInsigut med hjälp av Azure portal och en resursgrupphanterarmall").
     
    Vissa egenskaper har hårdkodats i mallen.  Du kan konfigurera dessa värden från mallen.
    
    Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-kluster och Storage-kontot av standardtyp måste finnas i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster. 
        
    Fler förklaringar av dessa egenskaper finns i [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Välj **Jag godkänner villkoren som anges ovan** och **Fäst på instrumentpanelen** och klicka sedan på **Köp**. Du ska se en ny panel som heter **Skicka malldistribution** på portalens instrumentpanel. Det tar cirka 20 minuter att skapa ett kluster. 

4. När klustret skapas, ändras namnet i rubriktexten på panelen till det resursgruppsnamn som du har angett. Klicka på panelen så ser du både klustret och standardlagringen för klustret i listan.
   
    ![HDInsight Linux komma igång resursgrupp](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Azure HDInsight klusterresursgrupp")

4. Klicka på klusternamnet för att öppna klustret.

   ![HDInsight Linux komma igång klusterinställningar](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "HDInsight-klusteregenskaper")


## <a name="run-hive-queries"></a>Köra Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här självstudiekursen ska du använda Ambari Hive-vyn från portalen. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Hive-data i HDInsight](hdinsight-use-hive.md).

1. För att öppna Ambari från föregående skärmbild klickar du på **Klusterinstrumentpanel** och sedan på **HDInsight-klusterinstrumentpanel**.  Du kan också gå till  **https://&lt;Klusternamn>.azurehdinsight.net**, där &lt;Klusternamn> är klustret du skapade i föregående avsnitt.

2. Ange det Hadoop-användarnamn och -lösenord som du angav när du skapade klustret. Standardanvändarnamnet är **admin**.

3. Öppna **Hive-vy** så som det visas på följande skärmbild:
   
    ![Välja Ambari-vyer](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "HDInsight Hive Viewer-menyn")

4. På fliken **FRÅGA** klistrar du in följande HiveQL-instruktioner i kalkylbladet:
   
        SHOW TABLES;

    ![HDInsight Hive-vyer](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "HDInsight Hive visa frågeredigeraren")
   
   > [!NOTE]
   > Hive kräver semikolon.       
   > 
   > 

5. Klicka på **Kör**. Fliken **RESULTAT** visas under fliken **FRÅGA** och visar information om jobbet. 
   
    När frågan har slutförts visas resultatet av åtgärden på fliken **FRÅGA**. En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.
   
    ![HDInsight Hive-vyer](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "HDInsight Hive visa frågeredigeraren")

6. Upprepa steg 4 och 5 för att köra följande fråga:
   
        SELECT * FROM hivesampletable;
   
7. Du kan också spara frågans resultat. Klicka på menyknappen till höger och ange om du vill ladda ned resultatet som en CSV-fil eller lagra den på lagringskontot som är associerat till klustret.

    ![Spara resultatet från Hive-frågan](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Spara resultatet från Hive-frågan")



När du har slutfört ett Hive-jobb kan du [exportera resultaten till Azure SQL Database eller SQL Server-databasen](apache-hadoop-use-sqoop-mac-linux.md) och även [visualisera resultat i Excel](apache-hadoop-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Hive och HiveQL med Hadoop i HDInsight för att analysera ett exempel på en Apache log4j-fil](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Rensa vägledningen
När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. 

> [!NOTE]
> Med hjälp av [Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md) kan du skapa HDInsight-kluster på begäran och konfigurera en TimeToLive-inställning för att ta bort klustren automatiskt. 
> 
> 

**Ta bort klustret och/eller Storage-kontot av standardtyp**

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Du kan klicka på **Ta bort** om du endast vill ta bort klustret men behålla standardlagringskontot:

    ![HDInsight-borttagningskluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Ta bort HDInsight-kluster")
1. Till höger klickar du på resursgruppens namn (markerat i föregående skärmbild) för att öppna resursgruppsidan.
2. Klicka på **Ta bort resursgrupp** för att ta bort resursgruppen som innehåller klustret och Storage-kontot av standardtyp eller klicka på klusternamnet på panelen **Resurser** och sedan på **Ta bort** på klusterbladet. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått veta hur du skapar ett Linux-baserat HDInsight-kluster med en Resource Manager-mall och hur du utför grundläggande Hive-frågor.

Mer information om att analysera data med HDInsight finns i följande artiklar:

* Mer information om att använda Hive med HDInsight, inklusive hur du utför Hive-frågor från Visual Studio, finns i [Använda Hive med HDInsight](hdinsight-use-hive.md).
* Du kan läsa mer om Pig, ett språk som används för att omvandla data, i [Använda Pig med HDInsight](hdinsight-use-pig.md).
* Du kan läsa mer om MapReduce, ett sätt att skriva appar som bearbetar data i Hadoop, i [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md).
* Du kan läsa mer om hur du använder HDInsight Tools för Visual Studio för att analysera data i HDInsight i [Komma igång med Visual Studio Hadoop-verktyg för HDInsight](apache-hadoop-visual-studio-tools-get-started.md).

Om du är redo att börja arbeta med dina egna data och vill veta mer om hur data lagras i HDInsight eller om att hämta data till HDInsight, hittar du mer information i följande artiklar:

* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](../hdinsight-upload-data.md).

Mer information om att skapa eller hantera HDInsight-kluster hittar du i följande artiklar:

* Mer information om att hantera ditt Linux-baserade HDInsight-kluster finns i [Hantera HDInsight-kluster med Ambari](../hdinsight-hadoop-manage-ambari.md).
* Mer information om de alternativ som du kan välja när du skapar ett HDInsight-kluster finns i [Skapa HDInsight i Linux med anpassade alternativ](../hdinsight-hadoop-provision-linux-clusters.md).
* Om du är bekant med Linux och Hadoop, men vill få närmare information om Hadoop i HDInsight, hittar du mer information i [Arbeta med HDInsight i Linux](../hdinsight-hadoop-linux-information.md). Detta ger information som:
  
  * URL:er för de tjänster som finns i klustret, till exempel Ambari och WebHCat
  * Platsen för Hadoop-filer och -exempel i det lokala filsystemet
  * Användning av Azure Storage (WASB) i stället för HDFS som datalagringsutrymme av standardtyp

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


