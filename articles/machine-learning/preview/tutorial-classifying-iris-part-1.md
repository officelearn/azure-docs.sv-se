---
title: "Förbereda data för klassificering av Iris, självstudie i Azure Machine Learning (förhandsversion) | Microsoft Docs"
description: "I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversionen) från slutpunkt till slutpunkt. Det här är del ett, som beskriver förberedelse av data."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Självstudie: Klassificera Iris del 1 – förbereda data

Azure Machine Learning (förhandsversionen) är en integrerad, avancerad lösning för datavetenskap och analys som datatekniker kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del ett i en serie med tre delar. I den här självstudien går vi igenom grunderna i Azure Machine Learning (förhandsversion). Lär dig att:

> [!div class="checklist"]
> * Skapa ett projekt i Azure Machine Learning Workbench
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar ett dataförberedningspaket

Den här självstudien använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i macOS.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Du behöver följande för att kunna slutföra den här självstudiekursen:
- Ett konto för Machine Learning-experimentering
- Azure Machine Learning Workbench installerat

Om du inte redan uppfyller dessa krav kan du följa stegen i artikeln [Snabbstart: Installera och starta](quickstart-installation.md) för att konfigurera det här kontot och installera Azure Machine Learning Workbench-programmet. 

## <a name="create-a-new-project-in-workbench"></a>Skapa ett nytt projekt i Workbench

Om du följde stegen i artikeln [Snabbstart: Installera och starta](quickstart-installation.md) bör du redan ha det här projektet och kan gå vidare till nästa avsnitt.

1. Öppna appen Azure Machine Learning Workbench och logga in om det behövs. 
   
   + I Windows startar du **Machine Learning Workbench** med hjälp av genvägen på skrivbordet. 
   + I macOS väljer du **Azure ML Workbench** i startfönstret.

1. Välj plustecknet (+) i fönstret **PROJEKT** och välj **Nytt projekt**.  

   ![Ny arbetsyta](media/tutorial-classifying-iris/new_ws.png)

