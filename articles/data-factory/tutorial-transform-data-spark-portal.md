---
title: Transformera data med Spark i Azure Data Factory | Microsoft Docs
description: "Den här självstudiekursen innehåller stegvisa instruktioner för hur du transformerar data genom att använda en Spark-aktivitet i Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: 8bd9382ed5a855368533c6bf2305682861c109c0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformera data i molnet genom att använda Spark-aktivitet i Azure Data Factory
I den här självstudien använder du Azure Portal för att skapa en Data Factory-pipeline som transformerar data med Spark-aktivitet och en länkad HDInsight-tjänst på begäran. I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa en pipeline med en Spark-aktivitet
> * Utlösa en pipelinekörning
> * Övervaka pipelinekörningen.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Azure Storage-konto**. Du skapar ett Python-skript och en indatafil och överför dem till Azure Storage. Spark-programmets utdata lagras på det här lagringskontot. Spark-klustret på begäran använder samma lagringskonto som den primära lagringen.  
* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Överföra Python-skriptet till ditt Blob Storage-konto
1. Skapa en Python-fil med namnet **WordCount_Spark.py** med följande innehåll: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Ersätt **&lt;storageAccountName&gt;** med namnet på ditt Azure-konto. Spara sedan filen. 
3. Skapa en behållare med namnet **adftutorial** i Azure Blob Storage om den inte finns. 
4. Skapa en mapp med namnet **spark**.
5. Skapa en undermapp med namnet **script** under mappen **spark**. 
6. Överför filen **WordCount_Spark.py** till undermappen **script**. 


