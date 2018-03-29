---
title: Förbereda data för klassificering av Iris, självstudie i Azure Machine Learning (förhandsversion) | Microsoft Docs
description: I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversionen) från slutpunkt till slutpunkt. Det här är del ett, som beskriver förberedelse av data.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: 7eec99cc0f63d4d82a7eda6dffc6c45471d9915d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>Självstudie 1: Klassificera Iris – förbereda data

Azure Machine Learning (förhandsversionen) är en integrerad, avancerad lösning för datavetenskap och analys som datatekniker kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är **del ett i en serie med tre delar**. I den här självstudien går vi igenom grunderna i Azure Machine Learning (förhandsversion) och visar hur du:

> [!div class="checklist"]
> * Skapa ett projekt i Azure Machine Learning Workbench
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar ett dataförberedningspaket

Den här självstudien använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

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

1. Fyll i formulärfälten och välj knappen **Skapa** för att skapa ett nytt projekt i Workbench.

   Fält|Föreslaget värde för självstudien|Beskrivning
   ---|---|---
   Projektnamn | myIris |Ange ett unikt namn som identifierar kontot. Du kan till exempel använda ditt eget namn eller namnet på din avdelning eller ditt projekt. Välj det som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). 
   Projektkatalog | c:\Temp\ | Ange i vilken katalogen projektet ska skapas.
   Projektbeskrivning | _lämna tomt_ | Valfritt fält som kan användas för att beskriva projekten.
   URL till GIT-lagringsplats för Visualstudio.com |_lämna tomt_ | Valfritt fält. Du kan associera ett projekt med en Git-lagringsplats i Visual Studio Team Services för källkontroll och samarbete. [Lär dig hur det konfigureras](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Vald arbetsyta | IrisGarden (om det finns) | Välj en arbetsyta som du har skapat för ditt experimenteringskonto i Azure Portal. <br/>Om du följde snabbstarten bör du ha en arbetsyta med namnet IrisGarden. Om inte väljer du den som du skapade när du skapade ditt experimenteringskonto eller något annan som du vill använda.
   Projektmall | Klassificera Iris | Mallar innehåller skript och data som du kan använda för att utforska produkten. Den här mallen innehåller de skript och data som behövs för den här snabbstarten och andra självstudier på den här på dokumentationsplatsen. 

   ![Nytt projekt](media/tutorial-classifying-iris/new_project.png)
 
 Ett nytt projekt skapas och instrumentpanelen för projektet öppnas med projektet. Nu kan du utforska projektets startsida, datakällor, anteckningsböcker och källkodsfiler. 

   ![Öppna projekt](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Skapa ett databearbetningspaket

Du kan därefter utforska och börja förbereda data i Azure Machine Learning Workbench. Varje transformering du utför i Workbench lagras i JSON-format i ett paket för förberedelse av lokala data (*.dprep-fil). Det här paketet för förberedelse av data är den primära behållaren för förberedelse av data i Workbench.

Det här paketet för förberedelse av data kan lämnas senare till en runtime, till exempel local-C#/CoreCLR, Scala/Spark eller Scala/HDI. 

1. Välj mappikonen för att öppna filvyn, välj sedan **iris.csv** för att öppna filen.

   Filen innehåller en tabell med 5 kolumner och 50 rader. Fyra kolumner är numeriska kolumner. Den femte kolumnen är en strängmålkolumn. Ingen av kolumnerna har rubriknamn.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Inkludera inte datafiler i projektmappen, särskilt om filerna är stora. Eftersom datafilen **iris.csv** är mycket liten inkluderades den i den här mallen i exempelsyfte. Mer information finns i [Läsa och skriva stora datafiler](how-to-read-write-files.md).

2. I **datavyn** klickar du på plustecknet (**+**) för att lägga till en ny datakälla. Sidan **Lägg till datakälla** öppnas. 

   ![Datavy i Azure Machine Learning Workbench](media/tutorial-classifying-iris/data_view.png)

3. Välj **Textfiler (\*.csv, \*.JSON, \*.txt...)** och klicka på **Nästa**.
   ![Datakälla i Azure Machine Learning Workbench](media/tutorial-classifying-iris/data-source.png)

4. Bläddra till filen **iris.csv** och klicka på **Slutför**. Här används standardvärden för parametrar som till exempel avgränsare och datatyper.

   >[!IMPORTANT]
   >Se till att du väljer filen **iris.csv** från den aktuella projektkatalogen för den här övningen. Annars kan senare steg misslyckas.
 
   ![Välj iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Den nya filen som heter **iris-1.dsource** skapas. Filen får ett unikt namn med ”-1” eftersom exempelprojektet redan har en onumrerad version av filen **iris.dsource**.  

   Filen öppnas och data visas. En serie med kolumnrubriker, från **Column1** till **Column5**, läggs automatiskt till i datauppsättningen. Om du rullar ned till slutet ser du att den sista raden i datauppsättningen är tom. Raden är tom på grund av en extra radbrytning i csv-filen.

   ![Datavy för iris](media/tutorial-classifying-iris/iris_data_view.png)

1. Välj knappen **Mått**. Histogram genereras och visas.

   Du kan växla tillbaka till datavyn genom att välja knappen **Data**.
   
   ![Datavy för iris](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Här visas histogram. En komplett uppsättning statistik som har beräknats för varje kolumn. 

   ![Datavy för iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Börja skapa ett dataförberedningspaket genom att välja knappen **Förbered**. Dialogrutan **Förbered** öppnas. 

   Exempelprojektet innehåller en **iris.dprep**-dataförberedningsfil som är vald som standard. 

   ![Datavy för iris](media/tutorial-classifying-iris/prepare.png)

1. Skapa ett nytt dataförberedningspaket genom att välja **+ New Data Preparation Package** (+ Nytt dataförberedningspaket) på den nedrullningsbara menyn.

   ![Datavy för iris](media/tutorial-classifying-iris/prepare_new.png)

1. Ange ett nytt värde för paketets namn (med **iris-1**) och välj sedan **OK**.

   Ett nytt dataförberedningspaket med namnet **iris-1.dprep** skapas och öppnas i redigeraren för dataförberedning.

   ![Datavy för iris](media/tutorial-classifying-iris/prepare_iris-1.png)

   Nu ska vi utföra lite grundläggande databearbetning. 

1. Markera varje kolumnrubrik för att redigera rubriktexten. Byt sedan namn varje kolumn på följande sätt: 

   Ange **Sepal Length** (Foderbladlängd), **Sepal Width** (Foderbladbredd), **Petal Length** (Kronbladlängd), **Petal Width** (Kronbladbredd) och **Species** (Typer) för de fem kolumnerna i den här ordningen.

   ![Byta namn på kolumner](media/tutorial-classifying-iris/rename_column.png)

1. Räkna distinkta värden:
   1. Välj kolumnen **Species** (Typer)
   1. Högerklicka för att välja den. 
   1. Välj **Antal värden** på den nedrullningsbara menyn. 

   Rutan **Inspectors** (Kontroller) öppnas nedanför data. Ett histogram med fyra staplar visas. Målkolumnen har fyra separata värden: **Iris-virginica**, **Iris-versicolor**, **Iris-setosa** och ett **(null)**-värde.

   ![Välj Antal värden](media/tutorial-classifying-iris/value_count.png)

   ![Histogram över antal värden](media/tutorial-classifying-iris/filter_out.png)

1. Om du vill filtrera bort null-värden väljer du fältet ”(null)” och sedan minustecknet (**-**). 

   Sedan blir (null)-raden grå för att visa att den har filtrerats bort. 

   ![Filtrera bort null-värden](media/tutorial-classifying-iris/filter_out2.png)

1. Lägg märke till de enskilda stegen för dataförberedelse som beskrivs i fönstret **STEG**. När du ändrade namn på kolumnerna och filtrerade raderna med null-värden registrerades varje åtgärd som ett dataförberedningssteg. Du kan redigera enskilda steg för att justera inställningarna, ändra ordning på stegen eller ta bort steg.

   ![Steg](media/tutorial-classifying-iris/steps.png)

1. Stäng redigeraren för dataförberedning. Stäng fliken genom att välja **x**-ikonen på fliken **iris-1** med grafikonen. Arbetet sparas automatiskt i filen **iris-1.dprep** som visas under rubriken **Data Preparations** (Dataförberedelser).

   ![Stäng](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generera Python/PySpark-kod som anropar ett dataförberedningspaket

 Ett dataförberedningspakets utdata kan utforskas direkt en Python eller Jupyter Notebook. Paketen kan köras i flera körningar inklusive lokal Python, Spark (även i Docker) och HDInsight. 

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

   Beroende på i vilken kontext den här koden körs representerar `df` olika typer av dataramar:
   + Vid Python-körningar används en [pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).
   + Vid körning i ett Spark-sammanhang används en [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html). 
   
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
