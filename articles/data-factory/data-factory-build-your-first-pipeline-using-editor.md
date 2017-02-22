---
title: "Skapa din första datafabrik (Azure Portal) | Microsoft Docs"
description: "I den här självstudien skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av Data Factory-redigeraren i Azure Portal."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/14/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: c9f2e3beafd19e0d4d62e409a80da336be17b90b


---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-portal"></a>Självstudier: Skapa din första Azure-datafabrik med Azure-portalen
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


I den här artikeln får du lära dig hur du använder [Azure Portal](https://portal.azure.com/) till att skapa din första Azure Data Factory. Om du vill gå igenom självstudien med andra verktyg/SDK:er kan du välja något av alternativen i listrutan. 

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 

## <a name="prerequisites"></a>Krav
1. Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen.
2. Den här artikeln ger inte någon konceptuell översikt över Azure Data Factory-tjänsten. Vi rekommenderar att du läser igenom den detaljerade översikt över tjänsten som finns i [Introduktion till Azure Data Factory](data-factory-introduction.md).  

## <a name="create-data-factory"></a>Skapa en datafabrik
En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan exempelvis vara en kopieringsaktivitet som kopierar data från en källa till ett måldataarkiv och en HDInsight Hive-aktivitet som kör Hive-skript för att transformera indata till produktutdata. Låt oss börja med att skapa datafabriken i det här steget.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **NY** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**.

   ![Bladet Skapa](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)
3. På bladet **Ny datafabrik** anger du **GetStartedDF** som namn.

   ![Bladet Ny datafabrik](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara **globalt unikt**. Om du tar emot felet **Datafabriksnamnet "GetStartedDF" är inte tillgängligt**. Ändra datafabrikens namn (till exempelvs dittnamnGetStartedDF) och försök skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
   >
   > Namnet på datafabriken kan komma att registreras som ett **DNS**-namn i framtiden och blir då synligt offentligt.
   >
   >
4. Välj den **Azure-prenumeration** där du vill att datafabriken ska skapas.
5. Välj befintlig **resursgrupp** eller skapa en resursgrupp. Skapa en resursgrupp för självstudien med namnet: **ADFGetStartedRG**.
6. Klicka på **Skapa** på bladet **Ny datafabrik**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
   >
   >
7. Du ser att datafabriken skapas på **startsidan** i Azure Portal enligt följande:   

   ![Skapar datafabrikstatus](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
8. Grattis! Du har skapat din första datafabrik. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken.     

    ![Bladet Datafabrik](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Du måste först skapa några Data Factory-entiteter innan du skapar en pipeline i datafabriken. Du skapar först länkade tjänster för att länka datalager/beräkningar till ditt datalager, sedan definierar du de indata- och utdatauppsättningar som ska representera in- och utdata i länkade datalager och därefter skapar du pipelinen med en aktivitet som använder dessa datauppsättningar.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget länkar du ditt Azure Storage-konto och ett Azure HDInsight-kluster på begäran till din datafabrik. In- och utdata för pipelinen i det här exemplet lagras i Azure Storage-kontot. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet. Identifiera vilka [beräkningstjänster](data-factory-data-movement-activities.md)/[för datalager](data-factory-compute-linked-services.md) som används i scenariot och länka dessa tjänster till datafabriken genom att skapa länkade tjänster.  

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure Storage-konto till din datafabrik. I de här självstudierna använder du samma Azure Storage-konto för att lagra indata/utdata och HQL-skriptfilen.

1. Klicka på **Författare och distribution** på bladet **DATA FACTORY** för **GetStartedDF**. Du bör se Data Factory-redigeraren.

   ![Ikonen Författare och distribution](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2. Klicka på **Nytt datalager** och välj **Azure-lagring**.

   ![Ny datalagring - Azure Storage - meny](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)
3. Du bör se JSON-skriptet för att skapa en länkad Azure-lagringstjänst i redigeraren.

   ![Länkad Azure-lagringstjänst](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Ersätt **kontonamn** med namnet på ditt Azure-lagringskonto och **kontonyckel** med åtkomstnyckeln för Azure-lagringskontot. Information om hur du hämtar lagringsåtkomstnyckeln finns i avsnitten om hur du visar, kopierar och återskapar åtkomstnycklar i [Manage your storage account](../storage/storage-create-storage-account.md#manage-your-storage-account) (Hantera ditt lagringskonto).
5. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

    ![Knappen Distribuera](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   När den länkade tjänsten har distribuerats, bör fönstret **Draft-1** försvinna och du ser **AzureStorageLinkedService** i trädvyn till vänster.

    ![Länkad lagringstjänst i menyn](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-azure-hdinsight-linked-service"></a>Skapa en Azure HDInsight-länkad tjänst
I det här steget ska du länka ett HDInsight-kluster på begäran till datafabriken. HDInsight-klustret skapas automatiskt vid körning och tas bort när bearbetningen är klar. Det är inaktivt under en angiven tidsrymd.

1. I **Data Factory-redigeraren**, klicka på **... Fler**, klicka på **Ny beräkning**, och välj **På begäran HDInsight-kluster**.

    ![Ny beräkning](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Kopiera och klistra in följande kodfragment till fönstret **Draft-1**. JSON-kodfragmentet beskriver de egenskaper som används för att skapa HDInsight-klustret på begäran.

    ```JSON
    {
      "name": "HDInsightOnDemandLinkedService",
      "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
          "version": "3.2",
          "clusterSize": 1,
          "timeToLive": "00:30:00",
          "linkedServiceName": "AzureStorageLinkedService"
        }
      }
    }
    ```

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Beskrivning |
   |:--- |:--- |
   | Version |Anger att versionen av HDInsight som skapades är 3.2. |
   | ClusterSize |Anger HDInsight-klustrets storlek. |
   | TimeToLive |Anger inaktivitetstiden för HDInsight-klustret innan det tas bort. |
   | linkedServiceName |Anger lagringskontot som används för att spara loggarna som genereras av HDInsight. |

    Observera följande punkter:

   * Data Factory skapar ett **Windows-baserat** HDInsight-kluster åt dig med JSON. Du hade också kunnat skapa ett **Linux-baserat** HDInsight-kluster. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
   * Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
   * HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt live-kluster (**timeToLive**). Klustret tas bort automatiskt när bearbetningen är klar.

       Allteftersom fler sektorer bearbetas kan du se mång behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: ”adf**datafabrikensnamn**-**denlänkadetjänstensnamn**-datumtidsstämpel”. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure Blob-lagring.

     Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
3. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

    ![Distribuera på begäran länkad HDInsight-tjänst](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)
4. Kontrollera att du ser både **AzureStorageLinkedService** och **HDInsightOnDemandLinkedService** i trädvyn till vänster.

    ![Trädvy med länkade tjänster](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som ska representera in- och utdata för Hive-bearbetning. Dessa datauppsättningar avser den **AzureStorageLinkedService** som du skapade tidigare i självstudien. Den länkade tjänsten pekar på ett Azure-lagringskonto och datauppsättningarna anger behållare, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.   

### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **Data Factory-redigeraren**, klicka på **... Fler** på kommandofältet klickar du på **Ny datamängd** och välj **Azure Blob-lagring**.

    ![Ny datauppsättning](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-kodfragmentet skapar du en datauppsättning med namnet **AzureBlobInput** som representerar indata för en aktivitet i pipelinen. Dessutom kan du ange att indata finns i blobbehållaren **adfgetstarted** och i mappen **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Beskrivning |
   |:--- |:--- |
   | typ |Typegenskapen har angetts till AzureBlob eftersom det finns data i Azure Blob-lagringen. |
   | linkedServiceName |refererar till AzureStorageLinkedService som du skapade tidigare. |
   | fileName |Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I det här fallet bearbetas bara input.log. |
   | typ |Loggfilerna är i textformat, så vi använder TextFormat. |
   | columnDelimiter |kolumner i loggfilerna avgränsas med kommatecken (,) |
   | frekvens/intervall |frekvensen är månad och intervallet är 1, vilket innebär att indatasektorerna är tillgängliga en gång i månaden. |
   | extern |den här egenskapen anges som true om indatan inte skapades av Data Factory-tjänsten. |
3. Klicka på **Distribuera** i kommandofältet för att distribuera den nyskapade datauppsättningen. Du bör se datauppsättningen i trädvyn till vänster.

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Nu skapar du den utdatauppsättning som representerar de utdata som lagras i Azure Blob Storage.

1. I **Data Factory-redigeraren**, klicka på **... Fler** på kommandofältet klickar du på **Ny datamängd** och välj **Azure Blob-lagring**.  
2. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-kodfragmentet skapar du en datauppsättning som kallas **AzureBlobOutput** och anger strukturen för de data som produceras av Hive-skriptet. Dessutom kan du ange att resultaten lagras i blobbehållaren **adfgetstarted** och i mappen **partitioneddata**. I avsnittet **tillgänglighet** anges att utdatauppsättningen skapas månadsvis.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Se **Skapa datauppsättning för indata** för beskrivningar av dessa egenskaper. Du anger inte den externa egenskapen för en utdatauppsättning, eftersom datauppsättningen produceras av Data Factory-tjänsten.
3. Klicka på **Distribuera** i kommandofältet för att distribuera den nyskapade datauppsättningen.
4. Kontrollera att datauppsättningen har skapats.

    ![Trädvy med länkade tjänster](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget ska du skapa din första pipeline med en **HDInsightHive**-aktivitet. Indatasektorn är tillgänglig månadsvis (frequency: Month, Interval: 1), utdatasektorn skapas varje månad och schemaegenskapen för aktiviteten har också inställningen Month. Inställningarna för utdatauppsättningen och aktivitetsschemaläggaren måste matcha. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. I slutet av det här avsnittet beskrivs de egenskaper som användes i följande JSON.

1. I **Data Factory-redigeraren** klickar du på **Tre punkter (...) Fler kommandon** och sedan på **Ny pipeline**.

    ![knappen ny pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Kopiera och klistra in följande kodfragment till fönstret Draft-1.

   > [!IMPORTANT]
   > Ersätt **storageaccountname** med namnet på ditt lagringskonto i JSON.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    I JSON-kodfragmentet skapar du en pipeline med en enda aktivitet, som använder Hive till att bearbeta data i ett HDInsight-kluster.

    Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i behållaren **adfgetstarted**.

    Avsnittet **Definierar** används för att ange körningsinställningar som skickas till Hive-skriptet som Hive-konfigurationsvärden (t.ex. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Egenskaperna **start** och **slut** för pipelinen anger den aktiva perioden för pipelinen.

    I JSON-aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Information om JSON-egenskaper som används i exemplet finns i avsnittet om Pipeline-JSON i [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md) (Pipelines och aktiviteter i Azure Data Factory).
   >
   >
3. Kontrollera följande:

   1. **Input.log**-filen finns i mappen **inputdata** i behållaren **adfgetstarted** i Azure-blobblagringen
   2. **partitionweblogs.hql**-filen finns i mappen **script** i behållaren **adfgetstarted** i Azure-blobblagringen. Slutför de nödvändiga stegen i [Självstudier - översikt](data-factory-build-your-first-pipeline.md) om du inte ser de här filerna.
   3. Kontrollera att du ersatte **storageaccountname** med namnet på ditt lagringskonto i JSON-pipelinen.
4. Klicka på **Distribuera** i kommandofältet för att distribuera pipelinen. Eftersom tiderna för **start** och **slut** har angetts tidigare och **isPaused** har angetts till false, kommer pipelinen (aktiviteten i pipelinen) köras omedelbart efter att du har distribuerat.
5. Kontrollera att du ser pipelinen i trädvyn.

    ![Trädvy med pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
6. Grattis, du har skapat din första pipeline!

## <a name="monitor-pipeline"></a>Övervaka pipeline
### <a name="monitor-pipeline-using-diagram-view"></a>Övervaka pipeline med diagramvyn
1. Klicka på **X** för att stänga bladen i Data Factory-redigeraren och för att gå tillbaka till Data Factory-bladet och klicka på **Diagram**.

    ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
2. I diagramvyn visas en översikt över pipelines och datauppsättningar som används i den här självstudien.

    ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)
3. Högerklicka på pipelinen i diagrammet om du vill visa alla aktiviteter i pipelinen. Klicka sedan på Öppna pipeline.

    ![Menyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
4. Kontrollera att du ser HDInsightHive-aktiviteten i pipelinen.

    ![Vyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Om du vill gå tillbaka till den föregående vyn klickar du på **Datafabrik** i adressfältmenyn längst upp.
5. Dubbelklicka på datauppsättningen **AzureBlobInput** i **diagramvyn**. Kontrollera att sektorn har statusen **Klar**. Det kan ta några minuter innan sektorn visas med statusen Klar. Om det inte händer trots att du har väntat ett tag, kontrollerar du om du har indatafilen (input.log) placerad i rätt behållare (adfgetstarted) och mapp (inputdata).

   ![Indatasektor med statusen Klar](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
6. Klicka på **X** för att stänga bladet **AzureBlobInput**.
7. Dubbelklicka på datauppsättningen **AzureBlobOutput** i **diagramvyn**. Den sektor som för närvarande bearbetas visas.

   ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
8. När bearbetningen är klar visas sektorn med statusen **Klar**.

   ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Förvänta dig därför att det       tar **cirka 30 minuter** för pipelinen att bearbeta sektorn.
   >
   >

9. När sektorn har statusen **Klar**, kontrollerar du mappen **partitioneddata** i behållaren **adfgetstarted** i blobblagringen för utdatan.  

   ![utdata](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)
10. Klicka på sektorn om du vill se information om den i ett **Datasektor**-blad.

   ![Information om datasektorn](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)  
11. Klicka på en aktivitet som körs i **Lista med aktivitetskörningar** för att se information om en aktivitetskörning (Hive-aktivitet i vårt exempel) i ett fönster med **Aktivitetskörningsinformation**.   

   ![Aktivitetskörningsinformation](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Du kan se Hive-frågan som kördes och statusinformation i loggfilerna. Dessa loggar är användbara vid felsökning av eventuella problem.
   Se artikeln [Övervaka och hantera pipelines med Azure-portalblad](data-factory-monitor-manage-pipelines.md) för mer information.

> [!IMPORTANT]
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen överför du därför indatafilen (input.log) till indatamappen i behållaren adfgetstarted.
>
>

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Övervaka pipeline med övervaknings- och hanteringsappen
Du kan också använda övervaknings- och hanteringsprogrammet till att övervaka dina pipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet.

1. Klicka på ikonen **Övervaka och hantera** på datafabrikens startsida.

    ![Ikonen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)
2. Du bör se **Övervakaren och hantera program**. Ändra **Starttid** och **Sluttid** till att matcha starttid (04-01-2016 12:00:00) och sluttid (04-02-2016 12:00:00) i pipelinen. Klicka sedan på **Tillämpa**.

    ![Appen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)
3. Välj ett aktivitetsfönster i listan **Aktivitetsfönster** om du vill se information om det.

    ![Information om aktivitetsfönstret](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Sammanfattning
I den här självstudien skapade du en Azure-datafabrik som bearbetar data genom att köra ett Hive-skript i ett Hadoop-kluster i HDInsight. Du utförde följande steg med hjälp av Data Factory-redigeraren i Azure Portal:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade två **länkade tjänster**:
   1. En länkad **Azure Storage-**tjänst som länkar din Azure Blob-lagring med in-/utdatafiler till datafabriken.
   2. En länkad **Azure HDInsight**-tjänst på begäran som länkar ett Hadoop-kluster i HDInsight på begäran till datafabriken. Azure Data Factory skapar ett Hadoop-kluster i HDInsight i rätt tid för att bearbeta indata och skapa utdata.
3. Du skapade två **datauppsättningar** som beskriver in- och utdata för Hive-aktiviteten för HDInsight i pipelinen.
4. Du skapade en **pipeline** med en **HDInsight Hive**-aktivitet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en pipeline med en transformeringsaktivitet (HDInsight-aktivitet) som kör ett Hive-skript på ett HDInsight-kluster på begäran. Om du vill se hur du använder en kopieringsaktivitet till att kopiera data från en Azure-blobb till Azure SQL kan du läsa mer i [Självstudie: Kopiera data från en Azure-blobb till Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |



<!--HONumber=Feb17_HO1-->


