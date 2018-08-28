---
title: Distribuera en självstudie om modeller i Azure Machine Learning
description: I den här kompletta självstudien får du lära dig att använda Azure Machine Learning från slutpunkt till slutpunkt. Detta är en del tre som beskriver hur du distribuerar modellen.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ms.openlocfilehash: de0c93ef5b907b56e6ad66a04bb728b5b9aabb9a
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "41918710"
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>Självstudie 3: Klassificera Iris – distribuera en modell
Azure Machine Learning (förhandsversionen) är en integrerad, avancerad lösning för datavetenskap och analys för datatekniker. Datatekniker kan använda den för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är **del tre i en serie med tre delar**. I den här delen av självstudien använder du Machine Learning (förhandsversion) för att:

> [!div class="checklist"]
> * Leta rätt på modellfilen.
> * Generera ett bedömningsskript och en schemafil.
> * Förbereda miljön.
> * Skapa en realtidswebbtjänst.
> * Köra realtidswebbtjänsten.
> * Granska blob-utdata. 

Den här självstudien använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:
- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
- Ett experimentkonto och Azure Machine Learning Workbench installerad enligt anvisningarna i denna [snabbstart](../service/quickstart-installation.md)
- Klassificeringsmodellen från [del 2 av självstudiekursen](tutorial-classifying-iris-part-2.md)
- En Docker-motor måste vara installerad och köras lokalt