1. Fyll i formulärfälten och tryck på knappen **Skapa** för att skapa ett nytt projekt i Workbench.

   Fält|Föreslaget värde för självstudien|Beskrivning
   ---|---|---
   Projektnamn | myIris |Ange ett unikt namn som identifierar kontot. Du kan till exempel använda ditt eget namn eller namnet på din avdelning eller ditt projekt. Välj det som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). 
   Projektkatalog | c:\Temp\ | Ange i vilken katalogen projektet ska skapas.
   Projektbeskrivning | _lämna tomt_ | Valfritt fält som kan användas för att beskriva projekten.
   Visualstudio.com |_lämna tomt_ | Valfritt fält. Ett projekt kan även associeras med en Git-lagringsplats i Visual Studio Team Services för källkontroll och samarbete. [Lär dig hur det konfigureras.](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Arbetsyta | IrisGarden (om det finns) | Välj en arbetsyta som du har skapat för ditt experimenteringskonto i Azure Portal. <br/>Om du följde snabbstarten bör du ha en arbetsyta med namnet IrisGarden. Om inte väljer du den som du skapade när du skapade ditt experimenteringskonto eller något annan som du vill använda.
   Projektmall | Klassificera Iris | Mallar innehåller skript och data som du kan använda för att utforska produkten. Den här mallen innehåller de skript och data som behövs för den här snabbstarten och andra självstudier på den här på dokumentationsplatsen. 

   ![Nytt projekt](media/tutorial-classifying-iris/new_project.png)
 
 Ett nytt projekt skapas och instrumentpanelen för projektet öppnas med projektet. Nu kan du utforska projektets startsida, datakällor, anteckningsböcker och källkodsfiler. 

## <a name="create-a-data-preparation-package"></a>Skapa ett databearbetningspaket

I den här delen av självstudien utforskar du data och startar processen att förbereda data. När du förbereder data i Azure Machine Learning Workbench lagras en JSON-representation av omvandlingarna som du utför i Workbench i ett paket för förberedelse av lokala data (*.dprep-fil). Det här paketet för förberedelse av data är den primära behållaren för förberedelse av data i Workbench.

Det här paketet för förberedelse av data kan lämnas för körning i en runtime, till exempel local-C#/CoreCLR, Scala/Spark eller Scala/HDI. där koden genereras för lämplig runtime för körning. 

1. Välj mappikonen för att öppna filvyn och välj sedan **iris.csv** för att öppna filen.  

   Filen är en tabell med 5 kolumner och 150 rader. Det har fyra numeriska kolumner och en strängmålkolumn. Den har inga kolumnrubriker.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Inkludera inte datafiler i projektmappen, särskilt om filerna är stora. Vi tar med **iris.csv** i den här mallen i demonstrationssyfte eftersom den är mycket liten. Mer information finns i [Läsa och skriva stora datafiler](how-to-read-write-files.md).

2. I **datavyn** klickar du på plustecknet (**+**) för att lägga till en ny datakälla. Sidan **Lägg till datakälla** öppnas. 

   ![Datavy](media/tutorial-classifying-iris/data_view.png)

3. Välj **Textfiler (*.csv, .JSON, .txt ...)**  och klicka på **Nästa**.
   ![Datakälla](media/tutorial-classifying-iris/data-source.png)
   

4. Bläddra till filen **iris.csv** och klicka på **Nästa**.  
 
   ![Välj iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Se till att du väljer filen **iris.csv** från den aktuella projektkatalogen för den här övningen. Annars kan senare steg misslyckas.
   
5. Lämna standardvärdena och klicka på **Slutför**.

6. Den nya filen som heter **iris-1.dsource** skapas. Filen får ett unikt namn med ”-1” eftersom exempelprojektet redan har en onumrerad version av filen **iris.dsource**.  

   Filen öppnas och data visas. En serie med kolumnrubriker, från **Column1** till **Column5**, läggs automatiskt till i datauppsättningen. Om du rullar ned till slutet ser du att den sista raden i datauppsättningen är tom. Raden är tom på grund av en extra radbrytning i csv-filen.

   ![Datavy för iris](media/tutorial-classifying-iris/iris_data_view.png)

7. Välj knappen **Mått**. Här visas histogram. En komplett uppsättning statistik som har beräknats för varje kolumn. Om du vill se data igen kan du välja knappen **Data**. 

   ![Datavy för iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Välj knappen **Förbered**. Dialogrutan **Förbered** öppnas. 

   Exempelprojektet levereras med en **iris.dprep**-fil. Som standard uppmanas du att skapa ett nytt dataflöde i dataförberedningspaketet **iris.dprep** som redan finns. 

   Välj **+ Nytt dataförberedningspaket** på den nedrullningsbara menyn, ange ett nytt värde för paketnamnet, använd **iris-1** och välj sedan **OK**.

   ![Datavy för iris](media/tutorial-classifying-iris/new_dprep.png)

   Ett nytt dataförberedningspaket med namnet **iris-1.dprep** skapas och öppnas i redigeraren för dataförberedning.

9. Nu ska vi utföra lite grundläggande databearbetning. Markera varje kolumnrubrik för att redigera rubriktexten och byt namn på varje kolumn enligt följande: 

   Ange **Sepal Length** (Foderbladlängd), **Sepal Width** (Foderbladbredd), **Petal Length** (Kronbladlängd), **Petal Width** (Kronbladbredd) och **Species** (Typer) för de fem kolumnerna i den här ordningen.

   ![Byta namn på kolumner](media/tutorial-classifying-iris/rename_column.png)

10. Om du vill räkna ut särskilda värden markerar du kolumnen **Species** (Typer) och sedan högerklickar du för att markera den. Välj **Antal värden** på den nedrullningsbara menyn. 

   Den här åtgärden öppnar rutan **Inspectors** (Kontroller) nedanför data. Ett histogram med fyra staplar visas. Målkolumnen har tre separata värden: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** och ett **(null)**-värde.

   ![Välj Antal värden](media/tutorial-classifying-iris/value_count.png)

11. Om du vill filtrera bort null-värden väljer du etiketten ”Null” och sedan minustecknet (**-**). Sedan blir Null-raden grå för att visa att den har filtrerats bort. 

   ![Histogram över antal värden](media/tutorial-classifying-iris/filter_out.png)

12. Lägg märke till de enskilda stegen som beskrivs i fönstret **STEG**. När du ändrade namn på kolumner och filtrerade rader med null-värden registrerades varje åtgärd som ett dataförberedningssteg. Du kan redigera enskilda steg för att justera inställningarna, ändra ordning på stegen eller ta bort steg.

   ![Steg](media/tutorial-classifying-iris/steps.png)

13. Stäng redigeraren för dataförberedning. Välj **Stäng** (x) på fliken **iris-1** med diagramikonen. Arbetet sparas automatiskt i filen **iris-1.dprep** som visas under rubriken **Data Preparations** (Dataförberedelser).

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generera Python/PySpark-kod som anropar ett dataförberedningspaket

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

1. Hitta filen **iris 1.dprep** under fliken Dataförberedelser.

1. Högerklicka på filen **iris 1.dprep** och välj alternativet för att **generera fil med kod för dataåtkomst** på snabbmenyn. 

   ![Generera kod](media/tutorial-classifying-iris/generate_code.png)

   En ny fil med namnet **iris 1.py** öppnas med följande rader kod för att anropa logiken som du skapade som ett paket för förberedelse av data:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Beroende på i vilken kontext den här koden körs representerar `df` olika typer av dataramar. En [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) används när den körs med Python-körning, eller så används en [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) vid körning i Spark. 
   
   Mer information om hur du förbereder data i Azure Machine Learning Workbench finns i guiden som hjälper dig att [komma igång med förberedelse av data](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använt Azure Machine Learning Workbench för att:
> [!div class="checklist"]
> * Skapa ett nytt projekt
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar ett dataförberedningspaket

Nu är du redo att gå vidare till nästa del av självstudieserien, där du får lära dig hur du skapar en Azure Machine Learning-modell:
> [!div class="nextstepaction"]
> [Självstudie 2 – Skapa modeller](tutorial-classifying-iris-part-2.md)
