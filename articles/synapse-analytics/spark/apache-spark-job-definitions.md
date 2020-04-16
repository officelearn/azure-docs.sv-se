---
title: 'Självstudiekurs - Azure Synapse Analytics: Spark-jobbdefinition för Synapse'
description: Självstudiekurs - Använd Azure Synapse Analytics för att skapa Spark-jobbdefinitioner och skicka dem till en Synapse Spark-pool.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422972"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Självstudiekurs: Använd Azure Synapse Analytics för att skapa Apache Spark-jobbdefinitioner för Synapse Spark-pooler

Den här självstudien visar hur du använder Azure Synapse Analytics för att skapa Spark-jobbdefinitioner och sedan skicka dem till en Synapse Spark-pool. Du kan använda plugin-programmet på några olika sätt:

* Utveckla och skicka en Spark-jobbdefinition på en Synapse Spark-pool.
* Visa jobbinformation efter inlämning.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Utveckla och skicka en Spark-jobbdefinition på en Synapse Spark-pool.
> * Visa jobbinformation efter inlämning.

## <a name="prerequisites"></a>Krav

* En Azure Synapse Analytics-arbetsyta. Instruktioner finns i [Skapa en Azure Synapse Analytics-arbetsyta](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Kom igång

Innan du skickar en Spark-jobbdefinition måste du vara storage blob data-ägare för ADLS Gen2-filsystemet som du vill arbeta med. Om du inte är det måste du lägga till behörigheten manuellt.

### <a name="scenario-1-add-permission"></a>Scenario 1: Lägg till behörighet

1. Öppna [Microsoft Azure](https://ms.portal.azure.com)och öppna sedan lagringskontot.

2. Klicka på **Behållare**och skapa sedan ett **filsystem**. I den här självstudien används `sparkjob`.

    ![Klicka på skicka-knappen för att skicka spark-jobbdefinition](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Dialogrutan Spark-inlämning](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Öppna `sparkjob`, klicka på **Åtkomstkontroll(IAM)** och klicka sedan på **Lägg till** och välj **Lägg till rolltilldelning**.

    ![Klicka på skicka-knappen för att skicka spark-jobbdefinition](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Klicka på skicka-knappen för att skicka spark-jobbdefinition](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klicka på **Rolltilldelningar**, ange användarnamn och verifiera sedan användarrollen.

    ![Klicka på skicka-knappen för att skicka spark-jobbdefinition](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenario 2: Förbereda mappstrukturen

Innan du skickar en Spark-jobbdefinition måste du ett jobb som du behöver göra överföra filer till ADLS Gen2 och förbereda mappstrukturen där. Vi använder Storage nod i Synapse Studio för att lagra filer.

1. Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klicka på **Data,** välj **Lagringskonton**och Ladda upp relevanta filer till adls Gen2-filsystemet. Vi stöder Scala, Java, .NET och Python. Den här självstudien använder exemplet i figuren som en demonstration, du kan ändra projektstrukturen som du vill.

    ![Ange värdet för spark-jobbdefinitionen](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Skapa en Spark-jobbdefinition

1. Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/)och välj **Utveckla**.

2. Välj **Spark-jobbdefinitioner** i den vänstra rutan.

3. Klicka på **åtgärdsnod** till höger om "Spark-jobbdefinitionerna".

     ![Skapa ny sparkjobbdefinition](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Välj Ny **spark-jobbdefinition** i listrutan **Åtgärder**

     ![Skapa ny sparkjobbdefinition](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. I fönstret Ny spark-jobbdefinition väljer du språk och anger sedan följande information:  

   * Välj **Språk** som **Spark(Scala)**.

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Namn på jobbdefinition| Ange ett namn för spark-jobbdefinitionen.  I den här självstudien används `job definition sample`. Det här namnet kan uppdateras när som helst tills det publiceras.|  
    |Huvuddefinitionsfil| Huvudfilen som används för jobbet. Välj en JAR-fil från ditt lagringsutrymme. Du kan välja **Ladda upp filen** för att överföra filen till ett lagringskonto. |
    |Huvudklassnamn| Den fullständigt kvalificerade identifieraren eller huvudklassen som finns i huvuddefinitionsfilen.|
    |Argument på kommandoraden| Valfria argument till jobbet.|
    |Referensfiler| Ytterligare filer som används som referens i huvuddefinitionsfilen. Du kan välja **Ladda upp filen** för att överföra filen till ett lagringskonto.|
    |Spark pool| Jobbet skickas till den valda Spark-poolen.|
    |Spark-version| Version av Spark som Spark-poolen körs.|
    |Testamentsexekutorer| Antal utförare som ska anges i den angivna Spark-poolen för jobbet.|
    |Körordningsstorlek| Antal kärnor och minne som ska användas för utförare som anges i den angivna Spark-poolen för jobbet.|  
    |Drivrutinsstorlek| Antal kärnor och minne som ska användas för drivrutin som anges i den angivna Spark-poolen för jobbet.|

    ![Ange värdet för spark-jobbdefinitionen](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Välj **Språk** som **PySpark(Python)**.

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Namn på jobbdefinition| Ange ett namn för spark-jobbdefinitionen.  I den här självstudien används `job definition sample`. Det här namnet kan uppdateras när som helst tills det publiceras.|  
    |Huvuddefinitionsfil| Huvudfilen som används för jobbet. Välj en PY-fil från ditt lagringsutrymme. Du kan välja **Ladda upp filen** för att överföra filen till ett lagringskonto.|
    |Argument på kommandoraden| Valfria argument till jobbet.|
    |Referensfiler| Ytterligare filer som används som referens i huvuddefinitionsfilen. Du kan välja **Ladda upp filen** för att överföra filen till ett lagringskonto.|
    |Spark pool| Jobbet skickas till den valda Spark-poolen.|
    |Spark-version| Version av Spark som Spark-poolen körs.|
    |Testamentsexekutorer| Antal utförare som ska anges i den angivna Spark-poolen för jobbet.|
    |Körordningsstorlek| Antal kärnor och minne som ska användas för utförare som anges i den angivna Spark-poolen för jobbet.|  
    |Drivrutinsstorlek| Antal kärnor och minne som ska användas för drivrutin som anges i den angivna Spark-poolen för jobbet.|

    ![Ange värdet för spark-jobbdefinitionen](./media/apache-spark-job-definitions/create-py-definition.png)

   * Välj **Språk** som **.NET Spark(C#/F#)**.

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Namn på jobbdefinition| Ange ett namn för spark-jobbdefinitionen.  I den här självstudien används `job definition sample`. Det här namnet kan uppdateras när som helst tills det publiceras.|  
    |Huvuddefinitionsfil| Huvudfilen som används för jobbet. Välj en ZIP-fil som innehåller ditt .NET for Spark-program (det vill än den huvudsakliga körbara filen, DLL-filer som innehåller användardefinierade funktioner och andra nödvändiga filer) från din lagring. Du kan välja **Ladda upp filen** för att överföra filen till ett lagringskonto.|
    |Huvud körbar fil| Den huvudsakliga körbara filen i huvuddefinitionen ZIP-fil.|
    |Argument på kommandoraden| Valfria argument till jobbet.|
    |Referensfiler| Ytterligare filer som behövs av arbetarnoderna för att köra .NET for Spark-programmet som inte ingår i huvuddefinitionen ZIP-filen (det vill än beroende burkar, ytterligare användardefinierade DLL-filer för funktionen och andra konfigurationsfiler). Du kan välja **Ladda upp filen** för att överföra filen till ett lagringskonto.|
    |Spark pool| Jobbet skickas till den valda Spark-poolen.|
    |Spark-version| Version av Spark som Spark-poolen körs.|
    |Testamentsexekutorer| Antal utförare som ska anges i den angivna Spark-poolen för jobbet.|
    |Körordningsstorlek| Antal kärnor och minne som ska användas för utförare som anges i den angivna Spark-poolen för jobbet.|  
    |Drivrutinsstorlek| Antal kärnor och minne som ska användas för drivrutin som anges i den angivna Spark-poolen för jobbet.|

    ![Ange värdet för spark-jobbdefinitionen](./media/apache-spark-job-definitions/create-net-definition.png)

6. Välj **Publicera** om du vill spara spark-jobbdefinitionen.

    ![Publicera spark-jobbdefinition](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Skicka en Spark-jobbdefinition

När du har skapat en Spark-jobbdefinition kan du skicka den till en Synapse Spark-pool. Kontrollera att du har gått igenom steg i **Kom igång** innan du provar exempel i den här delen.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenario 1: Skicka spark-jobbdefinition

1. Öppna ett sparkjobbdefinitionsfönster genom att klicka på det.

      ![Öppna sparkjobbdefinition som ska skickas ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Klicka på **skicka** ikonen om du vill skicka projektet till den valda Spark-poolen. Du kan klicka på fliken **Spark övervaknings-URL** för att se logquery för Spark-programmet.

    ![Klicka på skicka-knappen för att skicka spark-jobbdefinition](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialogrutan Spark-inlämning](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenario 2: Visa förlopp för spark-jobb som körs

1. Klicka på **Övervaka**och välj sedan alternativet **Spark-program.** Du hittar den inskickade Spark-programmet.

    ![Visa Spark-program](./media/apache-spark-job-definitions/view-spark-application.png)

2. Klicka sedan på Spark-programmet, **logquery-fönstret** visar. Du kan visa jobbkörningsstatusen från **LogQuery**.

    ![Visa spark-program LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: Kontrollera utdatafil

 1. Klicka på **Data**och välj sedan **Lagringskonton**. Efter en lyckad körning kan du gå till ADLS Gen2-lagringen och kontrollera att utdata genereras.

    ![Visa utdatafil](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Nästa steg

Den här självstudien visade hur du använder Azure Synapse Analytics för att skapa Spark-jobbdefinitioner och sedan skicka dem till en Synapse Spark-pool. Därefter kan du använda Azure Synapse Analytics för att skapa Power BI-datauppsättningar och hantera Power BI-data. 

- [Ansluta till datakällor i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualisera med Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
