---
title: "Distribuera en modell för Azure Machine Learning (förhandsversion) | Microsoft Docs"
description: "I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversionen) från slutpunkt till slutpunkt. Detta är en del tre som beskriver hur du distribuerar modellen."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/29/2017
ms.openlocfilehash: 97cd46819a4547ec743270871bcb6b4eef3eb365
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="classify-iris-part-3-deploy-a-model"></a>Klassificera Iris del 3: Distribuera en modell
Azure Machine Learning (förhandsversionen) är en integrerad, avancerad lösning för datavetenskap och analys för datatekniker. Datatekniker kan använda den för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del tre i en serie med tre delar. I den här delen av självstudien använder du Azure Machine Learning (förhandsversion) för att:

> [!div class="checklist"]
> * Leta rätt på modellfilen.
> * Generera ett bedömningsskript och en schemafil.
> * Förbereda miljön.
> * Skapa en realtidswebbtjänst.
> * Köra realtidswebbtjänsten.
> * Granska blob-utdata. 

 Den här självstudien använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i Mac OS.

## <a name="prerequisites"></a>Förutsättningar
Gå igenom de första två delarna i självstudieserien:

   * Gå igenom [självstudien Förbereda data](tutorial-classifying-iris-part-1.md), där du får skapa Machine Learning-resurser och installera Azure Machine Learning Workbench.

   * Gå igenom [självstudien Skapa en modell](tutorial-classifying-iris-part-2.md) där du får skapa en Logistic Regression-modell i Azure Machine Learning.

En Docker-motor måste vara installerad och köras lokalt. Du kan även distribuera ett Azure Container Service-kluster i Azure.

