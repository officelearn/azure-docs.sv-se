---
title: "Distribuera en modell för Azure Machine Learning (förhandsversion) | Microsoft Docs"
description: "I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversion) från slutpunkt till slutpunkt. Det här är del 3 om att distribuera modellen."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/27/2017
ms.openlocfilehash: 048d734277f855086a48ad00a52b873adbf419b4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="classifying-iris-part-3-deploy-a-model"></a>Klassificera Iris del 3: distribuera en modell
Azure Machine Learning (förhandsversion) är en integrerad, avancerad lösning för datavetenskap och analys, som datatekniker kan använda till att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del tre i en serie med tre delar. I den här delen av självstudien använder du Azure Machine Learning (förhandsversion) till att lära dig att:

> [!div class="checklist"]
> * Leta rätt på modellfilen
> * Generera ett bedömningsskript och en schemafil
> * Förbereda miljön
> * Skapa en realtidswebbtjänst
> * Köra realtidswebbtjänsten
> * Granska blob-utdata 

 I den här självstudien gör vi det enkelt för oss och använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i macOS.

## <a name="prerequisites"></a>Krav
Gå igenom de första två delarna i självstudieserien:

1. Gå igenom [självstudien Förbereda data](tutorial-classifying-iris-part-1.md), där du får skapa Azure Machine Learning-resurser och installera Azure Machine Learning Workbench.

2. Gå igenom [självstudien Skapa en modell](tutorial-classifying-iris-part-2.md) där du får skapa en Logistic Regression-modell i Azure Machine Learning.

3. Docker-motorn måste vara installerad och köras lokalt. Du kan även distribuera ett Azure Container Service-kluster i Azure.

