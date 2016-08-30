<properties
    pageTitle="Skapa din första datafabrik (Visual Studio) | Microsoft Azure"
    description="I den här självstudien skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av Visual Studio."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="05/16/2016"
    ms.author="spelluru"/>

# Skapa din första Azure-datafabrik med Microsoft Visual Studio
> [AZURE.SELECTOR]
- [Självstudier – översikt](data-factory-build-your-first-pipeline.md)
- [Använda Data Factory-redigeraren](data-factory-build-your-first-pipeline-using-editor.md)
- [Använda PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Använda Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Använda Resource Manager-mallen](data-factory-build-your-first-pipeline-using-arm.md)


I den här artikeln får du lära dig hur du använder Microsoft Visual Studio till att skapa din första Azure-datafabrik. 

## Förutsättningar

1. Du **måste** läsa igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutföra nödvändiga steg innan du fortsätter.
2. Den här artikeln ger inte någon konceptuell översikt över Azure Data Factory-tjänsten. Vi rekommenderar att du läser igenom den detaljerade översikt över tjänsten som finns i [Introduktion till Azure Data Factory](data-factory-introduction.md).  

## Skapa och distribuera Data Factory-entiteter  

### Förutsättningar

Du måste ha följande installerat på datorn: 

- Visual Studio 2013 eller Visual Studio 2015
- Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
- Hämta det senaste Azure Data Factory-pluginprogrammet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Om du använder Visual Studio 2013 kan du även uppdatera plugin-programmet genom att göra följande: I menyn klickar du på **Verktyg** -> **Tillägg och uppdateringar** -> **Online** -> **Visual Studio-galleriet** -> **Microsoft Azure Data Factory-verktyg för Visual Studio** -> **Uppdatera**. 
    
    

