---
title: "Skapa din första datafabrik (Visual Studio) | Microsoft Docs"
description: "I den här självstudien skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av Visual Studio."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 7398c0c9-7a03-4628-94b3-f2aaef4a72c5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 23927acae12f0db13fe6dd24a4e1fde8ced25d40
ms.lasthandoff: 03/07/2017


---
# <a name="tutorial-build-your-azure-first-data-factory-using-microsoft-visual-studio"></a>Självstudier: Skapa din första Azure-datafabrik med Microsoft Visual Studio
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>

I den här artikeln får du lära dig hur du använder Microsoft Visual Studio till att skapa din första Azure-datafabrik. Om du vill gå igenom självstudien med andra verktyg/SDK:er kan du välja något av alternativen i listrutan.

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 

## <a name="prerequisites"></a>Krav
1. Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen.
2. Du måste vara **administratör för Azure-prenumerationen** för att kunna publicera Data Factory-enheter från Visual Studio till Azure Data Factory.
3. Du måste ha följande installerat på datorn:
   * Visual Studio 2013 eller Visual Studio 2015
   * Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
   * Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Du kan även uppdatera plugin-programmet genom att göra följande: På menyn klickar du på **Verktyg** -> **Tillägg och uppdateringar** -> **Online** -> **Visual Studio-galleriet** -> **Microsoft Azure Data Factory-verktyg för Visual Studio** -> **Uppdatera**.