### <a name="upload-the-input-file"></a>Överför indatafilen
1. Skapa en fil med namnet **minecraftstory.txt** med lite text. Spark-programmet räknar antalet ord i texten. 
2. Skapa en undermapp med namnet `inputfiles` i mappen `spark`. 
3. Överför `minecraftstory.txt` till mappen `inputfiles`. 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sidan Ny datafabrik](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Namnet är inte tillgängligt – fel](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Vissa av stegen i den här snabbstarten förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. För närvarande kan du endast skapa datafabriker i Data Factory V2 i regionerna USA, östra; USA; östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du har skapat två länkade tjänster i det här avsnittet: 
    
- Den **länkade Azure Storage-tjänsten** som länkar ditt Azure Storage-konto till datafabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också Spark-skriptet som ska köras. 
- En **på begäran länkad HDInsight-tjänst**. Azure Data Factory skapar automatiskt ett HDInsight-kluster, kör Spark-programmet och tar sedan bort HDInsight-klustret när det har varit inaktivt under en förkonfigurerad tid. 

### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. På sidan **kom igång** växlar du till fliken **Redigera** på den vänstra panelen som på följande bild: 

    ![Ikonen Skapa en pipeline](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Klicka på **Anslutningar** längst ned i fönstret och sedan på **+Ny**. 

    ![Knapp för ny anslutning](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 

    ![Välj Azure Blob Storage](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Välj ditt **Azure Storage-kontonamn** och klicka på **Spara**. 

    ![Ange Blob Storage-konto](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Skapa en på begäran länkad HDInsight-tjänst

1. Klicka på knappen **+Ny** igen för att skapa ytterligare en länkad tjänst. 
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure HDInsight** och klickar på **Fortsätt**. 

    ![Välj Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureHDInsightLinkedService** som **namn**.
    2. Bekräfta att **HDInsight på begäran** har valts för **typ**.
    3. Välj **AzureStorage1** för **Länkad Azure Storage-tjänst**. Du skapade den här länkade tjänsten tidigare. Ange rätt namn i det här fältet om du tidigare använde ett annat namn. 
    4. Välj **spark** som **klustertyp**.
    5. Ange det **ID för tjänstens huvudnamn** som har behörighet att skapa ett HDInsight-kluster. Tjänstens huvudnamn måste vara medlem av prenumerationens deltagarrollen eller resursgruppen som klustret har skapats i. Mer information finns i [create Azure Active Directory application and service principal](../azure-resource-manager/resource-group-create-service-principal-portal.md) (skapa Azure Active Directory-program och ett huvudnamn för tjänsten).
    6. Ange **nyckel för tjänstens huvudnamn**. 
    7. Välj samma resursgrupp som du använde när du skapade datafabriken för **Resursgrupp**. Spark-klustret skapas i den här resursgruppen. 
    8. Öppna **OS-typ**.
    9. Ange ett **namn** för **klusteranvändaren**. 
    10. Ange **lösenordet** för användaren. 
    11. Klicka på **Spara**. 

        ![Inställningar för länkad HDInsight-tjänst](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. För den HDInsight-länkade tjänsten på begäran skapas HDInsight-klustret på samma plats som Azure Storage använde som primär lagring. Se till att du har tillräckligt med kärnkvoter så att klustret kan skapats på rätt sätt. Mer information finns i [Set up clusters in HDInsight with Hadoop, Spark, Kafka, and more](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) (Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera). 

## <a name="create-a-pipeline"></a>Skapa en pipeline

2. Klicka på + (plus)-knappen och sedan på **Pipeline** i menyn.

    ![Meny för ny pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. I verktygslådan **Aktiviteter** öppnar du **HDInsight** och drar och släpper **Spark**-aktiviteten från verktygslådan **Aktiviteter** till pipelinedesignytan. 

    ![Dra och släpp Spark-aktivitet](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. Utför följande steg i egenskaperna för **Spark**-aktivitetsfönstret längst ned: 

    1. växla till fliken **HDI-kluster**.
    2. Välj **AzureHDInsightLinkedService** som du skapade i föregående steg. 
        
    ![Ange en länkad HDInsight-tjänst](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Växla till fliken **Script/Jar** och gör följande: 

    1. Välj **AzureStorage1** för **Länkad jobbtjänst**.
    2. Klicka på **Bläddra i lagring**. 
    3. Navigera till **mappen adftutorial/spark/script**, välj **WordCount_Spark.py** och klicka på **Slutför**.      

    ![Ange Spark-skript](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. Verifiera pipelinen genom att klicka på knappen **Verifiera** i verktygsfältet. Klicka på knappen med **högerpilen** (>>) för att stänga verifieringsfönstret. 
    
    ![Verifieringsknapp](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Klicka på **Publicera**. Data Factory-gränssnittet publicerar entiteter (länkade tjänster och pipeline) till Azure Data Factory-tjänsten. 

## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning
Klicka på **Utlösare** i verktygsfältet och klicka på **Trigger Now** (Utlös nu). 

![Utlös nu](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Välj fliken **Övervaka**. Bekräfta att du ser en pipelinekörning. Det tar ungefär 20 minuter att skapa ett Spark-kluster. 

    ![Övervaka pipelinekörningar](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Klicka på **Uppdatera** med jämna mellanrum för att kontrollera status för pipelinekörningen. 

    ![Status för pipelinekörning](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna klickar du på åtgärden **View activity runs** (Visa aktivitetskörningar) i åtgärdskolumnen. Du kan gå tillbaka till vyn över pipelinekörningar genom att klicka på länken **Pipeliner** högst upp.

    ![Vy över Aktivitetskörningar](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verifiera utdata
Verifiera att utdatafilen har skapats i mappen spark/otuputfiles/wordcount för behållaren adftutorial. 

![Verifiera utdata](./media/tutorial-transform-data-spark-portal/verity-output.png)

Filen bör ha alla ord från indatafilen samma antal gånger som ordet förekommer i filen. Till exempel: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet transformerar data med en Spark-aktivitet och en länkad HDInsight-tjänst på begäran. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa en pipeline med en Spark-aktivitet
> * Utlösa en pipelinekörning
> * Övervaka pipelinekörningen.

Gå vidare till nästa självstudiekurs för att lära dig hur du transformerar data genom att köra Hive-skript på ett Azure HDInsight-kluster som är i ett virtuellt nätverk. 

> [!div class="nextstepaction"]
> [Självstudie: transformera data med Hive i Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).