## Skapa Visual Studio-projekt 
1. Starta **Visual Studio 2013** eller **Visual Studio 2015**. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. Dialogrutan **Nytt projekt** visas.  
2. I dialogrutan **Nytt projekt** väljer du mallen **DataFactory** och klickar på **Tomt Data Factory-projekt**.   

    ![Dialogrutan Nytt projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Ange ett **namn** för projektet, en **plats** och ett namn för **lösningen**. Klicka på **OK**.

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## Skapa länkade tjänster
En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan exempelvis vara en kopieringsaktivitet som kopierar data från en källa till ett måldataarkiv och en HDInsight Hive-aktivitet som kör Hive-skript för att transformera indata till produktutdata. Du anger namnet och inställningarna för datafabriken senare när du publicerar din Data Factory-lösning.

I det här steget länkar du ditt Azure-lagringskonto och ett Azure HDInsight-kluster på begäran till din datafabrik. Azure-lagringskontot kommer att innehålla in- och utdata för pipelinen i det här exemplet. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet. Du behöver identifiera vilka datalager/beräkningstjänster som används i scenariot och länka dessa tjänster till datafabriken genom att skapa länkade tjänster.  

#### Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till din datafabrik. I självstudien använder du samma Azure-lagringskonto för att lagra in-/utdata och HQL-skriptfilen. 

4. Högerklicka på **Länkade tjänster** i Solution Explorer, peka på **Lägg till** och klicka på **Nytt objekt**.      
5. I dialogrutan **Lägg till nytt objekt** väljer du **Länkad Azure-lagringstjänst** i listan och klickar på **Lägg till**. 
3. Ersätt **accountname** och **accountkey** med namnet på ditt Azure-lagringskonto och dess nyckel. Information om hur du hämtar din lagringsåtkomstnyckel finns i [Visa, kopiera och återskapa lagringsåtkomstnycklar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)

    ![Länkad Azure-lagringstjänst](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Spara filen **AzureStorageLinkedService1.json**.

#### Skapa en Azure HDInsight-länkad tjänst
I det här steget kommer du länka ett HDInsight-kluster på begäran till din datafabrik. HDInsight-klustret skapas automatiskt vid körning och tas bort när bearbetningen är klar. Det är inaktivt under en angiven tidsrymd. Du kan använda ditt eget HDInsight-kluster i stället för att använda ett HDInsight-kluster på begäran. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för mer information. . 

1. I **Solution Explorer** högerklickar du på **Länkade tjänster**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Länkad HDInsight-tjänst på begäran** och klicka på **Lägg till**. 
3. Ersätt **JSON** med följande:

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
    
    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:
    
    Egenskap | Beskrivning
    -------- | -----------
    Version | Detta visar att versionen av den HDInsight som skapades är 3.2. 
    ClusterSize | Detta skapar ett HDInsight-kluster med en nod. 
    TimeToLive | Detta anger inaktivitetstiden för HDInsight-klustret innan det tas bort.
    linkedServiceName | Detta anger det lagringskonto som kommer att användas för att lagra loggarna som genereras av HDInsight

    Observera följande: 
    
    - Data Factory skapar ett **Windows-baserat** HDInsight-kluster med ovanstående JSON. Du hade också kunnat skapa ett **Linux-baserat** HDInsight-kluster. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information. 
    - Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
    - HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar.
    
        Vartefter fler sektorer bearbetas visas fler behållare i din Azure blobblagring. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnet på de här behållarna följer ett mönster: ”adf**yourdatafactoryname**-**linkedservicename**- datetimestamp”. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure blobblagring.

    Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information. 
4. Spara filen **HDInsightOnDemandLinkedService1.json**.

## Skapa datauppsättningar
I det här steget skapar du de datauppsättningar som representerar in- och utdata för Hive-bearbetning. Dessa datauppsättningar avser den **AzureStorageLinkedService1** som du skapade tidigare i den här självstudien. Den länkade tjänsten pekar på ett Azure-lagringskonto och datauppsättningarna anger behållare, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.   

#### Skapa indatauppsättning

1. I **Solution Explorer** högerklickar du på **Tabeller**, pekar på **Lägg till** och klickar på **Nytt objekt**. 
2. Välj **Azure-blobb** i listan och ändra namnet på filen till **InputDataSet.json**. Klicka på **Lägg till**.
3. Ersätt **JSON** i redigeraren med följande: 

    I JSON-kodfragmentet skapar du en datauppsättning med namnet **AzureBlobInput** som representerar indata för en aktivitet i pipelinen. Dessutom kan du ange att indata finns i blobbehållaren **adfgetstarted** och i mappen **inputdata**.
        
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

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

  	| Egenskap | Beskrivning |
  	| :------- | :---------- |
  	| typ | Typegenskapen har angetts till AzureBlob eftersom det finns data i Azure-blobblagringen. |  
  	| linkedServiceName | refererar till AzureStorageLinkedService1 som du skapade tidigare. |
  	| fileName | Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I det här fallet bearbetas bara input.log. |
  	| typ | Loggfilerna är i textformat så vi använder TextFormat. | 
  	| columnDelimiter | kolumner i loggfilerna avgränsas med , (kommatecken) |
  	| frekvens/intervall | frekvensen är månad och intervallet är 1, vilket innebär att indatasektorerna är tillgängliga en gång i månaden. | 
  	| extern | den här egenskapen anges som true om indatan inte skapades av Data Factory-tjänsten. | 
      
    
3. Spara filen **InputDataset.json**. 

 
#### Skapa datauppsättning för utdata
Nu skapar du utdatauppsättningen som representerar de utdata som lagrades i Azure-blobblagringen. 

1. I **Solution Explorer** högerklickar du på **Tabeller**, pekar på **Lägg till** och klickar på **Nytt objekt**. 
2. Välj **Azure-blobb** i listan och ändra namnet på filen till **OutputDataset.json**. Klicka på **Lägg till**. 
3. Ersätt **JSON** i redigeraren med följande: 

    I JSON-kodfragmentet skapar du en datauppsättning som kallas **AzureBlobOutput** och anger strukturen för de data som kommer att produceras av Hive-skriptet. Dessutom kan du ange att resultaten lagras i blobbehållaren **adfgetstarted** och i mappen **partitioneddata**. I avsnittet **tillgänglighet** anges att utdatauppsättningen skapas månadsvis.
    
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

    Se **Skapa datauppsättning för indata** för beskrivningar av dessa egenskaper. Du anger inte den externa egenskapen för en utdatauppsättning, eftersom datauppsättningen produceras av Data Factory-tjänsten.

4. Spara filen **OutputDataset.json**.


### Skapa pipeline
I det här steget ska du skapa din första pipeline med en **HDInsightHive**-aktivitet. Observera att indatasektorn är tillgänglig en gång i månaden (frekvens: månad, intervall: 1), utdatasektorn skapas en gång i månaden och schemaegenskapen för aktiviteten också anges månadsvis (se nedan). Inställningarna för utdatauppsättningen och aktivitetsschemaläggaren måste matcha. Utdatauppsättningen är det som skapar schemat, så du måste skapa en utdatauppsättning även om aktiviteten inte producerar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. I slutet av det här avsnittet beskrivs de egenskaper som användes i följande JSON.

1. I **Solution Explorer** högerklickar du på **Pipelines**, pekar på **Lägg till** och klickar på **Nytt objekt**. 
2. Välj **Pipeline för Hive-transformering** i listan och klicka på **Lägg till**. 
3. Ersätt **JSON** med följande kodfragment.

    > [AZURE.IMPORTANT] ersätt **storageaccountname** med namnet på ditt lagringskonto.

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

    I JSON-kodfragmentet skapar du en pipeline med en enda aktivitet, som använder Hive till att bearbeta data i ett HDInsight-kluster.
    
    I JSON-kodfragmentet skapar du en pipeline med en enda aktivitet, som använder Hive till att bearbeta data i ett HDInsight-kluster.
    
    Hive-skriptfilen **partitionweblogs.hql** lagras på Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService1**) och i mappen **script** i behållaren **adfgetstarted**.

    Avsnittet **Definierar** används för att ange körningsinställningar som kommer att skickas till Hive-skriptet som Hive-konfigurationsvärden (t.ex. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Egenskaperna **start** och **slut** för pipelinen anger den aktiva perioden för pipelinen.

    I JSON-aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Se [Uppbyggnad av en pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) för mer information om JSON-egenskaper som användes i exemplet ovan. 
3. Spara filen **HiveActivity1.json**.

### Lägg till partitionweblogs.hql och input.log som ett beroende 

1. Högerklicka på **Beroenden** i fönstret **Solution Explorer**, peka på **Lägg till** och klicka på **Befintligt objekt**.  
2. Gå till **C:\ADFGettingStarted** och markera filerna **partitionweblogs.hql**, **input.log**. Klicka på **Lägg till**. Du har skapat dessa två filer som en del av förutsättningarna från [Självstudier – översikt](data-factory-build-your-first-pipeline.md).

När du publicerar lösningen i nästa steg laddas filen **partitionweblogs.hql** upp till skriptmappen i blobbehållaren **adfgetstarted**.   

### Publicera/distribuera Data Factory-entiteter

18. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**. 
19. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
20. Du bör se följande dialogruta:

    ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. På sidan Konfigurera datafabrik gör du följande: 
    1. välj alternativet **Skapa ny Data Factory**.
    2. Ange **FirstDataFactoryUsingVS** som **namn**. 
    
        > [AZURE.IMPORTANT] Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet **Datafabriksnamnet ”FirstDataFactoryUsingVS” är inte tillgängligt** när du publicerar, ändrar du namnet (till exempel yournameFirstDataFactoryUsingVS). Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj rätt prenumeration i fältet **Prenumeration**. 4. Välj **resursgrupp** för datafabriken som ska skapas. 5. Välj **region** för datafabriken. 6. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.) 
23. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
24. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
25. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar. 

Observera följande: 

- Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 

    - I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Du kan köra följande kommando för att kontrollera att Data Factory-providern är registrerad. 
    
            Get-AzureRmResourceProvider
    - Logga in med Azure-prenumerationen i [Azure-portalen](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure-portalen. Detta registrerar automatiskt providern åt dig.
-   Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
-   Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen

 
## Övervaka pipeline

6. Logga in på [Azure Portal](https://portal.azure.com/) genom att göra följande:
    1. Klicka på **Bläddra** och välj **Datafabriker**.
        ![Bläddra igenom datafabrikerna](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
    2. Välj **FirstDataFactoryUsingVS** från listan med datafabriker. 
7. På startsidan för din datafabrik klickar du på **Diagram**.
  
    ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. I diagramvyn visas en översikt över pipelines och datauppsättningar som används i den här självstudien.
    
    ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Högerklicka på pipelinen i diagrammet om du vill visa alla aktiviteter i pipelinen. Klicka sedan på Öppna pipeline. 

    ![Menyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Kontrollera att du ser HDInsightHive-aktiviteten i pipelinen. 
  
    ![Vyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Om du vill gå tillbaka till den föregående vyn klickar du på **Datafabrik** i adressfältmenyn längst upp. 
10. I **diagramvyn** dubbelklickar du på datauppsättningen **AzureBlobInput**. Kontrollera att sektorn har statusen **Klar**. Det kan ta några minuter innan sektorn visas med statusen Klar. Om det inte händer trots att du har väntat ett tag, kontrollerar du om du har indatafilen (input.log) placerad i rätt behållare (adfgetstarted) och mapp (inputdata).

    ![Indatasektor med statusen Klar](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Klicka på **X** för att stänga bladet **AzureBlobInput**. 
12. I **diagramvyn** dubbelklickar du på datauppsättningen **AzureBlobOutput**. Den sektor som för närvarande bearbetas visas.

    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. När bearbetningen är klar visas sektorn med statusen **Klar**.
    >[AZURE.IMPORTANT] Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter).  

    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png) 
    
10. När sektorn har statusen **Klar**, kontrollerar du mappen **partitioneddata** i behållaren **adfgetstarted** i blobblagringen för utdatan.  
 
    ![utdata](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

Se [Övervaka datauppsättningar och pipeline](data-factory-monitor-manage-pipelines.md) för instruktioner om hur du använder Azure Portal till att övervaka pipeline och datauppsättningar som du har skapat i den här självstudien.

Du kan också använda appen Övervaka och hantera till att övervaka dina datapipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervakningsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet. 

> [AZURE.IMPORTANT] Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen överför du därför indatafilen (input.log) till indatamappen i behållaren adfgetstarted.
 

## Använda Server Explorer för att visa datafabriker

1. I **Visual Studio** klickar du på **Visa** i menyn och därefter på **Server Explorer**.
2. I fönstret Server Explorer expanderar du **Azure** och **Data Factory**. Om du ser **Logga in till Visual Studio** anger du det **konto** som är associerat med din Azure-prenumeration och klickar på **Fortsätt**. Ange **lösenordet** och klicka på **Logga in**. Visual Studio försöker hämta information om alla Azure-datafabriker i din prenumeration. Du ser statusen för den här åtgärden i fönstret **Uppgiftslista för Data Factory**.

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Du kan också högerklicka på en datafabrik och välja **Exportera Data Factory till nytt projekt**, för att skapa ett Visual Studio-projekt som baseras på en befintlig datafabrik.

    ![Exportera datafabrik](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Uppdatera Data Factory-verktyg för Visual Studio

Om du vill uppdatera Azure Data Factory-verktyg för Visual Studio gör du följande:

1. Klicka på **Verktyg** i menyn och välj **Tillägg och uppdateringar**.
2. Välj **Uppdateringar** i den vänstra rutan och välj sedan **Visual Studio-galleriet**.
3. Välj **Azure Data Factory-verktyg för Visual Studio** och klicka på **Uppdatera**. Om du inte ser den här posten har du redan den senaste versionen av verktygen. 

## Använda konfigurationsfiler
Du kan använda konfigurationsfiler i Visual Studio för att konfigurera egenskaper för länkade tjänster/tabeller/pipelines olika för varje miljö. 

Fundera på följande JSON-definition för en länkad Azure-lagringstjänst. Att ange **connectionString** med olika värden för accountname och accountkey baseras på vilken miljö (Utveckling/Test/Produktion) som du distribuerar Data Factory-entiteter till. Du kan göra detta genom att använda separata konfigurationsfiler för varje miljö. 

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

### Lägga till en konfigurationsfil
Lägg till en konfigurationsfil för varje miljö genom att utföra följande steg:   

1. Högerklicka på Data Factory-projektet i Visual Studio-lösningen, peka på **Lägg till** och klicka på **Nytt objekt**.
2. Välj **Config** i listan med installerade mallar till vänster, välj **Konfigurationsfil**, ange ett **namn** för konfigurationsfilen och klicka på **Lägg till**.

    ![Lägga till en konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Lägg till konfigurationsparametrar och deras värden i det format som visas nedan:

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

    Det här exemplet anger egenskapen connectionString för en länkad Azure-lagringstjänst och en Azure SQL-länkad tjänst. Observera att syntaxen för att ange namnet är [JsonPath](http://goessner.net/articles/JsonPath/).   

    Om JSON har en egenskap med en värdematris som visas nedan:  

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
    
    Du måste konfigurera följande i konfigurationsfilen (använd nollbaserad indexering): 
        
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

### Egenskapsnamn med blanksteg
Om ett egenskapsnamn innehåller blanksteg, använder du hakparenteser enligt följande exempel (databasservernamn): 

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### Distribuera lösningen med en konfiguration
När du publicerar Azure Data Factory-entiteter i VS, kan du ange den konfiguration som du vill använda för att publicera åtgärden. 

Publicera entiteter i Azure Data Factory-projekt med hjälp av en konfigurationsfil:   

1. Högerklicka på Data Factory-projekt och klicka på **Publicera** för att se dialogrutan **Publicera objekt**. 
2. Välj en befintlig datafabrik eller ange värden för att skapa en ny datafabrik på sidan **Konfigurera datafabrik**. Klicka på **Nästa**.   
3. På sidan **Publicera objekt**: Här visas en listruta med tillgängliga konfigurationer för fältet **Välj distributionskonfiguration**.

    ![Välj konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Välj den **konfigurationsfil** som du vill använda och klicka på **Nästa**. 
5. Kontrollera att du ser namnet på JSON-filen på sidan **Sammanfattning** och klicka på **Nästa**. 
6. Klicka på **Slutför** när distributionen är klar. 

När du distribuerar används värden från konfigurationsfilen till att ange värden för egenskaper i JSON-filerna för Data Factory-entiteter (länkade tjänster, tabeller och pipelines) innan entiteterna distribueras till Azure Data Factory-tjänsten.   

## Sammanfattning 
I den här självstudien skapade du en Azure-datafabrik som bearbetar data genom att köra ett Hive-skript i ett Hadoop-kluster i HDInsight. Du använde Data Factory-redigeraren i Azure Portal för följande steg:  

1.  Du skapade en Azure **Data Factory**.
2.  Du skapade två **länkade tjänster**:
    1.  En länkad **Azure-lagrings**tjänst som länkar din Azure blobblagring med in-/utdatafiler till datafabriken.
    2.  En länkad **Azure HDInsight**-tjänst på begäran som länkar ett Hadoop-kluster i HDInsight på begäran till datafabriken. Azure Data Factory skapar ett Hadoop-kluster i HDInsight i rätt tid för att bearbeta indata och skapa utdata. 
3.  Du skapade två **datauppsättningar** som beskriver in- och utdata för HDInsight Hive-aktiviteten i pipelinen. 
4.  Du skapade en **pipeline** med en **HDInsight Hive**-aktivitet.  


## Nästa steg
I den här artikeln har du skapat en pipeline med en transformeringsaktivitet (HDInsight-aktivitet) som kör ett Hive-skript på ett HDInsight-kluster på begäran. Om du vill se hur du använder en kopieringsaktivitet till att kopiera data från en Azure-blobb till Azure SQL kan du läsa mer i [Självstudie: Kopiera data från en Azure-blobb till Azure SQL](data-factory-get-started.md).
  
## Se även
| Avsnitt | Beskrivning |
| :---- | :---- |
| [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) | Den här artikeln innehåller en lista med de datatransformeringsaktiviteter (till exempel HDInsight Hive-transformeringen som du använde i självstudien) som stöds av Azure Data Factory. | 
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) | I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) | Den här artikeln hjälper dig förstå pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) | Den här artikeln hjälper dig att förstå datauppsättningar i Azure Data Factory.
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) | Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. 


<!--HONumber=jun16_HO2-->