## <a name="download-the-model-pickle-file"></a>Ladda ned pickle-modellfilen
I föregående del av självstudien kördes skriptet **iris_sklearn.py** i Machine Learning Workbench lokalt. Åtgärden serialiserade Logistic Regression-modellen med hjälp av det populära Python-paketet för objektserialisering [pickle](https://docs.python.org/2/library/pickle.html). 

1. Öppna Machine Learning Workbench och öppna sedan projektet **myIris** som du skapade i den förra delen av självstudieserien.

2. När projektet är öppet väljer du knappen **Filer** (mappikonen) i det vänstra fönstret för att öppna fillistan i projektmappen.

3. Välj filen **iris_sklearn.py**. Python-koden öppnas på en ny textredigeringsflik i Workbench.

4. Granska filen **iris_sklearn.py** och se efter var pickle-filen genererades. Använd CTRL + F för att öppna dialogrutan **Sök** och leta efter ordet **pickle** i Python-koden.

   Det här kodfragmentet visar hur pickle-utdatafilen skapades. Pickle-utdatafilen heter **model.pkl** på disken. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Leta rätt på pickle-modellfilen bland utdatafilerna från en tidigare körning.
   
   När du körde skriptet **iris_sklearn.py** skrevs modellfilen till mappen **outputs** och hade namnet **model.pkl**. Den här mappen finns i körningsmiljön som du valde att köra skriptet i och inte i den lokala projektmappen. 
   
   - Välj knappen **Körningar** (klockikonen) i det vänstra fönstret för att öppna listan över **All körningar**.  
   - Fliken **Alla körningar** öppnas. Markera någon av de senaste körningarna i tabellen, där målet var **lokalt** och namnet på skriptet var **iris_sklearn.py**. 
   - Fönstret **Körningsegenskaper** öppnas. Notera avsnittet **Utdata** längst upp till höger i fönstret. 
   - Ladda ned pickle-filen genom att markera kryssrutan bredvid filen **model.pkl** och välja knappen **Ladda ned**. Spara den i roten för projektmappen. Filen behövs i kommande steg.

   ![Ladda ned pickle-filen](media/tutorial-classifying-iris/download_model.png)

   Läs mer om mappen `outputs` i artikeln [Läsa och skriva stora datafiler](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Generera ett bedömningsskript och schemafilerna
Om du vill distribuera webbtjänsten behöver du förutom modellfilen även ett bedömningsskript och eventuellt ett schema för indata till webbtjänsten. Bedömningsskriptet läser in filen **model.pkl** från den aktuella mappen och använder den till att skapa en nyligen förutspådd Iris-klass.  

1. Öppna Azure Machine Learning Workbench och öppna sedan projektet **myIris** som du skapade i den förra delen av självstudieserien.

2. När projektet är öppet väljer du knappen **Filer** (mappikonen) i det vänstra fönstret för att öppna fillistan i projektmappen.

3. Välj filen **iris_score.py**. Python-skriptet öppnas. Den här filen används som bedömningsfil.

   ![Bedömningsfil](media/tutorial-classifying-iris/model_data_collection.png)

4. Kör skriptet för att skapa schemafilen. Välj miljön **lokalt** och skriptet **score_iris.py** i kommandofältet och välj sedan knappen **Kör**. 

5. Det här skriptet skapar en JSON-fil i avsnittet **Utdata**, som fångar det indataschema som behövs i modellen.

6. Notera fönstret **Jobb** till höger om fönstret **Project Dashboard** (Instrumentpanel för projekt). Vänta tills det senaste jobbet **score_iris.py** visar den gröna statusen **Slutfört**. Välj hyperlänken **score_iris.py [1]** för den senaste jobbkörningen så att du ser körningsdetaljerna för körningen av **score_iris.py**. 

7. I avsnittet **Utdata** på sidan **Körningsegenskaper** väljer du den nya filen **service_schema.json**.  Markera kryssrutan bredvid namnet på filen och välj sedan **Ladda ned**. Spara filen i rotmappen för projektet.

8. Gå tillbaka till den föregående fliken där du öppnade skriptet **score_iris.py**. Genom att använda datainsamling kan du fånga modellindata och förutsägelser från webbtjänsten. Följande steg är särskilt intressanta vid datainsamling.

9. Granska koden längst upp i filimportklassen **ModelDataCollector** eftersom den innehåller modellens datainsamlingsfunktioner:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Granska följande kodrader i funktionen **init()** som skapar en instans av **ModelDataCollector**:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Granska följande kodrader i funktionen **run(input_df)** eftersom den samlar in indata och förutsägelser:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Du är nu redo att förbereda miljön för att operationalisera modellen.



## <a name="prepare-to-operationalize-locally"></a>Förbereda för att operationalisera lokalt
Använd _lokalt läge_ som distribution för att köra i Docker-behållare lokalt.

Du kan använda _lokalt läge_ för utveckling och testning. Docker-motorn måste köras lokalt för att utföra följande steg och operationalisera modellen. Du kan använda flaggan `-h` i slutet av kommandon för att visa hjälp om kommandot.

>[!NOTE]
>Om du inte har Docker-motorn lokalt kan du ändå gå vidare genom att skapa ett kluster i Azure för distribution. Se bara till att ta bort klustret efter självstudien så att du inte drar på dig löpande kostnader.

1. Öppna kommandoradsgränssnittet (CLI).
   Välj på **Öppna kommandotolken** i menyn **Arkiv** i Azure Machine Learning Workbench.

   Kommandotolken öppnas i den aktuella projektmappen **c:\temp\myIris>**.

2. Skapa miljön. Du måste utföra det här steget en gång per miljö. Exempelvis utför du det en gång för utvecklingsmiljön och en gång för produktion. Använd _lokalt läge_ för den här första miljön. Du kan prova med flaggorna `-c` eller `--cluster` i följande kommando om du vill konfigurera en miljö i _klusterläge_ senare.

   Observera att följande installationskommando kräver att du har deltagarbehörighet inom ramen för prenumerationen. Om du inte har det, behöver du som minst deltagarbehörighet för resursgruppen som du distribuerar till. Om du vill göra det senare måste du ange resursgruppens namn som en del av installationskommandot med flaggan `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Följ anvisningarna på skärmen för att etablera ett lagringskonto för lagring av Docker-avbildningar, en Azure Container Registry för listning av Docker-avbildningar och ett AppInsight-konto samlar in telemetri. Om du använde flaggan `-c` skapar den även ett Azure Container Service-kluster.
   
   Klusternamnet är ett sätt att identifiera miljön. Platsen måste vara samma som för modellhanteringskontot som du skapade från Azure Portal.

3. Skapa ett modellhanteringskonto. (Du behöver bara utföra den här konfigurationen en gång.)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. Konfigurera modellhanteringskontot.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. Konfigurera miljön.

   När installationen är klar använder du följande kommando för att ställa in de miljövariabler som behövs för operationalisering av miljön. Använd samma miljönamn, som du använde tidigare i steg 2. Använd samma resursgruppnamn som angavs i kommandofönstret när installationen slutfördes.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. Ange följande kommando för att kontrollera att du har konfigurerat operationaliseringsmiljön på rätt sätt för distribution av lokala webbtjänster:

   ```azurecli
   az ml env show
   ```

Nu är du redo att skapa realtidswebbtjänsten.

>[!NOTE]
>Du kan återanvända ditt modellhanteringskonto och -miljö för efterföljande webbtjänstdistributioner. Du behöver inte skapa dem för varje webbtjänst. Ett konto eller en miljö kan ha flera webbtjänster kopplade till sig.

## <a name="create-a-real-time-web-service-in-one-command"></a>Skapa en realtidswebbtjänst med ett kommando
1. Använd följande kommando för att skapa en realtidswebbtjänst:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Det här kommandot genererar ett webbtjänst-ID som du kan använda senare.

   Följande växlar används med kommandot **az ml service create realtime**:
   * `-n`: Appnamnet som måste skrivas med små bokstäver.
   * `-f`: Namnet på bedömningsskriptet.
   * `--model-file`: Modellfilen. I det här fallet är det pickle-filen model.pkl.
   * `-r`: Typ av modell. I det här fallet är det en Python-modell.
   * `--collect-model-data true`: Det här aktiverar datainsamling.
   * `-c`: Sökvägen till filen conda-beroenden där ytterligare paket har angetts.

   >[!IMPORTANT]
   >Tjänstnamnet, som även är namnet på den nya Docker-avbildningen, måste skrivas med små bokstäver. Annars får du ett felmeddelande. 

2. När du kör kommandot laddas modell- och bedömningsfilen upp till det lagringskonto du skapade i miljökonfigurationen. I distributionsprocessen skapas en Docker-avbildning med din modell, ditt schema och din bedömningsfil, som sedan skickas till Azure Container Registry: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   Kommandot hämtar sedan avbildningen lokalt till datorn och startar en Docker-behållare baserat på avbildningen. Om miljön är konfigurerad i klusterläge distribueras Docker-behållaren till Azure Container Registry Kubernetes-klustret i stället.

   Som en del av distributionen skapas en HTTP REST-slutpunkt för webbtjänsten lokalt på datorn. Efter några minuter bör kommandot slutföras med ett meddelande, så nu är webbtjänsten färdig att börja användas.

3. Använd kommandot **docker ps** för att se Docker-behållaren som körs:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Skapa en realtidswebbtjänst med separata kommandon
Som ett alternativ till kommandot **az ml service create realtime** som visades tidigare kan du utföra stegen separat. 

Registrera först modellen. Generera sedan manifestet, skapa Docker-avbildningen och skapa webbtjänsten. Med den här stegvisa metoden får du mer flexibilitet vid varje steg. Du kan även återanvända entiteter som genererats i föregående steg så att du endast återskapar entiteter när det behövs.

1. Registrera modellen genom att ange pickle-filnamnet

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Det här kommandot genererar ett modell-ID.

2. Skapa ett manifest.

   Använd följande kommando för att skapa ett manifest och ange modell-ID:t från föregående steg:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Det här kommandot genererar ett manifest-ID.

3. Skapa en Docker-avbildning.

   Använd följande kommando för att skapa en Docker-avbildning och ange manifest-ID:t från föregående steg. Du kan även inkludera conda-beroenden med hjälp av växeln `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c amlconfig\conda_dependencies.yml
   ```
   Det här kommandot genererar ett Docker-avbildnings-ID.
   
4. Skapa tjänsten.

   Använd följande kommando för att skapa en tjänst och ange avbildnings-ID:t från föregående steg:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Det här kommandot genererar ett webbtjänst-ID.

Nu är du redo att köra webbtjänsten.

## <a name="run-the-real-time-web-service"></a>Köra realtidswebbtjänsten

Testa **irisapp**-webbtjänsten som körs med hjälp av en JSON-kodad post med en matris med fyra slumptal:

1. Webbtjänsten innehåller exempeldata. Vid körning i lokalt läge kan du anropa kommandot **az ml service usage realtime**. Anropet hämtar ett exempelkörningskommando som är användbart när du testar tjänsten. Anropet hämtar även en bedömnings-URL som du kan använda när du ska införliva tjänsten i dina egna anpassade appar:

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. Om du vill testa tjänsten kör du tjänstkörningskommandot som returnerades:

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   Utdata är **”2”**, som är den förväntade klassen. (Dina resultat kan vara annorlunda.) 

3. Om du vill köra tjänsten utanför CLI-fönstret måste du hämta nycklar för autentisering:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Visa insamlade data i Azure Blob Storage

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta rätt på dina lagringskonton. Det gör du genom att välja **Fler tjänster**.

3. I sökrutan skriver du **Lagringskonton** och sedan trycker du på **Retur**.

4. Från sökrutan **Lagringskonton** väljer du den **lagringskontoresurs** som motsvarar din miljö. 

   > [!TIP]
   > Gör så här för att avgöra vilket lagringskonto som används:
   > 1. Öppna Azure Machine Learning Workbench.
   > 2. Välj det projekt som du arbetar med.
   > 3. Öppna kommandotolken från menyn **Arkiv**.
   > 4. Skriv `az ml env show -v` i kommandoraden och kontrollera värdet *storage_account*. Det här är namnet på ditt lagringskonto.

5. När fönstret med **lagringskontot** har öppnats väljer du **Behållare** i listan till vänster. Leta rätt på behållaren med namnet **modeldata**. 
 
   Om du inte ser några data kan du behöva vänta i upp till 10 minuter efter den första begäran till webbtjänsten för att se data spridas till lagringskontot.

   Data flödar till blobar med följande behållarsökväg:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Du kan använda dessa data från Azure Blob Storage. Det finns en mängd olika verktyg som använder både Microsoft-program och verktyg med öppen källkod, som till exempel:

   - Azure Machine Learning: Öppna csv-filen genom att lägga till csv-filen som en datakälla. 
   - Excel: Öppna de dagliga csv-filerna som ett kalkylblad.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): Skapa diagram med data från csv-data i blobar.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): Läs in csv-data till en Hive-tabell och kör SQL-frågor direkt mot blobar.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): Skapa en DataFrame med en stor andel csv-data.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Nästa steg
I den här tredje delen av självstudieserien i tre delar har du lärt dig att använda Azure Machine Learning för att:
> [!div class="checklist"]
> * Leta rätt på modellfilen.
> * Generera ett bedömningsskript och en schemafil.
> * Förbereda miljön.
> * Skapa en realtidswebbtjänst.
> * Köra realtidswebbtjänsten.
> * Granska blob-utdata. 

Du har nu kört ett inlärningsskript i olika beräkningsmiljöer, skapat en modell, serialiserat modellen och operationaliserat modellen via en Docker-baserad webbtjänst. 

Nu är du redo för avancerad förberedelse av data:
> [!div class="nextstepaction"]
> [Avancerad databearbetning](tutorial-bikeshare-dataprep.md)
