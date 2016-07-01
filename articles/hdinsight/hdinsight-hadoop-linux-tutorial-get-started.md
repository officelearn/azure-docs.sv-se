<properties
    pageTitle="Linux-vägledning: Komma igång med Hadoop och Hive | Microsoft Azure"
    description="Följ den här Linux-vägledningen för att komma igång med Hadoop i HDInsight. Lär dig hur du etablerar Linux-kluster och frågar efter data med Hive."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="03/16/2016"
    ms.author="jgao"/>

# Hadoop-vägledning: Komma igång med Linux-baserat Hadoop i HDInsight

> [AZURE.SELECTOR]
- [Linux-baserat](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Windows-baserat](hdinsight-hadoop-tutorial-get-started-windows.md)

Lär dig att skapa Linux-baserade [Hadoop](http://hadoop.apache.org/)-kluster i HDInsight och hur du kör Hive-jobb i HDInsight. [Apache Hive](https://hive.apache.org/) är den populäraste komponenten i Hadoop-ekosystemet. För närvarande finns HDInsight med fyra olika klustertyper: [Hadoop](hdinsight-hadoop-introduction), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) och [Storm](hdinsight-storm-overview.md).  Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga fyra klustertyper stöder Hive. En lista över stödda komponenter som hanteras i HDInsight finns i [Vad är nytt i de Hadoop-klusterversioner som tillhandahålls av HDInsight?](hdinsight-component-versioning.md)  

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### Krav

Innan du börjar den här vägledningen måste du ha:

- **Azure-prenumeration**: Gå till  [azure.microsoft.com/free](https://azure.microsoft.com/free) för att skapa ett kostnadsfritt provkonto för en månad.

## Skapa kluster

De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I det här avsnittet skapar du ett Linux-baserat Hadoop-kluster i HDInsight med [Azure ARM-mallen](../resource-group-template-deploy.md). ARM-mallen är helt anpassningsbar, vilket gör det enkelt att skapa Azure-resurser som HDInsight. Erfarenhet av Azure ARM-mallen krävs inte för att kunna följa de här självstudierna. Mer information om andra metoder för att skapa kluster och förstå de egenskaper som tillämpas i de här självstudierna finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Den ARM-mall som används i de här självstudierna finns i den offentliga blob-behållaren [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json). 

1. Klicka på följande bild för att logga in till Azure och öppna ARM-mallen i Azure-portalen. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Skriv följande på bladet **Parametrar**:

    ![HDInsight Linux komma igång ARM-mall i portal](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).

    - **Klusternamn**: Ange ett namn för det Hadoop-kluster som du vill skapa.
    - **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard **admin**.
    - **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn. 
    
    Andra parametrar är valfria för att följa de här självstudierna. Du kan lämna dem som de är. 
    
    Varje kluster är beroende av ett Azure Blob Storage-konto. Det kallas vanligtvis Storage-konto av standardtyp. HDInsight-kluster och Storage-kontot av standardtyp måste finnas i samma Azure-region. Storage-kontot tas inte bort om du tar bort kluster. I mallen definieras namnet för Storage-kontot av standardtyp som det klusternamn som har tillägget ”store”. 
    
3. Spara parametrarna genom att klicka på **OK**.
4. På bladet **Anpassad distribution** anger du **Nytt resursgruppnamn** för att skapa en ny resursgrupp.  Resursgruppen är en behållare som grupperar klustret, det beroende lagringskontot och andra s. Resursgruppens plats kan skilja sig åt från klusterplatsen.
5. Klicka på **Juridiska villkor** och sedan på **Skapa**.
6. Kontrollera att rutan **PIN-kod för instrumentpanelen** är markerad och klicka sedan på **Skapa**. En ny panel visas med rubriken **Skicka malldistribution**. Det tar cirka 20 minuter att skapa ett kluster. 
7.  När klustret skapas, ändras namnet i rubriktexten på panelen till det resursgruppsnamn som du har angett. Portalen öppnar automatiskt två blad med klustret och klusterinställningarna. 

    ![HDInsight Linux komma igång klusterinställningar](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).

    Det finns två resurser i listan, klustret och Storage-kontot av standardtyp.

##Köra Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här vägledningen kommer du att använda Ambari Hive-vyn från portalen för att köra vissa Hive-jobb. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Hive-data i HDInsight](hdinsight-use-hive.md).

1. Gå till **https://&lt;Klusternamn >. azurehdinsight.net**, där &lt;Klusternamn > är klustret du skapade i föregående avsnitt för att öppna Ambari.
2. Ange det Hadoop-användarnamn och -lösenord som du angav i föregående avsnitt. Standardanvändarnamnet är **admin**.
3. Öppna **Hive-vy** så som det visas på följande skärmbild:

    ![Välja ambari-vyer](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. I avsnittet __Frågeredigeraren__ på sidan klistrar du in följande HiveQL-instruktioner i kalkylbladet:

        SHOW TABLES;

    >[AZURE.NOTE] Hive kräver semikolon.       
        
5. Klicka på __Kör__. Avsnittet __Frågeprocessresultat__ bör visas under frågeredigeraren och visa information om jobbet. 

    När frågan har slutförts visas resultatet av åtgärden i avsnittet __Frågeprocessresultat__. En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.

    ![HDInsight Hive-vyer](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

6. Upprepa steg 4 och 5 för att köra följande fråga:

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] Observera listrutan __Spara resultaten__ högst upp till vänster om avsnittet __Frågeprocessresultat__. Du kan använda denna antingen för att hämta resultaten eller spara dem i HDInsight-lagringen som en CSV-fil.

7. Klicka på **Historik** för att hämta en lista över jobben.

När du har slutfört ett Hive-jobb kan du [exportera resultaten till Azure SQL Database eller SQL Server-databasen](hdinsight-use-sqoop-mac-linux.md) och även [visualisera resultat i Excel](hdinsight-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Hive och HiveQL med Hadoop i HDInsight för att analysera ett exempel på en Apache log4j-fil](hdinsight-use-hive.md).

##Rensa vägledningen

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. 

>[AZURE.NOTE] Med hjälp av [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) kan du skapa HDInsight-kluster på begäran och konfigurera en TimeToLive-inställning för att ta bort klustren automatiskt. 

**Ta bort klustret och/eller Storage-kontot av standardtyp**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till portalens instrumentpanel och klicka på panelen med det resursgruppsnamn som du använde när du skapade klustret.
3. Klicka på **Ta bort** på resursbladet för att ta bort resursgruppen som innehåller klustret och Storage-kontot av standardtyp eller klicka på klusternamnet på panelen **Resurser** och sedan på **Ta bort** på klusterbladet. Tänk på att Storage-kontot tas bort när du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## Nästa steg

I den här vägledningen har du fått veta hur du skapar ett Linux-baserat HDInsight-kluster med en ARM-mall och hur du utför grundläggande Hive-frågor.

Mer information om att analysera data med HDInsight finns här:

- Mer information om att använda Hive med HDInsight, inklusive hur du gör Hive-frågor från Visual Studio, finns i [Använda Hive med HDInsight][hdinsight-use-hive].

- Du kan läsa mer om Pig, ett språk som används för att omvandla data, i [Använda Pig med HDInsight][hdinsight-use-pig].

- Du kan läsa mer om MapReduce, ett sätt att skriva appar som bearbetar data i Hadoop, i [Använda MapReduce med HDInsight][hdinsight-use-mapreduce].

- Du kan läsa mer om hur du använder HDInsight Tools för Visual Studio för att analysera data i HDInsight i [Komma igång med Visual Studio Hadoop-verktyg för HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Om du är redo att börja arbeta med dina egna data och vill veta mer om hur data lagras i HDInsight eller om att hämta data till HDInsight, hittar du mer information här:

- Mer information om hur HDInsight använder Azure Blob Storage finns i [Använda Azure Blob Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight][hdinsight-upload-data].

Mer information om att skapa eller hantera HDInsight-kluster hittar du här:

- Mer information om att hantera ditt Linux-baserade HDInsight-kluster finns i [Hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md).

- Mer information om de alternativ som du kan välja när du skapar ett HDInsight-kluster finns i [Skapa HDInsight i Linux med anpassade alternativ](hdinsight-hadoop-provision-linux-clusters.md).

- Om du är bekant med Linux och Hadoop, men vill få närmare information om Hadoop i HDInsight, hittar du mer information i [Arbeta med HDInsight i Linux](hdinsight-hadoop-linux-information.md). Detta ger information som:

    * URL:er för de tjänster som finns i klustret, till exempel Ambari och WebHCat
    * Platsen för Hadoop-filer och -exempel i det lokala filsystemet
    * Användning av Azure Storage (WASB) i stället för HDFS som datalagringsutrymme av standardtyp


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png



<!--HONumber=Jun16_HO2-->