## <a name="download-the-model-pickle-file"></a>Ladda ned pickle-modellfilen
I föregående del av självstudien kördes skriptet **iris_sklearn.py** i Azure Machine Learning Workbench lokalt. Åtgärden serialiserade Logistic Regression-modellen med hjälp av det populära Python-paketet för objektserialisering **[pickle](https://docs.python.org/2/library/pickle.html)**. 

1. Starta **Azure Machine Learning Workbench** och öppna projektet **myIris** som du skapade i den förra delen av självstudieserien.

2. När projektet är öppet klickar du på knappen **Filer** (mappikon) i det vänstra verktygsfältet i Azure Machine Learning Workbench, så att du öppnar fillistan i projektmappen.

3. Om du markerar filen **iris_sklearn.py** viss Python-koden i en ny textredigeringsflik i Workbench.

4. Granska filen **iris_sklearn.py** och se efter var pickle-filen genererades. Använd CTRL + F för att öppna dialogrutan Sök och leta efter ordet **pickle** i python-koden.

   Det här kodstycket visar hur pickle-utdatafilen skapades. Lägg märke till att pickle-utdatafilen heter **model.pkl** på disken. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Leta rätt på pickle-modellfilen bland utdatafilerna från en tidigare körning.
   
   När du körde skriptet **iris_sklearn.py** skrevs modellfilen till mappen **outputs** och hade namnet **model.pkl**. Den här mappen finns i körningsmiljön du valde att köra skriptet i, och inte i den lokala projektmappen. 
   
   - Du kan hitta filen genom att öppna Azure Machine Learning Workbench och klicka på fliken **Körningar** (klockikonen) i det vänstra verktygsfältet så att listan **Alla körningar** visas.  
   - Fliken **Alla körningar** öppnas. Markera någon av de senaste körningarna i tabellen, där målet var **lokalt** och namnet på skriptet var **iris_sklearn.py**. 
   - Sidan **Körningsegenskaper** öppnas. Notera avsnittet **Utdata** uppe till höger på sidan. 
   - Ladda ned pickle-filen genom att markera kryssrutan bredvid filen **model.pkl** och klicka på knappen **Ladda ned**. Spara den i roten för projektmappen. Du behöver den i kommande steg.

   ![Ladda ned pickle-filen](media/tutorial-classifying-iris/download_model.png)

   Läs mer om mappen med **utdata** i artikeln [Läsa och skriva stora datafiler](how-to-read-write-files.md).

## <a name="get-scoring-and-schema-files"></a>Skapa bedömnings- och schemafiler
Om du vill distribuera webbtjänsten behöver du förutom modellfilen även ett bedömningsskript och eventuellt ett schema för indata till webbtjänsten. Bedömningsskriptet läser in filen **model.pkl** från den aktuella mappen och använder den till att skapa en nyligen förutspådd Iris-klass.  

1. Starta **Azure Machine Learning Workbench** och öppna projektet **myIris** som du skapade i den förra delen av självstudieserien.

2. När projektet är öppet klickar du på knappen **Filer** (mappikon) i det vänstra verktygsfältet i Azure Machine Learning Workbench, så att du öppnar fillistan i projektmappen.

3. Välj filen **iris_score.py**. Python-skriptet öppnas. Den här filen används som bedömningsfil.

   ![Bedömningsfil](media/tutorial-classifying-iris/model_data_collection.png)

4. Du skapar schemafilen genom att köra skriptet. Välj miljön **lokalt** och skriptet **iris-score.py** i kommandofältet och klicka sedan på knappen **Kör**. 

5. Det här skriptet skapar en JSON-fil i mappen **outputs**, som fångar det indataschema som behövs i modellen.

6. Lägg märke till fönstret Jobb till höger om Machine Learning Workbench-fönstret. Vänta tills det senaste jobbet **iris-score.py** visar den gröna statusen **Slutfört**. Klicka på hyperlänken **iris-score.py [1]** för den senaste jobbkörningen så att du ser körningsdetaljerna för körningen av **iris-score.py**. 

7. I avsnittet **Utdata** på sidan Körningsegenskaper väljer du den nya filen **service_schema.json**. **Markera** filen och klicka på **Ladda ned**. Spara filen i rotmappen för projektet.

8. Gå tillbaka till den föregående fliken där du öppnade skriptet **iris-score.py**. 

   Notera användningen av datainsamling som låter dig fånga modellindata och förutsägelser från webbtjänsten. Följande punkter är särskilt intressanta vid datainsamling:

9. Granska koden längst upp i filimportklassen ModelDataCollector, som innehåller modellens datainsamlingsfunktioner:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Granska följande kodrader i funktionen **init()** som skapar en instans av ModelDataCollector:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Granska följande kodrader i funktionen **run(input_df)** som samlar in indata och förutsägelser:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Du är nu redo att förbereda miljön för att operationalisera modellen.

>[!NOTE]
>För att distribuera modeller måste du ha ägaråtkomst till en Azure-prenumeration.

## <a name="prepare-to-operationalize-locally"></a>Förbereda för att operationalisera lokalt
Använd _lokalt läge_ som distribution för att köra i Docker-behållare lokalt.

Du kan använda _lokalt läge_ för utveckling och testning. Docker-motorn måste köras lokalt när du ska utföra följande steg och operationalisera modellen. Du kan använda flaggan `-h` i slutet av kommandon för att visa hjälp om kommandot.

>[!NOTE]
>Om du inte har Docker-motorn lokalt kan du ändå gå vidare genom att skapa ett kluster i Azure för distribution. Se bara till att ta bort klustret efter självstudien så att du inte drar på dig löpande kostnader.

1. Öppna kommandotolken i Azure Machine Learning Workbench genom att klicka på **Öppna kommandotolken** på menyn Arkiv.

   Kommandotolken öppnas i den aktuella projektmappen **c:\temp\myIris>**.

2. Kontrollera att Azure-resursprovidern **Microsoft.ContainerRegistry** är registrerad för din prenumeration. Registrera den här resursprovidern innan du kan skapa en miljö i steg 3. Du kan kontrollera om den redan är registrerad med följande kommando:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Du bör se utdata som liknar följande: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Om **Microsoft.ContainerRegistry** inte är registrerad kan du registrera den med hjälp av följande kommando:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   Registreringen kan ta några minuter, och du kan kontrollera status för den med hjälp av ovanstående **az provider list** eller med följande kommando:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   Den tredje utdataraden visar **"registrationState": "Registering"**. Vänta en stund och upprepa visningskommandot tills utdata visar **"registrationState": "Registered"**.

3. Skapa miljön. Det här steget måste köras en gång per miljö (exempelvis en gång för utvecklingsmiljön och en gång för produktion). Använd _lokalt läge_ för den här första miljön. (Du kan prova med flaggorna `-c` eller `--cluster` i följande kommando om du vill konfigurera en miljö i _klusterläge_ senare.)

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Följ anvisningarna på skärmen för att etablera ett lagringskonto för lagring av Docker-avbildningar, en ACR (Azure Container Registry) för listning av Docker-avbildningar och ett AppInsight-konto för att samla in telemetri. Om du använde flaggan `-c` skapas även ett ACS-kluster (Azure Container Service).
   
   Klusternamnet är ett sätt att identifiera miljön. Platsen måste vara samma som för modellhanteringskontot du skapade på Azure Portal.

4. Skapa ett modellhanteringskonto (det här behöver du bara göra en gång).  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Konfigurera modellhanteringskontot.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Konfigurera miljön.
När installationen är klar ställer du in de miljövariabler som behövs för operationaliseringen med följande kommando: Använd samma miljönamn som du använde tidigare i steg 4. Använd samma resursgruppnamn som angavs i kommandofönstret när installationen slutfördes.
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. Ange följande kommando för att kontrollera att du har konfigurerat operationaliseringsmiljön på rätt sätt för distribution av lokala webbtjänster:

   ```azurecli
   az ml env show
   ```

Nu är du redo att skapa realtidswebbtjänsten.

>[!NOTE]
>Du kan återanvända ditt modellhanteringskonto och -miljö för efterföljande webbtjänstdistributioner. Du behöver inte skapa dem för varje webbtjänst. Ett konto eller en miljö kan ha flera webbtjänster kopplade till sig.

## <a name="create-a-real-time-web-service-in-one-command"></a>Skapa en realtidswebbtjänst med ett kommando
1. Kör följande kommando för att skapa en realtidswebbtjänst:

   ```azurecli
   az ml service create realtime -f iris-score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Det här genererar ett webbtjänst-ID som du kan använda senare.

   Följande växlar används med kommandot **az ml service create realtime**:
   * -n: appnamnet, måste skrivas med små bokstäver.
   * -f: namnet på bedömningsskriptet
   * --model-file: modellfilen, i det här fallet pickle-filen model.pkl
   * -r: typen av modell, i det här fallet en python-modell
   * --collect-model-data true: aktiverar datainsamling

   >[!IMPORTANT]
   >Tjänstnamnet (som också är namnet på den nya Docker-avbildningen) måste skrivas med små bokstäver, annars genereras ett fel. 

2. När du kör kommandot laddas modell- och bedömningsfilen upp till det lagringskonto du skapade i miljökonfigurationen. I distributionsprocessen skapas en Docker-avbildning med din modell, schemat och bedömningsfilen, som sedan skickas till ACR-registret: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   Avbildningen hämtas sedan lokalt till datorn, och en Docker-behållare startas baserat på avbildningen. Om miljön är konfigurerad i klusterläge distribueras Docker-behållaren till ACS Kubernetes-klustret i stället.

   Som en del av distributionen skapas en HTTP REST-slutpunkt för webbtjänsten lokalt på datorn. Efter några minuter bör kommandot slutföras med ett meddelande, så nu är webbtjänsten färdig att börja användas!

3. Du kan se Docker-behållaren som körs med kommandot **docker ps**:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-using-separate-commands"></a>Skapa en realtidswebbtjänst med separata kommandon
Som ett alternativ till kommandot **az ml service create realtime** ovan kan du utföra stegen separat. Registrera först modellen, generera sedan manifestet, skapa Docker-avbildningen och skapa webbtjänsten. Med den här stegvisa metoden får du mer flexibilitet vid varje steg. Du kan även återanvända entiteter som genererats i föregående steg så att du endast återskapar entiteter när det behövs.

1. Registrera modellen genom att ange pickle-filnamnet

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Det här kommandot genererar ett modell-ID.

2. Skapa manifestet

   Använd det här kommandot när du ska skapa ett manifest, och ange modell-ID:t från föregående steg:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris-score.py -r python -i <model ID> -s service_schema.json
   ```
   Det här kommandot genererar ett manifest-ID.

3. Skapa en Docker-avbildning

   Använd det här kommandot när du ska skapa en Docker-avbildning, och ange manifest-ID:t från föregående steg:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Det här kommandot genererar ett Docker-avbildnings-ID.
   
4. Skapa tjänsten

   Använd det här kommandot när du ska skapa en tjänst, och ange avbildnings-ID:t från föregående steg:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Det här kommandot genererar ett webbtjänst-ID.

Nu är du redo att köra webbtjänsten.

## <a name="run-the-real-time-web-service"></a>Köra realtidswebbtjänsten

Testa **irisapp**-webbtjänsten som körs genom att skicka en JSON-kodad post till den, med en matris med fyra slumptal.

1. Den webbtjänst du skapade innehåller exempeldata. Vid körning i lokalt läge kan du anropa kommandot **az ml service show realtime**. Anropet hämtar ett exempelkörningskommando som är användbart när du testar tjänsten. Då får du även en bedömnings-URL som du kan använda när du ska införliva tjänsten i dina egna anpassade appar:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Om du vill testa tjänsten kör du tjänstkörningskommandot som returnerades.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   Utdata är **”2”**, som är den förväntade klassen. (Dina resultat kan vara annorlunda.) 

3. Om du vill köra tjänsten utanför CLI-fönstret måste du hämta nycklar för autentisering:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Visa insamlade data i Azure Blob Storage

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Leta rätt på dina lagringskonton. Det gör du genom att klicka på **Fler tjänster**.

3. I sökrutan skriver du **lagringskonton** och trycker på **Retur**.

4. På söksidan **Lagringskonton** väljer du den **lagringskontoresurs** som motsvarar din miljö. 

   > [!TIP]
   > Du kan se vilket lagringskonto som används genom att öppna Azure Machine Learning Workbench, välja det projekt du arbetar med, öppna kommandotolken från menyn **Arkiv**, skriva `az ml env show -v` och kontrollera värdet för *storage_account*. Det här är namnet på ditt lagringskonto.

5. När sidan **Lagringskonto** öppnas klickar du objektet **Behållare** i den vänstra listan. Leta rätt på behållaren med namnet **modeldata**. 
 
   Om du inte ser några data kan du behöva vänta upp till 10 minuter efter den första begäran för webbtjänsten innan data börjar spridas till lagringskontot.

   Data flödar till blobar med följande behållarsökväg:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Du kan använda dessa data från Azure-blobar. Det finns en mängd olika verktyg vid användning av både Microsoft-program och verktyg med öppen källkod som:

   - Azure ML Workbench: öppna csv-filen i Azure ML Workbench genom att lägga till csv-filen som en datakälla. 
   - Excel: öppna den dagliga csv-filen som ett kalkylblad.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): skapa diagram med data från csv-data i blobar.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): läs in csv-data till en Hive-tabell och kör SQL-frågor direkt mot bloben.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): skapa en DataFrame med en stor andel csv-data.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Nästa steg
I den här tredje delen av självstudieserien i tre delar har du lärt dig att använda Azure Machine Learning till att:
> [!div class="checklist"]
> * Leta rätt på modellfilen
> * Generera ett bedömningsskript och en schemafil
> * Förbereda miljön
> * Skapa en realtidswebbtjänst
> * Köra realtidswebbtjänsten
> * Granska blob-utdata 

Du har nu kört ett inlärningsskript i olika beräkningsmiljöer, skapat en modell, serialiserat modellen och operationaliserat modellen via en Docker-baserad webbtjänst. 

Nu är du redo för avancerad databearbetning:
> [!div class="nextstepaction"]
> [Avancerad databearbetning](tutorial-bikeshare-dataprep.md)