Nu ska vi använda Visual Studio för att skapa en Azure-datafabrik.

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt
1. Starta **Visual Studio 2013** eller **Visual Studio 2015**. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. Dialogrutan **Nytt projekt** visas.  
2. I dialogrutan **Nytt projekt** väljer du mallen **DataFactory** och klickar på **Tomt Data Factory-projekt**.   

    ![Dialogrutan Nytt projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Ange ett **namn** för projektet, en **plats** och ett namn för **lösningen**. Klicka på **OK**.

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan till exempel vara en kopieringsaktivitet som kopierar data från en källa till ett måldatalager och en Hive-aktivitet för HDInsight som kör Hive-skript för att transformera indata. I [stödda datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) står alla källor och mottagare som stöds av Kopiera aktivitet. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för att se listan över Compute Services som stöds av Data Factory.

I det här steget länkar du ditt Azure Storage-konto och ett Azure HDInsight-kluster på begäran till din datafabrik. In- och utdata för pipelinen i det här exemplet lagras i Azure Storage-kontot. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet. Identifiera vilka datalager/beräkningstjänster som används i scenariot och länka dessa tjänster till datafabriken genom att skapa länkade tjänster.  

Du anger namnet och inställningarna för datafabriken senare när du publicerar din Data Factory-lösning.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst
I det här steget länkar du ditt Azure Storage-konto till din datafabrik. I den här självstudiekursen använder du samma Azure Storage-konto för att lagra indata/utdata och HQL-skriptfilen.

1. Högerklicka på **Länkade tjänster** i Solution Explorer, peka på **Lägg till** och klicka på **Nytt objekt**.      
2. I dialogrutan **Lägg till nytt objekt** väljer du **Länkad Azure Storage-tjänst** i listan och klickar på **Lägg till**.
3. Ersätt **accountname** och **accountkey** med namnet på ditt Azure Storage-konto och dess nyckel. Information om hur du hämtar lagringsåtkomstnyckeln finns i avsnitten om hur du visar, kopierar och återskapar åtkomstnycklar i [Manage your storage account](../storage/storage-create-storage-account.md#manage-your-storage-account) (Hantera ditt lagringskonto).
    ![Länkad Azure Storage-tjänst](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Spara filen **AzureStorageLinkedService1.json**.

#### <a name="create-azure-hdinsight-linked-service"></a>Skapa en Azure HDInsight-länkad tjänst
I det här steget ska du länka ett HDInsight-kluster på begäran till datafabriken. HDInsight-klustret skapas automatiskt vid körning och tas bort när bearbetningen är klar. Det är inaktivt under en angiven tidsrymd. Du kan använda ditt eget HDInsight-kluster i stället för att använda ett HDInsight-kluster på begäran. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för mer information.

1. I **Solution Explorer** högerklickar du på **Länkade tjänster**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Länkad HDInsight-tjänst på begäran** och klicka på **Lägg till**.
3. Ersätt **JSON** med följande JSON:

    ```JSON
    {
      "name": "HDInsightOnDemandLinkedService",
      "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
          "version": "3.2",
          "clusterSize": 1,
          "timeToLive": "00:30:00",
          "linkedServiceName": "AzureStorageLinkedService1"
        }
      }
    }
    ```

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Beskrivning |
   | -------- | ----------- |
   | Version | Anger att versionen av HDInsight som skapades är 3.2. |
   | ClusterSize |Anger HDInsight-klustrets storlek. |
   | TimeToLive |Anger inaktivitetstiden för HDInsight-klustret innan det tas bort. |
   | linkedServiceName |Anger lagringskontot som används för att spara loggarna som genereras av HDInsight |

    Observera följande punkter:

   * Data Factory skapar ett **Windows-baserat** HDInsight-kluster åt dig med ovanstående JSON. Du hade också kunnat skapa ett **Linux-baserat** HDInsight-kluster. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
   * Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
   * HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt live-kluster (**timeToLive**). Klustret tas bort automatiskt när bearbetningen är klar.

       Allteftersom fler sektorer bearbetas kan du se mång behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure Blob-lagring.

     Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
4. Spara filen **HDInsightOnDemandLinkedService1.json**.

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som ska representera in- och utdata för Hive-bearbetning. Dessa datauppsättningar avser den **AzureStorageLinkedService1** som du skapade tidigare i den här självstudien. Den länkade tjänsten pekar på ett Azure Storage-konto och datauppsättningarna anger behållare, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.   

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **Solution Explorer** högerklickar du på **Tabeller**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Azure-blobb** i listan och ändra namnet på filen till **InputDataSet.json**. Klicka på **Lägg till**.
3. Ersätt **JSON** i redigeraren med följande JSON-kodavsnitt:

    I JSON-kodfragmentet skapar du en datauppsättning med namnet **AzureBlobInput** som representerar indata för en aktivitet i pipelinen. Dessutom kan du ange att indata finns i blobbehållaren `adfgetstarted` och i mappen `inputdata`.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
   | -------- | ----------- |
   | typ |Typegenskapen har angetts till AzureBlob eftersom det finns data i Azure Blob-lagringen. |
   | linkedServiceName |refererar till AzureStorageLinkedService1 som du skapade tidigare. |
   | fileName |Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I det här fallet bearbetas bara input.log. |
   | typ |Loggfilerna är i textformat, så vi använder TextFormat. |
   | columnDelimiter |kolumner i loggfilerna avgränsas med kommatecken (,) |
   | frekvens/intervall |frekvensen är månad och intervallet är 1, vilket innebär att indatasektorerna är tillgängliga en gång i månaden. |
   | extern |den här egenskapen anges som true om indatan inte skapades av Data Factory-tjänsten. |
4. Spara filen **InputDataset.json**.

#### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Nu skapar du den utdatauppsättning som representerar de utdata som lagras i Azure Blob Storage.

1. I **Solution Explorer** högerklickar du på **Tabeller**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Azure-blobb** i listan och ändra namnet på filen till **OutputDataset.json**. Klicka på **Lägg till**.
3. Ersätt **JSON** i redigeraren med följande JSON:

    I JSON-kodfragmentet skapar du en datauppsättning som kallas **AzureBlobOutput** och anger strukturen för de data som produceras av Hive-skriptet. Dessutom kan du ange att resultaten lagras i blobbehållaren `adfgetstarted` och i mappen `partitioneddata`. I avsnittet **tillgänglighet** anges att utdatauppsättningen skapas månadsvis.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService1",
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
4. Spara filen **OutputDataset.json**.

### <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du din första pipeline med en **HDInsightHive**-aktivitet. Indatasektorn är tillgänglig månadsvis (frequency: Month, Interval: 1), utdatasektorn skapas varje månad och schemaegenskapen för aktiviteten anges också månadsvis. Inställningarna för utdatauppsättningen och aktivitetsschemaläggaren måste matcha. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. I slutet av det här avsnittet beskrivs de egenskaper som användes i följande JSON.

1. I **Solution Explorer** högerklickar du på **Pipelines**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Pipeline för Hive-transformering** i listan och klicka på **Lägg till**.
3. Ersätt **JSON** med följande kodfragment.

   > [!IMPORTANT]
   > ersätt **storageaccountname** med namnet på ditt lagringskonto.
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
                        "scriptLinkedService": "AzureStorageLinkedService1",
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

    Hive-skriptfilen **partitionweblogs.hql** lagras på Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService1**) och i mappen `script` i behållaren `adfgetstarted`.

    Avsnittet **Definierar** används för att ange körningsinställningar som skickas till Hive-skriptet som Hive-konfigurationsvärden (t.ex. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Egenskaperna **start** och **slut** för pipelinen anger den aktiva perioden för pipelinen.

    I JSON-aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Information om JSON-egenskaper som används i exemplet finns i avsnittet om Pipeline-JSON i [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md) (Pipelines och aktiviteter i Azure Data Factory).

4. Spara filen **HiveActivity1.json**.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Lägg till partitionweblogs.hql och input.log som ett beroende
1. Högerklicka på **Beroenden** i fönstret **Solution Explorer**, peka på **Lägg till** och klicka på **Befintligt objekt**.  
2. Gå till **C:\ADFGettingStarted** och markera filerna **partitionweblogs.hql**, **input.log**. Klicka på **Lägg till**. Du har skapat dessa två filer som en del av förutsättningarna från [Självstudier – översikt](data-factory-build-your-first-pipeline.md).

När du publicerar lösningen i nästa steg laddas filen **partitionweblogs.hql** upp till skriptmappen i blobbehållaren `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Publicera/distribuera Data Factory-entiteter
1. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**.
2. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
3. Du bör se följande dialogruta:

   ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. På sidan **Konfigurera datafabrik** går du igenom följande steg:

   1. välj alternativet **Skapa ny Data Factory**.
   2. Ange ett unikt **namn** för datafabriken. Exempel: **FirstDataFactoryUsingVS09152016**. Namnet måste vara globalt unikt.
   3. Välj rätt prenumeration i fältet **Prenumeration**. Om du inte ser någon prenumeration kontrollerar du att du har loggat in med ett konto som är en administratör eller en medadministratör för prenumerationen.
   4. Välj **resursgrupp** för datafabriken som ska skapas.
   5. Välj **region** för datafabriken.
   6. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.)

        > [!IMPORTANT]
        > Om du får felet **Datafabriksnamnet ”FirstDataFactoryUsingVS” är inte tillgängligt** när du publicerar, ändrar du namnet (till exempel yournameFirstDataFactoryUsingVS). Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.   
1. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
2. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
3. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.

Viktiga saker att observera:

- Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen:
    - I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern.
        ```PowerShell    
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad.

        ```PowerShell
        Get-AzureRmResourceProvider
        ```
    - Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.
- Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
- Om du vill skapa Data Factory-instanser måste du vara administratör eller medadministratör för Azure-prenumerationen

## <a name="monitor-pipeline"></a>Övervaka pipeline
### <a name="monitor-pipeline-using-diagram-view"></a>Övervaka pipeline med diagramvyn
1. Logga in på [Azure Portal](https://portal.azure.com/) genom att göra följande:
   1. Klicka på **Fler tjänster** och på **Datafabriker**.
       
        ![Bläddra igenom datafabrikerna](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Välj namnet på din datafabrik (till exempel: **FirstDataFactoryUsingVS09152016**) från listan med datafabriker.
   
       ![Välj din datafabrik](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. På startsidan för din datafabrik klickar du på **Diagram**.

    ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. I diagramvyn visas en översikt över pipelines och datauppsättningar som används i den här självstudien.

    ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Högerklicka på pipelinen i diagrammet om du vill visa alla aktiviteter i pipelinen. Klicka sedan på Öppna pipeline.

    ![Menyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Kontrollera att du ser HDInsightHive-aktiviteten i pipelinen.

    ![Vyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Om du vill gå tillbaka till den föregående vyn klickar du på **Datafabrik** i adressfältmenyn längst upp.
6. Dubbelklicka på datauppsättningen **AzureBlobInput** i **diagramvyn**. Kontrollera att sektorn har statusen **Klar**. Det kan ta några minuter innan sektorn visas med statusen Klar. Om det inte händer trots att du har väntat ett tag, kontrollerar du att du har indatafilen (input.log) placerad i rätt behållare (`adfgetstarted`) och mapp (`inputdata`).

   ![Indatasektor med statusen Klar](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Klicka på **X** för att stänga bladet **AzureBlobInput**.
8. Dubbelklicka på datauppsättningen **AzureBlobOutput** i **diagramvyn**. Den sektor som för närvarande bearbetas visas.

   ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. När bearbetningen är klar visas sektorn med statusen **Klar**.

   > [!IMPORTANT]
   > Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Förvänta dig därför att det tar **cirka 30 minuter** för pipelinen att bearbeta sektorn.  
   >
   >

    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. När sektorn har statusen **Redo**, kontrollerar du mappen `partitioneddata` i behållaren `adfgetstarted` i ditt Blob Storage för utdata.  

    ![utdata](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Klicka på sektorn om du vill se information om den i ett **Datasektor**-blad.

    ![Information om datasektorn](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Klicka på en aktivitet som körs i **Lista med aktivitetskörningar** för att se information om en aktivitetskörning (Hive-aktivitet i vårt exempel) i ett fönster med **Aktivitetskörningsinformation**. 
  
    ![Aktivitetskörningsinformation](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    Du kan se Hive-frågan som kördes och statusinformation i loggfilerna. Dessa loggar är användbara vid felsökning av eventuella problem.  

Se [Övervaka datauppsättningar och pipeline](data-factory-monitor-manage-pipelines.md) för instruktioner om hur du använder Azure Portal till att övervaka pipeline och datauppsättningar som du har skapat i den här självstudien.

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Övervaka pipeline med övervaknings- och hanteringsappen
Du kan också använda övervaknings- och hanteringsprogrammet till att övervaka dina pipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet.

1. Klicka på ikonen Övervaka och hantera.

    ![Ikonen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Du bör se programmet Övervaka och hantera. Ändra **Starttid** och **Sluttid** till att matcha starttid (04-01-2016 12:00) och sluttid (04-02-2016 12:00) i pipelinen. Klicka sedan på **Tillämpa**.

    ![Appen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Välj ett aktivitetsfönster i listan Aktivitetsfönster om du vill se information om det.
    ![Aktivitetsfönsterinformation](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen laddar du därför upp indatafilen (input.log) till mappen `inputdata` i behållaren`adfgetstarted`.
>
>

## <a name="use-server-explorer-to-view-data-factories"></a>Använda Server Explorer för att visa datafabriker
1. I **Visual Studio** klickar du på **Visa** i menyn och därefter på **Server Explorer**.
2. I fönstret Server Explorer expanderar du **Azure** och **Data Factory**. Om du ser **Logga in till Visual Studio** anger du det **konto** som är associerat med din Azure-prenumeration och klickar på **Fortsätt**. Ange **lösenordet** och klicka på **Logga in**. Visual Studio försöker hämta information om alla Azure-datafabriker i din prenumeration. Du ser statusen för den här åtgärden i fönstret **Uppgiftslista för Data Factory**.

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Du kan också högerklicka på en datafabrik och välja **Exportera Data Factory till nytt projekt** om du vill skapa ett Visual Studio-projekt som baseras på en befintlig datafabrik.

    ![Exportera datafabrik](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Uppdatera Data Factory-verktyg för Visual Studio
Om du vill uppdatera Azure Data Factory-verktyg för Visual Studio går du igenom följande steg:

1. Klicka på **Verktyg** i menyn och välj **Tillägg och uppdateringar**.
2. Välj **Uppdateringar** i den vänstra rutan och välj sedan **Visual Studio-galleriet**.
3. Välj **Azure Data Factory-verktyg för Visual Studio** och klicka på **Uppdatera**. Om du inte ser den här posten har du redan den senaste versionen av verktygen.

## <a name="use-configuration-files"></a>Använda konfigurationsfiler
Du kan använda konfigurationsfiler i Visual Studio för att konfigurera egenskaper för länkade tjänster/tabeller/pipelines olika för varje miljö.

Fundera på följande JSON-definition för en länkad Azure Storage-tjänst. Att ange **connectionString** med olika värden för accountname och accountkey baseras på vilken miljö (Utveckling/Test/Produktion) som du distribuerar Data Factory-entiteter till. Du kan göra detta genom att använda separata konfigurationsfiler för varje miljö.

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Lägga till en konfigurationsfil
Lägg till en konfigurationsfil för varje miljö genom att utföra följande steg:   

1. Högerklicka på Data Factory-projektet i Visual Studio-lösningen, peka på **Lägg till** och klicka på **Nytt objekt**.
2. Välj **Config** i listan med installerade mallar till vänster, välj **Konfigurationsfil**, ange ett **namn** för konfigurationsfilen och klicka på **Lägg till**.

    ![Lägga till en konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Lägg till konfigurationsparametrar och deras värden i det format som visas nedan.

    ```JSON
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Det här exemplet anger egenskapen connectionString för en länkad Azure Storage-tjänst och en Azure SQL-länkad tjänst. Observera att syntaxen för att ange namnet är [JsonPath](http://goessner.net/articles/JsonPath/).   

    Om JSON har en egenskap med en värdematris enligt följande kod:  

    ```JSON
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Konfigurera egenskaper enligt följande konfigurationsfil (använd nollbaserad indexering):

    ```JSON
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Egenskapsnamn med blanksteg
Om ett egenskapsnamn innehåller blanksteg, använder du hakparenteser enligt följande exempel (databasservernamn):

```JSON
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Distribuera lösningen med en konfiguration
När du publicerar Azure Data Factory-entiteter i VS, kan du ange den konfiguration som du vill använda för att publicera åtgärden.

Publicera entiteter i Azure Data Factory-projekt med hjälp av en konfigurationsfil:   

1. Högerklicka på Data Factory-projektet och visa dialogrutan **Publicera objekt** genom att klicka på **Publicera**.
2. Välj en befintlig datafabrik eller ange värden för att skapa en datafabrik på sidan **Konfigurera datafabrik**. Klicka på **Nästa**.   
3. På sidan **Publicera objekt**: Här visas en listruta med tillgängliga konfigurationer för fältet **Välj distributionskonfiguration**.

    ![Välj konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Välj den **konfigurationsfil** som du vill använda och klicka på **Nästa**.
5. Kontrollera att du ser namnet på JSON-filen på sidan **Sammanfattning** och klicka på **Nästa**.
6. Klicka på **Slutför** när distributionen är klar.

När du distribuerar används värden från konfigurationsfilen till att ange värden för egenskaper i JSON-filerna för Data Factory-entiteter innan entiteterna distribueras till Azure Data Factory-tjänsten.   

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
| [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) |Den här artikeln innehåller en lista med de datatransformeringsaktiviteter (till exempel HDInsight Hive-transformeringen som du använde i självstudien) som stöds av Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |

