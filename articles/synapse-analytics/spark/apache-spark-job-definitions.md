---
title: 'Självstudie – Apache Spark för Azure Synapse Analytics: Apache Spark jobb definition för Synapse'
description: Självstudie – Använd Azure Synapse Analytics för att skapa Spark-jobb definitioner och skicka dem till en Apache Spark för Azure Synapse Analytics-poolen.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587943"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Självstudie: använda Azure Synapse Analytics för att skapa Apache Spark jobb definitioner för Synapse Spark-pooler

Den här självstudien visar hur du använder Azure Synapse Analytics för att skapa Spark-jobb definitioner och sedan skickar dem till en Synapse Spark-pool. Du kan använda plugin-programmet på några sätt:

* Utveckla och skicka en spark-jobbiljett på en Synapse Spark-pool.
* Visa jobb information efter överföring.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
>
> * Utveckla och skicka en spark-jobbiljett på en Synapse Spark-pool.
> * Visa jobb information efter överföring.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Synapse Analytics-arbetsyta. Instruktioner finns i [skapa en Azure Synapse Analytics-arbetsyta](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Kom igång

Innan du skickar en spark-jobbiljett måste du vara ägare till Storage BLOB-data för det ADLS Gen2-filsystem som du vill arbeta med. Om du inte gör det måste du lägga till behörigheten manuellt.

### <a name="scenario-1-add-permission"></a>Scenario 1: Lägg till behörighet

1. Öppna [Microsoft Azure](https://ms.portal.azure.com)och öppna lagrings konto.

2. Klicka på **behållare**och skapa ett **fil system**. I den här självstudien används `sparkjob`.

    ![Klicka på knappen Skicka för att skicka Spark-jobbet definition](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Dialog rutan för Spark-sändning](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Öppna `sparkjob` , klicka på **Access Control (IAM)** och klicka sedan på **Lägg till** och välj **Lägg till roll tilldelning**.

    ![Klicka på knappen Skicka för att skicka Spark-jobbet definition](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Klicka på knappen Skicka för att skicka Spark-jobbet definition](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klicka på **roll tilldelningar**, indata för användar namn och kontrol lera sedan användar rollen.

    ![Klicka på knappen Skicka för att skicka Spark-jobbet definition](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenario 2: förbereda mappstruktur

Innan du skickar en spark-jobbiljett måste du använda ett jobb för att ladda upp filer till ADLS Gen2 och förbereda mappstruktur där. Vi använder Storage-noden i Synapse Studio för att lagra filer.

1. Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klicka på **data**, Välj **lagrings konton**och ladda upp relevanta filer till ADLS Gen2 fil systemet. Vi stöder Scala, Java, .NET och python. I den här självstudien används exemplet i bilden som en demonstration. du kan ändra projekt strukturen som du vill.

    ![Ange värdet för Spark-jobbets definition](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Skapa en spark-jobb definition

1. Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/)och välj **utveckla**.

2. Välj **Spark-jobb definitioner** från det vänstra fönstret.

3. Klicka på noden **åtgärder** till höger om jobb definitioner för Spark.

     ![Skapa ny Spark-jobb definition](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. I list rutan **åtgärder** väljer du **ny Spark jobb definition**

     ![Skapa ny Spark-jobb definition](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. I fönstret ny Spark-jobbiljett väljer du språk och anger följande information:  

   * Välj **språk** som **Spark (Scala)**.

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Jobb definitions namn| Ange ett namn för din spark-jobb definition.  I den här självstudien används `job definition sample`. Detta namn kan uppdateras när som helst tills det publiceras.|  
    |Huvud definitions fil| Huvud filen som används för jobbet. Välj en JAR-fil från lagrings platsen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. |
    |Huvud klass namn| Den fullständigt kvalificerade identifieraren eller den huvudsakliga klass som finns i huvud definitions filen.|
    |Kommando rads argument| Valfria argument för jobbet.|
    |Reference-filer| Ytterligare filer som används som referens i huvud definitions filen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
    |Spark-pool| Jobbet skickas till den valda Spark-poolen.|
    |Spark-version| Den version av Spark som Spark-poolen körs på.|
    |Körare| Antal körningar som anges i den angivna Spark-poolen för jobbet.|
    |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Spark-poolen för jobbet.|  
    |Driv rutins storlek| Antal kärnor och minne som ska användas för driv rutin som anges i den angivna Spark-poolen för jobbet.|

    ![Ange värdet för Spark-jobbets definition](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Välj **språk** som **PySpark (python)**.

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Jobb definitions namn| Ange ett namn för din spark-jobb definition.  I den här självstudien används `job definition sample`. Detta namn kan uppdateras när som helst tills det publiceras.|  
    |Huvud definitions fil| Huvud filen som används för jobbet. Välj en PY-fil från lagrings platsen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
    |Kommando rads argument| Valfria argument för jobbet.|
    |Reference-filer| Ytterligare filer som används som referens i huvud definitions filen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
    |Spark-pool| Jobbet skickas till den valda Spark-poolen.|
    |Spark-version| Den version av Spark som Spark-poolen körs på.|
    |Körare| Antal körningar som anges i den angivna Spark-poolen för jobbet.|
    |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Spark-poolen för jobbet.|  
    |Driv rutins storlek| Antal kärnor och minne som ska användas för driv rutin som anges i den angivna Spark-poolen för jobbet.|

    ![Ange värdet för Spark-jobbets definition](./media/apache-spark-job-definitions/create-py-definition.png)

   * Välj **språk** som **.net Spark (C#/f #)**.

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Jobb definitions namn| Ange ett namn för din spark-jobb definition.  I den här självstudien används `job definition sample`. Detta namn kan uppdateras när som helst tills det publiceras.|  
    |Huvud definitions fil| Huvud filen som används för jobbet. Välj en ZIP-fil som innehåller ditt .NET for Spark-program (det vill säga den viktigaste körbara filen, DLL-filer som innehåller användardefinierade funktioner och andra filer som krävs) från din lagrings plats. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
    |Huvud program fil| Den körbara filen i huvud definitionens ZIP-fil.|
    |Kommando rads argument| Valfria argument för jobbet.|
    |Reference-filer| Ytterligare filer som krävs av arbetsnoderna för att köra .NET for Spark-programmet som inte ingår i huvud definitionens ZIP-fil (det vill säga beroende jar v7, ytterligare användardefinierade funktions-DLL-filer och andra konfigurationsfiler). Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
    |Spark-pool| Jobbet skickas till den valda Spark-poolen.|
    |Spark-version| Den version av Spark som Spark-poolen körs på.|
    |Körare| Antal körningar som anges i den angivna Spark-poolen för jobbet.|
    |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Spark-poolen för jobbet.|  
    |Driv rutins storlek| Antal kärnor och minne som ska användas för driv rutin som anges i den angivna Spark-poolen för jobbet.|

    ![Ange värdet för Spark-jobbets definition](./media/apache-spark-job-definitions/create-net-definition.png)

6. Välj **publicera** för att spara Spark-jobbets definition.

    ![Publicera Spark jobb definition](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Skicka en spark Job-Definition

När du har skapat en spark-jobbiljett kan du skicka den till en Synapse Spark-pool. Se till att du har gått igenom stegen i **Get-Started** innan du provar exemplen i den här delen.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenario 1: skicka Spark jobb definition

1. Öppna ett Spark-jobb definitions fönster genom att klicka på det.

      ![Öppna Spark jobb definition för att skicka in ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Klicka på **Skicka** -ikonen för att skicka projektet till den valda Spark-poolen. Du kan klicka på fliken **Spark Monitoring URL** om du vill se LogQuery för Spark-programmet.

    ![Klicka på knappen Skicka för att skicka Spark-jobbet definition](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialog rutan för Spark-sändning](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenario 2: Visa körnings förlopp för Spark-jobb

1. Klicka på **övervaka**och välj alternativet **Spark-program** . Du kan hitta det skickade Spark-programmet.

    ![Visa Spark-program](./media/apache-spark-job-definitions/view-spark-application.png)

2. Klicka sedan på fönstret Spark-program, **LogQuery** visas. Du kan visa jobb körnings förloppet från **LogQuery**.

    ![Visa Spark Application LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: kontrol lera utdatafilen

 1. Klicka på **data**och välj sedan **lagrings konton**. Efter en lyckad körning kan du gå till ADLS Gen2 lagring och kontrol lera att utdata har genererats.

    ![Visa utdatafil](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien demonstreras hur du använder Azure Synapse Analytics för att skapa Spark-jobb definitioner och sedan skickar dem till en Synapse Spark-pool. Nu kan du använda Azure Synapse Analytics för att skapa Power BI data uppsättningar och hantera Power BI data. 

- [Ansluta till datakällor i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualisera med Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