## <a name="download-the-model-pickle-file"></a>Ladda ned pickle-modellfilen
I föregående del av självstudien kördes skriptet **iris_sklearn.py** i Machine Learning Workbench lokalt. Den här åtgärden serialiserade Logistic Regression-modellen med hjälp av det populära Python-paketet för objektserialisering [pickle](https://docs.python.org/3/library/pickle.html). 

1. Öppna programmet Machine Learning Workbench. Öppna projektet **myIris** som du skapade i de tidigare delarna av självstudieserien.

1. När projektet är öppet väljer du knappen **Filer** (mappikonen) i det vänstra fönstret för att öppna fillistan i projektmappen.

1. Välj filen **iris_sklearn.py**. Python-koden öppnas på en ny textredigeringsflik i Workbench.

1. Granska filen **iris_sklearn.py** och se efter var pickle-filen genererades. Använd CTRL + F för att öppna dialogrutan **Sök** och leta efter ordet **pickle** i Python-koden.

   Det här kodfragmentet visar hur pickle-utdatafilen skapades. Pickle-utdatafilen heter **model.pkl** på disken. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

1. Leta rätt på pickle-modellfilen bland utdatafilerna från en tidigare körning.
   
   När du körde skriptet **iris_sklearn.py** skrevs modellfilen till mappen **outputs** och hade namnet **model.pkl**. Den här mappen finns i körningsmiljön som du valde att köra skriptet i och inte i den lokala projektmappen. 
   
   a. Välj knappen **Körningar** (klockikonen) i det vänstra fönstret för att öppna listan över **All körningar**. 

   b. Fliken **Alla körningar** öppnas. Markera någon av de senaste körningarna i tabellen, där målet var **lokalt** och namnet på skriptet var **iris_sklearn.py**. 

   c. Fönstret **Körningsegenskaper** öppnas. Notera avsnittet **Utdata** längst upp till höger i fönstret.

   d. Ladda ned pickle-filen genom att markera kryssrutan bredvid filen **model.pkl** och välja **Ladda ned**. Spara filen i roten för projektmappen. Filen behövs i kommande steg.

   ![Ladda ned pickle-filen](media/tutorial-classifying-iris/download_model.png)

   Läs mer om mappen `outputs` i [Läsa och skriva stora datafiler](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Generera ett bedömningsskript och schemafilerna
Om du vill distribuera webbtjänsten tillsammans med modellfilen behöver du också ett bedömningsskript. Du kan också behöva ett schema för webbtjänstens indata. Bedömningsskriptet läser in filen **model.pkl** från den aktuella mappen och använder den för att skapa nya förutsägelser.

1. Öppna programmet Machine Learning Workbench. Öppna projektet **myIris** som du skapade i den förra delen av självstudieserien.

1. När projektet är öppet väljer du knappen **Filer** (mappikonen) i det vänstra fönstret för att öppna fillistan i projektmappen.

1. Välj filen **iris_score.py**. Python-skriptet öppnas. Den här filen används som bedömningsfil.

   ![Bedömningsfil](media/tutorial-classifying-iris/model_data_collection.png)

1. Kör skriptet för att skapa schemafilen. Välj miljön **lokalt** och skriptet **score_iris.py** i kommandofältet och välj sedan **Kör**. 

   Det här skriptet skapar en JSON-fil i avsnittet **Utdata**, som fångar det indataschema som behövs i modellen.

1. Notera fönstret **Jobb** till höger om fönstret **Project Dashboard** (Instrumentpanel för projekt). Vänta tills det senaste jobbet **score_iris.py** visar den gröna statusen **Slutfört**. Välj hyperlänken **score_iris.py** för den senaste jobbkörningen så att du ser körningsdetaljerna för körningen. 

1. I avsnittet **Utdata** på sidan **Körningsegenskaper** väljer du den nya filen **service_schema.json**. Markera kryssrutan bredvid namnet på filen och välj sedan **Ladda ned**. Spara filen i rotmappen för projektet.

1. Gå tillbaka till den föregående fliken där du öppnade skriptet **score_iris.py**. Genom att använda datainsamling kan du fånga modellindata och förutsägelser från webbtjänsten. Följande steg är särskilt intressanta vid datainsamling.

1. Granska koden längst upp i filen som importerar klassen **ModelDataCollector** eftersom den innehåller modellens datainsamlingsfunktioner:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

1. Granska följande kodrader i funktionen **init()** som skapar en instans av **ModelDataCollector**:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

1. Granska följande kodrader i funktionen **run(input_df)** eftersom den samlar in indata och förutsägelser:

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

Du är nu redo att förbereda miljön för att operationalisera modellen.

## <a name="prepare-to-operationalize-locally-for-development-and-testing-your-service"></a>Förbereda för att operationalisera lokalt [för utveckling och testning av din tjänst]
Använd _lokalt läge_ som distribution för att köra i Docker-containrar lokalt.

Du kan använda _lokalt läge_ för utveckling och testning. Docker-motorn måste köras lokalt när du ska utföra följande steg och operationalisera modellen. Du kan använda flaggan `-h` i slutet av varje kommando för att visa motsvarande hjälpmeddelande.

>[!NOTE]
>Om du inte har Docker-motorn lokalt kan du ändå gå vidare genom att skapa ett kluster i Azure för distribution. Du kan behålla klustret för återanvändning eller ta bort det efter självstudien så att du inte drar på dig löpande kostnader.

>[!NOTE]
>Webbtjänster som distribuerats lokalt visas inte i Azure Portals lista över tjänster. De körs i Docker på den lokala datorn.

1. Öppna kommandoradsgränssnittet (CLI).
   Välj på **Öppna kommandotolken** i menyn **Arkiv** i Machine Learning Workbench.

   Kommandotolken öppnas i den aktuella projektmappen **c:\temp\myIris>**.


1. Kontrollera att Azure-resursprovidern **Microsoft.ContainerRegistry** är registrerad för din prenumeration. Du måste registrera den här resursprovidern innan du kan skapa en miljö i steg 3. Du kan kontrollera om den redan är registrerad med följande kommando:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Du bör se utdata som ser ut så här: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Om **Microsoft.ContainerRegistry** inte är registrerad så kan du registrera den med hjälp av följande kommando:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   Registreringen kan ta några minuter. Du kan kontrollera status för den med hjälp av det ovanstående kommandot **az provider list** eller med följande kommando:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   Den tredje utdataraden visar **"registrationState": "Registering"**. Vänta en stund och upprepa **visningskommandot** tills utdata visar **"registrationState": "Registered"**.

   >[!NOTE] 
   Om du distribuerar till ett ACS-kluster måste du registrera **Microsoft.ContainerService**-resursprovidern och använda exakt samma metod.

1. Skapa miljön. Du måste utföra det här steget en gång per miljö. Exempelvis utför du det en gång för utvecklingsmiljön och en gång för produktion. Använd _lokalt läge_ för den här första miljön. Du kan prova med flaggorna `-c` eller `--cluster` i följande kommando om du vill konfigurera en miljö i _klusterläge_ senare.

   Följande installationskommando kräver att du har deltagarbehörighet inom ramen för prenumerationen. Om du inte har det behöver du som minst deltagarbehörighet för resursgruppen som du distribuerar till. Om du vill göra det senare måste du ange resursgruppens namn som en del av installationskommandot med flaggan `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Följ anvisningarna på skärmen för att etablera ett lagringskonto för lagring av Docker-avbildningar, en Azure Container Registry för listning av Docker-avbildningar och ett Azure Application Insights-konto som samlar in telemetri. Om du använder växeln `-c` skapar kommandot även ett Container Service-kluster.
   
   Klusternamnet är ett sätt att identifiera miljön. Platsen måste vara samma som för modellhanteringskontot som du skapade från Azure Portal.

   Kontrollera statusen med följande kommando för att se om miljön är rätt konfigurerad:

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   Kontrollera att Etableringsstatus har värdet ”Succeeded”, se nedan, innan du ställer in miljön i steg 5:

   ![Etableringsstatus](media/tutorial-classifying-iris/provisioning_state.png)
 
1. Om du inte skapade ett konto för modellhantering i tidigare delar av den här självstudien bör du göra det nu. Du behöver bara utföra den här konfigurationen en gång.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
1. Konfigurera modellhanteringskontot.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

1. Konfigurera miljön.

   När installationen är klar använder du följande kommando för att ställa in de miljövariabler som behövs för operationalisering av miljön. Använd samma miljönamn som du använde tidigare i steg 3. Använd samma resursgruppnamn som angavs i kommandofönstret när installationen slutfördes.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

1. Ange följande kommando för att kontrollera att du har konfigurerat operationaliseringsmiljön på rätt sätt för distribution av lokala webbtjänster:

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

   * `-f`: Namnet på bedömningsskriptet.

   * `--model-file`: Modellfilen. I det här fallet är det pickle-filen model.pkl.

   * `-s`: tjänstens schema. Detta skapades i ett tidigare steg genom att köra skriptet **score_iris.py** lokalt.

   * `-n`: Appnamnet som måste skrivas med små bokstäver.

   * `-r`: Körning av modellen. I det här fallet är det en Python-modell. Giltiga körningar är `python` och `spark-py`.

   * `--collect-model-data true`: Den här växeln aktiverar datainsamling.

   * `-c`: Sökvägen till filen conda-beroenden där ytterligare paket har angetts.

   >[!IMPORTANT]
   >Tjänstnamnet, som även är namnet på den nya Docker-avbildningen, måste skrivas med små bokstäver. Annars får du ett felmeddelande. 

1. När du kör kommandot laddas modell- och bedömningsfilerna upp till det lagringskonto du skapade i miljökonfigurationen. I distributionsprocessen skapas en Docker-avbildning med din modell, ditt schema och din bedömningsfil, som sedan skickas till Azure Container Registry: **\<ACR_name\>.azurecr.io/\<imagename\>:\<version\>**. 

   Kommandot hämtar sedan avbildningen lokalt till datorn och startar en Docker-container baserat på avbildningen. Om miljön är konfigurerad i klusterläge distribueras Docker-behållaren till Azure Container Registry Kubernetes-klustret i stället.

   Som en del av distributionen skapas en HTTP REST-slutpunkt för webbtjänsten lokalt på datorn. Efter några minuter bör kommandot slutföras med ett meddelande. Nu är webbtjänsten färdig att börja användas!

1. Använd kommandot **docker ps** för att se Docker-containern som körs:

   ```azurecli
   docker ps
   ```

## <a name="optional-alternative-create-a-real-time-web-service-by-using-separate-commands"></a>[Valfritt alternativ] Skapa en realtidswebbtjänst med separata kommandon
Som ett alternativ till kommandot **az ml service create realtime** som visades tidigare kan du utföra stegen separat. 

Registrera först modellen. Generera sedan manifestet, skapa Docker-avbildningen och skapa webbtjänsten. Med den här stegvisa metoden får du mer flexibilitet vid varje steg. Du kan även återanvända entiteterna som genererats i tidigare steg så att du endast återskapar entiteterna när det behövs.

1. Registrera modellen genom att ange pickle-filnamnet

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Det här kommandot genererar ett modell-ID.

1. Skapa ett manifest.

   Använd följande kommando för att skapa ett manifest och ange modell-ID:t från föregående steg:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json -c aml_config\conda_dependencies.yml
   ```
   Det här kommandot genererar ett manifest-ID.

1. Skapa en Docker-avbildning.

   Använd följande kommando för att skapa en Docker-avbildning och ange manifest-ID:t från föregående steg. Du kan även inkludera conda-beroenden med hjälp av växeln `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> 
   ```
   Det här kommandot genererar ett Docker-avbildnings-ID.
   
1. Skapa tjänsten.

   Använd följande kommando för att skapa en tjänst och ange avbildnings-ID:t från föregående steg:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Det här kommandot genererar ett webbtjänst-ID.

Nu är du redo att köra webbtjänsten.

## <a name="run-the-real-time-web-service"></a>Köra realtidswebbtjänsten

Testa **irisapp**-webbtjänsten som körs med hjälp av en JSON-kodad post med en matris med fyra slumptal.

1. Webbtjänsten innehåller exempeldata. Vid körning i lokalt läge kan du anropa kommandot **az ml service usage realtime**. Anropet hämtar ett exempelkörningskommando som du kan använda för att testa tjänsten. Anropet hämtar även en bedömnings-URL som du kan använda när du ska införliva tjänsten i dina egna anpassade appar.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

1. Om du vill testa tjänsten kör du tjänstkörningskommandot som returnerades:
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d '{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}'
   ```

   Utdata är **”Iris-setosa”**, som är den förväntade klassen. (Dina resultat kan vara annorlunda.) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Visa insamlade data i Azure Blob Storage

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Leta rätt på dina lagringskonton. Det gör du genom att välja **Alla tjänster**.

1. I sökrutan skriver du **Lagringskonton** och sedan trycker du på Retur.

1. Från sökrutan **Lagringskonton** väljer du den **lagringskontoresurs** som motsvarar din miljö. 

   > [!TIP]
   > Gör så här för att avgöra vilket lagringskonto som används:
   > 1. Öppna Machine Learning Workbench.
   > 1. Välj det projekt som du arbetar med.
   > 1. Öppna kommandotolken från menyn **Arkiv**.
   > 1. Skriv `az ml env show -v` i kommandoraden och kontrollera värdet *storage_account*. Det här är namnet på ditt lagringskonto.

1. När fönstret **Lagringskonto** har öppnats väljer du **Blobar** från avsnittet **Tjänster**. Leta rätt på containern med namnet **modeldata**. 
 
   Om du inte ser några data kan du behöva vänta i upp till 10 minuter efter den första begäran till webbtjänsten för att se data spridas till lagringskontot.

   Data flödar till blobar med följande containersökväg:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

1. Du kan använda dessa data från Azure Blob Storage. Det finns en mängd olika verktyg som använder både Microsoft-program och verktyg med öppen källkod, som till exempel:

   * Machine Learning: Öppna csv-filen genom att lägga till csv-filen som en datakälla.

   * Excel: Öppna de dagliga csv-filerna som ett kalkylblad.

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): Skapa diagram med data från csv-data i blobar.

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): Läs in csv-data till en Hive-tabell och kör SQL-frågor direkt mot blobar.

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): Skapa en DataFrame med en stor andel CSV-data.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg
I den här tredje delen av självstudieserien i tre delar har du lärt dig att använda Machine Learning-tjänsten för att:
> [!div class="checklist"]
> * Leta rätt på modellfilen.
> * Generera ett bedömningsskript och en schemafil.
> * Förbereda miljön.
> * Skapa en realtidswebbtjänst.
> * Köra realtidswebbtjänsten.
> * Granska blob-utdata. 

Du har kört ett utbildningsskript i olika beräkningsmiljöer. Du har skapat, serialiserat och operationaliserat en modell via en Docker-baserad webbtjänst. 

Nu är du redo för avancerad förberedelse av data:
> [!div class="nextstepaction"]
> [Självstudie 4 – Avancerad dataförberedelse](tutorial-bikeshare-dataprep.md)
