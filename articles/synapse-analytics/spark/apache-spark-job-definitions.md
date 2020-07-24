---
title: 'Självstudie: skapa Apache Spark jobb definition i Synapse Studio'
description: Självstudie – Använd Azure Synapse Analytics för att skapa Spark-jobb definitioner och skicka dem till en Apache Spark för Azure Synapse Analytics-poolen.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: ac3e163ffefcb7b164860b0c4fa42edc866227e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065633"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Självstudie: skapa Apache Spark jobb definition i Synapse Studio

Den här självstudien visar hur du använder Azure Synapse Studio för att skapa Apache Spark jobb definitioner och sedan skickar dem till en Apache Spark pool.

Den här självstudien omfattar följande uppgifter:

* Skapa en Apache Spark jobb definition för PySpark (python)
* Skapa en Apache Spark jobb definition för Spark (Scala)
* Skapa en Apache Spark jobb definition för .NET Spark (C#/F #)
* Skicka in en Apache Spark jobb definition som ett batch-jobb
* Lägg till en Apache Spark jobb definition i pipeline

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med den här självstudien måste du uppfylla följande krav:

* En Azure Synapse Analytics-arbetsyta. Instruktioner finns i [skapa en Azure Synapse Analytics-arbetsyta](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* En Apache Spark pool
* Ett ADLS Gen2 lagrings konto. Du måste vara ägare till Storage BLOB-data för det ADLS Gen2-filsystem som du vill arbeta med. Om du inte gör det måste du lägga till behörigheten manuellt.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Skapa en Apache Spark jobb definition för PySpark (python)

I det här avsnittet skapar du en Apache Spark jobb definition för PySpark (python).

1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Du kan gå till [exempel filer för att skapa Apache Spark jobb definitioner](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) för att ladda ned **WORDCOUNT.py** och **shakespear.txt**. Ladda sedan upp dessa filer till Azure Storage: Klicka på **data**, Välj **lagrings konton**och ladda upp relaterade filer till ditt ADLS Gen2-filsystem. Hoppa över det här steget om filerna redan finns i Azure Storage. 

     ![överför python-fil](./media/apache-spark-job-definitions/upload-python-file.png)

3. Klicka på **utveckla** hubb, Välj **Spark-jobb definitioner** i den vänstra rutan, klicka på... Noden åtgärd bredvid **jobb definitionen Spark**och välj sedan **ny Spark Job Definition** i snabb menyn.

     ![Skapa ny definition för python](./media/apache-spark-job-definitions/create-new-definition.png)

4. Välj **PySpark (python)** från List rutan språk i Apache Spark huvud fönstret för jobb definitionen.

5. Fyll i information för Apache Spark jobb definition. Du kan kopiera exempel informationen.

     |  Egenskap   | Beskrivning   |  
     | ----- | ----- |  
     |Jobb definitions namn| Ange ett namn för din Apache Spark jobb definition. Detta namn kan uppdateras när som helst tills det publiceras. Urvalsundersökningar`job definition sample`|
     |Huvud definitions fil| Huvud filen som används för jobbet. Välj en PY-fil från lagrings platsen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. Urvalsundersökningar`abfss://…/path/to/wordcount.py`|
     |Kommando rads argument| Valfria argument för jobbet. Urvalsundersökningar`abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Reference-filer| Ytterligare filer som används som referens i huvud definitions filen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. |
     |Spark-pool| Jobbet skickas till den valda Apache Spark poolen.|
     |Spark-version| Den version av Apache Spark som Apache Sparks poolen körs på.|
     |Körare| Antal körningar som anges i den angivna Apache Spark poolen för jobbet.|
     |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Apache Spark poolen för jobbet.|  
     |Driv rutins storlek| Antal kärnor och minne som ska användas för den driv rutin som anges i den angivna Apache Spark poolen för jobbet.|

     ![Ange värdet för Spark-jobbets definition för python](./media/apache-spark-job-definitions/create-py-definition.png)

6. Välj **publicera** för att spara Apache Spark jobb definitionen.

     ![publicera py-definition](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Skapa en Apache Spark jobb definition för Apache Spark (Scala)

I det här avsnittet skapar du en Apache Spark jobb definition för Apache Spark (Scala).

 1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Du kan gå till [exempelfiler för att skapa Apache Spark jobb definitioner](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) för att ladda ned **WORDCOUNT. jar** och **shakespear.txt**. Ladda sedan upp dessa filer till Azure Storage: Klicka på **data**, Välj **lagrings konton**och ladda upp relaterade filer till ditt ADLS Gen2-filsystem. Hoppa över det här steget om filerna redan finns i Azure Storage. 
 
     ![Förbered Scala-struktur](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Klicka på **utveckla** hubb, Välj **Spark-jobb definitioner** i den vänstra rutan, klicka på... Noden åtgärd bredvid **jobb definitionen Spark**och välj sedan **ny Spark Job Definition** i snabb menyn.
     ![Skapa ny definition för Scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Välj **Spark (Scala)** i list rutan språk i den Apache Spark jobb definitionens huvud fönster.

 5. Fyll i information för Apache Spark jobb definition. Du kan kopiera exempel informationen.

     |  Egenskap   | Beskrivning   |  
     | ----- | ----- |  
     |Jobb definitions namn| Ange ett namn för din Apache Spark jobb definition. Detta namn kan uppdateras när som helst tills det publiceras. Urvalsundersökningar`job definition sample`|
     |Huvud definitions fil| Huvud filen som används för jobbet. Välj en JAR-fil från lagrings platsen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. Urvalsundersökningar`abfss://…/path/to/wordcount.jar`|
     |Huvud klass namn| Den fullständigt kvalificerade identifieraren eller den huvudsakliga klass som finns i huvud definitions filen. Urvalsundersökningar`WordCount`|
     |Kommando rads argument| Valfria argument för jobbet. Urvalsundersökningar`abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Reference-filer| Ytterligare filer som används som referens i huvud definitions filen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
     |Spark-pool| Jobbet skickas till den valda Apache Spark poolen.|
     |Spark-version| Den version av Apache Spark som Apache Sparks poolen körs på.|
     |Körare| Antal körningar som anges i den angivna Apache Spark poolen för jobbet.|  
     |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Apache Spark poolen för jobbet.|
     |Driv rutins storlek| Antal kärnor och minne som ska användas för den driv rutin som anges i den angivna Apache Spark poolen för jobbet.|

     ![Ange värdet för Spark-jobbets definition för Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Välj **publicera** för att spara Apache Spark jobb definitionen.

     ![publicera Scala-definition](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Skapa en Apache Spark jobb definition för .NET Spark (C#/F #)

I det här avsnittet skapar du en Apache Spark jobb definition för .NET Spark (C#/F #).
 1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Du kan gå till [exempel filer för att skapa Apache Spark jobb definitioner](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) för att ladda ned **wordcount.zip** och **shakespear.txt**. Ladda sedan upp dessa filer till Azure Storage: Klicka på **data**, Välj **lagrings konton**och ladda upp relaterade filer till ditt ADLS Gen2-filsystem. Hoppa över det här steget om filerna redan finns i Azure Storage. 

     ![Förbered dotNet-struktur](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Klicka på **utveckla** hubb, Välj **Spark-jobb definitioner** i den vänstra rutan, klicka på... Noden åtgärd bredvid **jobb definitionen Spark**och välj sedan **ny Spark Job Definition** i snabb menyn.

     ![Skapa ny definition för dotNET](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Välj **.net Spark (C#/f #)** från List rutan språk i fönstret för Apache Spark jobb definitions huvud.

 5. Fyll i information för Apache Spark jobb definition. Du kan kopiera exempel informationen.
    
     |  Egenskap   | Beskrivning   |  
     | ----- | ----- |  
     |Jobb definitions namn| Ange ett namn för din Apache Spark jobb definition. Detta namn kan uppdateras när som helst tills det publiceras. Urvalsundersökningar`job definition sample`|
     |Huvud definitions fil| Huvud filen som används för jobbet. Välj en ZIP-fil som innehåller ditt .NET för Apache Spark-program (det vill säga den viktigaste körbara filen, DLL-filer som innehåller användardefinierade funktioner och andra filer som krävs) från din lagrings plats. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. Urvalsundersökningar`abfss://…/path/to/wordcount.zip`|
     |Huvud program fil| Den körbara filen i huvud definitionens ZIP-fil. Urvalsundersökningar`WordCount`|
     |Kommando rads argument| Valfria argument för jobbet. Urvalsundersökningar`abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Reference-filer| Ytterligare filer som krävs av arbetsnoderna för att köra .NET för Apache Spark program som inte ingår i huvud definitionens ZIP-fil (det vill säga beroende jar v7, ytterligare användardefinierade funktions-DLL-filer och andra konfigurationsfiler). Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
     |Spark-pool| Jobbet skickas till den valda Apache Spark poolen.|
     |Spark-version| Den version av Apache Spark som Apache Sparks poolen körs på.|
     |Körare| Antal körningar som anges i den angivna Apache Spark poolen för jobbet.|  
     |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Apache Spark poolen för jobbet.|
     |Driv rutins storlek| Antal kärnor och minne som ska användas för den driv rutin som anges i den angivna Apache Spark poolen för jobbet.|

     ![Ange värdet för Spark-jobbets definition för dotNET](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Välj **publicera** för att spara Apache Spark jobb definitionen.

      ![publicera dotNet-definition](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Skicka in en Apache Spark jobb definition som ett batch-jobb

När du har skapat en Apache Spark jobb definition kan du skicka den till en Apache Spark-pool. Se till att du är ägare till Storage BLOB-data för det ADLS Gen2-filsystem som du vill arbeta med. Om du inte gör det måste du lägga till behörigheten manuellt.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scenario 1: skicka Apache Spark jobb definition
 1. Öppna fönstret Apache Spark jobb definition genom att klicka på det.

      ![Öppna Spark jobb definition för att skicka in ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Klicka på **Skicka** -ikonen för att skicka projektet till den valda Apache Spark poolen. Du kan klicka på fliken **Spark Monitoring URL** om du vill se LogQuery för det Apache Spark programmet.

    ![Klicka på knappen Skicka för att skicka Spark-jobbet definition](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialog rutan för Spark-sändning](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scenario 2: Visa Apache Spark jobb som kör förlopp

 1. Klicka på **övervaka**och välj alternativet **Spark-program** . Du kan hitta det skickade Apache Spark programmet.

     ![Visa Spark-program](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Klicka sedan på fönstret Apache Spark program, **LogQuery** visas. Du kan visa jobb körnings förloppet från **LogQuery**.
     
     ![Visa Spark Application LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: kontrol lera utdatafilen

 1. Klicka på **data**och välj sedan **lagrings konton**. Efter en lyckad körning kan du gå till ADLS Gen2 lagring och kontrol lera att utdata har genererats.

     ![Visa utdatafil](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Lägg till en Apache Spark jobb definition i pipeline

I det här avsnittet lägger du till en Apache Spark jobb definition i pipeline.

 1. Öppna en befintlig Apache Spark jobb definition.

 2. Klicka på ikonen längst upp till höger i Apache Spark jobb definition, Välj **befintlig pipeline**eller **ny pipeline**. Mer information hittar du på pipeline-sidan.

     ![Lägg till i pipeline](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Lägg till i pipeline](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Nästa steg

Den här självstudien visar hur du använder Azure Synapse Studio för att skapa Apache Spark jobb definitioner och sedan skickar dem till en Apache Spark pool. Nu kan du använda Azure Synapse Studio för att skapa Power BI data uppsättningar och hantera Power BI data.

